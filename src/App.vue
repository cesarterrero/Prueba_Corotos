<template>
  <nav class="navbar navbar-light bg-dark">
    <div class="container-fluid">
      <span class="fs-4 mb-0 h1 text-white">Formulario</span>

      <div class="d-flex justify-content-flex-end">
        <span class="text-white"> Records: {{ records.length }}</span>
      </div>
    </div>
  </nav>

  <div align="center">
    <form action="">
      <div class="col-md-6 form-group">
        <label class="form-label margen">Nombre Completo </label>
        <input
          type="text"
          class="form-control"
          id="nombre"
          v-model="record.nombre"
          placeholder="Nombre y Apellido"
        />
      </div>
      <div class="col-md-6">
        <label class="form-label margen">Edad</label>
        <input
          type="number"
          required
          class="form-control"
          id="edad"
          v-model="record.edad"
          placeholder="Edad entre 1 a 100 años"
        />
      </div>
      <div class="col-md-6">
        <label class="form-label margen">Seleccione un color</label>
        <select id="color" class="form-select" v-model="record.color">
          <option>Rojo</option>
          <option>Amarillo</option>
          <option>Verde</option>
        </select>
      </div>
      <div class="col-md-6">
        <label class="form-label margen">Biografia</label>
        <textarea
          name="Bio"
          id="bio"
          cols="30"
          rows="5"
          class="form-control"
          maxlength="100"
          style="resize: none"
          v-model="record.bio"
          placeholder="Breve descripsion sobre usted"
        ></textarea>
      </div>

      <div class="col-md-6 margen">
        <button type="button" class="btn btn-primary" @click="save">
          Guardar
        </button>
      </div>
    </form>

    <div class="table-responsive container">
      <table class="table table-striped">
        <thead class="table-dark align-middle">
          <tr align="center">
            <th scope="col">#Id</th>
            <th scope="col">Nombre</th>
            <th scope="col">Edad</th>
            <th scope="col">Color</th>
            <th scope="col">Bio</th>
            <th scope="col" colspan="2">Tareas</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row of records" :key="row.id" align="center">
            <th scope="row">{{ row.id }}</th>
            <td>{{ row.nombre }}</td>
            <td>{{ row.edad }}</td>
            <td>{{ row.color }}</td>
            <td>{{ row.bio }}</td>
            <td colspan="2">
              <div class="btn-group">
                <button
                  type="button"
                  class="btn btn-primary"
                  @click="edit(row)"
                >
                  Editar
                </button>
                <button
                  type="button"
                  class="btn btn-danger"
                  @click="remove(row)"
                >
                  Eliminar
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="text-center" v-if="!records.length">No hay records</div>
  </div>
</template>

<script>
export default {
  data: () => ({
    record: { color: 'Rojo' },

    records: [],
  }),

  watch: {
    records(current) {
      localStorage.setItem('miData', JSON.stringify(current));
    },
  },

  mounted() {
    const records = JSON.parse(localStorage.getItem('miData'));
    records.edad = parseInt(records.edad, 10);
    this.records = records;
  },

  computed: {
    hasRecordProperties() {
      return (
        !this.record.nombre ||
        !this.record.edad ||
        !this.record.color ||
        !this.record.bio
      );
    },
  },

  methods: {
    save() {
      if (this.hasRecordProperties) {
        this.$swal.fire({
          position: 'center',
          icon: 'warning',
          title: 'Todos los datos son requeridos',
          showConfirmButton: false,
          timer: 1500,
        });
        return;
      } else if (this.record.edad < 1 || this.record.edad > 100) {
        this.$swal.fire({
          position: 'center',
          icon: 'warning',
          title: 'La edad tiene que estar entre 1 y 100 años ',
        });

        return;
      }

      const records = this.records.slice();

      const record = {
        ...this.record,
      };

      if (!record.id) {
        record.id = Math.random();
        records.push(record);

        this.$swal.fire({
          position: 'center',
          icon: 'success',
          title: 'Datos creados',
          showConfirmButton: false,
          timer: 1500,
        });
      } else {
        const index = records.findIndex((x) => x.id === record.id);
        records[index] = record;

        this.$swal.fire({
          position: 'center',
          icon: 'success',
          title: 'Datos actualizados',
          showConfirmButton: false,
          timer: 1500,
        });
      }

      this.records = records.slice();
      this.record = { color: 'Rojo' };
    },

    edit(row) {
      this.record = {
        ...row,
      };

      console.log(this.record.nombre);
    },

    remove(row) {
      this.$swal
        .fire({
          title: 'Desea borrar los datos?',
          text: 'Los datos se perderan para siempre!',
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Si, eliminalo!',
        })
        .then((result) => {
          if (result.isConfirmed) {
            const records = this.records.slice();
            const index = records.findIndex((x) => x.id === row.id);
            records.splice(index, 1);

            this.records = records.slice();
            this.$swal.fire(
              'Eliminado!',
              'Los datos han sido eliminados.',
              'success'
            );
          }
        });
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
}
.margen {
  margin: 20px;
}
</style>
