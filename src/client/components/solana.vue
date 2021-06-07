<template>
    <div class="column">
        <div class="block">
            <h3>Solana Network</h3>
            <div v-if="hasConnection && !hasProgramError">
                <div class="columns">
                    <div class="column">
                        <h4 class="has-text-success">Connected</h4>
                        <ul>
                            <li><code>{{connectionInfo.rpcUrl}}</code></li>
                            <li><code>{{connectionInfo.version}}</code></li>
                        </ul>
                    </div>
                </div>
            </div>
            <div v-else>
                <h4 class="has-text-danger">Disconnected</h4>
                <button @click="connect" class="button is-primary">Connect</button>
                <div v-if="hasProgramError" class="notification is-danger">
                    {{ hasProgramError }}
                </div>
            </div>
        </div>

        <div class="block columns">
            <div class="column">
                <div class="block box content">
                    <h4>Payers</h4>
                    <div class="block">
                        <button class="button is-small" @click="createPayer">Create Payer</button>
                    </div>
                    <div v-for="(p, ind) in getPayers" 
                        class="block columns" 
                        :class="isHighlighted(p.payerAccount.publicKey.toBase58()) ? 'has-background-info-light' : ''"
                    >
                        <div class="column is-three-quarters">
                            <div>
                                PubKey<code>{{p.payerAccount.publicKey.toBase58()}}</code>
                            </div>
                            <div>
                                Balance: <code>{{p.payerBalance}} SOL</code>
                            </div>
                        </div>
                        <div class="column">
                            <button class="button is-small" v-on:click="() => airdropAccount(ind)">Airdrop</button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="column">
                <div v-if="programReady" class="block box">
                    <h4>Program Info</h4>
                    <div class="block">
                        Program ID: <code>{{programInfo.programId.toBase58()}}</code>
                    </div>
                    <div class="block content">
                        <h4>Accounts</h4>
                        <div class="field has-addons">
                            <div class="control">
                                <span class="select is-small">
                                    <select ref="payerIndex">
                                        <option v-for="(p, ind) in getPayers" :value="ind">
                                            {{p.payerAccount.publicKey.toBase58()}}
                                        </option>
                                    </select>
                                </span>
                            </div>
                            <div class="control">
                                <input type="text" class="input is-small" ref="seed" placeholder="seed" />
                            </div>
                            <div class="control">
                                <a class="button is-info is-small" @click="createProgramAccount">
                                    Create Account
                                </a>
                            </div>
                        </div>
                        <div class="container">
                            <div 
                                v-for="a in getProgramAccounts"
                                class="block columns"
                                @mouseover="() => highlightPayer(a)"
                             >
                                <div class="column">
                                    <code>{{a.accountKey.toBase58()}}</code>
                                </div>
                                <div class="column">
                                    <button class="button is-small" v-on:click="() => showProgramAccount(a)">View</button>
                                </div>
                            </div>
                            <div class="block">
                                <div class="content">
                                    <h4>Create Instruction</h4>
                                    <div class="field has-addons">
                                        <input type="text" class="input is-small" ref="instKey">
                                        <button class="button is-small" @click="addKeyToInstruction">Add Key</button>
                                    </div>
                                    <div class="field">
                                        Data: <input type="input" class="input is-small" ref="instData" />
                                    </div>
                                    <button class="button is-small">Create</button>
                                </div>
                            </div>
                        </div>

                        <div class="modal" :class="showModal ? 'is-active' : ''" >
                            <div class="modal-background" @click="hideModal"></div>
                            <div class="modal-content">
                                <div v-if="showModal" class="box content">
                                    <div>lamports: {{ modalAccountInfo.lamports }}</div>
                                    <div>executable: {{ modalAccountInfo.executable }}</div>
                                    <div>owner program: {{ modalAccountInfo.owner.toBase58() }}</div>
                                    <div>rent epoch: {{ modalAccountInfo.rentEpoch }}</div>
                                    <button class="modal-close is-large" aria-label="close" @click="hideModal"></button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>


        <div class="columns">
            <div class="column">
                <div class="box content">
                    <h4>Tokens</h4>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { mapGetters } from "vuex";
export default {
    data() {
        return {
            highlighted: null,
            modalAccountInfo: {},
            showModal: false,
            instructions: [],
            instructionKeys: [],
            instructionData: null,
        };
    },
    created () {
        this.connect();
    },
    "methods": {
        connect() {
            this.$store.dispatch("setupConnection");
        },
        runTest() {
            this.$store.dispatch("runTestTransaction");
        },
        createProgramAccount() {
            const seed = this.$refs.seed.value;
            const payerIndex = this.$refs.payerIndex.value;
            if (seed && seed.length > 1 && payerIndex > -1){
                this.$store.dispatch("createProgramKeyAndAccountFromSeed", { seed, payerIndex });
            }
            else {
                alert("Missing seed");
            }
        },
        createPayer () {
            this.$store.dispatch("setupPayer");
        },
        highlightPayer (p) {
            this.highlighted = p.payerAccount.publicKey.toBase58();
        },
        isHighlighted (k) {
            return (k === this.highlighted);
        },
        async showProgramAccount (pa) {
            this.modalAccountInfo = await this.$store.dispatch("getProgramAccountInfo", pa);
            this.showModal = true;
        },
        hideModal () {
            this.showModal = false;
        },
        airdropAccount (accountIndex) {
            this.$store.dispatch("airdropAccount", accountIndex);
        },
        clearInstructions () {
            this.instructions = [];
        },
        addKeyToInstruction () {
            const instKey = this.$refs.instKey.value;
            instructionKeys.push(instKey);
        }
    },
    "computed": {
        ...mapGetters([
            "hasConnection",
            "connectionInfo",
            "connectionError",
            "hasPayer", // deprecated
            "getPayers",
            "payerInfo",
            "programReady",
            "programInfo",
            "hasProgramError",
            "getProgramAccounts",
        ]),
        testAvailable (){
            if (this.hasConnection && this.hasPayer && this.programReady) {
                return true;
            }
            return false;
        }
    },
    "watch": {
        hasConnection(n) {
            console.log("connection is live", n);
            this.$store.dispatch("setupProgram");
            // this.$store.dispatch("setupPayer");
        },
        hasPayer(n) {
            // if (n && this.hasConnection) {
            //     this.$store.dispatch("setupProgram");
            // }
        },
        connectionError(n) {
            if (n) {
                // alert("No connection to the network");
            }
        }
    }
}
</script>