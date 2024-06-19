<script setup lang="ts">
import { type Ref, ref } from 'vue'
import { DateTime } from 'luxon'

const DATE_FORMAT = 'yyyy-MM-dd'

class DevCycle {
  constructor(
    public name: string,
    public startDate: DateTime,
    public endDate: DateTime,
    public productionVersion: string,
    public productionPublishDate: DateTime,
    public stagingVersion: string,
    public stagingPublishDate: DateTime,
  ) {}

  get duration(): number {
    return this.endDate.diff(this.startDate, 'days').days
  }

  day(duration: number): DateTime {
    return this.startDate.plus({ days: duration });
  }
}

const devCycles = ref([]) as Ref<DevCycle[]>;

devCycles.value.push(
  new DevCycle(
    '221 (RC 1.197)',
    DateTime.fromFormat('2024-06-07', DATE_FORMAT),
    DateTime.fromFormat('2024-06-27', DATE_FORMAT),
    '1.196',
    DateTime.fromFormat('2024-06-26', DATE_FORMAT),
    '1.197',
    DateTime.fromFormat('2024-06-10', DATE_FORMAT)
  )
)

</script>

<template>
  <div class="container">
    <div v-for="devCycle in devCycles" :key="devCycle.name" class="timeline">
      <div v-for="d in devCycle.duration" :key="d" class="container">
        <div v-if="d === 1">
          <div class="day">
            {{ devCycle.startDate.toLocaleString() }}
          </div>
        </div>


        <div v-else-if="d === devCycle.duration">
          <div class="day">
            {{ devCycle.endDate.toLocaleString() }}
          </div>
        </div>


        <div v-else-if="devCycle.day(d).equals(devCycle.productionPublishDate)" class="timeline__row">
          <div class="day">
            <div>Production publish ({{ devCycle.stagingVersion }})</div>
            <div>{{ devCycle.productionPublishDate.toLocaleString() }}</div>
          </div>
        </div>

        <div v-else-if="devCycle.day(d).equals(devCycle.stagingPublishDate)" class="timeline__row">
          <div class="day">
            <div>Staging publish ({{ devCycle.stagingVersion }})</div>
            <div>{{ devCycle.productionPublishDate.toLocaleString() }}</div>
          </div>
        </div>


        <div v-else class="timeline__link" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.timeline {
  display: flex;
  flex-direction: column;
}
.timeline__link {
  width: 5px;
  height: 50px;
  background: cornflowerblue;
}
.timeline__row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-direction: column;
}
.day {
  padding: 10px;
  height: 50px;
  border-radius: 999px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background: cornflowerblue;
}
</style>
