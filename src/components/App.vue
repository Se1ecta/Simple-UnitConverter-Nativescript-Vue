<template>
  <Page>
    <ActionBar title="Unit converter"  />
    <StackLayout class="converter">
      <ScrollView orientation="horizontal">
        <StackLayout orientation="horizontal" padding="10 15">
          <StackLayout
            class="unit-type-wrapper"
            @tap="addActiveClass(0)"
            :class="items[0].active ? 'active' : ''"
          >
            <Image
              src.decode="font://&#xf545;"
              class="unit-image fas"
              stretch="none"
              :class="items[0].active ? 'active' : ''"
            />
            <Label text="Length"></Label>
          </StackLayout>
          <StackLayout
            class="unit-type-wrapper"
            @tap="addActiveClass(1)"
            :class="items[1].active ? 'active' : ''"
          >
            <Image
              src.decode="font://&#xf5cd;"
              class="unit-image fas"
              stretch="none"
            />
            <Label text="Mass"></Label>
          </StackLayout>
          <StackLayout
            class="unit-type-wrapper"
            @tap="addActiveClass(2)"
            :class="items[2].active ? 'active' : ''"
          >
            <Image
              src.decode="font://&#xf624;"
              class="unit-image fas"
              stretch="none"
            />
            <Label text="Speed"></Label>
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
        { title: "Length", active: true },
        { title: "Mass", active: false },
        { title: "Speed", active: false },
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
      this.items.forEach((item, index) => {
        return (item.active = i === index);
      });
      console.log(this.activeUnitName);
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
        const full_name = conversion.full_name;
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

.unit-type-wrapper {
  display: flex;
  justify-content: center;
  padding: 5;
  text-shadow: 1px 1px 1px #ccc;
  margin-right: 15;
  Image {
    color: #447dd4;
    font-size: 14;
    text-align: center;
  }
  Label {
    text-align: center;
    
  }
  &:last-child {
    margin-right: 0;
  }
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
  Image {
    // color: orange;
  }
}
</style>
