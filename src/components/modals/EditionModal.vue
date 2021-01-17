<template>
  <Modal class="editions" v-if="modals.edition" @close="toggleModal('edition')">
    <div v-if="!isCustom">
      <h3>Selecione uma Edição:</h3>
      <ul class="editions">
        <li
          v-for="edition in editions"
          class="edition"
          :class="['edition-' + edition.id]"
          :style="{
            backgroundImage: `url(${require('../../assets/editions/' +
              edition.id +
              '.png')})`
          }"
          :key="edition.id"
          @click="setEdition(edition)"
        >
          {{ edition.name }}
        </li>
        <li
          class="edition edition-custom"
          @click="isCustom = true"
          :style="{
            backgroundImage: `url(${require('../../assets/editions/custom.png')})`
          }"
        >
          Cenários/Personagens Personalizados
        </li>
      </ul>
    </div>
    <div class="custom" v-else>
      <h3>Carregue um Cenário/Personagem Personalizado</h3>
       Para jogar com um script personalizado, você precisa selecionar os personagens que deseja
       para jogar no oficial
      <a href="https://bloodontheclocktower.com/script-tool/" target="_blank"
        >Ferramenta de Script</a
      >
       e carregue o "custom-list.json" gerado diretamente aqui ou
       forneça uma URL para esse arquivo JSON hospedado.<br />
      <br />
      Para jogar com personagens personalizados, leia
      <a
        href="https://github.com/bra1n/townsquare#custom-characters"
        target="_blank"
        >a Documentação</a
      >
      sobre como escrever um arquivo de definição de caractere personalizado.
      <b>Carregue apenas arquivos JSON personalizados de fontes em que você confia!</b>
      <h3>Alguns scripts personalizados populares:</h3>
      <ul class="scripts">
        <li
          v-for="(script, index) in scripts"
          :key="index"
          @click="handleURL(script[1])"
        >
          {{ script[0] }}
        </li>
      </ul>
      <input
        type="file"
        ref="upload"
        accept="application/json"
        @change="handleUpload"
      />
      <div class="button-group">
        <div class="button" @click="openUpload">
          <font-awesome-icon icon="file-upload" /> Insira o Arquivo JSON
        </div>
        <div class="button" @click="promptURL">
          <font-awesome-icon icon="link" /> Insira o Link
        </div>
        <div class="button" @click="isCustom = false">
          <font-awesome-icon icon="undo" /> Voltar
        </div>
      </div>
    </div>
  </Modal>
</template>

<script>
import editionJSON from "../../editions";
import { mapMutations, mapState } from "vuex";
import Modal from "./Modal";

export default {
  components: {
    Modal
  },
  data: function() {
    return {
      editions: editionJSON,
      isCustom: false,
      scripts: [
        [
          "Deadly Penance Day",
          "https://gist.githubusercontent.com/bra1n/0337cc44c6fd2c44f7589256ed5486d2/raw/16be38fa3c01aaf49827303ac80577bdb52c0b25/penanceday.json"
        ],
        [
          "Catfishing 9.0",
          "https://gist.githubusercontent.com/bra1n/8a5ec41a7bbf945f6b7dfc1cef72b569/raw/fed370d55554e0d83e9d56023c230099f41d0660/catfishing.json"
        ],
        [
          "Madness Por: Kildare",
          "https://gist.githubusercontent.com/botcbr/3397ae5dd4f9747cf4c88823522e0b18/raw/bd19ff1e93d22997d756738b8877729be2b8777b/Madness.json"
        ],
        [
          "The Power of the Death Por: Kildare",
          "https://gist.githubusercontent.com/botcbr/3086824e41906d9450a9ab63addd5cd8/raw/7573c8953b789774eff1812a4f3afabca146da5b/The%2520Power%2520of%2520Death.txt"
        ],
        [
          "Folia 5.1 Por: Kamekura",
          "https://gist.githubusercontent.com/botcbr/31e9d343d3a789d1a6137d767d67f0d2/raw/f3219865864aec70027ecd2d9368c2d2452c96d9/Folia.txt"
        ],
        [
          "Trouble with Violets",
          "https://gist.githubusercontent.com/botcbr/a9c768df3add03194b57a834f40e0069/raw/981bfe29ace12bff7483f5df2006126b80b735bf/TroubleWithViolets"
        ],
        [
          "On Thin Ice (5 ou 6 jogadores)",
          "https://gist.githubusercontent.com/bra1n/8dacd9f2abc6f428331ea1213ab153f5/raw/0cacbcaf8ed9bddae0cca25a9ada97e9958d868b/on-thin-ice.json"
        ],
        [
          "Race To The Bottom (5 ou 6 jogadores)",
          "https://gist.githubusercontent.com/bra1n/63e1354cb3dc9d4032bcd0623dc48888/raw/5acb0eedcc0a67a64a99c7e0e6271de0b7b2e1b2/race-to-the-bottom.json"
        ],
        [
          "Frankenstein's Mayor por: Ted (5 ou 6 jogadores)",
          "https://gist.githubusercontent.com/bra1n/32c52b422cc01b934a4291eeb81dbcee/raw/5bf770693bbf7aff5e86601c82ca4af3222f4ba6/Frankensteins_Mayor_by_Ted.json"
        ],
        [
          "Vigormortis High School (5 ou 6 jogadores)",
          "https://gist.githubusercontent.com/bra1n/1f65bd4a999524719d5dabe98c3c2d27/raw/22bbec6bf56a51a7459e5ae41ed47e41971c5445/VigormortisHighSchool.json"
        ]
      ]
    };
  },
  computed: mapState(["modals"]),
  methods: {
    openUpload() {
      this.$refs.upload.click();
    },
    handleUpload() {
      const file = this.$refs.upload.files[0];
      if (file && file.size) {
        const reader = new FileReader();
        reader.addEventListener("load", () => {
          try {
            const roles = JSON.parse(reader.result);
            this.parseRoles(roles);
          } catch (e) {
            alert("Error reading custom script: " + e.message);
          }
          this.$refs.upload.value = "";
        });
        reader.readAsText(file);
      }
    },
    promptURL() {
      const url = prompt("Enter URL to a custom-script.json file");
      if (url) {
        this.handleURL(url);
      }
    },
    async handleURL(url) {
      const res = await fetch(url);
      if (res && res.json) {
        try {
          const script = await res.json();
          this.parseRoles(script);
        } catch (e) {
          alert("Error loading custom script: " + e.message);
        }
      }
    },
    parseRoles(roles) {
      if (!roles || !roles.length) return;
      const metaIndex = roles.findIndex(({ id }) => id === "_meta");
      let meta = {};
      if (metaIndex > -1) {
        meta = roles.splice(metaIndex, 1).pop();
      }
      const customRoles = roles.map(role => {
        role.id = role.id.toLocaleLowerCase().replace(/[^a-z0-9]/g, "");
        return role;
      });
      this.$store.commit("setCustomRoles", customRoles);
      this.$store.commit(
        "setEdition",
        Object.assign({}, meta, { id: "custom" })
      );
      // check for fabled and set those too, if present
      if (customRoles.some(({ id }) => this.$store.state.fabled.has(id))) {
        const fabled = [];
        customRoles.forEach(({ id }) => {
          if (this.$store.state.fabled.has(id)) {
            fabled.push(this.$store.state.fabled.get(id));
          }
        });
        this.$store.commit("players/setFabled", { fabled });
      }
      this.isCustom = false;
    },
    ...mapMutations(["toggleModal", "setEdition"])
  }
};
</script>

<style scoped lang="scss">
ul.editions .edition {
  font-family: PiratesBay, sans-serif;
  letter-spacing: 1px;
  text-align: center;
  padding-top: 15%;
  background-position: center center;
  background-size: 100% auto;
  background-repeat: no-repeat;
  width: 30%;
  margin: 5px;
  font-size: 120%;
  text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
    1px 1px 0 #000, 0 0 5px rgba(0, 0, 0, 0.75);
  cursor: pointer;
  &:hover {
    color: red;
  }
}

.custom {
  text-align: center;
  input[type="file"] {
    display: none;
  }
  .scripts {
    list-style-type: disc;
    font-size: 120%;
    cursor: pointer;
    display: block;
    width: 50%;
    text-align: left;
    margin: 10px auto;
    li:hover {
      color: red;
    }
  }
}
</style>
