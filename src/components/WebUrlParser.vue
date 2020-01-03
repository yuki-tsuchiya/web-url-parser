<template>
    <div class="container card">
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
            <div class="output-container text-left paragraph">
                <strong class="title">Protocol:</strong>
                <span v-bind:id="'protocol-val'">{{protocol}}</span>
            </div>
            <div class="d-flex align-items-center btn-container float-right">
                <b-button id="tooltip-target-0" class="btn btn-sm btn-secondary"
                          @click="copyText(protocol, 'protocol-val')">Copy
                </b-button>
                <b-tooltip target="tooltip-target-0" triggers="focus">
                    copy to clipboard
                </b-tooltip>
            </div>
        </div>
        <hr/>
        <div class="row">
            <div class="text-left paragraph output-container">
                <strong class="title">Domain:</strong>
                <span v-bind:id="'domain-val'">{{domain}}</span>
            </div>
            <div class="d-flex align-items-center btn-container">
                <b-button id="tooltip-target-1" class="btn btn-sm btn-secondary" @click="copyText(domain,'domain-val')">
                    Copy
                </b-button>
                <b-tooltip target="tooltip-target-1" triggers="focus">
                    copy to clipboard
                </b-tooltip>
            </div>
        </div>
        <hr/>
        <div class="row">
            <div class="text-left paragraph output-container">
                <strong class="title">Path:</strong>
                <span v-bind:id="'path-val'">{{path}}</span>
            </div>
            <div class="d-flex align-items-center btn-container">
                <b-button id="tooltip-target-2" class="btn btn-sm btn-secondary" @click="copyText(path, 'path-val')">
                    Copy
                </b-button>
                <b-tooltip target="tooltip-target-2" triggers="focus">
                    copy to clipboard
                </b-tooltip>
            </div>
        </div>
        <hr/>
        <div class="row">
            <div class="text-left paragraph output-container">
                <strong class="title">Query String Parameters:</strong>
            </div>
        </div>
        <div class="row output-q-string" v-for="(param, key) in params" :key="key">
            <div class="text-left q-string output-container">
                <span class="q-title"><span class="q-title-text">{{param.key}}</span>:</span>
                <span v-bind:id="'q-string-val-'+key">{{param.val}}</span>
            </div>
            <div class="d-flex align-items-center btn-container">
                <b-button v-bind:id="'q-string-tooltip-target-'+key" class="btn btn-sm btn-info"
                          @click="copyText(param.val, 'q-string-val-'+key)">Copy
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
                protocol: '',
                domain: '',
                path: '',
                params: [],
                isValidUrl: true,
            }
        },
        watch: {
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
                    this.protocol = newUrl.protocol;
                    this.domain = newUrl.host;
                    this.path = decodeURI(newUrl.pathname);
                    this.isValidUrl = true;
                } catch (e) {
                    this.params = [];
                    this.protocol = '';
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
                            result['key'] = decodeURI(item[0]);
                            result['val'] = '';
                        } else {
                            result['key'] = decodeURI(item[0]);
                            result['val'] = decodeURI(item[1]);
                        }
                        results.push(result);
                    });
                    return results;
                } else {
                    return [];
                }
            },
            copyText: function (text, targetId) {
                if (!navigator.clipboard || !navigator.clipboard.writeText) {
                    alert('cannot user writetext');
                    let range = document.createRange();
                    const yourCode = document.getElementById(targetId);
                    range.selectNode(yourCode);
                    window.getSelection().addRange(range);
                    document.execCommand('copy');
                } else {
                    navigator.clipboard
                        .writeText(text);
                    setTimeout(() => {
                        document.activeElement.blur();
                    }, 500);
                }
            }
        }
    }
</script>

<style scoped>
    #url-input {
        min-height: 160px;
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
        margin-right: 6px;
    }

    .q-title-text {
        color: #c62929;
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

    h3 {
        font-weight: 700;
        margin-top: 10px;
        margin-bottom: 10px;
    }

    strong {
        margin-right: 10px;
    }

    .title {
        /*color: #c62cba;*/
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

    .container {
        min-height: 95vh;
    }

    .btn-container {
        width: 60px;
    }

    .output-container {
        width: calc(100% - 60px);
        padding-left: 20px;
        padding-right: 20px;
    }

    .output-q-string {
        font-size: 14px;
    }
</style>
