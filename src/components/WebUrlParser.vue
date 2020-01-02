<template>
    <div class="container">
        <div class="row">
            <div class="col-12">
                <div class="form-group url-group">
                    <h3>Web URL Parser</h3>
                    <textarea id="url-input" v-model="url" class="form-control"
                              placeholder="please enter url(ex. https://...)">
                    </textarea>
                </div>
            </div>
        </div>
        <div class="row warning-container">
            <div class="col-12 text-left warning" v-if="!isValidUrl">Invalid URL</div>
        </div>
        <div class="row">
            <div class="col-11 text-left paragraph">
                <strong class="title">Domain:</strong>
                <span>{{domain}}</span>
            </div>
            <div class="col-1 d-flex align-items-center">
                <b-button id="tooltip-target-1" class="btn btn-sm btn-success" @click="copyText(domain)">Copy</b-button>
                <b-tooltip target="tooltip-target-1" triggers="focus">
                    copy to clipboard
                </b-tooltip>
            </div>
        </div>
        <hr/>
        <div class="row">
            <div class="col-11 text-left paragraph">
                <strong class="title">Path:</strong>
                <span>{{path}}</span>
            </div>
            <div class="col-1 d-flex align-items-center">
                <b-button id="tooltip-target-2" class="btn btn-sm btn-success" @click="copyText(path)">Copy</b-button>
                <b-tooltip target="tooltip-target-2" triggers="focus">
                    copy to clipboard
                </b-tooltip>
            </div>
        </div>
        <hr/>
        <div class="row">
            <div class="col-12 text-left paragraph">
                <strong class="title">Query String Parameters:</strong>
            </div>
        </div>
        <div class="row" v-for="(param, key) in params" :key="key">
            <div class="col-11 text-left q-string">
                <strong class="q-title">{{param.key}}:</strong>
                <span>{{param.val}}</span>
            </div>
            <div class="col-1 d-flex align-items-center">
                <b-button v-bind:id="'q-string-tooltip-target-'+key" class="btn btn-sm btn-success"
                          @click="copyText(param.val)">Copy
                </b-button>
                <b-tooltip v-bind:target="'q-string-tooltip-target-'+key" triggers="focus">
                    copy to clipboard
                </b-tooltip>
            </div>
            <div class="col-12">
                <hr/>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'WebUrlParser',
        props: {
            msg: String
        },
        data: function () {
            return {
                url: '',
                domain: '',
                path: '',
                params: [],
                isValidUrl: true,
            }
        },
        watch: {
            // この関数は question が変わるごとに実行されます。
            url: function (after) {
                if (after === '') {
                    this.params = [];
                    this.domain = '';
                    this.path = '';
                    this.isValidUrl = true;
                    return;
                }
                try {
                    const newUrl = new URL(after);

                    const queryString = newUrl.search;

                    this.params = this.getParams(queryString);

                    this.domain = newUrl.host;
                    this.path = newUrl.pathname;
                    this.isValidUrl = true;
                } catch (e) {
                    this.params = [];
                    this.domain = '';
                    this.path = '';
                    this.isValidUrl = false;

                }
            }
        },
        methods: {
            getParams: function (queryStr) {
                if (queryStr) {
                    let results = [];
                    queryStr.substr(1).split('&').forEach((part) => {
                        const item = part.split('=');
                        const result = {};
                        if (item.length === 1) {
                            result['key'] = item[0];
                            result['val'] = '';
                        } else {
                            result['key'] = item[0];
                            result['val'] = decodeURIComponent(item[1]);
                        }
                        results.push(result);
                    });
                    return results;
                } else {
                    return [];
                }
            },
            copyText: function (text) {
                navigator.clipboard
                    .writeText(text);
                setTimeout(() => {
                    document.activeElement.blur();
                }, 500);
            }
        }
    }
</script>

<style scoped>
    #url-input {
        min-height: 200px;
        padding-bottom: 0;
    }

    .url-group {
        margin-bottom: 0;
    }

    .q-string {
        text-align: center;
    }

    .q-title {
        margin-left: 6px;
    }

    .paragraph {
        margin-bottom: 4px;
    }

    .btn {
        text-align: center;
    }

    hr {
        margin: 4px;
    }

    strong {
        margin-right: 10px;
    }

    .title {
        color: #000;
    }

    .warning {
        color: #e72222;
    }

    .warning-container {
        min-height: 28px;
        margin-bottom: 6px;
    }

    span {
        overflow: hidden;
        word-wrap: break-word;
        word-break: break-all;
    }

    textarea {
        word-break: break-all;
    }
</style>
