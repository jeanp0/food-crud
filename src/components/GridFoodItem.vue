<template>
  <tr>
    <th scope="col">{{ aliment.id }}</th>
    <td>{{ aliment.name }}</td>
    <td>{{ aliment.stock }}</td>
    <td>{{ aliment.price }}</td>
    <td class="d-flex justify-content-around">
      <FormFoodButton variant="info" text="Edit" :alimentSelected="aliment" />
      <button class="btn btn-danger" @click="deleteFood()">Delete</button>
    </td>
  </tr>
</template>

<script>
import FormFoodButton from "@/components/FormFoodButton";
import { mapState, mapActions } from "vuex";

export default {
  name: "GridFoodItem",
  props: ["aliment"],
  components: {
    FormFoodButton,
  },
  data() {
    return {};
  },
  methods: {
    ...mapActions(["setEditForm"]),

    async deleteFood() {
      const swalWithBootstrapButtons = this.$swal.mixin({
        customClass: {
          confirmButton: "btn btn-success",
          cancelButton: "btn btn-danger",
        },
        buttonsStyling: false,
      });

      swalWithBootstrapButtons
        .fire({
          title: "Are you sure?",
          text: "You won't be able to revert this!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonText: "Yes, delete it!",
          cancelButtonText: "No, cancel!",
          reverseButtons: true,
        })
        .then(async (result) => {
          if (result.isConfirmed) {
            // delete a register
            console.log("Deleting food...");
            const url = `http://localhost:8081/food/${this.aliment.id}`;
            await fetch(url, {
              method: "DELETE",
            }).catch((error) => console.error("Error:", error));
            this.$emit("fetch-food");

            // sweet alert
            swalWithBootstrapButtons.fire(
              "Deleted!",
              `The ${this.aliment.name} has been deleted.`,
              "success"
            );
          } else if (
            /* Read more about handling dismissals below */
            result.dismiss === this.$swal.DismissReason.cancel
          ) {
            swalWithBootstrapButtons.fire(
              "Cancelled",
              `The ${this.aliment.name} is safe :)`,
              "error"
            );
          }
        });
    },
  },
  computed: {
    ...mapState(["alimentSelected"]),
  },
};
</script>
