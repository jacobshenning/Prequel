<template>
    <div class="flex flex-col justify-center items-center mt-6">
        <div class="flex w-4/5">

            <!--    Laravel prequel title, logo and current database connection    -->
            <div class="w-1/3">
                <div class="flex flex-row">
                    <div class="mr-1 mt-1">
                        <img class="no-drag"
                             height="32rem"
                             width="32rem"
                             alt="Protoqol Prequel"
                             src="/vendor/prequel/favicon.png">
                    </div>
                    <h1 class="text-2xl">
                        <span class="font-bold">Laravel</span>
                        Prequel
                        <a href="https://github.com/Protoqol"
                           target="_blank"
                           title="Creator of Laravel Prequel"
                           class="not-italic text-xs font-light">
                            PROTOQOL
                        </a>
                    </h1>
                </div>
                <div v-if="!error.error">
                    <p title="Current connection"
                       class="ml-2 mt-1 self-center flex flex-row items-center mr-1 tracking-wide text-gray-700">
                        {{env.user}}@{{env.host}}:{{env.port}}/{{env.database}}
                    </p>
                </div>
            </div>

            <!--     Table actions and information     -->
            <div class="w-1/3 flex flex-col justify-center items-center">
                <h1 v-if="activeTable" class="text-lg flex flex-row justify-center items-center font-semibold">
                    <span v-if="!tableLoading"
                          class="text-gray-600 font-thin">
                        {{activeTable}}
                    </span>
                    <img v-else class="mb-1" width="20" height="20" src="/vendor/prequel/loader.gif"
                         alt="Loading table data...">
                </h1>
                <label v-if="activeTable" class="flex flex-row">
                    <input list="columnList"
                           class="bg-white shadow appearance-none border rounded w-1/3 py-2 px-3 text-gray-700 leading-tight focus:outline-none"
                           type="text"
                           name="column"
                           autocomplete="off"
                           v-model="input.column"
                           placeholder="Column..."
                           :disabled="loading">

                    <datalist id="columnList">
                        <option v-for="struct in tableStructure" :value="struct.Field"></option>
                    </datalist>
                    <span class="m-2 font-bold text-lg">=</span>

                    <!-- Add pattern based on column select -->
                    <input v-if="activeTable"
                           class="bg-white shadow appearance-none border rounded w-3/5 py-2 px-3 text-gray-700 leading-tight focus:outline-none"
                           type="text"
                           name="value"
                           placeholder="Value..."
                           v-model="input.value"
                           @keyup.enter="searchHandler"
                           :disabled="loading">

                    <button class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-1 px-2 mr-2 border border-gray-400 rounded shadow"
                            title="Get value for selected column"
                            @click="searchHandler">
                        Get
                    </button>
                </label>
            </div>

            <!--    Buttons    -->
            <div v-if="!error.error" class="w-1/3 flex flex-row justify-end items-center">
                <button class="mr-4 flex justify-center items-center h-10 w-10 hover:bg-indigo-100 active:bg-indigo-200 rounded shadow"
                        :class="readability ? 'readability-enabled' : 'readability-disabled'"
                        :title="`Enhance readability (${readability ? 'Enabled' : 'Disabled'})`"
                        @click="readabilityButtonHandler">
                    <font-awesome-icon class="ml-1" icon="glasses"/>&nbsp;
                </button>

                <button class="mr-4 flex justify-center items-center h-10 w-10 hover:bg-indigo-100 active:bg-indigo-200 rounded shadow"
                        title="Set Dark Mode (Not available yet)"
                        :class="view.darkMode ? 'dark-mode-enabled' : 'dark-mode-disabled'"
                        @click="view.darkMode = (!view.darkMode)">
                    <font-awesome-icon class="ml-1" icon="adjust"/>&nbsp;
                </button>

                <button class="mr-4 flex justify-center items-center h-10 w-10 hover:bg-indigo-100 active:bg-indigo-200 rounded shadow"
                        :class="showSideBar ? 'sidebar-enabled' : 'sidebar-disabled'"
                        :title="`${sideBarStatusText} side bar`"
                        @click="sideBarButtonHandler">
                    <font-awesome-icon class="ml-1"
                                       :class="showSideBar ? 'chevron-point-left' : 'chevron-point-right'"
                                       icon="chevron-circle-up"/>&nbsp;
                </button>
            </div>
        </div>

        <!--    Divider    -->
        <span class="block my-4 w-4/5 divider-bottom"></span>
    </div>
</template>

<script>

  export default {
    name   : 'Header',
    props  : ['error', 'activeTable', 'env', 'loading', 'tableLoading', 'tableStructure'],
    data() {
      return {
        timeBeforeQuickFindMs: 750,
        sideBarStatusText    : 'Collapse',
        showSideBar          : true,
        readability          : true,

        view: {
          darkMode: false,
        },

        input: {
          column: '',
          value : '',
        },
      };
    },
    methods: {

      /**
       | Handle input when searching for data inside a table
       */
      searchHandler: function() {
        if (this.input.column && this.input.value) {
          this.$emit('shouldBeLoading');
        }
      },

      /**
       | Handles collapse button action
       | Emits event to collapse/expand sidebar.
       */
      sideBarButtonHandler: function() {
        this.showSideBar       = !this.showSideBar;
        this.sideBarStatusText = this.showSideBar ? 'Collapse' : 'Expand';
        this.$emit('collapseSideBar');
      },

      /**
       | Handles readability button actions.
       | Emits event to change readability globally.
       */
      readabilityButtonHandler: function() {
        this.readability = !this.readability;
        this.$emit('enhanceReadability');
      },

      /**
       | Handles config changes.
       | Holds data like readability or side bar preferences in localStorage
       */
      configHandler: function() {
        // TODO store button states in localStorage or something similair
      },
    },
  };
</script>


<style lang="scss">
    .divider-bottom {
        border-bottom: 1px solid #d5dfe9;
    }

    .dark-mode-enabled {
        color: #fff;
        background-color: #667eea;
        transition: 0.5s ease;

        &:hover {
            background-color: rgba(105, 144, 255, 0.7);
            transition: 0.5s ease;
        }
    }

    .dark-mode-disabled {
        color: #2d3748;
        background-color: transparent;
        transition: 0.5s ease;

        &:hover {
            background-color: #667eea;
            transition: 0.5s ease;
        }
    }

    .readability-enabled {
        color: #fff;
        background-color: #667eea;
        transition: 0.5s ease;

        &:hover {
            background-color: rgba(105, 144, 255, 0.7);
            transition: 0.5s ease;
        }
    }

    .readability-disabled {
        color: #2d3748;
        background-color: transparent;
        transition: 0.5s ease;

        &:hover {
            background-color: #667eea;
            transition: 0.5s ease;
        }
    }

    .sidebar-enabled {
        color: #2d3748;
        background-color: transparent;
        transition: 0.5s ease;

        &:hover {
            background-color: #667eea;
            transition: 0.5s ease;
        }
    }

    .sidebar-disabled {
        background-color: #667eea;
        color: #fff;
        transition: 0.5s ease;

        &:hover {
            background-color: rgba(105, 144, 255, 0.7);
            transition: 0.5s ease;
        }
    }

    .chevron-point-left {
        transform: rotate(270deg);
        transition: 0.5s ease;
    }

    .chevron-point-right {
        transform: rotate(90deg);
        transition: 0.5s ease;
    }
</style>