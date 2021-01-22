<template>
    <div class="p-3 mb-2" id="cards">
        <h4>Description</h4>

        <div>Convict Name: {{specs[0].GivenNames}} {{specs[0].FamilyName}}</div>
        <div>Convict ID: {{specs[0].ConvictId}}</div>
        <div>Ship: {{specs[0].ShipNameToVDL}}</div>
        <div v-if="!!specs[0].TradeFirst">Occupation: {{specs[0].TradeFirst}}</div>
        <div v-if="!!specs[0].BirthDateYear">Birth Year: {{specs[0].BirthDateYear}}</div>
        <br/>
    </div>
</template>

<script>
    export default {
        name: "Description",
        props: ["specs"],
        data() {
            return {
            }
        }
    }
</script>

<style scoped>

</style>
