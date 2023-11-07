<script setup>
import { ref } from 'vue';

import WelcomeItem from './WelcomeItem.vue'
import IconSmile from './icons/IconSmile.vue'
import IconRelieved from './icons/IconRelieved.vue'
import IconCrying from './icons/IconCrying.vue'
import IconDisappoint from './icons/IconDisappoint.vue'
import IconAngry from './icons/IconAngry.vue'
import axios from 'axios';
import HelloWorld from '../components/HelloWorld.vue'

const apiAnswer = ref(['...','...','...','...', '...', '...']);
let isLoading = ref([false,false,false,false,false,false]);

const getsentence= (idx) => {
  switch(idx){
    case 1:
      sendFeelings('happy', 1);
      break;
    case 2:
    sendFeelings('relieved', 2);
      break;
    case 3:
    sendFeelings('sad', 3);
      break;
    case 4:
      console.log('disappointed');
    sendFeelings('disappointed', 4);
      break;
    case 5:
    sendFeelings('angry', 5);
      break;
  }
}



const fetchAIResponse = async (message, idx) => {
  const apiKey = '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'; 
  const apiEndpoint = 'https://api.openai.com/v1/chat/completions';

  try {
    isLoading.value[idx] = true;
    const response = await axios.post(apiEndpoint, {
      model: "gpt-3.5-turbo",
      messages: [{ role: "user", content: message }],
      temperature: 0.8,
      max_tokens: 1024,
      top_p: 1,
      frequency_penalty: 0.5,
      presence_penalty: 0.5,
      stop: ["Human"],
    }, {
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${apiKey}`,
      },
    });
    isLoading.value[idx] = false;
    return response.data.choices[0].message.content;
  } catch (error) {
    console.error('OpenAI API 호출 중 오류 발생:', error);
    return 'OpenAI API 호출 중 오류 발생';
  }
  
};


    //  버튼 클릭 시 호출될 함수
    const sendFeelings = async (feeling, idx) => {
      
      const aiResponse = await fetchAIResponse(`I'm ${feeling} tell me one sentence to be more happy`, idx);
      apiAnswer.value[idx] = await aiResponse;
      apiAnswer.value[idx] = apiAnswer.value[idx].replace(/\"/gi, "");
      
    };


</script>

<template>
  <HelloWorld title="One sentence From GPT" />
  <WelcomeItem>
    <template #icon>
      <div  @click="getsentence(1)"><IconSmile /> </div>
    </template>
    <template #heading>Happy</template>
    <template #sentence>
      <div v-if="isLoading[1]" class="loading-container">
        <div>
          로딩중
        </div>
      </div>
      <div v-else>
        {{apiAnswer[1]}}
      </div>
    </template>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <div  @click="getsentence(2)">
        <IconRelieved />
      </div>
    </template>
    <template #heading>Relieved</template>
    <template #sentence>
      <div v-if="isLoading[2]" class="loading-container">
        <div>
          로딩중
        </div>
      </div>
      <div v-else>
        {{apiAnswer[2]}}
      </div>
    </template>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <div  @click="getsentence(3)">
        <IconCrying />
      </div>
    </template>
    <template #heading>Sad</template>
    <template #sentence>
      <div v-if="isLoading[3]" class="loading-container">
        <div>
          로딩중
        </div>
      </div>
      <div v-else>
        {{apiAnswer[3]}}
      </div>
    </template>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <div  @click="getsentence(4)">
        <IconDisappoint />
      </div>
    </template>
    <template #heading>Disapointed</template>
    <template #sentence>
      <div v-if="isLoading[4]" class="loading-container">
        <div>
          로딩중
        </div>
      </div>
      <div v-else>
        {{apiAnswer[4]}}
      </div>
    </template>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <div  @click="getsentence(5)">
        <IconAngry/>
      </div>
    </template>
    <template #heading>Angry</template>
    <template #sentence>
      <div v-if="isLoading[5]" class="loading-container">
        <div>
          로딩중
        </div>
      </div>
      <div v-else>
        {{apiAnswer[5]}}
      </div>
    </template>
  </WelcomeItem>
</template>
