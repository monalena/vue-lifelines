<template>
    <div>
        <div id="chart"></div>
        <!--<div></div>-->
            <!--<div v-for="(item, i) in selectedLifelines" :key="i">-->
                <!--<div>{{parseDate(item.newDate)}}, {{item.Event.substring(0,120)+""}}</div>-->
            <!--</div>-->
    </div>
</template>

<script>
    import * as d3 from 'd3';
    export default {
        name: 'Lifeline',
        props: ['selectedLifelines', 'specs'],
        data() {
            return {
                convictName: '',
            }
        },
        methods: {
            updateLifeline: function() {
                this.parseName(this.specs);
                this.createChart(this.selectedLifelines);
            },
            parseDate: function(date) {
                let parseDate = d3.timeFormat('%d-%m-%Y');
                return parseDate(date);
            },
            parseName: function(data) {
                this.convictName = data[0].GivenNames + " " + data[0].FamilyName;
            },
            createChart(data) {
                // Define time format
                var timeFormat = d3.timeFormat('%Y');
                var niceTimeFormat = d3.timeFormat('%d-%m-%Y');
                var parseTime = d3.timeParse('%Y-%m-%d');

                let lifeline_idx = 0;
                let min_date = parseTime(data[0][0].Date);
                let max_date = min_date;
                data.forEach(function(lifeline) {
                    lifeline.forEach(function(d) {
                        d.yValue = 0.4+lifeline_idx;
                        d.dates =  parseTime(d.Date);
                        if (d.dates < min_date) {
                            min_date = d.dates;
                        }
                        if (d.dates > max_date) {
                            max_date = d.dates;
                        }
                    });
                    lifeline_idx++;
                });



                // set the dimensions and margins of the graph
                let hgt = 500;
                if (this.selectedLifelines.length > 1) {
                    hgt = 300;
                }
                const margin = {top: 10, right: 20, bottom: 30, left: 50},
                    width = 1200 - margin.left - margin.right,
                    height = hgt * data.length - margin.top - margin.bottom;

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
                const y = d3.scaleLinear().domain([0,data.length]).range([height, 0]);

                // const yAxis = d3.axisLeft(y).ticks(0);


                const x = d3.scaleTime()
                    .domain([min_date, max_date])
                    .range([ 0, width ]);

                // Calculate spacing for x coordinates to avoid overlap
                data.forEach(function(lifeline) {
                    let prev_x = -100;
                    lifeline.forEach(function (d) {
                        d.x = Math.max(prev_x + 24, x(d.dates));
                        prev_x = d.x;
                    });
                });

                const xAxis = d3.axisBottom(x)
                    .tickFormat(timeFormat);

                svg.append('g')
                    .attr('transform', "translate(0," + height + ")")
                    .call(xAxis);

                // svg.append('g')
                //     .call(yAxis)

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

                data.forEach((lifeline) => {
                    // Add the lifeline
                    svg.append("path")
                        .datum(lifeline)
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("d", d3.line()
                            .x(function (d) {
                                return d.x
                            })
                            .y(function (d) {
                                return y(d.yValue)
                            })
                        )
                        .on("mouseover", function () {
                            return tooltip.style("visibility", "visible");
                        })
                        .on("mousemove", function () {
                            return tooltip.style("top",
                                (d3.event.pageY - 10) + "px").style("left", (d3.event.pageX + 10) + "px");
                        })
                        .on("mouseout", function () {
                            return tooltip.style("visibility", "hidden");
                        });


                    lifeline.forEach(function (d) {
                        // draw line connecting to date
                        svg.append('line')
                            .attr("fill", "none")
                            .attr("stroke", "black")
                            .attr("stroke-width", 1)
                            .attr("x1", x(d.dates))
                            .attr("y1", y(d.yValue) + 4)
                            .attr("x2", d.x)
                            .attr("y2", y(d.yValue) + 20)
                        ;
                        // draw line connecting to description
                        svg.append('line')
                            .attr("fill", "none")
                            .attr("stroke", "black")
                            .attr("stroke-width", 1)
                            .attr("x1", x(d.dates))
                            .attr("y1", y(d.yValue) - 4)
                            .attr("x2", d.x)
                            .attr("y2", y(d.yValue) - 20)
                        ;
                    });
                    // draw line to extend lifeline to left (before trial)
                    svg.append('line')
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("x1", x(lifeline[0].dates) - 4)
                        .attr("y1", y(lifeline[0].yValue))
                        .attr("x2", x(lifeline[0].dates) - 50)
                        .attr("y2", y(lifeline[0].yValue))
                    ;
                    // draw dots on lifeline
                    svg.append('g')
                        .selectAll('dot')
                        .data(lifeline)
                        .enter()
                        .append('circle')
                        .attr('cx', function (d) {
                            return x(d.dates);
                        })
                        .attr('cy', function (d) {
                            return y(d.yValue);
                        })
                        .attr('r', function () {
                            return 4;
                        })
                        .style('fill', '#69b3a2')
                        .style('opacity', '1')
                        .attr('stroke', 'black')
                        .on('click', (d) => {
                            if (d.VoyageId !== '') {
                                this.$emit('selectVoyage', d.VoyageId);
                            } else if (d.Person !== '') {
                                this.$emit('selectPerson', d.Person);
                            }

                        })
                        .on("mouseover", function (d) {

                            if (d.VoyageId !== '' || d.Person !== '') {
                                console.log("d", d);
                                d3.select(this).attr('r', 8);
                            }
                        })
                        .on("mouseout", function () {
                            d3.select(this).attr('r', 4);
                        });
                    //
                    // svg.append('g')
                    //     .selectAll('dateText')
                    //     .data(lifeline)
                    //     .enter()
                    //     .append("text")
                    //     .text(d =>  niceTimeFormat(d.dates))
                    //     .attr('x', d => x(d.dates))
                    //     .attr('y', d => y(d.yValue- 0.1));

                    // draw the date beneath the line
                    //Rotation after https://observablehq.com/@weitinglin/d3-rotating-text-labels
                    svg.append('g')
                        .selectAll('dateText')
                        .data(lifeline)
                        .enter()
                        .append("text")
                        .text(d => niceTimeFormat(d.dates))
                        .attr('transform', (d) => {
                            return 'translate( ' + (d.x + 4) + ' , ' + (y(d.yValue)+90) + '),' + 'rotate(-90)';
                        })
                        .attr('x', 0)
                        .attr('y', 0)
                        .on("mouseover", function () {
                            return tooltip.style("visibility", "visible");
                        })
                        .on("mousemove", function () {
                            return tooltip.style("top",
                                (d3.event.pageY - 10) + "px").style("left", (d3.event.pageX + 10) + "px");
                        })
                        .on("mouseout", function () {
                            return tooltip.style("visibility", "hidden");
                        });

                    //draw the event description on top of the line
                    svg.append('g')
                        .selectAll('eventText')
                        .data(lifeline)
                        .enter()
                        .append('text')
                        .text(d => {
                            if (d.eventDescription.length >= 50) {
                                const short = d.eventDescription.substring(0, 50);
                                const pos = short.lastIndexOf(" ");
                                return d.eventDescription.substring(0, pos) + ' ...';
                            } else {
                                return d.eventDescription;
                            }
                        })
                        .attr('transform', (d) => {
                            return 'translate( ' + (d.x - 2) + ' , ' + (y(d.yValue)-22) + '),' + 'rotate(-30)';
                        })
                        .attr('x', 0)
                        .attr('y', 0)
                        .on("mouseover", function (d) {
                            div.transition()
                                .duration(200)
                                .style("opacity", 1);
                            div.html(d.eventDescription)
                                .style("left", (d3.event.pageX) + "px")
                                .style("top", (d3.event.pageY - 50) + "px");
                        })
                        .on("mouseout", function () {
                            div.transition()
                                .duration(500)
                                .style("opacity", 0);
                        });


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
            selectedLifelines: function(newLifeline) {
                this.createChart(newLifeline);
            }
        },
    }
</script>

<style>
    #chart {
        overflow: auto;
        /*max-height: 800px;*/
    }

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
