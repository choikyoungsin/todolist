<template>

        <nav class="navbar">
            <ul>
                <!-- <li><router-link to="/">Home</router-link></li> -->
                <!-- <li><router-link to="/Todos">TODOS</router-link></li> -->
                <li><router-link :to="{name:'Home'}">홈</router-link></li>
                <li><router-link :to="{name:'Todos'}">투두리스트</router-link></li>
                <!-- to="/" 이렇게 쓰다가 name을 쓰려면 이렇게 써야함 -->
            </ul>
        </nav>

    <router-view/>
</template>

<script>
    import './App.css'
    export default{

    }

</script>

<style>
   
</style>