<template>
<div>
    <loading :active.sync="isLoading"></loading>
    <table class="table mt-4" v-if="orders.length">
        <thead>
            <tr>
                <th>購買時間</th>
                <th>Email</th>
                <th>購買款項</th>
                <th width="150">應付金額</th>
                <th class="text-right">是否付款</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="item in sortOrder" :key="item.id" :class="{'text-secondary': !item.is_paid}">
                <td>{{ item.create_at | date }}</td>
                <td>
                    <span v-text="item.user.email" v-if="item.user"></span>
                </td>
                <td>
                    <ul class="list-unstyled">
                        <li v-for="(product, i) in item.products" :key="i">
                            {{ product.product.title }} 數量：{{ product.qty }}
                            {{ product.product.unit }}
                        </li>
                    </ul>
                </td>
                <td class="text-right">{{ item.total | currency }}</td>
                <td class="text-right">
                    <span v-if="item.is_paid" class="text-success">已付款</span>
                    <span v-else class="text-danger">尚未啟用</span>
                </td>
            </tr>
        </tbody>
    </table>
    <pagination :pages="pagination" @changedpage="getOrders"></pagination>
</div>
</template>

<script>
import Pagination from '../views/Pagination'
export default {
    data() {
        return {
            orders: [],
            pagination: {},
            isLoading: false,
        };
    },
    components: {
        Pagination
    },
    methods: {
        getOrders(currentPage = 1) {
            const vm = this;
            const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/orders?page=${currentPage}`;
            console.log(process.env.VUE_APP_APIPATH, process.env.VUE_APP_CUSTOMPATH);
            vm.isLoading = true;
            this.$http.get(api).then((response) => {
                vm.isLoading = false;
                vm.orders = response.data.orders;
                vm.pagination = response.data.pagination;
                console.log(response.data);
            });
        },
    },
    computed: {
        sortOrder() {
            const vm = this;
            let newOrder = [];
            if (vm.orders.length) {
                newOrder = vm.orders.sort((a, b) => {
                    const aIsPaid = a.is_paid ? 1 : 0;
                    const bIsPaid = b.is_paid ? 1 : 0;
                    return bIsPaid - aIsPaid;
                })
            }
            return newOrder;
        }
    },
    created() {
        this.getOrders();
    }
}
</script>
