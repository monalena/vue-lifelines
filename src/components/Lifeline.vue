<template>
    <div>
        <div id="chart"></div>
        <!--<div></div>-->
            <!--<div v-for="(item, i) in lifeline" :key="i">-->
                <!--<div>{{parseDate(item.newDate)}}, {{item.Event.substring(0,120)+""}}</div>-->
            <!--</div>-->
    </div>
</template>

<script>
    import * as d3 from 'd3';
    export default {
        name: 'Lifeline',
        props: ['lifeline', 'specs'],
        data() {
            return {
                convictName: '',
            }
        },
        methods: {
            updateLifeline: function() {
                this.parseName(this.specs);
                this.createChart(this.lifeline);
            },
            parseDate: function(date) {
                let parseDate = d3.timeFormat('%d-%m-%Y');
                return parseDate(date);
            },
            parseName: function(data) {
                this.convictName = data[0].GivenNames + " " + data[0].FamilyName;
            },
            createChart: function (data) {

                // Define time format
                var timeFormat = d3.timeFormat('%Y');
                var niceTimeFormat = d3.timeFormat('%d-%m-%Y');
                var parseTime = d3.timeParse('%Y-%m-%d');

                data.forEach(function(d) {
                    d.yValue = 0.4;
                    d.dates =  parseTime(d.Date);
                })


                // set the dimensions and margins of the graph
                const margin = {top: 10, right: 20, bottom: 30, left: 50},
                    width = 1200 - margin.left - margin.right,
                    height = 400 - margin.top - margin.bottom;

                // append the svg object to the body of the page
                d3.select("#chart").selectAll("*").remove();
                const svg = d3.select('#chart')
                    .append('svg')
                    .attr('width', width + margin.left + margin.right)
                    .attr('height', height + margin.top + margin.bottom)
                    .append('g')
                    .attr('transform',
                        "translate(" + margin.left + "," + margin.top + ")");

                // Add Y and X axes
                const y = d3.scaleLinear().range([height, 0]);

                const yAxis = d3.axisLeft(y).ticks(0);


                const x = d3.scaleTime()
                    .domain(d3.extent(data, function(d) {return (d.dates);}))
                    .range([ 0, width ]);

                const xAxis = d3.axisBottom(x)
                    .tickFormat(timeFormat);

                svg.append('g')
                    .attr('transform', "translate(0," + height + ")")
                    .call(xAxis);

                svg.append('g')
                    .call(yAxis)

                //Convict name tooltip (applied to line, circle and date)
                // https://stackoverflow.com/questions/10805184/show-data-on-mouseover-of-circle
                const tooltip = d3.select("body")
                    .append("div")
                    .style("position", "absolute")
                    .style("z-index", "10")
                    .style("visibility", "hidden")
                    .attr("class", "tooltip")
                    .text(this.convictName);

                // Event description tooltip (applied to event)
                // https://gist.github.com/d3noob/4e4485d94aebf63ae8059258c40f2609
                const div = d3.select("body").append("div")
                    .attr("class", "tooltip")
                    .style("opacity", 0);

                // Add the line
                svg.append("path")
                    .datum(data)
                    .attr("fill", "none")
                    .attr("stroke", "black")
                    .attr("stroke-width", 1)
                    .attr("d", d3.line()
                        .x(function(d) { return x(d.dates) })
                        .y(function(d) { return y(d.yValue) })
                    )
                    .on("mouseover", function(){return tooltip.style("visibility", "visible");})
                    .on("mousemove", function(){return tooltip.style("top",
                        (d3.event.pageY-10)+"px").style("left",(d3.event.pageX+10)+"px");})
                    .on("mouseout", function(){return tooltip.style("visibility", "hidden");});


                svg.append('g')
                    .selectAll('dot')
                    .data(data)
                    .enter()
                    .append('circle')
                    .attr('cx', function(d){return x(d.dates);})
                    .attr('cy', function(d){return y(d.yValue);})
                    .attr('r', function () { return 4; })
                    .style('fill', '#69b3a2')
                    .style('opacity', '1')
                    .attr('stroke', 'black')
                    .on("mouseover", function(){return tooltip.style("visibility", "visible");})
                    .on("mousemove", function(){return tooltip.style("top",
                        (d3.event.pageY-10)+"px").style("left",(d3.event.pageX+10)+"px");})
                    .on("mouseout", function(){return tooltip.style("visibility", "hidden");});
                //
                // svg.append('g')
                //     .selectAll('dateText')
                //     .data(data)
                //     .enter()
                //     .append("text")
                //     .text(d =>  niceTimeFormat(d.dates))
                //     .attr('x', d => x(d.dates))
                //     .attr('y', d => y(d.yValue- 0.1));

                //Rotation after https://observablehq.com/@weitinglin/d3-rotating-text-labels
                svg.append('g')
                    .selectAll('dateText')
                    .data(data)
                    .enter()
                    .append("text")
                    .text(d =>  niceTimeFormat(d.dates))
                    .attr('transform', (d)=>{
                        return 'translate( '+(x(d.dates)+6)+' , '+(height-56)+'),'+ 'rotate(-90)';})
                    .attr('x', 0)
                    .attr('y', 0)
                    .on("mouseover", function(){return tooltip.style("visibility", "visible");})
                    .on("mousemove", function(){return tooltip.style("top",
                        (d3.event.pageY-10)+"px").style("left",(d3.event.pageX+10)+"px");})
                    .on("mouseout", function(){return tooltip.style("visibility", "hidden");});

                svg.append('g')
                    .selectAll('eventText')
                    .data(data)
                    .enter()
                    .append('text')
                    .text(d => d.eventDescription)
                    .attr('transform', (d)=>{
                        return 'translate( '+(x(d.dates)-2)+' , '+(height/2+22)+'),'+ 'rotate(-30)';})
                    .attr('x', 0)
                    .attr('y', 0)
                    .on("mouseover", function(d) {
                        div.transition()
                            .duration(200)
                            .style("opacity", 1);
                        div.html(d.eventDescription)
                            .style("left", (d3.event.pageX) + "px")
                            .style("top", (d3.event.pageY - 50) + "px");
                    })
                    .on("mouseout", function() {
                        div.transition()
                            .duration(500)
                            .style("opacity", 0);
                    });


            }
        },
        mounted() {
            this.updateLifeline();

        },
        watch: {
            specs: function(newSpecs) {
                this.parseName(newSpecs);
            },
            lifeline: function(newLifeline) {

                this.createChart(newLifeline);
            }
        },
    }
</script>

<style>
    text {
        font-size: small;
        font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
    }

    div.tooltip {
        position: absolute;
        text-align: center;
        font-size: small;
        font-weight: bold;
        font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
        background-color: ivory;
        border-radius: 5px;
        padding: 5px;


    }
</style>
