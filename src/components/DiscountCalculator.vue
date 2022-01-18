<template>
    <h1>Discount Calculator</h1>

    <main>
        <section>
            <div class="form-control">
                <label>Price $</label>
                <input type="number" v-model="price" />
            </div>

            <div class="form-control">
                <label>Tax %</label>
                <input type="number" v-model="tax" />
            </div>

            <h2>Simple Discount</h2>

            <div class="form-control">
                <label>Discount %</label>
                <input type="number" v-model="discount" />
            </div>

            <div id="buttons">
                <button @click="applySimpleDiscount(30)">30%</button>
                <button @click="applySimpleDiscount(40)">40%</button>
                <button @click="applySimpleDiscount(50)">50%</button>
                <button @click="applySimpleDiscount(60)">60%</button>
            </div>

            <p>
                <strong>Grand Total</strong>
                : ${{ grandTotal }}
            </p>
        </section>

        <section>
            <h2>BOGO</h2>

            <div class="form-control-overflow">
                <label>% Off 2nd Item</label>
                <input type="number" v-model="bogoDiscount" />
            </div>

            <p>
                <strong>Grand Total for Two Items</strong>
                : ${{ bogoGrandTotal }}
            </p>
            <p>
                <strong>Price Per Item</strong>
                : ${{ bogoPricePerItem }}
            </p>
        </section>
    </main>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";

export default defineComponent({
    setup() {
        const price = ref<number>(0);
        const discount = ref<number>(0);
        const tax = ref<number>(7.5);
        const bogoDiscount = ref<number>(0);

        if ('geolocation' in navigator) {
            navigator.geolocation.getCurrentPosition(async (position) => {
                const posName = await getPositionInfo(position);
                const taxRate = await getTaxRate(posName);
                tax.value = taxRate;
            })
        }

        interface Position {
            lat: number,
            lon: number,
            city: string,
            state: string,
            country: string
        }

        async function getPositionInfo(position: GeolocationPosition): Promise<Position> {
            let pos: Position = {
                lat: position.coords.latitude,
                lon: position.coords.longitude,
                city: '',
                state: '',
                country: ''
            };

            const res = await fetch(`https://location-iq-service.herokuapp.com/cityinfo?lat=${pos.lat}&lon=${pos.lon}`)
            const apiData = await res.json();
            pos.city = apiData.city;
            pos.state = apiData.state;
            pos.country = apiData.country;

            return pos;
        }

        async function getTaxRate(position: Position) {
            interface CityInfo {
                city: string,
                state: string,
                country: string,
                tax: number
            }

            const res = await fetch(`https://location-iq-service.herokuapp.com/taxrate?city=${position.city}&state=${position.state}&country=${position.country}`)

            const cityInfo: CityInfo = await res.json();

            return cityInfo.tax;
        }

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

        const roundToTwo = (num: number) => {
            return Math.round(num * 100) / 100;
        }

        const applySimpleDiscount = (discountAmount: number) => {
            discount.value = discountAmount;
        }

        return {
            price,
            discount,
            tax,
            grandTotal,
            bogoGrandTotal,
            bogoDiscount,
            bogoPricePerItem,
            applySimpleDiscount
        };
    },
});
</script>

<style scoped>
#buttons {
    text-align: center;
}

@media (min-width: 48rem) {
    main {
        display: flex;
        justify-content: space-between;
        width: 70%;
        gap: 5rem;
    }

    section {
        flex-grow: 1;
    }

    .form-control label {
        text-align: center;
    }

    .form-control-overflow label,
    .form-control-overflow input {
        display: block;
        width: 100%;
    }
}
</style>