<template>
  <div class="map-container"></div>
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
      let map = new google.maps.Map(this.$el, mapOptions);

      // Add marker
      let marker = new google.maps.Marker({
        position: myLatlng,
        map: map
      });

      marker.setMap(map);

      google.maps.event.addListener(map, "center_changed", () => {
        let lat = map.getCenter().lat();
        let lon = map.getCenter().lng();
        let newLatLng = {lat: lat, lng: lon};
        marker.setPosition(newLatLng);

        this.$emit('locationUpdated', newLatLng)
      });
    }
  }
</script>
