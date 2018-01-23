<template>
    <div class="avalon">
        <div class="character-wrapper">
            <div class="character-select">
                <mu-raised-button
                    @click="addCharacter(item)"
                    v-for="(item, index) in character"
                    :key="index"
                    :label="item.num ? item.chn + '*' + item.num : item.chn"
                    class="demo-raised-button character-btn c-line-clamp1"
                    :primary="item.isEnemy"
                    :disabled="identityDisabled"/>
            </div>
            <div class="character-control">
                <mu-raised-button @click="removeCharacter" label="* 清空 *" class="demo-raised-button character-btn c-line-clamp1" :disabled="identityDisabled" secondary/>
                <mu-raised-button @click="shuffle" label="* 洗牌 *" class="demo-raised-button character-btn c-line-clamp1" :disabled="identityDisabled" secondary/>
          </div>
            </div>
        <div class="identity-wrapper">
            <p class="identity-title">{{ order }}号玩家，你的身份是：</p>
            <p class="identity-content">{{ showIdentity ? selected[order - 1] : '???' }}</p>
            <div class="identity-controller">
                  <mu-raised-button label="上一位" @click="prePlayer" class="demo-raised-button identity-button" :disabled="identityDisabled || !selected.length || order === 1"/>
                  <mu-raised-button :label="showIdentity ? '隐藏身份' : '查看身份'" @click="clickIdentity" class="demo-raised-button identity-button" :disabled="identityDisabled || !selected.length"/>
                  <mu-raised-button v-if="order === 1 || order < selected.length" label="下一位" @click="nextPlayer" class="demo-raised-button identity-button" :disabled="identityDisabled || !selected.length || order >= selected.length"/>
                  <mu-raised-button v-else label="完成" @click="finishIdentity" :disabled="identityDisabled" class="demo-raised-button identity-button"/>
            </div>
        </div>
        <div class="task-wrapper">
            <div class="task-content">
                <p>请做出你的选择</p>
                <p>（已有{{ taskArr.length }}位玩家做出选择）</p>
            </div>
            <div class="task-controller">
                <mu-raised-button label="成功" @click="taskOperate(true)" class="demo-raised-button task-button" :disabled="taskDisabled"/>
                <mu-raised-button label="失败" @click="taskOperate(false)" class="demo-raised-button task-button" :disabled="taskDisabled"/>
                <mu-raised-button label="查看结果" @click="showTaskResult" class="demo-raised-button task-button" :disabled="taskDisabled || taskArr.length === 0"/>
            </div>
        </div>
        <div class="game-replay">
            <mu-raised-button @click="replay" label="大 侠 请 重 新 来 过" class="game-replay-button"/>
        </div>
        <mu-dialog :open="dialog" @close="close">
            {{ taskResult ? "✅ 任务成功" : "❌ 任务失败" }}
            <mu-flat-button slot="actions" primary @click="close" label="确定"/>
        </mu-dialog>
        <mu-dialog :open="replayDialog"  @close="replayClose">
            确定开始新的一局吗？
            <mu-flat-button slot="actions" @click="replayClose" primary label="取消"/>
            <mu-flat-button slot="actions" primary @click="replayConfirm" label="确定"/>
        </mu-dialog>
    </div>
</template>

<script>
export default {
    name: 'home',
    data() {
        return {
            character: [{
                chn: '梅林',
                num: 0
            }, {
                chn: '派西维尔',
                num: 0
            }, {
                chn: '忠臣',
                num: 0
            }, {
                chn: '莫甘娜',
                num: 0,
                isEnemy: true
            }, {
                chn: '莫德雷德',
                num: 0,
                isEnemy: true
            }, {
                chn: '刺客',
                num: 0,
                isEnemy: true
            }, {
                chn: '奥伯伦',
                num: 0,
                isEnemy: true
            }, {
                chn: '爪牙',
                num: 0,
                isEnemy: true
            }],
            selected: [],
            order: 1,
            showIdentity: false,
            identityDisabled: false,
            taskDisabled: true,
            taskArr: [],
            taskResult: '',
            dialog: false,
            replayDialog: false
        };
    },
    methods: {
        addCharacter(item) {
            this.order = 1;
            this.showIdentity = false;
            this.identityDisabled = false;
            this.taskDisabled = true;
            if (item.num < 9) {
                this.selected.push(item.chn);
                item.num ++ ;
            }
        },
        removeCharacter() {
            this.selected = [];
            this.order = 1;
            this.character.forEach(item => {
                item.num = 0;
            });
        },
        clickIdentity() {
            this.showIdentity = !this.showIdentity;
        },
        nextPlayer() {
            this.showIdentity = false;
            this.order++;
        },
        prePlayer() {
            this.showIdentity = false;
            this.order--;
        },
        shuffle() {
            this.selected.sort(() => Math.random() > 0.5);
            this.showIdentity = false;
        },
        finishIdentity() {
            this.identityDisabled = true;
            this.showIdentity = false;
            this.taskDisabled = false;
        },
        taskOperate(bool) {
            this.taskArr.push(bool);
        },
        showTaskResult() {
            if (this.taskArr.length > 0) {
                  this.taskResult = this.taskArr.reduce((a, b) => a && b);
                this.dialog = true;
            }
        },
        close() {
            this.dialog = false;
            this.taskArr = [];
        },
        replay() {
            this.replayDialog = true;
        },
        replayClose() {
            this.replayDialog = false
        },
        replayConfirm() {
            this.replayDialog = false;
            this.selected = [];
            this.order = 1;
            this.showIdentity = false;
            this.identityDisabled = false;
            this.taskDisabled = true;
            this.taskArr = [];
            this.taskResult = '';
            this.dialog = false;
            this.character.forEach(item => item.num = 0);
        }
    },
    computed: {

    }
}
</script>

<style lang="styl" scoped>
.avalon {
    padding: 20px 17px;
}
.character-wrapper {
    border-bottom: 1px solid #ddd;
    padding-bottom: 10px;
}
.character-select, .character-control {
    display: flex;
    flex-wrap: wrap;
}
.character-btn {
    flex: 0 0 32%;
    margin-right: 2%;
    margin-bottom: 5px;
}
.character-btn:nth-child(3n) {
    margin-right: 0;
}
.identity-title, .identity-content, .task-content {
    text-align: center;
}
.identity-title, .task-content {
    font-size: 14px;
    line-height: 14px;
    margin-top: 15px;
}
.identity-content {
    font-size: 20px;
    line-height: 20px;
    margin-top: 20px;
}
.task-content p {
    margin-bottom: 5px;
}
.identity-controller, .task-controller {
    display: flex;
    justify-content: space-between;
}
.identity-controller, .task-controller {
    margin-top: 25px;
}
.identity-button, .task-button {
    flex: 0 0 32%;
}
p {
    margin: 0
}
.identity-wrapper, .task-wrapper {
    border-bottom: 1px solid #ddd;
    padding-bottom: 15px;
}
.game-replay {
    margin-top: 25px;
    padding-bottom: 25px;
    border-bottom: 1px solid #ddd;
}
.game-replay-button {
    width: 100%
}
</style>
