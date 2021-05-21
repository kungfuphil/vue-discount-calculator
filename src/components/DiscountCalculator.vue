<template>
  <h1>Discount Calculator</h1>

  <label>Price $</label>
  <input type="number" v-model="price" />

  <label>Tax %</label>
  <input type="number" v-model="tax" />

  <h2>Simple Discount</h2>

  <label>Discount %</label>
  <input type="number" v-model="discount" />

  <p>Grand Total ${{ grandTotal }}</p>

  <h2>BOGO</h2>

  <label>% Off Second Item</label>
  <input type="number" v-model="bogoDiscount" />

  <p>Grand Total for Two Items ${{ bogoGrandTotal }}</p>
  <p>Price Per Item ${{bogoPricePerItem}}</p>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";

export default defineComponent({
  setup() {
    const price = ref<number>(0);
    const discount = ref<number>(0);
    const tax = ref<number>(7.5);
    const bogoDiscount = ref<number>(0);

    const taxRate = computed((): number => {
      return tax.value / 100;
    });
    const discountRate = computed((): number => {
      if (discount.value <= 0) {
        return 0;
      }
      return (100 - discount.value) / 100;
    });
    const bogoDiscountRate = computed((): number => {
      if (bogoDiscount.value <= 0) {
        return 0;
      }
      return (100 - bogoDiscount.value) / 100;
    });

    const grandTotal = computed((): number => {
      let discountedPrice =
        discountRate.value > 0 ? price.value * discountRate.value : price.value;
      let totalTax = discountedPrice * taxRate.value;

      return roundToTwo(+discountedPrice + totalTax);
    });

    const bogoGrandTotal = computed((): number => {
      let discountedPrice = price.value * 2;

      if (bogoDiscountRate.value > 0) {
        discountedPrice =
          +(price.value * bogoDiscountRate.value) + +price.value;
      }

      let totalTax = discountedPrice * taxRate.value;

      return roundToTwo(discountedPrice + totalTax);
    });

    const bogoPricePerItem = computed((): number => {
      return roundToTwo(bogoGrandTotal.value / 2);
    })

    const roundToTwo: number = (num: number) => {
      return +(Math.round(+(num + "e+2")) + "e-2");
    };

    return {
      price,
      discount,
      tax,
      grandTotal,
      bogoGrandTotal,
      bogoDiscount,
      bogoPricePerItem
    };
  },
});
</script>
