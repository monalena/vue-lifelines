<template>
    <div>
        <div id="chart"></div>
    </div>
</template>

<script>
    import * as d3 from 'd3';

    export default {
        name: 'Faceline',
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
                g.attr('transform','translate('+x+','+y+') scale(0.015 0.015) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M504.971 199.362l-22.627-22.627c-9.373-9.373-24.569-9.373-33.941 0l-5.657 5.657L329.608 69.255l5.657-5.657c9.373-9.373 9.373-24.569 0-33.941L312.638 7.029c-9.373-9.373-24.569-9.373-33.941 0L154.246 131.48c-9.373 9.373-9.373 24.569 0 33.941l22.627 22.627c9.373 9.373 24.569 9.373 33.941 0l5.657-5.657 39.598 39.598-81.04 81.04-5.657-5.657c-12.497-12.497-32.758-12.497-45.255 0L9.373 412.118c-12.497 12.497-12.497 32.758 0 45.255l45.255 45.255c12.497 12.497 32.758 12.497 45.255 0l114.745-114.745c12.497-12.497 12.497-32.758 0-45.255l-5.657-5.657 81.04-81.04 39.598 39.598-5.657 5.657c-9.373 9.373-9.373 24.569 0 33.941l22.627 22.627c9.373 9.373 24.569 9.373 33.941 0l124.451-124.451c9.372-9.372 9.372-24.568 0-33.941z");
                return g;
            },
            // helper function to draw heart onto lifelines vis
            drawHeart(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.01 0.01) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z");
                return g;
            },
            // helper function to draw anchors onto lifelines vis
            drawAnchor(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') scale(0.012 0.012) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M12.971 352h32.394C67.172 454.735 181.944 512 288 512c106.229 0 220.853-57.38 242.635-160h32.394c10.691 0 16.045-12.926 8.485-20.485l-67.029-67.029c-4.686-4.686-12.284-4.686-16.971 0l-67.029 67.029c-7.56 7.56-2.206 20.485 8.485 20.485h35.146c-20.29 54.317-84.963 86.588-144.117 94.015V256h52c6.627 0 12-5.373 12-12v-40c0-6.627-5.373-12-12-12h-52v-5.47c37.281-13.178 63.995-48.725 64-90.518C384.005 43.772 341.605.738 289.37.01 235.723-.739 192 42.525 192 96c0 41.798 26.716 77.35 64 90.53V192h-52c-6.627 0-12 5.373-12 12v40c0 6.627 5.373 12 12 12h52v190.015c-58.936-7.399-123.82-39.679-144.117-94.015h35.146c10.691 0 16.045-12.926 8.485-20.485l-67.029-67.029c-4.686-4.686-12.284-4.686-16.971 0L4.485 331.515C-3.074 339.074 2.28 352 12.971 352zM288 64c17.645 0 32 14.355 32 32s-14.355 32-32 32-32-14.355-32-32 14.355-32 32-32z");
                return g;
            },
            // helper function to draw certificate onto lifelines vis
            drawSquare(svg, x, y) {
                let g = svg.append('g');
                g.attr('transform','translate('+x+','+y+') rotate(45) scale(0.007 0.01) ');
                let p = g.append('path');
                p.attr('fill', '#000000');
                p.attr('d', "M400 32H48C21.5 32 0 53.5 0 80v352c0 26.5 21.5 48 48 48h352c26.5 0 48-21.5 48-48V80c0-26.5-21.5-48-48-48z");
                return g;
            },
            labelCoords(path, distance, offset) {
                const l = path.node().getTotalLength();
                const d = l * distance;
                const p = path.node().getPointAtLength(d);

                const pBefore = path.node().getPointAtLength(d - 2);
                const pAfter = path.node().getPointAtLength(d + 2);

                // vector calculation (vector from pBefore to pAfter, turned 90deg left)
                const rnx = (pAfter.y - pBefore.y);
                const rny = -(pAfter.x - pBefore.x);
                const rnl = Math.sqrt(rnx*rnx + rny*rny);

                return {'x': p.x+offset*(rnx / rnl), 'y': p.y+offset*(rny / rnl)};

            },
            rectCoords(path, start, end, offset) {
                const l = path.node().getTotalLength();
                const dStart = l * start;
                const dEnd = l * end;


                let segments = "";

                for (let d = dStart; d < dEnd; d += 2) {
                    const p = path.node().getPointAtLength(d);
                    const pBefore = path.node().getPointAtLength(d - 2);
                    const pAfter = path.node().getPointAtLength(d + 2);
                    // vector calculation (vector from pBefore to pAfter, turned 90deg right)
                    const rnx = -(pAfter.y - pBefore.y);
                    const rny = (pAfter.x - pBefore.x);
                    const rnl = Math.sqrt(rnx*rnx + rny*rny);

                    if (d === dStart) {
                        segments += "M ";
                    } else {
                        segments += " L ";
                    }
                    segments += (p.x + offset*(rnx/rnl)) + " " + (p.y + offset*(rny/rnl));
                }
                // console.log("rectCoords", dStart, dEnd, segments);
                return segments;
            },

            // draw the chart
            createChart(data) {

                // suppress error message of mounted calls chart before data load is ready
                if (data.length === 0 ) {
                    return;
                }

                // only show first lifeline
                data = [data[0]];

                // Define time format
                // var timeFormat = d3.timeFormat('%Y');
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

                    lifeline_idx++;
                });



                // Set the dimensions and margins of the graph
                let hgt = 1500;
                // let textMax = 120;
                // if (this.selectedLifelines.length > 1) {
                //     hgt = 300;
                // }
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
                        "translate(" + margin.left + "," + margin.top + ") scale(3)");

                const downFace = 'm 198.58316,259.5654 c 5.55744,5.24695 13.01141,-2.77264 13.291,-3.51821 6.71705,-6.21881 11.46347,-13.42294 15.24556,-21.10923 6.19161,-12.41878 8.14291,-26.42767 10.55462,-40.26391 2.3861,-26.38577 11.70312,-38.46099 12.09523,-56.47523 0.38568,-14.65771 -2.15273,-28.34919 -7.40429,-42.034532 -8.3215,-17.788541 -17.28161,-28.552261 -26.582,-35.572967 -8.7625,-6.246553 -22.8588,-11.159656 -43.3912,-14.463736 -18.11224,-2.453094 -36.22449,-1.474704 -54.33673,1.954559 -16.10181,3.596435 -30.914541,8.997577 -44.563943,16.027381 -13.965022,7.956389 -24.372703,17.24678 -30.491115,28.145647 -8.508929,16.384918 -8.415898,31.156968 -9.381882,46.127588 0.583349,19.69544 2.303433,38.846 3.518205,50.81852 2.605046,17.14244 7.055894,33.87469 14.072824,50.03671 3.388044,6.54745 5.426431,16.13163 10.554616,18.76376 l 5.081852,1.95456 c 4.387012,1.15841 5.466111,-2.09374 7.036414,-4.69094 2.578647,-6.45348 6.384767,-13.72527 6.254585,-18.37286 1.126324,-9.92579 -0.457745,-17.91558 -2.736381,-25.40926 -2.160267,-10.24599 -5.165457,-20.82994 -9.772793,-32.05476 -3.729386,-12.93218 -4.335512,-23.18728 -4.690941,-33.2275 -0.172553,-9.57903 -0.930213,-18.4432 4.30003,-28.92747 4.910576,-6.93969 10.241759,-12.19695 15.636467,-17.20011 8.849441,-6.560108 18.316752,-10.648733 28.145652,-13.291004 12.08433,-3.807539 21.93864,-5.880617 29.60838,-6.254588 11.39778,-1.02909 21.64658,-0.449616 30.59202,1.954559 11.4305,2.91959 20.44749,8.494028 26.97291,16.809206 6.35128,8.689087 11.86648,17.530197 14.07282,26.972907 2.53334,8.67077 4.00287,17.10513 3.1273,25.01836 -0.0308,4.3604 -0.4071,7.68437 -0.77743,11.02622 -1.60771,9.16399 -3.1457,10.69983 -5.60673,15.16157 -2.24685,3.61073 -4.5721,6.38513 -7.16708,8.99536 -3.17085,3.76087 -6.3417,7.09788 -9.51255,10.16261 -1.8304,1.82323 -3.92479,3.60246 -7.81824,5.08185 -2.01711,0.96948 -3.80322,1.24596 -5.47276,1.17273 -1.64266,0.004 -3.1954,-0.44129 -4.69094,-1.17273 -1.14345,-0.72062 -0.97587,-1.90078 -2.34547,-3.90912 -1.28707,-2.3944 -3.06016,-4.886 -5.08186,-7.42732 -3.08869,-3.87793 -4.24587,-8.2016 -5.47276,-12.50918 -0.72443,-2.87423 -0.88206,-5.74847 -0.52447,-8.6227 0.77833,-2.35458 1.61032,-4.70916 3.44978,-7.06374 0.86691,-1.20508 1.7467,-2.48311 2.43514,-2.67681 0.62016,-0.16469 1.05603,0.22349 1.56648,0.38795 1.25707,0.54271 2.49266,1.21429 3.80689,1.41399 1.8692,0.30041 3.6755,0.23874 5.65596,0.32631 2.46576,0.0729 4.7391,-0.0946 6.96117,-0.32631 2.55134,-0.2232 5.14048,-0.40104 7.39626,-0.97891 1.97656,-0.61636 3.52614,-1.23271 4.4595,-1.84907 0.86283,-0.54384 1.53817,-1.11071 1.95783,-1.63152 l 1.30522,-1.63153 c 0.36256,-0.29529 0.72513,-0.239 1.08769,-0.10877 0.46269,0.31156 0.0927,0.81528 -0.21754,1.30522 -0.38384,0.68887 -0.92579,1.42379 -1.94508,2.18175 -0.57307,0.41204 -1.17896,0.755 -2.07935,1.19007 -1.0345,0.51746 -2.50084,1.05291 -5.00335,1.63153 -1.98509,0.46451 -4.64958,0.75918 -7.61379,0.97891 -2.20528,0.25633 -4.90914,0.31323 -7.72255,0.32631 -1.26157,-0.0436 -3.03617,-0.12767 -5.11212,-0.54384 -2.22253,-0.71442 -2.97072,-0.41191 -4.35073,-1.08768 -0.13221,-0.21967 -0.0177,-0.48046 0.21753,-0.76138 -0.0215,-0.20395 0.23925,-0.24657 -1.84906,-1.63153 -0.58074,-0.38599 -1.43223,-0.22952 -2.17536,-0.87015 -0.48091,-0.5801 -0.75214,-1.16019 -0.87015,-1.74029 0.17159,-1.24745 0.47309,-1.32564 0.76138,-1.52276 0.51529,-0.1867 1.05575,-0.27275 1.63152,-0.21754 2.92195,-0.0155 6.13699,0.078 8.15763,-0.21753 3.43693,-0.42856 6.16571,-0.92794 9.02778,-1.41399 2.71397,-0.5605 4.98563,-1.25844 7.19364,-1.91518 3.19685,-0.93215 5.75245,-1.88165 7.98274,-2.78532 1.97705,-0.78967 3.97688,-1.5777 4.40193,-2.47822 0.45173,-0.71397 0.67068,-1.45705 0.21754,-2.28413 -0.19129,-0.53884 -0.98563,-0.77615 -1.84907,-0.97892 -1.59527,-0.23405 -3.19054,-0.0939 -4.78581,0 -2.00911,0.31418 -4.06212,0.65343 -6.8524,1.41399 -3.81302,1.11547 -7.51859,2.25242 -10.76808,3.48059 -3.91642,1.42595 -7.42472,2.91991 -10.6593,4.4595 -3.10241,1.72468 -6.33184,3.57637 -10.333,6.1998 -3.23301,2.0754 -6.34855,4.32702 -9.35408,6.74364 -0.99972,1.0229 -1.847,2.12201 -2.3929,3.37182 -0.75024,1.3666 -1.32462,2.55734 -1.84906,3.69813 -0.48577,1.40413 -0.73051,2.91782 -0.43508,4.67704 l 1.19645,5.43842 1.7403,8.81024 1.08768,8.37517 c 0.0524,2.29058 0.20677,4.62197 -0.43507,6.63487 -0.30075,1.54178 -0.75624,2.30983 -1.19645,3.15428 -0.90641,1.62369 -1.81281,2.37433 -2.71922,3.15429 -1.07208,0.90112 -1.55632,0.92049 -2.06659,0.97891 -1.2693,-0.0402 -2.64703,-0.17729 -4.02477,-0.7075 -2.79945,-0.66076 -4.45947,-1.8407 -6.30823,-2.77309 -2.0987,-1.34286 -3.7721,-2.82749 -5.00335,-4.4595 -0.82866,-1.30006 -1.51925,-2.33006 -1.63152,-3.91667 -0.18085,-1.12383 -0.47869,-2.27691 -0.10877,-3.26305 0.12121,-0.55088 0.57209,-1.13167 1.41365,-1.25033 1.09394,0.035 1.76642,0.26737 3.15428,0.87014 2.55281,1.35776 2.57081,1.36011 3.26339,2.12149 0.90296,0.87649 2.20563,1.62398 3.15429,3.37182 0.75906,1.57367 1.48393,3.09604 1.95783,4.24197 0.96558,1.92574 1.7261,4.13856 3.37182,5.11211 1.37599,0.77096 2.8371,1.07375 4.35073,1.08769 2.34713,-0.0538 4.21284,-0.4446 5.8735,-0.97892 1.97981,-0.75405 3.73755,-1.58213 4.89457,-2.61044 2.77355,-2.30737 3.1378,-2.99183 3.58936,-3.80689 1.04125,-1.34148 1.36411,-2.68296 1.30522,-4.02444 -0.22018,-1.55659 -0.9111,-2.68166 -1.95783,-3.48058 -1.12025,-0.81483 -2.37149,-1.01843 -3.69812,-0.87015 -1.79473,0.53097 -2.35071,1.73293 -2.50168,3.15428 -0.0582,1.33834 -0.32973,2.64621 -0.87014,3.91566 -0.94113,2.49457 -2.74376,4.07738 -4.45951,5.8735 -0.5575,0.69028 -1.30626,1.06178 -2.0666,1.41399 -1.49336,0.71924 -3.40712,1.07814 -4.4595,2.17536 -0.77407,0.72694 -1.10068,1.48829 -0.97892,2.28414 l 0.87015,2.93675 c 0.23705,0.6799 0.28427,1.34624 -0.43507,1.95783 -0.99563,0.69278 -1.65131,1.45354 -1.95784,2.28414 -0.95361,2.33109 -0.76961,3.825 0.43508,6.41733 l 0.97891,1.84906 c -0.63047,0.67475 -1.09726,1.51318 -1.52275,2.39291 -1.13451,2.55625 0.008,4.23979 1.19645,6.1998 0.4977,1.15183 0.50673,1.89017 0.10877,2.28413 -0.49034,0.59735 -1.15443,1.02094 -1.84907,1.41399 -2.5779,-0.4035 -5.10437,-0.24122 -7.61378,0.10877 -1.99409,0.48195 -3.98818,0.9963 -5.98227,2.17537 -1.81346,1.15998 -3.37928,2.4025 -4.1332,3.91566 -0.55892,1.66527 -0.82887,3.3787 -0.3263,5.22088 0.72512,2.61886 1.45025,3.85409 2.17537,4.89458 1.34148,2.14935 2.68295,3.09047 4.02443,4.02443 2.48477,1.68424 4.03031,1.86571 5.65596,2.17536 3.69346,0.41461 6.6133,0.44242 9.24531,0.32631 4.48586,-0.0659 9.26697,0.004 11.52945,-1.08767 3.42909,-1.42144 5.70378,-3.15772 7.17871,-5.11212 1.44136,-1.70404 2.4356,-3.40808 2.28414,-5.11212 -0.0741,-1.0549 0.1432,-1.46876 -0.76138,-4.35073 -0.8447,-1.192 -1.59747,-2.49892 -1.74029,-4.56828 -0.006,-0.59437 -0.14988,-0.95874 0.87014,-3.26305 0.73467,-0.74229 1.52422,-1.37481 2.61045,-1.41399 1.1864,-0.2558 2.68622,-0.44892 3.04551,-0.87014 1.14287,-0.73379 1.73854,-1.74117 2.17537,-2.82798 0.84304,-1.19645 0.52394,-2.39291 0.54384,-3.58936 -0.13283,-1.25677 -0.18266,-2.43054 -0.65261,-4.02443 -0.29249,-1.0388 -0.52345,-2.21299 -1.30522,-2.17537 -0.79016,-0.15443 -1.23338,0.0959 -1.41399,0.65261 -0.31004,1.02273 1.33888,2.94459 0.66375,3.69345 -0.50193,0.54269 -0.79849,0.87642 -1.3275,1.14162 -1.68791,1.09858 -3.38038,2.19545 -5.86235,2.99626 -3.55351,1.57487 -5.66005,2.01284 -7.83133,2.50167 -2.86603,0.56501 -5.85706,0.9217 -9.02777,0.97891 -3.49156,0.13913 -6.70601,-0.18357 -9.89793,-0.54384 -2.90951,-0.48973 -5.78458,-1.18605 -8.5927,-2.28413 -5.32383,-2.16806 -4.821,-1.99612 -6.41734,-3.04552 -0.67107,-0.77259 -1.33849,-1.55489 -1.41399,-3.91566 0.0426,-0.99936 0.30557,-1.44772 0.54385,-1.95783 1.06809,-0.77739 1.67268,-1.36165 1.52275,-1.63153 0.23333,-0.61635 -0.18747,-1.23271 -0.65261,-1.84906 -0.34826,-0.54584 0.55879,-0.97756 1.41399,-1.41399 2.91838,-1.23746 5.10402,-1.62977 6.74364,-2.0666 2.71465,-0.54567 4.95185,-1.28231 8.70147,-1.41399 3.55961,-0.20729 7.10222,-0.34661 10.55054,-0.10877 3.82453,0.29985 6.66255,0.67559 9.02777,1.08769 3.27123,0.68887 6.73279,1.37773 8.2664,2.0666 1.15781,0.6536 2.33433,1.29941 2.17537,2.50167 -0.0822,0.61442 -0.13302,1.23511 -0.76138,1.74029 -1.43232,1.2857 -3.03563,1.37441 -4.56827,1.95783 -2.61836,0.56426 -5.31848,0.965 -8.04886,1.30522 -3.39727,0.59526 -6.62824,1.05194 -9.35409,1.08769 -3.69812,0.0113 -7.39625,-0.004 -11.09437,-0.43507 -2.67925,-0.38109 -5.26827,-1.21333 -7.72256,-2.71921 -2.04467,-1.67516 -3.8545,-3.52646 -5.00334,-5.8735 -1.22374,-3.06925 -2.07932,-6.3686 -4.1332,-8.91901 -2.46814,-3.96977 -6.64378,-7.76879 -10.11546,-11.63821 -3.491999,-3.47992 -6.339051,-7.37026 -8.592706,-11.63822 -2.121343,-4.23581 -3.883734,-8.59126 -5.32965,-13.05221 -1.913031,-4.44884 -4.149023,-9.02686 -5.003348,-13.05221 -0.569852,-2.13306 -1.275509,-3.22496 -1.196451,-10.333 0.814455,-3.69663 2.503031,-6.37345 4.67704,-8.48393 1.126259,-1.47287 2.652657,-2.67898 4.459504,-3.69812 2.030344,-1.05631 4.060687,-1.07311 6.091031,-0.54385 1.849063,0.10395 3.69813,0.53169 5.54719,1.08769 l 14.03112,6.74364 4.89458,2.3929 c 1.48837,0.65832 2.54012,1.04374 3.04551,1.08769 1.08561,0.18024 1.17522,-0.13752 1.30522,-0.43508 0.39418,-0.71895 -0.48364,-1.55354 -1.41399,-2.3929 l -18.70816,-11.85576 c -1.51832,-0.83125 -2.96833,-1.31762 -4.24197,-1.41399 -1.731788,-0.15002 -3.138617,-0.0292 -4.133195,0.43508 -0.939585,0.48444 -1.428533,1.11909 -1.631526,1.84906 l 0.108767,1.19645 c -0.0087,1.28936 -0.208479,1.43208 -0.326305,2.0666 0.04729,0.80047 0.318135,1.51152 0.978916,2.0666 0.986103,0.83389 1.613993,1.66778 1.740294,2.50167 0.0907,0.9064 0.259926,1.81281 0,2.71921 -0.365376,1.01517 -0.452361,2.03035 -0.326305,3.04552 0.333471,1.06534 0.547978,1.29792 0.761378,1.52276 0.917448,1.12082 1.607925,1.56072 2.284136,1.95783 1.09418,0.49117 2.23976,0.94807 3.69813,1.19645 l 7.06994,1.84906 c 2.53892,0.39938 4.97195,0.63462 6.85241,0.76138 1.65138,0.0791 3.59763,0.25655 4.56827,0.10877 0.57111,0.0136 0.80925,-0.13922 0.76138,-0.43508 -0.12934,-0.40666 -0.55684,-0.66423 -1.30522,-0.76137 -3.02011,-0.0617 -5.61848,-0.54507 -8.2664,-0.97892 -2.95332,-0.46076 -5.58708,-0.98544 -8.15763,-1.52276 -1.91687,-0.39034 -3.95556,-0.76545 -4.67704,-1.30522 -0.72766,-0.38796 -1.479348,-1.01617 -2.284133,-2.17536 -0.389236,-0.77397 -0.425978,-1.47743 -0.435073,-2.17537 0.09792,-0.97148 0.260661,-1.55406 0.435073,-2.0666'
                // const sideFace = 'm 90.897177,271.83805 5.120968,-37.12703 c -7.756303,3.15573 -14.703454,7.01585 -24.169482,5.89727 -5.985983,-1.38675 -7.444881,-6.57286 -7.763484,-9.72702 l 0.896868,-5.22639 2.017281,-7.58701 c -3.935281,-0.42029 -5.915664,-1.03108 -6.647308,-5.11267 -0.605081,-1.81525 2.612402,-5.57714 6.220559,-4.70252 -3.071734,-0.45069 -4.8049,-1.08627 -5.621456,-4.69421 1.606875,-1.08781 3.979627,-1.38494 5.194709,-5.54772 l -0.426746,-3.84072 c -2.929633,-2.59577 -7.085123,-1.9226 -10.885736,-2.19576 -1.970149,-0.94214 -3.300262,-2.61576 -3.085961,-6.05442 l 15.474697,-22.35896 c 5.993082,-4.75401 7.262378,-8.25765 8.559421,-10.50711 -0.364016,-2.78204 -1.664418,-5.22544 -2.615968,-7.84219 -1.880515,-1.76248 -3.400032,-3.70546 -4.032476,-6.09197 l -0.11179,-10.60666 3.099023,-6.46323 4.267473,-4.69422 -4.694222,-14.08266 C 67.504153,87.325262 71.137463,82.20911 75.079716,77.520727 78.438214,69.056346 86.093572,64.411396 95.16465,61.024864 c 17.98413,-5.346245 36.43082,-6.992009 55.0504,-7.254702 38.04081,2.672582 61.73334,12.519303 67.85282,31.152551 18.11129,6.556314 29.62812,27.062437 31.15256,68.706317 0.8339,29.80995 -5.3586,49.5019 -20.48387,56.33065 -17.57337,10.2051 -26.19738,24.7059 -73.82729,20.48386 l -1.70699,8.1082 c -0.67552,10.55677 1.77018,18.43822 5.54772,25.17809 l 16.76897,18.16285';

                const facePath = svg.append('path')
                    .attr('d', downFace)
                    .attr('stroke', 'black')
                    .attr('stroke-width', 0.5)
                    .attr('fill', 'none');

                // Add Y and X axes
                // const y = d3.scaleLinear().domain([0,data.length]).range([height, 0]);
                const x = d3.scaleTime()
                    .domain([min_date, max_date])
                    .range([ 0, 1 ]);

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


                // Event description tooltip (applied to event)
                // https://gist.github.com/d3noob/4e4485d94aebf63ae8059258c40f2609
                const div = d3.select("body").append("div")
                    .attr("class", "tooltip")
                    .style("opacity", 0);

                // Draw the path of the lifeline
                data.forEach((lifeline) => {

                    // Draw sentence rectangles beneath lifeline
                    svg.append('g')
                        .selectAll('time-span-rect')
                        . data(lifeline["sentences"])
                        . enter()
                        .append('path')
                        .attr('stroke', (d) => {return this.sentenceColor[d.numericCode]})
                        .attr('d', (d) => {
                            return this.rectCoords(facePath, x(d.cleanStartDate), x(d.cleanEndDate), 2);
                        })
                        .attr('fill','none')
                        .attr('stroke-width', '3')
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
                        .append('path')
                        .attr('stroke', (d) => {return this.sentenceColor[d.numericCode]})
                        .attr('d', (d) => {
                            return this.rectCoords(facePath, x(d.cleanStartDate), x(d.cleanEndDate), 5);
                        })
                        .attr('fill','none')
                        .attr('stroke-width', '3')
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


                    lifeline["events"].forEach( (d) => {
                        var symbol, symX;
                        // var symY;
                        if (d.Source === "Transportation Crime and Trial Records V1") {
                            symX = x(d.dates);
                            const p = this.labelCoords(facePath, symX, 7);
                            // symY = y(d.yValue) - 15;
                            symbol = this.drawGavel(svg, p.x, p.y);
                        } else if (d.Source === "PermissionToMarryRecordsV1" || d.Source === "MarriageRecordsV1") {
                            symX = x(d.dates);
                            const p = this.labelCoords(facePath, symX, 4);
                            // symY = y(d.yValue) - 12;
                            symbol = this.drawHeart(svg, p.x, p.y);
                        } else if (d.Source === "FAS DB Download") {
                            symX = x(d.dates);
                            const p = this.labelCoords(facePath, symX, 7);
                            // symY = y(d.yValue) - 13;
                            symbol = this.drawAnchor(svg, p.x, p.y);
                        } else if (d.Source === "FreedomsV4") {
                            symX = x(d.dates);
                            const p = this.labelCoords(facePath, symX, 5);
                            // symY = y(d.yValue) - 13;
                            symbol = this.drawSquare(svg, p.x, p.y);
                        } else {
                            symX = x(d.dates);
                            const p = this.labelCoords(facePath, symX, 4);
                            symbol = svg.append('circle')
                                    .attr('cx', function () {
                                        return p.x;
                                    })
                                    .attr('cy', function () {
                                        return p.y;
                                    })
                                    .attr('r', function () {
                                        return 2;
                                    })
                                    .attr('fill', () => {
                                        return d.Source in this.offenceColor ? this.offenceColor[d.Source] : "#000000";
                                    })
                                    //.style('fill', 'black')//'#69b3a2'
                                    .style('opacity', '1')
                                    .attr('stroke', 'none')
                            }

                           symbol.on("mouseover", function() {
                               div.transition()
                                   .duration(200)
                                   .style("opacity", 1);
                               div.html(niceTimeFormat(d.dates) + ': ' + d.eventDescription)
                                   .style("left", (d3.event.pageX) + "px")
                                   .style("top", (d3.event.pageY - 40) + "px");
                           })
                           .on("mouseout", function() {
                               div.transition()
                                   .duration(500)
                                   .style("opacity", 0);
                           });
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


    }

    .convictName {
        font-size: x-small;
        font-weight: bold;
    }
</style>
