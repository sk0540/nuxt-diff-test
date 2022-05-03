<template>
    <div id="app">
        <div class="titleinfo">
            <h1 class="title">文章比較ツール diff</h1>
            <p class="desc"><a href="https://github.com/google/diff-match-patch">google-diff-match-patch</a> 使用</p>
        </div>
        <div class="appform">
            <p class="inputinfo">左右の枠に比較したい文章を入力してください</p>
            <div class="inputforms">
                <textarea class="d" v-model="input.A" />
                <textarea v-model="input.B" />
            </div>
            <div class="diffbutton">
                <button class="b" @click="func">比較</button>
            </div>
            <div class="resultpane">
                <p class="resultinfo">差分表示（背景着色）</p>
                <div class="results">
                    <div class="resultA">
                        <span v-for="outA in output.A" v-bind:style="{background:outA.color}">{{ outA.text }}</span>
                    </div>
                    <div class="resultB">
                        <span v-for="outB in output.B" v-bind:style="{background:outB.color}">{{ outB.text }}</span>
                    </div>
                </div>
                <div class="extrainfo">※ 比較はブラウザ上で行います<br>（サーバに送信しません）</div>
            </div>
        </div>
        <div class="copyright">©︎ 2022 sk0540</div>
    </div>
</template>

<script>




export default {


    head() {
        return {
            title: "文章比較ツール diff",


        }
    },

    data() {
        return {
            text: "Hello world",
            input: {
                A: '',
                B: ''
            },
            diffs: [],
            output: {
                A: [{
                    color: '',
                    text: '',
                }],
                B: [{
                    color: '',
                    text: '',
                }]
            },


        };
    },

    methods: {
        func() {
            this.output.A.splice(0);
            this.output.B.splice(0);


            let dmp = new diff_match_patch();
            this.diffs = dmp.diff_main(this.input.A, this.input.B);
            let br_out = { color: '', text: '\n' };
            for (let i = 0; i < this.diffs.length; i++) {
                let c_out = { color: '', text: '' };

                let cdiff = this.diffs[i];

                c_out.text = cdiff[1]





                if (cdiff[0] == 0) {
                    c_out.color = "";
                    this.output.A.push(c_out);
                    this.output.B.push(c_out);
                } else if (cdiff[0] == -1) {
                    c_out.color = "#FD9";
                    this.output.A.push(c_out);
                    for (let i = 0; i < c_out.text.length; i++) {
                        if (c_out.text.charAt(i) == '\n') {
                            this.output.B.push(br_out);
                        }
                    }

                } else if (cdiff[0] == 1) {
                    c_out.color = "#CF9";
                    this.output.B.push(c_out);
                    for (let i = 0; i < c_out.text.length; i++) {
                        if (c_out.text.charAt(i) == '\n') {

                            this.output.A.push(br_out);
                        }
                    }
                }
            }
        },
        diff_lineMode(text1, text2) {
            var dmp = new diff_match_patch();
            var a = dmp.diff_linesToChars_(text1, text2);
            var lineText1 = a.chars1;
            var lineText2 = a.chars2;
            var lineArray = a.lineArray;
            var diffs = dmp.diff_main(lineText1, lineText2, false);
            dmp.diff_charsToLines_(diffs, lineArray);
            return diffs;
        }
    }
};
</script>

<style>
body {
    margin: 0;
    box-sizing: border-box;
}

#app {
    margin: 0 auto;
    padding: 0 3.75rem;
    max-width: 45rem;
}

.titleinfo {
    padding: 0 1.5rem;
    padding-top: 1.25rem;
    border-left: 1px solid #999;
}

h1 {
    margin: 0;
    line-height: 1.25;
}

.desc {
    margin: 0;
}

a {
    text-decoration: none;
    color: #07c;
}

a:hover {
    color: #0dd;
}

a:active {
    color: #0dd;
}

.appform {
    margin: 2rem 0.5rem;
}

.inputinfo {
    text-align: center;
    margin: 1.25rem 0;
}

.inputforms {
    display: flex;
    margin: 1.25rem 0;
}

textarea {
    flex-grow: 1;
    height: 9rem;
    resize: none;
    margin: 0 1rem;
    background: none;
    appearance: none;
    border: none;
    outline: none;
    border-radius: 0;
}

textarea {
    border: solid 4px #aaa;
    border-radius: 8px;
}

textarea:hover {
    border: solid 4px #666;
}

textarea:focus {
    border: solid 4px #07c;
}

.diffbutton {
    text-align: center;
    margin: 1.5rem 0;
}

button {
    letter-spacing: 0.5em;
    text-indent: 0.5em;
    background: none;
    appearance: none;
    border: none;
    outline: none;
    cursor: pointer;
    padding: 0;
    line-height: 1.5em;
    font-size: 1.25rem;
    font-weight: 600;
    padding: 3px 29px 3px 29px;
    background: #09d;
    color: #fff;
    border-radius: 8px;
}

button:hover {
    background: #07c;
}

button:active {
    background: #0dd;
}

.resultpane {
    position: relative;
}

.resultinfo {
    padding: 0 2.5rem;
    margin: 1rem 0;
    font-weight: 600;
}

.results {
    display: flex;
    margin: 0.75rem 0;
}

.resultinfo:before {
    position: absolute;
    background: #aaa;
    content: "";
    left: 1.125rem;
    top: 0.375rem;
    height: 0.75rem;
    width: 0.75rem;
    border-radius: 0.25rem;
}

.resultA,
.resultB {
    word-break: break-all;
    white-space: pre-wrap;
    flex-grow: 1;
    width: 50%;
    margin: 0 1rem;
    padding: 0.5em 1rem;
    border-left: solid 1px #aaa;
    border-right: solid 1px #aaa;
}

span {
    margin-bottom: 0;
}

.extrainfo {
    margin: 0.75rem 1rem;
    text-align: right;
    font-size: 0.75rem;
    line-height: 2;
}

.copyright {
    margin-top: 1rem;
    margin-bottom: 1rem;
    text-align: center;
    font-size: 0.75rem;
    line-height: 2;
}

/*
#app {
    margin: 0 auto;
    max-width: 60em;
    padding: 0 1em;
}

.inputforms {
    display: flex;
    margin-top:1.5em;
}

textarea {
    flex-grow: 1;
    margin: 0 2%;
    height: 6rem;
    resize: none;


}

.d {
    border: solid 1px #999;
    border-radius: 2px;
}
.d:hover{
    border: solid 1px #555;

}
.d:focus{
  outline: solid 1px #07f;

}

.diffbutton {
    text-align: center;
}

button {
    letter-spacing: 0.5em;
    text-indent: 0.5em;
    
}

.b{
    padding: 3px 8px;
    border: none;
    background:#0cf;
    border-radius: 3px;
    transform: translateY(-4px);
    box-shadow: 0 4px 0 #09f;
}

 .b:hover{
    transform: translateY(0);
    box-shadow: 0 0 0 #09f;
    background:#09f;
 }
.b:active{
    background:#0fc;
    transform: translateY(0);
     box-shadow: 0 0 0 #09f;

}


.results {
    display: flex;
}

.resultA,
.resultB {
    flex-grow: 1;
    width: 50%;
    margin: 0 2%;
    background: #f7fcff;
    word-break: break-all;
    white-space: pre-wrap;
}*/
</style>