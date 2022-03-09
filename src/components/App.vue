<template>
  <Page>
    <ActionBar title="Welcome to NativeScript-Vue!" />
    <StackLayout class="converter">
      <ScrollView orientation="horizontal">
        <StackLayout orientation="horizontal" ref="unitTypes">
          <StackLayout class="unit-type-wrapper" @tap="addActiveClass(0)">
            <Image
              src.decode="font://&#xf545;"
              class="unit-image fas"
              stretch="none"
              :class="activeUnit === 'Length' ? 'active' : ''"
            />
            <Label text="Length"></Label>
          </StackLayout>
          <StackLayout class="unit-type-wrapper" @tap="addActiveClass(1)">
            <Image
              src.decode="font://&#xf5cd;"
              class="unit-image fas"
              stretch="none"
              :class="activeUnit === 'Mass' ? 'active' : ''"
            />
          </StackLayout>
          <StackLayout class="unit-type-wrapper">
            <Image
              src.decode="font://&#xf624;"
              class="unit-image fas"
              stretch="none"
            />
          </StackLayout>
        </StackLayout>
      </ScrollView>
      <FlexboxLayout
        justifyContent="space-between"
        alignItems="flex-start"
        padding="20 10"
      >
        <TextField width="50%" keyboardType="number" v-model="value" />
        <DropDown
          backroundColor="red"
          itemsTextAlignment="center"
          :items="measuresShortNames"
          :selectedIndex="selectedIndex"
          width="50%"
          @selectedIndexChange="selectedIndexChanged($event)"
          marginTop="15"
          class="drop-down"
        />
        <!-- <ListPicker
          :items="measuresShortNames"
          :selectedIndex="selectedIndex"
          width="50%"
          @selectedIndexChange="selectedIndexChanged($event)"
        /> -->
      </FlexboxLayout>
      <StackLayout>
        <ScrollView height="100%">
          <StackLayout>
            <FlexboxLayout
              class="list-group"
              v-for="(measure, index) in convertValues()"
              :key="index"
              ><StackLayout>
                <Label :text="measure.convertedValue"></Label>
              </StackLayout>
              <FlexboxLayout class="measure-content">
                <Label :text="measure.shortName"></Label>
                <Label :text="measure.fullName" opacity="0.6"></Label>
              </FlexboxLayout>
            </FlexboxLayout>
          </StackLayout>
        </ScrollView>
      </StackLayout>
    </StackLayout>
  </Page>
</template>

<script >
import newUnits from "../assets/data/new_units.json";
export default {
  components: {},
  data() {
    return {
      value: 1,
      activeUnit: null,
      units: newUnits.data,
      selectedIndex: 0,
      items: [
        { title: "Length", icon: "font://&#xf545;", active: true },
        { title: "Mass", icon: "font://&#xf545;", active: false },
        { title: "Lenght", icon: "", active: false },
        { title: "Lenght", icon: "", active: false },
        { title: "Lenght", icon: "", active: false },
      ],
    };
  },
  watch: {
    value() {
      this.convertValues();
    },
  },
  computed: {
    activeUnitName() {
      let result = null;
      this.items.forEach((unit) => {
        if (unit.active === true) {
          result = unit.title;
        }
      });
      return result;
    },
    measures() {
      const result = [];
      Object.values(this.units).forEach((data) => {
        if (data.title === this.activeUnitName) {
          Object.values(data.measures).forEach((val) => {
            result.push(val);
          });
        }
      });
      return result;
    },
    conversions() {
      let result = null;
      this.measures.forEach((measure) => {
        if (measure.short_name === this.selectedValue) {
          result = measure.conversions;
        }
      });
      return result;
    },

    measuresShortNames() {
      const result = [];
      this.measures.forEach((measure) => {
        result.push(measure.short_name);
      });
      return result;
    },
    selectedValue() {
      return this.measuresShortNames[this.selectedIndex];
    },
  },
  methods: {
    addActiveClass(i) {
      this.clearActiveUnits();
      this.items[i].active = true;
      this.activeUnit = this.items[i].title;
      console.log("active");
      console.log(this.items[i]);
      console.log(this.items);
    },
    clearActiveUnits() {
      this.items.forEach((unit) => {
        unit.active = false;
      });
    },
    convertValues() {
      console.log("I start convert value");
      const result = [];
      this.conversions.forEach((conversion) => {
        const short_name = conversion.title;
        const full_name = conversion.full_name
        const value = conversion.coefficient * this.value;
        console.log(value);
        result.push({
          shortName: short_name,
          fullName: full_name,
          convertedValue: value,
        });
      });
      return result;
    },
    selectedIndexChanged(args) {
      this.selectedIndex = args.value;
      console.log(this.selectedIndex);
    },
  },
  mounted() {
    console.log(this.measuresShortNames);
  },
};
</script>

<style lang="scss" scoped>
ActionBar {
  background-color: #53ba82;
  color: #ffffff;
}

.drop-down {
  // border-bottom-width: 0.6;
  // border-color: #aaaaaa6e;
}

.unit-type-wrapper {
  padding: 5;
}
.unit-image {
  font-size: 14;
  color: black;
}

.converter {
  padding: 5;
}
.list-group {
  height: 100%;
  justify-content: space-between;
  align-items: center;
  border-bottom-width: 1;
  border-bottom-color: rgba(43, 40, 40, 0.151);
  padding: 5 20;
  margin: 0 10;
  Label {
    color: black;
  }
  &-item-unit {
    font-size: 18;
  }
  &-item-value {
    font-size: 18;
  }
  .measure-content {
    width: 20%;
    justify-content: center;
    flex-direction: column;
    align-items: flex-start;
  }
}
.active {
  color: orange;
}
</style>
