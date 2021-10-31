<template>
  <div class="main-wrapper">
    <div class="flex justify-between items-center">
      <h4 class="text-3xl font-normal leading-normal mt-0 mb-2 text-purple-800">Enter Repository name</h4>
      <div class="tooltip-container">
      <Icon name="tooltip" class="tooltip"/>
      </div>
    </div>
    <input class="bg-gray-200 repo-container appearance-none border-2 mb-4 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500" id="inline-full-name" type="text"  @input="debounceLeading" v-model="queryString">
    <div v-if="!inputError">
    <div class="repo-container mb-4" v-for="repo in takenData" :key="repo">
    <div class="bg-white shadow overflow-hidden sm:rounded-lg">
      <div class="border-t border-gray-200">
        <dl>
          <div class="bg-gray-50 px-4 py-5 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
            <dt class="text-sm font-medium text-gray-500">
              Repository author
            </dt>
            <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">
              <a :href="repo.owner.html_url" target="_blank">{{ repo.owner.login }}</a>
            </dd>
          </div>
          <div class="bg-white px-4 py-5 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
            <dt class="text-sm font-medium text-gray-500">
              Repository name
            </dt>
            <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">
              <a :href="repo.html_url" target="_blank">{{ repo.name }}</a>
            </dd>
          </div>
          <div class="bg-gray-50 px-4 py-5 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
            <dt class="text-sm font-medium text-gray-500">
              Description
            </dt>
            <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2" v-if="repo.description">
              {{ repo.description }}
            </dd>
            <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2" v-else>
              -
            </dd>
          </div>
          <div class="bg-white px-4 py-5 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
            <dt class="text-sm font-medium text-gray-500">
              Programming language
            </dt>
            <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">
              {{ repo.language }}
            </dd>
          </div>
          <div class="bg-gray-50 px-4 py-5 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6">
            <dt class="text-sm font-medium text-gray-500">
              Stars
            </dt>
            <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">
              {{ repo.stargazers_count }}
            </dd>
          </div>
        </dl>
      </div>
    </div>
    </div>
    </div>
    <div class="repo-container" v-else>
      <span>{{inputError}}</span>
    </div>
  </div>
</template>

<script>
import Icon from './Icon'

export default {
  name: 'HelloWorld',
  components: {
    Icon
  },
  data () {
    return {
      queryString:'',
      takenData: null,
      inputError: ''
    }
  },
  methods: {
    debounceLeading () {
      clearTimeout(this.debounce);
      this.debounce = setTimeout(async () => {
        try {
          await fetch(`https://api.github.com/search/repositories?q=${this.queryString}`)
              .then(response => response.json())
              .then(response => {
                this.takenData = response.items
                this.setLocation(this.queryString)
              })
        } catch (e) {
          this.takenData = null
          this.inputError = 'Something was wrong'
          console.log(e)
        }
      }, 500);
    },
    setLocation(curLoc) {
      try {
        history.pushState(null, null, curLoc);
        return;
      } catch(e) {
        location.hash = '#' + curLoc;
      }
    }
  }
}
</script>

<style scoped>
.main-wrapper {
  margin: 30px auto;
  max-width: 700px;
}
.tooltip-container {
  position: relative;
}
.tooltip-container:hover:after {
  position: absolute;
  content: 'Задержка перед запросом 0.3 сек';
  top: 5px;
  width: 150px;
  right: 15px;
  padding: 5px;
  color: #eee;
  border-radius: 3px;
  background-color: #8d9a9f;
}
</style>
