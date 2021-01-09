<template>
  <div class="intro">
    <img src="static/apple-icon.png" alt="" />
    Bem-vindo a (não oficial)
    <b>Cidade Virtual</b> para Blood on the Clocktower! Por favor
    adicione mais jogadores através do
    <span class="button" @click="toggleMenu">
      <font-awesome-icon icon="cog" /> Menu
    </span>
    no canto superior direito ou pressionando <b>[A]</b>. Você também pode entrar em uma sessão de jogo
     pressionando <b>[J]</b>.<br />
    <div class="footer">
      Este projeto é gratuito e traduzido de uma versão americana de código aberto e pode ser encontrado em
      <a href="https://github.com/bra1n/townsquare" target="_blank">GitHub</a>.
      Não é afiliado ao Pandemonium Institute. "Blood on the
      Clocktower" não é afiliado ao Steven Medway e ao Pandemonium Institute.
    </div>
  </div>
</template>

<script>
import { mapMutations } from "vuex";

export default {
  methods: mapMutations(["toggleMenu"])
};
</script>

<style scoped lang="scss">
// Intro
.intro {
  text-align: center;
  width: 50%;
  font-size: 120%;
  position: absolute;
  padding: 10px;
  background: rgba(0, 0, 0, 0.5);
  border: 3px solid black;
  border-radius: 10px;
  z-index: 3;
  a {
    color: white;
  }
  img {
    position: absolute;
    bottom: 100%;
    left: 50%;
    width: 25vh;
    margin-left: -12.5vh;
    margin-bottom: 2vh;
    max-width: 192px;
    border-radius: 50%;
    box-shadow: 0 0 10px black;
    border: 3px solid black;
  }
  .footer {
    font-size: 60%;
    opacity: 0.75;
  }
}
</style>
