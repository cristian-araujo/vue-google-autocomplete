<template>
  <input
    :id="id"
    :name="name"
    :class="classname"
    :placeholder="placeholder"
    :value="value"
    ref="autocompleteInput"
    @input="updateValue($event.target.value)"
  />
</template>

<script>
export default {
  name: 'VueGoogleAutocomplete',
  props: {
    id: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true
    },
    classname: {
      type: String,
      default: ''
    },
    placeholder: {
      type: String,
      default: ''
    },
    country: {
      type: Array,
      default: () => []
    },
    value: {
      type: String,
      default: ''
    },
    types: {
      type: String,
      default: 'address'
    }
  },
  emits: ['placechanged'],
  data() {
    return {
      autocomplete: null
    };
  },
  mounted() {
    this.initAutocomplete();
  },
  methods: {
    initAutocomplete() {
      // Asíncronamente carga el script de Google Maps API si aún no está cargado
      if (typeof google !== 'undefined' && google.maps.places) {
        const options = {
          types: this.types.split(','),
          componentRestrictions: { country: this.country }
        };
        this.autocomplete = new google.maps.places.Autocomplete(this.$refs.autocompleteInput, options);
        this.autocomplete.addListener('place_changed', this.onPlaceChanged);
      } else {
        console.error('Google Maps API script not loaded.');
      }
    },
    onPlaceChanged() {
      const place = this.autocomplete.getPlace();
      this.$emit('placechanged', place);
    },
    updateValue(value) {
      this.$emit('input', value);
    }
  },
  watch: {
    value(newValue) {
      if (newValue !== this.$refs.autocompleteInput.value) {
        this.$refs.autocompleteInput.value = newValue;
      }
    }
  }
};
</script>

<style scoped>
/* Estilos adicionales si es necesario */
</style>
