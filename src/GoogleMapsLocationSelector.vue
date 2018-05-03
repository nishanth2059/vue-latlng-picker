<template>
  <div class="latlng-picker">
    <div class="latlng-picker__map"></div>
    <input type="text" class="latlng-picker__autocomplete" ref="input">
  </div>
</template>

<script>
  export default {
    props: {
      latitude: {
        type: Number,
        default: 55.01657628017477
      },
      longitude: {
        type: Number,
        default: -7.309233337402361
      },
      zoom: {
        type: Number,
        default: 12
      }
    },

    data(){
      return {
        lat: this.latitude,
        lng: this.longitude,
      }
    },

    mounted(){
      // Set coordinates
      let myLatlng = new google.maps.LatLng(this.lat, this.lng);

      // Options
      let mapOptions = {
        zoom: this.zoom,
        center: myLatlng
      };

      // Apply options
      this.map = new google.maps.Map(this.$el, mapOptions);

      // Create search bar autocomplete
      this.autocomplete = new google.maps.places.Autocomplete(this.$refs.input, Object.assign({
        types: ['geocode']
      }))
      this.map.controls[google.maps.ControlPosition.TOP_LEFT].push(this.$refs.input)
      this.autocomplete.addListener('place_changed', this.moveMarker)


      // Add marker
      let marker = new google.maps.Marker({
        position: myLatlng,
        map: this.map
      });

      marker.setMap(this.map);

      google.maps.event.addListener(this.map, "center_changed", () => {
        let lat = this.map.getCenter().lat();
        let lon = this.map.getCenter().lng();
        let newLatLng = {lat: lat, lng: lon};
        marker.setPosition(newLatLng);

        this.$emit('locationUpdated', newLatLng)
      });
    },

    methods: {
      moveMarker () {
        var place = this.autocomplete.getPlace()
        var location = place.geometry && place.geometry.location
        if (location) {
          this.place = place
          this.map.panTo(location)
        }
      }
    }
  }
</script>

<style>
.latlng-picker__autocomplete {
    padding: 7px 14px;
    margin: 10px;
    width: 30%;
    min-width: 300px;
    font-family: Roboto;
    font-size: 15px;
    font-weight: 300;
    text-overflow: ellipsis;
    border: 0;
    border-radius: 2px 0 0 2px;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.3);
  }
</style>
