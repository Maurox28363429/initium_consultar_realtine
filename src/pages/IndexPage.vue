<template>
  <q-page class="flex flex-center fondo">
    <q-card class="row card" style="padding: 1em; margin: 1em">
      <div class="col-12" style="display: none">
        <q-select
          filled
          v-model="model"
          use-input
          input-debounce="0"
          label="Curso"
          :options="options"
          @filter="filterFn"
          style="width: 250px"
          behavior="menu"
        >
          <template v-slot:no-option>
            <q-item>
              <q-item-section class="text-grey"> No results </q-item-section>
            </q-item>
          </template>
        </q-select>
      </div>
      <div class="col-12" style="text-align: center">
        <img
          src="https://fabricaresultados.com/wp-content/uploads/2021/07/logo_360x.jpg"
          style="width: 200px; margin: 0 auto"
        />
      </div>
      <div class="col-12" style="padding: 1em">
        <q-input color="green" v-model="search" label="Nombre">
          <template v-slot:prepend>
            <q-icon name="search" />
          </template>
        </q-input>
      </div>
      <div class="col-12">
        <q-list bordered>
          <q-item-label header>Cursantes con pase</q-item-label>
          <q-separator />
          <q-item
            v-for="(pase, index) in pases_list"
            :key="index"
            class="q-my-sm"
            clickable
            v-ripple
          >
            <q-item-section avatar>
              <q-avatar color="primary" text-color="white">
                {{ pase.client.name[0] }}
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label
                >{{ pase.client.name }}
                {{ pase.client.last_name }}</q-item-label
              >
              <q-item-label caption lines="1">{{
                pase.client.email
              }}</q-item-label>
            </q-item-section>

            <q-item-section side>
              <q-icon
                name="radio_button_checked"
                :color="true == false ? 'red' : 'green'"
              />
            </q-item-section>
          </q-item>
        </q-list>
      </div>
      <div class="col-12" style="text-align: center; padding: 1em 0px">
        <q-pagination
          v-model="current_page"
          :max="last_page"
          direction-links
          outline
          color="blue"
          active-design="unelevated"
          active-color="blue"
          active-text-color="white"
        />
      </div>
    </q-card>
  </q-page>
</template>
<style>
.fondo {
  background-image: url(17545.jpg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  background-size: 100% 100%;
}
</style>
<script setup>
import { api } from "boot/axios";
import { ref, onMounted, watch } from "vue";
import PocketBase from "pocketbase";
import { useQuasar } from "quasar";
const $q = useQuasar();
const pb = new PocketBase("https://pocketbase.real.phoenixtechsa.com");
pb.collection("Initium_listado_pendientes").subscribe("*", function (e) {
  //aqui se debe recargar
  $q.notify({
    type: "positive",
    message: "Base de datos actualizada !",
  });
  cargar_data();
});
const search = ref("");
const current_page = ref(1);
const pases_list = ref([]);
const last_page = ref(1);
watch(current_page, () => {
  cargar_data();
});
watch(search, () => {
  cargar_data();
});
const cargar_data = async () => {
  await api
    .get("pases_list?page=" + current_page.value + "&search=" + search.value)
    .then((m) => {
      pases_list.value = m.data.data;
      current_page.value = m.data.pagination.currentPage;
      last_page.value = m.data.pagination.lastPage;
    });
};
onMounted(async () => {
  await cargar_data();
});
const contacts = ref([
  {
    id: 1,
    name: "Ruddy Jedrzej",
    email: "rjedrzej0@discuz.net",
    letter: "R",
    active: true,
  },
  {
    id: 2,
    name: "Mallorie Alessandrini",
    email: "malessandrini1@marketwatch.com",
    letter: "M",
    active: false,
  },
  {
    id: 3,
    name: "Elisabetta Wicklen",
    email: "ewicklen2@microsoft.com",
    letter: "E",
    active: true,
  },
  {
    id: 4,
    name: "Seka Fawdrey",
    email: "sfawdrey3@wired.com",
    letter: "S",
    active: false,
  },
]);
const stringOptions = ["Google", "Facebook", "Twitter", "Apple", "Oracle"];
const options = ref(stringOptions);

const filterFn = (val, update) => {
  if (val === "") {
    update(() => {
      options.value = stringOptions;
    });
    return;
  }
  update(() => {
    const needle = val.toLowerCase();
    options.value = stringOptions.filter(
      (v) => v.toLowerCase().indexOf(needle) > -1
    );
  });
};
</script>
