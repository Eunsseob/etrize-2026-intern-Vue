<script setup>

    defineProps({
        tableData: {
            type: Object,
        }

        ,deleteUser: {
            type: Function,
        }

        ,updateUser: {
            type: Function,
        }

        ,clickUser: {
            type: Function, 
        }
    })

</script>

<template>
    <table border="1" cellpadding="5" cellspacing="0">
            <tr>
                <th></th>
                <!-- 키값이 없으니깐 헤더를 직접 호출한다 -->
                <th v-for="header in tableData.header" :key="header">{{ header }}</th>
            </tr>
            <tr v-for="row in tableData.rows" :key="row.id">
                <td><button @click="deleteUser(row.id)">삭제</button></td>
                
                <!-- 이름 클릭시 clickUser 호출 -->
                <td v-for="header in tableData.header" :key="header">
                    <span 
                        v-if="header === 'name'" 
                        @click="clickUser(row)"
                    >
                        {{ row[header] }}
                    </span>
                    <span v-else>{{ row[header] }}</span>
                </td>
            </tr>
    </table>
</template>