<template>
    <div class="col-sm-12 ml-40" id="legend">
        <h5>Legend</h5>
        <div class="row">
            <div class="col-sm-3 ml-4">
                <h6>Life Events</h6>
                <div class="row">
                    <div class="col-sm-2">
                        <div><img src=".././assets/gavel-solid.svg" height="15"></div>
                        <div><img src=".././assets/anchor-solid.svg" height="13"></div>
                        <div><img src=".././assets/square-solid.svg" height="12" style="transform:rotate(45deg);"></div>
                        <div><img src=".././assets/heart-solid.svg" height="13"></div>
                    </div>
                    <div class="col-sm-10">
                        <div>Trial</div>
                        <div>Voyage</div>
                        <div>Freedoms</div>
                        <div>Marriage or Permission to mary</div>
                    </div>

                </div>
            </div>
            <div class="col-sm-4 ml-4">
                <h6>Offences against</h6>
                <div class="row">
                    <div class="col-sm-2">
                        <div><span class="dot" id="I"></span></div>
                        <div><span class="dot" id="II"></span></div>
                        <div><span class="dot" id="III"></span></div>
                        <div><span class="dot" id="IV"></span></div>
                        <div><span class="dot" id="V"></span></div>
                    </div>
                    <div class="col-sm-10">
                        <div>Person - Class I</div>
                        <div>Property - Class II</div>
                        <div>Currency - Class III</div>
                        <div>Good Order - Class IV</div>
                        <div>Convict Regulation - Class V</div>
                    </div>

                </div>
            </div>
            <div class="col-sm-3 ml-4">
                <h6>Sentences</h6>
                <div class="row">
                    <div class="col-sm-2">
                        <div><span class="square" id="factory"></span></div>
                        <div><span class="square" id="crime"></span></div>
                        <div><span class="square" id="labour"></span></div>
                        <div><span class="square" id="separate"></span></div>
                        <div><span class="square" id="solitary"></span></div>
                    </div>
                    <div class="col-sm-10">
                        <div>Factory</div>
                        <div>Crime Class</div>
                        <div>Hard Labour</div>
                        <div>Separate Treatment</div>
                        <div>Solitary Confinement</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Legend"
    }
</script>

<style scoped>
    #legend {
        border-style: solid;
        border-width: 1px;
        border-radius: 2px;
    }
    text {
        font-size: xx-small;
        font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
    }
    h5 {
        text-align: center;
    }
    h6 {
        text-align: center;
    }
    .dot {
        border-style: solid;
        border-width: 1px;
        height: 12px;
        width: 12px;
        border-radius: 50%;
        display: inline-block;
    }
    .square {
        border-style: solid;
        border-width: 1px;
        height: 10px;
        width: 10px;
        border-radius: 10%;
        display: inline-block;
    }
    .rotate90 {
        -webkit-transform: rotate(90deg);
        -moz-transform: rotate(90deg);
        -o-transform: rotate(90deg);
        -ms-transform: rotate(90deg);
        transform: rotate(90deg);
    }
    #I {
        background-color: #EFB366;
    }
    #II {
        background-color: #8AB17D;
    }
    #III {
        background-color: #264653;
    }
    #IV {
        background-color: #C64C2E;
    }
    #V {
        background-color: #287271;
    }
    #factory {
        background-color: #2a9d8f;
    }
    #crime {
        background-color: #e76f51;
    }
    #labour {
        background-color: #e9c46a;
    }
    #separate {
        background-color: #D3D3D3;
    }
    #solitary {
        background-color: #000000;
    }

</style>
