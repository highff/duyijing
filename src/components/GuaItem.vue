<script lang="ts">
import { getGuaData } from "../services/Yijing";

type Yao = {
  kind: string;
  yaoCi: string;
  xiaoXiang: string;
  className: string;
};
type ViewObject = {
  id: string;
  name: string;
  nameClass: string;
  guaCi: string;
  tuanCi: string;
  daXiang: string;
  activeYao: Yao | undefined;
  yaos: Yao[];
};
export default {
  props: {
    id: String,
  },
  data() {
    const guaVal = getGuaData(this.id || "");
    const yaos = guaVal.id.split("").map((x: string, index: number) => {
      const kind = x === "1" ? "yang" : "yin";
      return {
        kind,
        yaoCi: guaVal.yao_ci[5 - index],
        xiaoXiang: guaVal.xiao_xiang[5 - index],
        className: kind as string,
      };
    });
    const vo: ViewObject = {
      id: guaVal.id,
      name: guaVal.name,
      nameClass: guaVal.name.length === 1 ? "name" : "name2",
      guaCi: guaVal.gua_ci,
      tuanCi: guaVal.tuan_ci,
      daXiang: guaVal.da_xiang,
      activeYao: undefined,
      yaos,
    };
    return { vo };
  },
  methods: {
    yaoClick(k: number) {
      const yaos = this.vo.yaos.map((x: Yao, i: number) => {
        const status = i === k ? "active" : "inactive";
        return {
          ...x,
          className: x.kind + " " + status,
        };
      });

      this.vo = {
        ...this.vo,
        activeYao: yaos[k],
        yaos,
      };
    },
    guaClick() {
      this.vo = {
        ...this.vo,
        activeYao: undefined,
        yaos: this.vo.yaos.map((x: Yao) => {
          return {
            ...x,
            className: x.kind,
          };
        }),
      };
    },
  },
};
</script>
<template>
  <div class="gua-item">
    <div class="gua">
      <label :class="vo.nameClass" @click="guaClick">
        {{ vo.name }}
      </label>
      <span
        v-for="(item, index) in vo.yaos"
        v-bind:key="index"
        :class="item.className"
        @click="yaoClick(index)"
      >
      </span>
    </div>
    <div class="text">
      <section name="guaCi" v-if="!vo.activeYao">
        <label>??????</label>
        <p>{{ vo.guaCi }}</p>
      </section>
      <section name="daXiang" v-if="!vo.activeYao">
        <label>??????</label>
        <p>{{ vo.daXiang }}</p>
      </section>
      <section name="tuanCi" v-if="!vo.activeYao">
        <label>??????</label>
        <p>{{ vo.tuanCi }}</p>
      </section>
      <section name="yaoCi" v-if="vo.activeYao">
        <label>??????</label>
        <p>{{ vo.activeYao?.yaoCi }}</p>
      </section>
      <section name="xiaoXiang" v-if="vo.activeYao">
        <label>??????</label>
        <p>{{ vo.activeYao?.xiaoXiang }}</p>
      </section>
    </div>
  </div>
</template>

<style scoped>
.gua-item {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: space-around;
  column-gap: 1rem;
}

.gua {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}

.gua .name,
.gua .name2 {
  font-size: var(--secondary-title-font-size);
  cursor: default;
}

.gua .yang,
.gua .yin {
  display: block;
  cursor: default;
  margin-bottom: 15px;
  height: 30px;
  width: 160px;
}
.gua .yang {
  background-color: var(--color-red);
}
.gua .yin {
  border-color: var(--color-black);
  border-left-style: solid;
  border-left-width: 75px;
  border-right-style: solid;
  border-right-width: 75px;
}

.gua .yang.inactive,
.gua .yin.inactive {
  opacity: 0.2;
}

.text {
  flex: 2;
  margin-top: 55px;
}
.text section label {
  font-size: var(--secondary-title-font-size);
}
</style>
