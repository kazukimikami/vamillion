<template>
    <div class="inuyasha">
        <img src="@/assets/img/inuyasha.png">
        <div v-for="generateText in generateTexts" :key="generateText">
            <div class="box">
                {{ generateText }}
            </div>
        </div>
    </div>
    <form class="form" @submit.prevent="handleClick">
        <input class="input" type="text" placeholder="Let's talk!" v-model.lazy="keyword" required>
        <button class="button" type="submit">Talk</button>
    </form>
</template>

<script setup lang="ts">
const keyword = ref<string>('');
const generateTexts = ref<string[]>(['いくぜ！鉄砕牙！']);

const character = computed(() => `あなたは犬夜叉です。犬夜叉の口調で話してください。一人称は「俺」です。日本語で回答して下さい。`);
const prompt = computed(() => `${keyword.value}`);

const handleClick = async () => {
    generateTexts.value.push('少し待つんだ。');
    const input = document.getElementsByClassName('input')[0];
    const button = document.getElementsByClassName('button')[0];
    // TODO 絶対いい方法あるから暫定ってことで。
    input.disabled = true;
    button.disabled = true;

    const { data } = await useFetch('/api/generate', {
        method: 'POST',
        body: {
            character,
            prompt
        }
    })

    if (data.value.error) {
        generateTexts.value.pop();
        generateTexts.value.push('エラーだぜ！エラー内容は' + data.value.error.message + 'らしいぞ。');
        input.disabled = false;
        button.disabled = false;
    } else {
        generateTexts.value.pop();
        generateTexts.value.push(data.value.choices[0].message.content);
        input.disabled = false;
        button.disabled = false;
    }
}
</script>

<style scoped>
.inuyasha {
    margin: 0 auto;
    text-align: center;
    max-width: 60%;
}
.inuyasha > img {
    width: 57%;
    margin: 2%;
}
.box {
    margin-bottom: 10px;
}
.form {
    text-align: center;
    padding: 20px;
}
input {
    width: 45%;
    margin-bottom: 20px;
}
</style>

