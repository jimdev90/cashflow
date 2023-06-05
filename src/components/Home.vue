<template>
    <Layout>
        <template #header>
            <Header></Header>
        </template>
        <template #resume>
            <Resume
                    :label="label"
                    :totalAmount="totalAmount"
                    :amount="amount"
            >
                <template #graphic>
                    <Graphic
                            :amounts="amounts"
                            @select="select"
                    />
                </template>
                <template #action>
                    <Action
                            @create="create"
                    />
                </template>

            </Resume>
        </template>
        <template #movements>
            <Movements
                    :movements="movements"
                    @remove="remove"
            ></Movements>
        </template>
    </Layout>
</template>
<script>
import Layout from './Layout.vue'
import Header from './Header.vue'
import Resume from './Resume/Index.vue'
import Movements from './Movements/Index.vue'
import Action from "@/components/Action.vue";
import Graphic from "@/components/Resume/Graphic.vue";

export default {
    name: 'Home',
    components: {
        Graphic,
        Action,
        Layout,
        Header,
        Resume,
        Movements,
    },
    data() {
        return {
            amount: null,
            label: 'Ahorro Total',
            movements: []
        }
    },
    mounted() {
        const movements = JSON.parse(localStorage.getItem('movements'));

        if (Array.isArray(movements)) {
            this.movements = movements.map(m => {
                return {...m, time: new Date(m.time)}
            });
        }

        console.log(movements)
    },
    methods: {
        create(movement) {
            this.movements.push(movement);
            this.save();
        },
        remove(id) {
            const index = this.movements.findIndex(m => m.id === id);
            this.movements.splice(index, 1);
            this.save();
        },
        save() {
            localStorage.setItem('movements', JSON.stringify(this.movements));
        },
        select(el) {
            console.log(el);
            this.amount = el;
        }
    },
    computed: {
        amounts() {
            const lastDays = this.movements
                .filter(({time}) => {

                    const today = new Date();
                    const oldDate = today.setDate(today.getDate() - 30);
                    return time >= oldDate;

                })
                .map(m => m.amount)

            return lastDays.map((m, i) => {
                const lastMovements = lastDays.slice(0, i + 1);
                return lastMovements.reduce((suma, movement) => {
                    return suma + movement
                }, 0);
            });
        },
        totalAmount() {
            return this.movements.reduce((suma, m) => {
                return suma + m.amount;
            }, 0);
        }
    },
}
</script>
