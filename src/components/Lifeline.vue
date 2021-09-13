<template>
    <div>
        <div id="chart"></div>
    </div>
</template>

<script>
    import * as d3 from 'd3';

     const spanRepercuss = new Set([40,51,52]);

    export default {
        name: 'Lifeline',
        props: ['selectedLifelines'],
        data() {
            return {
                convictName: '',
                sentenceColor: { //not used
                    10: "#2a9d8f",
                    13: "#e76f51",
                    14: "#e9c46a",
                    30: "#D3D3D3",
                    31: "#000000"
                },
                offenceColor: {
                    "Colonial OffencesV1I": "#EFB366",
                    "Colonial OffencesV1II": "#8AB17D",
                    "Colonial OffencesV1III": "#264653",
                    "Colonial OffencesV1IV": "#C64C2E",
                    "Colonial OffencesV1V": "#287271"
                }
            }
        },
        methods: {
            updateLifeline: function() {
                // this.parseName(this.specs);
                this.createChart(this.selectedLifelines);
            },
            // helper function display date correctly
            parseDate: function(date) {
                let parseDate = d3.timeFormat('%d-%m-%Y');
                return parseDate(date);
            },
            // helper function to display name - not used atm
            parseName: function(data) {
                this.convictName = data[0].GivenNames + " " + data[0].FamilyName;
            },
            // helper function to draw gavel onto lifelines vis (since vue-font awesome can't be brought into d3 code)
            drawGavel(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.03 0.03) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M504.971 199.362l-22.627-22.627c-9.373-9.373-24.569-9.373-33.941 0l-5.657 5.657L329.608 69.255l5.657-5.657c9.373-9.373 9.373-24.569 0-33.941L312.638 7.029c-9.373-9.373-24.569-9.373-33.941 0L154.246 131.48c-9.373 9.373-9.373 24.569 0 33.941l22.627 22.627c9.373 9.373 24.569 9.373 33.941 0l5.657-5.657 39.598 39.598-81.04 81.04-5.657-5.657c-12.497-12.497-32.758-12.497-45.255 0L9.373 412.118c-12.497 12.497-12.497 32.758 0 45.255l45.255 45.255c12.497 12.497 32.758 12.497 45.255 0l114.745-114.745c12.497-12.497 12.497-32.758 0-45.255l-5.657-5.657 81.04-81.04 39.598 39.598-5.657 5.657c-9.373 9.373-9.373 24.569 0 33.941l22.627 22.627c9.373 9.373 24.569 9.373 33.941 0l124.451-124.451c9.372-9.372 9.372-24.568 0-33.941z");
                return g;
            },
            // helper function to draw heart onto lifelines vis
            drawHeart(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.025 0.025) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z");
                return g;
            },
            // helper function to draw anchors onto lifelines vis
            drawAnchor(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.025 0.025) ');
                g.append('rect')
                    .attr("width", 512)
                    .attr("height", 512)
                    .attr("x", 0)
                    .attr("y", 0)
                    .attr("fill", "transparent");
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M12.971 352h32.394C67.172 454.735 181.944 512 288 512c106.229 0 220.853-57.38 242.635-160h32.394c10.691 0 16.045-12.926 8.485-20.485l-67.029-67.029c-4.686-4.686-12.284-4.686-16.971 0l-67.029 67.029c-7.56 7.56-2.206 20.485 8.485 20.485h35.146c-20.29 54.317-84.963 86.588-144.117 94.015V256h52c6.627 0 12-5.373 12-12v-40c0-6.627-5.373-12-12-12h-52v-5.47c37.281-13.178 63.995-48.725 64-90.518C384.005 43.772 341.605.738 289.37.01 235.723-.739 192 42.525 192 96c0 41.798 26.716 77.35 64 90.53V192h-52c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h52v190.015c-58.936-7.399-123.82-39.679-144.117-94.015h35.146c10.691 0 16.045-12.926 8.485-20.485l-67.029-67.029c-4.686-4.686-12.284-4.686-16.971 0L4.485 331.515C-3.074 339.074 2.28 352 12.971 352zM288 64c17.645 0 32 14.355 32 32s-14.355 32-32 32-32-14.355-32-32 14.355-32 32-32z");
                return g;
            },
            // helper function to draw certificate onto lifelines vis
            drawSquare(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') rotate(45) scale(0.02 0.02) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M400 32H48C21.5 32 0 53.5 0 80v352c0 26.5 21.5 48 48 48h352c26.5 0 48-21.5 48-48V80c0-26.5-21.5-48-48-48z");
                return g;
            },
            // helper function to draw scissors (for head shaven/heir cut) onto lifelines vis
            drawScissors(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.025 0.025) ');
                g.append('rect')
                    .attr("width", 512)
                    .attr("height", 512)
                    .attr("x", 0)
                    .attr("y", 0)
                    .attr("fill", "transparent");
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M278.06 256L444.48 89.57c4.69-4.69 4.69-12.29 0-16.97-32.8-32.8-85.99-32.8-118.79 0L210.18 188.12l-24.86-24.86c4.31-10.92 6.68-22.81 6.68-35.26 0-53.02-42.98-96-96-96S0 74.98 0 128s42.98 96 96 96c4.54 0 8.99-.32 13.36-.93L142.29 256l-32.93 32.93c-4.37-.61-8.83-.93-13.36-.93-53.02 0-96 42.98-96 96s42.98 96 96 96 96-42.98 96-96c0-12.45-2.37-24.34-6.68-35.26l24.86-24.86L325.69 439.4c32.8 32.8 85.99 32.8 118.79 0 4.69-4.68 4.69-12.28 0-16.97L278.06 256zM96 160c-17.64 0-32-14.36-32-32s14.36-32 32-32 32 14.36 32 32-14.36 32-32 32zm0 256c-17.64 0-32-14.36-32-32s14.36-32 32-32 32 14.36 32 32-14.36 32-32 32z");
                return g;
            },
            // helper function to draw coins (for fines) onto lifelines vis
            drawCoins(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.025 0.025) ');
                g.append('rect')
                    .attr("width", 512)
                    .attr("height", 512)
                    .attr("x", 0)
                    .attr("y", 0)
                    .attr("fill", "transparent");
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M0 405.3V448c0 35.3 86 64 192 64s192-28.7 192-64v-42.7C342.7 434.4 267.2 448 192 448S41.3 434.4 0 405.3zM320 128c106 0 192-28.7 192-64S426 0 320 0 128 28.7 128 64s86 64 192 64zM0 300.4V352c0 35.3 86 64 192 64s192-28.7 192-64v-51.6c-41.3 34-116.9 51.6-192 51.6S41.3 334.4 0 300.4zm416 11c57.3-11.1 96-31.7 96-55.4v-42.7c-23.2 16.4-57.3 27.6-96 34.5v63.6zM192 160C86 160 0 195.8 0 240s86 80 192 80 192-35.8 192-80-86-80-192-80zm219.3 56.3c60-10.8 100.7-32 100.7-56.3v-42.7c-35.5 25.1-96.5 38.6-160.7 41.8 29.5 14.3 51.2 33.5 60 57.2z");
                return g;
            },
            // helper function to draw bread (for bread and water sentences) onto lifelines vis
            drawBread(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.02 0.02) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M288 0C108 0 0 93.4 0 169.14 0 199.44 24.24 224 64 224v256c0 17.67 16.12 32 36 32h376c19.88 0 36-14.33 36-32V224c39.76 0 64-24.56 64-54.86C576 93.4 468 0 288 0z"
                );
                return g;
            },
            // helper function to draw collar (for iron collar) onto lifelines vis
            drawCollar(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.02 0.02) ');
                g.append('rect')
                    .attr("width", 512)
                    .attr("height", 512)
                    .attr("x", 0)
                    .attr("y", 0)
                    .attr("fill", "transparent");
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M288 39.056v16.659c0 10.804 7.281 20.159 17.686 23.066C383.204 100.434 440 171.518 440 256c0 101.689-82.295 184-184 184-101.689 0-184-82.295-184-184 0-84.47 56.786-155.564 134.312-177.219C216.719 75.874 224 66.517 224 55.712V39.064c0-15.709-14.834-27.153-30.046-23.234C86.603 43.482 7.394 141.206 8.003 257.332c.72 137.052 111.477 246.956 248.531 246.667C393.255 503.711 504 392.788 504 256c0-115.633-79.14-212.779-186.211-240.236C302.678 11.889 288 23.456 288 39.056z");
                return g;
            },
            // helper function to draw clock (for sentence extentions) onto lifelines vis
            drawClock(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.02 0.02) ');
                g.append('rect')
                    .attr("width", 512)
                    .attr("height", 512)
                    .attr("x", 0)
                    .attr("y", 0)
                    .attr("fill", "transparent");
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "m 504,255.53099 c 0.253,136.64 -111.18,248.37199 -247.82,248.46801 -59.015,0.042 -113.223,-20.53002 -155.822,-54.91101 -11.077,-8.94 -11.905,-25.541 -1.839,-35.607 l 11.267,-11.267 c 8.609,-8.609 22.353,-9.551 31.891,-1.984 31.385,24.905 71.104,39.77 114.323,39.77 101.705,0 184,-82.311 184,-184 0,-101.705 -82.311,-183.999993 -184,-183.999993 -48.814,0 -93.149,18.969003 -126.068,49.932003 l 50.754,50.75399 c 10.08,10.08 2.941,27.314 -11.313,27.314 H 24 C 15.163,199.99999 8,192.837 8,184 V 38.626999 C 8,24.373 25.234,17.234 35.314,27.312999 L 84.686,76.684996 C 129.209,34.135999 189.552,8 256,8 392.81,8 503.747,118.78 504,255.53099 Z m -180.912,78.784 9.823,-12.63 c 8.138,-10.463 6.253,-25.542 -4.21,-33.679 L 288,256.34899 V 152 c 0,-13.255 -10.745,-24 -24,-24 h -16 c -13.255,0 -24,10.745 -24,24 v 135.65099 l 65.409,50.874 c 10.463,8.137 25.541,6.253 33.679,-4.21 z");
                return g;
            },
            // helper function to draw clock (for sentence extentions) onto lifelines vis
            drawBaby(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.03 0.03) ');
                g.append('rect')
                    .attr("width", 512)
                    .attr("height", 512)
                    .attr("x", 0)
                    .attr("y", 0)
                    .attr("fill", "transparent");
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M192 160c44.2 0 80-35.8 80-80S236.2 0 192 0s-80 35.8-80 80 35.8 80 80 80zm-53.4 248.8l25.6-32-61.5-51.2L56.8 383c-11.4 14.2-11.7 34.4-.8 49l48 64c7.9 10.5 19.9 16 32 16 8.3 0 16.8-2.6 24-8 17.7-13.2 21.2-38.3 8-56l-29.4-39.2zm142.7-83.2l-61.5 51.2 25.6 32L216 448c-13.2 17.7-9.7 42.8 8 56 7.2 5.4 15.6 8 24 8 12.2 0 24.2-5.5 32-16l48-64c10.9-14.6 10.6-34.8-.8-49l-45.9-57.4zM376.7 145c-12.7-18.1-37.6-22.4-55.7-9.8l-40.6 28.5c-52.7 37-124.2 37-176.8 0L63 135.3C44.9 122.6 20 127 7.3 145-5.4 163.1-1 188 17 200.7l40.6 28.5c17 11.9 35.4 20.9 54.4 27.9V288h160v-30.8c19-7 37.4-16 54.4-27.9l40.6-28.5c18.1-12.8 22.4-37.7 9.7-55.8z");
                return g;
            },


            // draw the chart
            createChart(data) {
                // suppress error message of mounted calls chart before data load is ready
                if (data.length === 0 ) {
                    return;
                }

                // Define time format
                var timeFormat = d3.timeFormat('%Y');
                var niceTimeFormat = d3.timeFormat('%d-%m-%Y');
                var parseTime = d3.timeParse('%Y-%m-%d');

                // Define where to draw the lifeline events on the screen
                let lifeline_idx = 0;
                let min_date = parseTime(data[0].events[0].Date);
                let max_date = min_date;
                data.forEach(function(lifeline) {
                    lifeline["events"].forEach(function(d) {
                        d.yValue = 0.4+lifeline_idx;
                        d.dates =  parseTime(d.Date);
                        if (d.dates < min_date) {
                            min_date = d.dates;
                        }
                        if (d.dates > max_date) {
                            max_date = d.dates;
                        }
                    });
                    // also do conversion for sentences and confinement
                    lifeline["sentences"].forEach(function(d) {
                        d.yValue = 0.4+lifeline_idx;
                    });
                    lifeline["confinement"].forEach(function(d) {
                        d.yValue = 0.4+lifeline_idx;
                    });
                    lifeline["repercussion"].forEach(function(d) {
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



                // Set the dimensions and margins of the graph
                let hgt = 500;
                let textMax = 120;
                if (this.selectedLifelines.length > 1) {
                    hgt = 300;
                }
                const margin = {top: 10, right: 20, bottom: 30, left: 50},
                    width = 1200 - margin.left - margin.right,
                    height = hgt * data.length - margin.top - margin.bottom;

                // Append the svg object to the body of the page
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
                const x = d3.scaleTime()
                    .domain([min_date, max_date])
                    .range([ 0, width - textMax ]);

                // Calculate spacing for x coordinates of event and date text to avoid overlap
                data.forEach(function(lifeline) {
                    let prev_x = -100;
                    lifeline["events"].forEach(function (d) {
                        d.x = Math.max(prev_x + 15, x(d.dates));
                        prev_x = d.x;
                    });
                    let max_x = prev_x;
                    prev_x = x(max_date);

                    for (let i = lifeline["events"].length-1; i >= 0; i--) {
                        let d = lifeline["events"][i];
                        // at the end of the loop, prev_x has the lowest value
                        prev_x = Math.min(prev_x - 15, x(d.dates));
                        // d.x = prev_x;
                    }
                    prev_x = Math.max(250, prev_x);
                    let min_circle = x(lifeline["events"][0].dates);
                    let max_circle = x(lifeline["events"][lifeline["events"].length-1].dates);
                    // let line_center = (max_circle - min_circle) / 2;
                    // let text_center = (max_x - prev_x) /2;

                    let overhang = max_x - max_circle;
                    if (min_circle - overhang < 5) {
                        overhang = min_circle - 5;
                    }

                    // let adjustment = text_center - line_center;
                    lifeline["events"].forEach(function (d) {
                        d.x = d.x - overhang;
                    });
                });

                // Add Bottom axis
                const xAxis = d3.axisBottom(x)
                    .tickFormat(timeFormat);

                svg.append('g')
                    .attr('transform', "translate(0," + height + ")")
                    .call(xAxis);

                // svg.append('g')
                //     .call(yAxis)


                // Event description tooltip (applied to event)
                // https://gist.github.com/d3noob/4e4485d94aebf63ae8059258c40f2609
                const div = d3.select("body").append("div")
                    .attr("class", "tooltip")
                    .style("opacity", 0);

                // Draw the path of the lifeline
                data.forEach((lifeline) => {
                    // Add the lifeline
                    svg.append("path")
                        .datum(lifeline["events"])
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("d", d3.line()
                            .x(function (d) {
                                return x(d.dates)
                            })
                            .y(function (d) {
                                return y(d.yValue)
                            })
                        );

                    // Draw line to extend lifeline to left (before trial)
                    svg.append('line')
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("x1", x(lifeline["events"][0].dates) - 4)
                        .attr("y1", y(lifeline["events"][0].yValue))
                        .attr("x2", x(lifeline["events"][0].dates) - 40)
                        .attr("y2", y(lifeline["events"][0].yValue))
                        .style("stroke-dasharray", ("4, 4"))
                    ;
                    // Draw line to extend lifeline to right (before trial)
                    // Temporary solution? to omit death info and have this instead
                    svg.append('line')
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("x1", x(lifeline["events"][lifeline["events"].length - 1 ].dates) + 4)
                        .attr("y1", y(lifeline["events"][lifeline["events"].length - 1 ].yValue))
                        .attr("x2", x(lifeline["events"][lifeline["events"].length - 1 ].dates) + 40)
                        .attr("y2", y(lifeline["events"][lifeline["events"].length - 1 ].yValue))
                        .style("stroke-dasharray", ("4, 4"))
                    ;

                    // Draw more lines
                    lifeline["events"].forEach(function (d) {
                        // Line connecting to date
                        // svg.append('line')
                        //     .attr("fill", "none")
                        //     .attr("stroke", "black")
                        //     .attr("stroke-width", 1)
                        //     .attr("x1", x(d.dates))
                        //     .attr("y1", y(d.yValue) + 16) //+ 4
                        //     .attr("x2", d.x)
                        //     .attr("y2", y(d.yValue) + 36) // + 20
                        // ;

                        // Line connecting to description
                        if (d.Source !== "FAS DB Relations") {
                            svg.append('line')
                                .attr("fill", "none")
                                .attr("stroke", "black")
                                .attr("stroke-width", 1)
                                .attr("x1", x(d.dates))
                                .attr("y1", y(d.yValue) - 9)
                                .attr("x2", d.x)
                                .attr("y2", y(d.yValue) - 25)
                            ;
                        }

                    });


                    //duplicate line underneath sentence rectangles
                    svg.append("path")
                        .datum(lifeline["events"])
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("d", d3.line()
                            .x(function (d) {
                                return x(d.dates)
                            })
                            .y(function (d) {
                                return y(d.yValue) + 16
                            })
                        )
                    svg.append("path")
                        .datum(lifeline["events"])
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("d", d3.line()
                            .x(function (d) {
                                return x(d.dates)
                            })
                            .y(function (d) {
                                return y(d.yValue) + 8
                            })
                        )
                    // Draw line to extend lifeline to left (before trial)
                    svg.append('line')
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("x1", x(lifeline["events"][0].dates) - 4)
                        .attr("y1", y(lifeline["events"][0].yValue) + 16)
                        .attr("x2", x(lifeline["events"][0].dates) - 40)
                        .attr("y2", y(lifeline["events"][0].yValue) + 16)
                        .style("stroke-dasharray", ("4, 4"))
                    ;
                    // Draw line to extend lifeline to right (before trial)
                    svg.append('line')
                        .attr("fill", "none")
                        .attr("stroke", "black")
                        .attr("stroke-width", 1)
                        .attr("x1", x(lifeline["events"][lifeline["events"].length - 1 ].dates) + 4)
                        .attr("y1", y(lifeline["events"][lifeline["events"].length - 1 ].yValue) + 16)
                        .attr("x2", x(lifeline["events"][lifeline["events"].length - 1 ].dates) + 40)
                        .attr("y2", y(lifeline["events"][lifeline["events"].length - 1 ].yValue) + 16)
                        .style("stroke-dasharray", ("4, 4"))
                    ;


                    // Draw sentence rectangles beneath lifeline
                    svg.append('g')
                        .selectAll('time-span-rect')
                        . data(lifeline["sentences"])
                        . enter()
                        .append('rect')
                        .attr('class', 'time-span-rect')
                        .attr('x', function(d) {return x(d.cleanStartDate)})
                        .attr('y', function(d) {return y(d.yValue) + 0 })
                        .attr('width', function(d) {
                            return x(d.cleanEndDate) - x(d.cleanStartDate)
                        })
                        .attr('height', 8)
                        .attr('fill', (d) => {return this.sentenceColor[d.numericCode]})
                        .attr('stroke', 'black')
                        .on("mouseover", function (d) {
                        div.transition()
                            .duration(200)
                            .style("opacity", 1);
                        div.html(function() {
                            if (!isNaN(d.Extension)) {
                                return d.Days + " days " + d.SentenceCat;
                            } else {
                                return d.Days + " days " + d.SentenceCat + " (sentence extended)";
                            }})
                            .style("left", (d3.event.pageX) + "px")
                            .style("top", (d3.event.pageY - 40) + "px");
                    })
                        .on("mouseout", function () {
                            div.transition()
                                .duration(500)
                                .style("opacity", 0);
                        });


                    // Draw confinement rectangles beneath lifeline
                    svg.append('g')
                        .selectAll('time-span-rect')
                        . data(lifeline["confinement"])
                        . enter()
                        .append('rect')
                        .attr('class', 'time-span-rect')
                        .attr('x', function(d) {return x(d.cleanStartDate)})
                        .attr('y', function(d) {return y(d.yValue) + 8 })
                        .attr('width', function(d) {
                            if (!isNaN(d.cleanEndDate)) {
                                return x(d.cleanEndDate) - x(d.cleanStartDate)
                            } else {return x(d.cleanStartDate) + 40}})
                        .attr('height', 8)
                        .attr('fill', (d) => {return this.sentenceColor[d.numericCode]})
                        .attr('stroke', 'black')
                        .on("mouseover", function (d) {
                            div.transition()
                                .duration(200)
                                .style("opacity", 1);
                            div.html(function() {
                                if (!isNaN(d.Extension)) {
                                    return d.Days + " days " + d.SentenceCat;
                                } else {
                                    return d.Days + " days " + d.SentenceCat + " (sentence extended)";
                                }})
                                .style("left", (d3.event.pageX) + "px")
                                .style("top", (d3.event.pageY + 10) + "px");
                        })
                        .on("mouseout", function () {
                            div.transition()
                                .duration(500)
                                .style("opacity", 0);
                        });

                    lifeline["repercussion"].forEach( (d) => {
                        var symbol, symX, symY;
                        if (d.numericCode === 40) {
                            symX = x(d.dates) - 6;
                            symY = y(d.yValue) + 20;
                            symbol = this.drawBread(svg, symX, symY);
                        } else if (d.numericCode === 41) {
                            symX = x(d.dates) + 6;
                            symY = y(d.yValue) + 20;
                            symbol = this.drawScissors(svg, symX, symY);
                        } else if (d.numericCode === 42) {
                            symX = x(d.dates) + 6;
                            symY = y(d.yValue) + 20;
                            symbol = this.drawCollar(svg, symX, symY);
                        } else if (d.numericCode === 22) {
                            symX = x(d.dates) - 6;
                            symY = y(d.yValue) + 20;
                            symbol = this.drawCoins(svg, symX, symY);
                        }  else if (d.numericCode === 51 || d.numericCode === 52) {
                            symX = x(d.dates) - 0;
                            symY = y(d.yValue) + 20;
                            symbol = this.drawClock(svg, symX, symY);
                        }
                        symbol.on("mouseover", function() {
                            // symbol.attr('transform', 'translate(' + (symX - 2) + ',' + (symY - 2) + ') scale(0.035 0.035)');

                            div.transition()
                                .duration(200)
                                .style("opacity", 1);
                            div.html(function () {
                                if (spanRepercuss.has(d.numericCode) && (d.Days !== "")) {
                                    return d.Days + " days " + d.SentenceCat;
                                } else if (d.numericCode === 41) {
                                    return "head shaved";
                                } else {
                                    return d.SentenceCat;
                                }
                            })
                                .style("left", (d3.event.pageX) + "px")
                                .style("top", (d3.event.pageY + 12) + "px");
                        })
                            .on("mouseout", function() {
                                div.transition()
                                    .duration(500)
                                    .style("opacity", 0);
                                // symbol.attr('transform', 'translate(' + symX + ',' + symY + ') scale(0.025 0.025)');

                            });
                    });


                    lifeline["events"].forEach( (d) => {
                        var symbol, symX, symY;
                        if (d.Source === "Transportation Crime and Trial Records V1") {
                            symX = x(d.dates) - 6;
                            symY = y(d.yValue) - 15;
                            symbol = this.drawGavel(svg, symX, symY);
                        } else if (d.Source === "PermissionToMarryRecordsV1" || d.Source === "MarriageRecordsV1") {
                            symX = x(d.dates) - 6;
                            symY = y(d.yValue) - 12;
                            symbol = this.drawHeart(svg, symX, symY);
                        } else if (d.Source === "FAS DB Download") {
                            symX = x(d.dates) - 7;
                            symY = y(d.yValue) - 13;
                            symbol = this.drawAnchor(svg, symX, symY);
                        } else if (d.Source === "FreedomsV4") {
                            symX = x(d.dates) - 0;
                            symY = y(d.yValue) - 13;
                            symbol = this.drawSquare(svg, symX, symY);
                        } else if (d.Source === "FAS DB Relations") {
                            symX = x(d.dates) - 0;
                            symY = y(d.yValue) + 45;
                            symbol = this.drawBaby(svg, symX, symY);
                        } else {
                                symbol = svg.append('circle')
                                    .attr('cx', function () {
                                        return x(d.dates);
                                    })
                                    .attr('cy', function () {
                                        return y(d.yValue) -6;
                                    })
                                    .attr('r', function () {
                                        return 5;
                                    })
                                    .attr('fill', () => {
                                        return d.Source in this.offenceColor ? this.offenceColor[d.Source] : "#000000";
                                    })
                                    //.style('fill', 'black')//'#69b3a2'
                                    .style('opacity', '1')
                                    .attr('stroke', 'black')
                            }

                        symbol.on('click', () => {
                               if (d.VoyageId !== '') {
                                   this.$emit('selectVoyage', d.VoyageId);
                               } else if (d.Person !== '') {
                                   // console.log("person", d.Person);
                                   this.$emit('selectPerson', d.Person);
                               } else if (d.IncidentId !== '') {
                                   // console.log("person", d.Person);
                                   this.$emit('selectIncident', d.IncidentId);
                               }
                                div.transition()
                                    .duration(200)
                                    .style("opacity", 0);
                           })
                           .on("mouseover", function() {
                               if (d.VoyageId !== '' || d.Person !== '') {
                                   // console.log("d", d);
                                   // d3.select(this).attr('r', 8);
                                   symbol.attr('transform','translate('+(symX - 2)+','+(symY - 2)+') scale(0.035 0.035)');
                                   d3.select(this).style("cursor", "pointer");
                               }
                               if(d.IncidentId !== '') {
                                   d3.select(this).style("cursor", "pointer");
                                   symbol.attr('r', function () { return 8;})
                               }
                               div.transition()
                                   .duration(200)
                                   .style("opacity", 1);
                               div.html(niceTimeFormat(d.dates) + ': ' + d.eventDescription)
                                   .style("left", (d3.event.pageX) + "px")
                                   .style("top", (d3.event.pageY - 50) + "px");
                           })
                           .on("mouseout", function() {
                               d3.select(this).style("cursor", "default");
                               if (d.VoyageId !== '' || d.Person !== '') {
                                   // d3.select(this).attr('r', 5);
                                   symbol.attr('transform', 'translate(' + symX + ',' + symY + ') scale(0.025 0.025)');
                               }
                               if(d.IncidentId !== '') {
                                   symbol.attr('r', function () { return 5;})
                               }
                               div.transition()
                                   .duration(200)
                                   .style("opacity", 0);
                           });
                    });

                    // Draw the cicles on the lifeline
                    // svg.append('g')
                    //     .selectAll('dot')
                    //     .data(lifeline["events"])
                    //     .enter()
                    //     .append('circle')
                    //     .attr('cx', function (d) {
                    //         return x(d.dates);
                    //     })
                    //     .attr('cy', function (d) {
                    //         return y(d.yValue) -6;
                    //     })
                    //     .attr('r', function () {
                    //         return 5;
                    //     })
                    //     .attr('fill', (d) => {
                    //         return d.Source in this.offenceColor ? this.offenceColor[d.Source] : "#000000";
                    //     })
                    //     //.style('fill', 'black')//'#69b3a2'
                    //     .style('opacity', '1')
                    //     .attr('stroke', 'black')
                    //     // network event on click
                    //     .on('click', (d) => {
                    //         if (d.VoyageId !== '') {
                    //             this.$emit('selectVoyage', d.VoyageId);
                    //         } else if (d.Person !== '') {
                    //             // console.log("person", d.Person);
                    //             this.$emit('selectPerson', d.Person);
                    //         }
                    //
                    //     })
                    //     .on("mouseover", function (d) {
                    //         d3.select(this).style("cursor", "pointer");
                    //         if (d.VoyageId !== '' || d.Person !== '') {
                    //             // console.log("d", d);
                    //             d3.select(this).attr('r', 8);
                    //         }
                    //     })
                    //     .on("mouseout", function () {
                    //         d3.select(this).style("cursor", "default");
                    //         d3.select(this).attr('r', 5);
                    //     });


                    // Draw the date beneath the line
                    // Rotation after https://observablehq.com/@weitinglin/d3-rotating-text-labels
                    // svg.append('g')
                    //     .selectAll('dateText')
                    //     .data(lifeline["events"])
                    //     .enter()
                    //     .append("text")
                    //     .text(d => niceTimeFormat(d.dates))
                    //     .attr('transform', (d) => {
                    //         return 'translate( ' + (d.x + 4) + ' , ' + (y(d.yValue)+88) + '),' + 'rotate(-90)';
                    //     })
                    //     .attr('x', 0)
                    //     .attr('y', 0);

                    // Draw the event description on top of the line
                        svg.append('g')
                        .selectAll('eventText')
                        .data(lifeline["events"])
                        .enter()
                        .append('text')
                        .text(d => {
                            if (d.Source !== "FAS DB Relations") {
                                if (d.eventDescription.length >= 50 && this.selectedLifelines.length > 1) {
                                    const short = d.eventDescription.substring(0, 50);
                                    const pos = short.lastIndexOf(" ");
                                    return d.eventDescription.substring(0, pos) + ' ...';
                                } else {
                                    return d.eventDescription;
                                }
                            }
                        })
                        .attr('transform', (d) => {
                            return 'translate( ' + (d.x - 2) + ' , ' + (y(d.yValue)-27) + '),' + 'rotate(-30)';
                        })
                        .attr('x', 0)
                        .attr('y', 0)
                        .on("mouseover", function (d) {
                            div.transition()
                                .duration(200)
                                .style("opacity", 1);
                            div.html(niceTimeFormat(d.dates) + ': ' + d.eventDescription)
                                .style("left", (d3.event.pageX) + "px")
                                .style("top", (d3.event.pageY - 50) + "px");
                        })
                        .on("mouseout", function () {
                            div.transition()
                                .duration(500)
                                .style("opacity", 0);
                        });

                    // Draw the convict name on the top left if there is more than one lifeline
                    // Easiest way to get name in two lines was to draw them separately
                    if (this.selectedLifelines.length > 1) {
                        svg.append('g')
                            .append('text')
                            .text(lifeline["nominal_data"].GivenNames)
                            .attr("x", 0)
                            .attr("y", y(lifeline["events"][0].yValue) - 100)
                            .attr("class", "convictName")
                            .on('click', () => {
                                    // console.log(lifeline["nominal_data"].ConvictId);
                                    this.$emit('selectConvict', lifeline["nominal_data"].ConvictId);
                                })
                            .on("mouseover", function () {
                                d3.select(this).style("cursor", "pointer");
                            })
                            .on("mouseout", function () {
                                d3.select(this).style("cursor", "default");
                            });

                        svg.append('g')
                            .append('text')
                            .text(lifeline["nominal_data"].FamilyName)
                            .attr("x", 0)
                            .attr("y", y(lifeline["events"][0].yValue) - 88)
                            .attr("class", "convictName")
                            .on('click', () => {
                                // console.log(lifeline["nominal_data"].ConvictId);
                                this.$emit('selectConvict', lifeline["nominal_data"].ConvictId);
                                window.scrollTo(0, 0);
                            })
                            .on("mouseover", function () {
                                d3.select(this).style("cursor", "pointer");
                            })
                            .on("mouseout", function () {
                                d3.select(this).style("cursor", "default");
                            });
                    }


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
        font-size: xx-small;
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
        pointer-events: none;


    }

    .convictName {
        font-size: x-small;
        font-weight: bold;
    }
</style>
