<script setup>

    import {ref, reactive, onMounted} from 'vue'
    import TodoItem from '../components/TodoItem.vue'
    import BasicTable from '../components/BasicTable.vue'
    import axios from 'axios'

    // msg 을 넘버로 넣으면 자동으로 연산해서 넘버기입
    const msg = 131
    const seen = ref('test')

    const testobj = ref({
        name: '홍길동',
        age: 20
    })

    const testobj1 = ref({
        name: '할로',
        age: 99
    })

    const todos = reactive([
        {id:1, text:'할일1'},
        {id:2, text:'할일2'},
        {id:3, text:'할일3'},
    ])  

    const todos1 = ref([
        {id:1, text:'할일1'},
        {id:2, text:'할일2'},
        {id:3, text:'할일3'},
    ])

    const dataChange = () => {

        // reactive 값 변경시 고치는 방법
        todos.push({
            id: 4,
            text: '할일4'
        });

        seen.value = 'test1'

        // ref 값 변경시 고치는 방법 값을 가져올때 getter/setter 느낌으로 가져온다고 생각하면됨
        todos1.value = [
            // 스프레드 연산 복사해서 붙이겠다.
            ...todos1.value,
            {id:4, text:'할일5'}
        ]

        // 존재하면 기존값을 유지하고 없으면 새로 추가하겠다.
        // ref는 무조건 .value로 접근해야됨
        testobj.value = {...testobj.value, age: 30, gender: '남자'}

        testobj1.value.age = 10
        testobj1.value.address = '서울'
    }

    const tableData = reactive({
        header: ['name', 'age', 'address', 'email'],
        rows: [
            {name: '홍길동', age: 20, address: '서울', email: 'hong@example.com', id: 1},
            {name: '김철수', age: 25, address: '부산', email: 'kim@example.com', id: 2},
            {name: '이영희', age: 22, address: '대구', email: 'lee@example.com', id: 3}
        ]
    })

    const userData = reactive({
        id: null,
        name: '',
        age : 0,
        address: '',
        email: ''
    })

    // 중복삭제를 방지하기 위해만듬
    const nextIdv= ref(tableData.rows.length + 1)

    const addUser = () => {
        tableData.rows.push({
            id: nextIdv.value,
            name: userData.name,
            age: userData.age,
            address: userData.address,
            email: userData.email
        });
        tableData.rows.push(newUser)
        nextIdv.value++;

        // 입력 필드 초기화
        userData.name = '';
        userData.age = 0;
        userData.address = '';
        userData.email = '';
        selectedId.value = null;
    }

    const deleteUser = (id) => {
        // 필터를 거쳐서 id가 일치하지 않는 것들만 남긴다. 즉 id가 일치하는 것은 삭제된다.
        tableData.rows = tableData.rows.filter(row => row.id !== id);
    }

    // 선택된 유저 ID 추가. 업데이트할 때 어떤 유저를 업데이트할지 알아야되니까
    const selectedId = ref(null)

    const clickUser = (user) => {
        // const findUser = tableData.rows.find(row => row.id === user.id);
        // if (!findUser) return

        // selectedId.value = findUser.id  // 어떤 row인지 기억 해서 내용을 수정하는 방향
        userData.id = user.id
        userData.name = user.name;
        userData.age = user.age;
        userData.address = user.address;
        userData.email = user.email;
    }
    
    // 클릭한 유저의 ID를 찾아서 그 유저의 데이터를 저장한다.
    const user = tableData.rows.find(row => row.id === selectedId.value);
        if (user) {
            user.name = userData.name;
            user.age = userData.age;
            user.address = userData.address;
            user.email = userData.email;
    }

    const updateUser = () => {
        tableData.rows.map(user => {
            if (user.id === userData.id) {
                user.name = userData.name;
                user.age = userData.age;
                user.address = userData.address;
                user.email = userData.email;
            }
            return user;
        })
    }

    const userData1 = reactive({
        id: null,
        name: '',
        address: '',
        email: ''
    })

    const userList = reactive([])

    onMounted(() => {
        dataCall();
    })

    const dataCall = () => {
        axios.get('https://jsonplaceholder.typicode.com/users',
            {params: {
            _limit: 5
            }
        }
        )
            .then(response => {
                console.log(response.data);
                userList.push(...response.data)
            })
            .catch(error => {
                console.error('Error fetching user data:', error);
            });
    };

</script>

<template>
  <h1>안녕하세요</h1>
  <!-- // 중괄호 2번써서 키값을 불러온다   -->
  <p>{{ msg + 111 }}</p>
  <!-- 바인딩 할꺼냐 그냥 쓸꺼냐 차이임.  -->
  <!-- <p v-bind:title="msg" id = "msg">타이틀이 보일겁니다.</p> -->
    <p :title="msg" id = "msg">타이틀이 보일겁니다.</p>

    <p v-if="seen =='test1'">if가 보일겁니다.</p>
    <p v-else-if="seen =='test'">else if가 보일겁니다.</p>
    <p v-else>else가 보일겁니다.</p>
    <p>
         <!-- v-model은 양방향 렌더링을 지원하는 디렉티브입니다. 
          위에 스크립트 있어야됨-->
        <input v-model="seen"/>
    </p>

    <ol>
        <!-- // {{ }} << 이걸로 키값을 호출하고 반복문을 사용해서 호출해라 -->
        <li v-for="todo in todos" :key="todo.id">
            {{ todo.text }}
        </li>
    </ol>

    
    <ol>
        <!-- // {{ }} << 이걸로 키값을 호출하고 반복문을 사용해서 호출해라 -->
        <li v-for="todo in todos1" :key="todo.id">
            {{ todo.text }}
        </li>
    </ol>

    <p>{{ testobj.name }} - {{ testobj.age }}</p>
    <p>{{ testobj }}</p>

    <p>{{ testobj1.name }} - {{ testobj1.age }}</p>
    <p>{{ testobj1 }}</p>

    <!-- onClick이랑 동작이 똑같음 -->
    <button @click="dataChange">데이터 변경</button>

    <ol>
        <TodoItem v-for="item in todos" 
            :key="item.id" 
            :todo="item"/>
    </ol>

    <p>
        name: <input v-model="userData.name"/>
        age: <input v-model="userData.age"/>
        <p></p>
        address: <input v-model="userData.address"/>
        email: <input v-model="userData.email"/>
    </p>

    <p>
        <button @click="addUser">추가</button>
    &emsp;
        <button @click="updateUser">수정</button>
    &emsp;
        <button @click="dataCall">데이터호출</button>
    </p>
    
    <ul>
        <li v-for="user in userList" :key="user.id">
            {{ user.name }} - {{ user.email }}
        </li>
    </ul>

    <BasicTable :tableData="tableData" 
    :deleteUser="deleteUser" 
    :clickUser="clickUser"
    :updateUser="updateUser"/>

</template>