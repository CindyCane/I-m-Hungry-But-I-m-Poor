<template>
    <section id="mapWrapper">
        <div id="mapErrorBlock" v-show="errorState">
            <div id="mapErrorBlockContent">
                <img id="mapErrorBlockContentImage" alt="Sad Burger" src="../assets/errorBurger.svg">
                <h3 id="mapErrorBlockContentTitle">Uh oh.</h3>
                <p id="mapErrorBlockContentMessage">{{errorMessage}}</p>
                <button id="mapErrorBlockContentButton" @click="reloadPage">Refresh</button>
            </div>
        </div>
        <gmap-map id="mapTheMap" ref="mapRef" :center="center" :zoom="zoom" :options="{styles: styles}">
            <gmap-marker :key="index" v-show="mapMarkerState" v-for="(m, index) in markers" :icon="{url: require('../assets/marker.svg'),scaledSize: new google.maps.Size(32, 32),anchor: new google.maps.Point(16, 32),}" :label="{text: (index+1).toString(), color: '#ffffff'}" :position="m.position" @click="center=m.position"></gmap-marker>
        </gmap-map>
        <transition name="foodOptionsFade">
            <div id="mapFoodOptionsWrapper" v-show="optionsState">
                <div class="mapFoodOptions" v-for="foodChain in foodChainList" v-on:click="addMarker(foodChain.foodChainName, foodChain.foodChainSearch)">
                    <img class="mapFoodOptionsLogo" v-bind:src="foodChain.foodChainLogo" v-bind:alt="foodChain.foodChainName">
                </div>
            </div>
        </transition>
        <transition name="foodOptionsFade">
            <div id="mapFoodMoreWrapper" v-show="optionsState">
                <h3 id="mapFoodMoreButton" @click="showFoodList()">All Restaurants</h3>
            </div>
        </transition>
        <transition name="foodMoreListFade">
            <div id="mapFoodMoreListWrapper" v-show="foodChainListState">
                <div id="mapFoodMoreList">
                    <div id="mapFoodMoreListCloseWrapper">
                        <img id="mapFoodMoreListClose" alt="Close" src="../assets/close.svg" @click="showFoodList()">
                    </div>
                    <div id="mapFoodMoreListOptionsWrapper">
                        <div class="mapFoodMoreListOptions" v-for="foodChain in foodChainMoreList" v-on:click="addMarker(foodChain.foodChainMoreName, foodChain.foodChainMoreSearch)">
                            <img class="mapFoodMoreListOptionsLogo" v-bind:src="foodChain.foodChainMoreLogo" v-bind:alt="foodChain.foodChainMoreName">
                        </div>
                    </div>
                </div>
            </div>
        </transition>
        <transition name="placeListFade">
            <div id="mapPlaceListWrapper" v-show="mapState">
                <div id="mapFoodListButtonWrapper">
                    <h3 id="mapFoodListButton" @click="backToMenu()">Back to Menu</h3>
                </div>
                <h1 id="mapPlaceName">{{ placeName }}</h1>
                <ul id="mapPlaceList" v-show="mapPlaceState">
                    <li v-for="(place, index) in places">
                        <div class="mapPlaceListOne">
                            <img class="mapPlaceListMarker" alt="Marker" src="../assets/marker.svg">
                            <h3 class="mapPlaceListNumber">{{ index+1 }}</h3>
                        </div>
                        <div class="mapPlaceListTwo">
                            <h3 class="mapPlaceListAddress">{{ place.address }}</h3>
                            <div class="mapPlaceListRating">
                                <h3 class="mapPlaceListRatingNumber">{{ place.ratingFinal }}</h3>
                                <div v-bind:class="place.rating1"></div>
                                <div v-bind:class="place.rating2"></div>
                                <div v-bind:class="place.rating3"></div>
                                <div v-bind:class="place.rating4"></div>
                                <div v-bind:class="place.rating5"></div>
                                <h3 class="mapPlaceListRatingTimes">{{ place.ratingTimes }}</h3>
                            </div>
                            <h3 class="mapPlaceListDistance">{{ place.distance }}</h3>
                            <h3 v-bind:class="place.openClass">{{ place.open }}</h3>
                        </div>
                        <div class="mapPlaceListThree">
                            <a class="mapPlaceListLink" v-bind:href="place.url" target="_blank">
                                <img class="mapPlaceListDirection" alt="Direction" src="../assets/direction.svg">
                                <h3 class="mapPlaceListDirectionWord">Directions</h3>
                            </a>
                        </div>
                    </li>
                </ul>
            </div>
        </transition>

    </section>
</template>

<script>
    import {
        gmapApi
    } from 'vue2-google-maps'

    export default {
        computed: {
            google: gmapApi
        },
        name: 'BodyMap',
        data() {
            return {
                center: {
                    lat: 43.6532,
                    lng: -79.3832
                },
                markers: [],
                currentMarker: {},
                zoom: 15,
                styles: [{
                        "featureType": "landscape.natural",
                        "elementType": "geometry.fill",
                        "stylers": [{
                                "visibility": "on"
                            },
                            {
                                "color": "#e0efef"
                            }
                        ]
                    },
                    {
                        "featureType": "poi",
                        "elementType": "geometry.fill",
                        "stylers": [{
                                "visibility": "on"
                            },
                            {
                                "hue": "#1900ff"
                            },
                            {
                                "color": "#c0e8e8"
                            }
                        ]
                    },
                    {
                        "featureType": "road",
                        "elementType": "geometry",
                        "stylers": [{
                                "lightness": 100
                            },
                            {
                                "visibility": "simplified"
                            }
                        ]
                    },
                    {
                        "featureType": "road",
                        "elementType": "labels",
                        "stylers": [{
                            "visibility": "off"
                        }]
                    },
                    {
                        "featureType": "transit.line",
                        "elementType": "geometry",
                        "stylers": [{
                                "visibility": "on"
                            },
                            {
                                "lightness": 700
                            }
                        ]
                    },
                    {
                        "featureType": "water",
                        "elementType": "all",
                        "stylers": [{
                            "color": "#7dcdcd"
                        }]
                    }
                ],

                errorState: false,
                errorMessage: "I am error",
                places: [],
                placeName: "",
                mapPlaceState: true,
                mapMarkerState: true,
                mapState: false,
                optionsState: true,
                foodChainListState: false,
                foodChainList: [{
                        foodChainName: "Tim Hortons",
                        foodChainSearch: "Tim Hortons",
                        foodChainLogo: require('../assets/restruants/timHortons.svg')
                    },
                    {
                        foodChainName: "McDonald's",
                        foodChainSearch: "McDonald's",
                        foodChainLogo: require('../assets/restruants/mcDonalds.svg')
                    },
                    {
                        foodChainName: "Starbucks",
                        foodChainSearch: "Starbucks",
                        foodChainLogo: require('../assets/restruants/starbucks.svg')
                    },
                    {
                        foodChainName: "Harvey's",
                        foodChainSearch: "Harvey's Restruant",
                        foodChainLogo: require('../assets/restruants/harveys.svg')
                    },
                    {
                        foodChainName: "A&W",
                        foodChainSearch: "A&W",
                        foodChainLogo: require('../assets/restruants/aAndw.svg')
                    },
                    {
                        foodChainName: "Pizza Pizza",
                        foodChainSearch: "Hot and Fresh Pizza Pizza",
                        foodChainLogo: require('../assets/restruants/pizzaPizza.svg')
                    },
                    {
                        foodChainName: "Wendy's",
                        foodChainSearch: "Wendy's",
                        foodChainLogo: require('../assets/restruants/wendys.svg')
                    },
                    {
                        foodChainName: "Burger King",
                        foodChainSearch: "Burger King Corporation",
                        foodChainLogo: require('../assets/restruants/burgerKing.svg')
                    },

                ],
                foodChainMoreList: [{
                        foodChainMoreName: "A&W",
                        foodChainMoreSearch: "A&W",
                        foodChainMoreLogo: require('../assets/restruants/aAndw.svg')
                    },
                    {
                        foodChainMoreName: "Arby's",
                        foodChainMoreSearch: "Arby's Restaurant",
                        foodChainMoreLogo: require('../assets/restruants/arbys.svg')
                    },
                    {
                        foodChainMoreName: "Baskin-Robbins",
                        foodChainMoreSearch: "Baskin-Robbins",
                        foodChainMoreLogo: require('../assets/restruants/baskinRobbins.svg')
                    },
                    {
                        foodChainMoreName: "Booster Juice",
                        foodChainMoreSearch: "Booster Juice Ltd.",
                        foodChainMoreLogo: require('../assets/restruants/boosterJuice.svg')
                    },
                    {
                        foodChainMoreName: "Boston Pizza ",
                        foodChainMoreSearch: "Boston's The Gourmet Pizza Restaurant",
                        foodChainMoreLogo: require('../assets/restruants/bostonPizza.svg')
                    },
                    {
                        foodChainMoreName: "Burger King",
                        foodChainMoreSearch: "Burger King Corporation",
                        foodChainMoreLogo: require('../assets/restruants/burgerKing.svg')
                    },
                    {
                        foodChainMoreName: "Carl's Jr",
                        foodChainMoreSearch: "Carl's Jr LLC.",
                        foodChainMoreLogo: require('../assets/restruants/carlsJr.svg')
                    },
                    {
                        foodChainMoreName: "Chipotle Mexican Grill",
                        foodChainMoreSearch: "Chipotle Mexican Grill",
                        foodChainMoreLogo: require('../assets/restruants/chipotle.svg')
                    },
                    {
                        foodChainMoreName: "Coffee Time",
                        foodChainMoreSearch: "Coffee Time",
                        foodChainMoreLogo: require('../assets/restruants/coffeeTime.svg')
                    },
                    {
                        foodChainMoreName: "Dairy Queen",
                        foodChainMoreSearch: "Dairy Queen",
                        foodChainMoreLogo: require('../assets/restruants/dairyQueen.svg')
                    },
                    {
                        foodChainMoreName: "Domino's Pizza",
                        foodChainMoreSearch: "Domino's",
                        foodChainMoreLogo: require('../assets/restruants/dominos.svg')
                    },
                    {
                        foodChainMoreName: "Dunkin'",
                        foodChainMoreSearch: "Dunkin' Chain Known for Donuts & Coffee",
                        foodChainMoreLogo: require('../assets/restruants/dunkin.svg')
                    },
                    {
                        foodChainMoreName: "Five Guys",
                        foodChainMoreSearch: "Five Guys Enterprises LLC",
                        foodChainMoreLogo: require('../assets/restruants/fiveGuys.svg')
                    },
                    {
                        foodChainMoreName: "Harvey's",
                        foodChainMoreSearch: "Harvey's Restruant",
                        foodChainMoreLogo: require('../assets/restruants/harveys.svg')
                    },
                    {
                        foodChainMoreName: "Hero Certified Burgers",
                        foodChainMoreSearch: "Hero Certified Burgers",
                        foodChainMoreLogo: require('../assets/restruants/heroCertifiedBurgers.svg')
                    },
                    {
                        foodChainMoreName: "Jack in the Box",
                        foodChainMoreSearch: "Jack in the Box",
                        foodChainMoreLogo: require('../assets/restruants/jackInTheBox.svg')
                    },
                    {
                        foodChainMoreName: "Jimmy John's",
                        foodChainMoreSearch: "Jimmy John's Franchise, LLC",
                        foodChainMoreLogo: require('../assets/restruants/jimmyJohns.svg')
                    },
                    {
                        foodChainMoreName: "Jimmy the Greek",
                        foodChainMoreSearch: "Jimmy the Greek",
                        foodChainMoreLogo: require('../assets/restruants/jimmyTheGreek.svg')
                    },
                    {
                        foodChainMoreName: "Jollibee",
                        foodChainMoreSearch: "Jollibee",
                        foodChainMoreLogo: require('../assets/restruants/jollibee.svg')
                    },
                    {
                        foodChainMoreName: "KFC",
                        foodChainMoreSearch: "KFC",
                        foodChainMoreLogo: require('../assets/restruants/kfc.svg')
                    },
                    {
                        foodChainMoreName: "Krispy Kreme",
                        foodChainMoreSearch: "Krispy Kreme Doughnuts, Inc.",
                        foodChainMoreLogo: require('../assets/restruants/krispyKreme.svg')
                    },
                    {
                        foodChainMoreName: "Little Caesars",
                        foodChainMoreSearch: "Little Caesar Enterprises Inc.",
                        foodChainMoreLogo: require('../assets/restruants/littleCaesars.svg')
                    },
                    {
                        foodChainMoreName: "Manchu Wok",
                        foodChainMoreSearch: "Manchu Wok Inc.",
                        foodChainMoreLogo: require('../assets/restruants/manchuWok.svg')
                    },
                    {
                        foodChainMoreName: "McDonald's",
                        foodChainMoreSearch: "McDonald's",
                        foodChainMoreLogo: require('../assets/restruants/mcDonalds.svg')
                    },
                    {
                        foodChainMoreName: "Mr. Sub",
                        foodChainMoreSearch: "Mr. Sub",
                        foodChainMoreLogo: require('../assets/restruants/mrSub.svg')
                    },
                    {
                        foodChainMoreName: "Papa John's Pizza",
                        foodChainMoreSearch: "Papa John's Pizza",
                        foodChainMoreLogo: require('../assets/restruants/papaJohns.svg')
                    },
                    {
                        foodChainMoreName: "Pizza Hut",
                        foodChainMoreSearch: "Pizza Hut",
                        foodChainMoreLogo: require('../assets/restruants/pizzaHut.svg')
                    },
                    {
                        foodChainMoreName: "Pizza Pizza",
                        foodChainMoreSearch: "Hot and Fresh Pizza Pizza",
                        foodChainMoreLogo: require('../assets/restruants/pizzaPizza.svg')
                    },
                    {
                        foodChainMoreName: "Popeyes",
                        foodChainMoreSearch: "Popeyes Louisiana Kitchen, Inc.",
                        foodChainMoreLogo: require('../assets/restruants/popeyes.svg')
                    },
                    {
                        foodChainMoreName: "Quiznos",
                        foodChainMoreSearch: "Quiznos",
                        foodChainMoreLogo: require('../assets/restruants/quiznos.svg')
                    },
                    {
                        foodChainMoreName: "Starbucks",
                        foodChainMoreSearch: "Starbucks",
                        foodChainMoreLogo: require('../assets/restruants/starbucks.svg')
                    },
                    {
                        foodChainMoreName: "Second Cup",
                        foodChainMoreSearch: "Second Cup Coffee Co.",
                        foodChainMoreLogo: require('../assets/restruants/secondCup.svg')
                    },
                    {
                        foodChainMoreName: "Sonic Drive-In",
                        foodChainMoreSearch: "Sonic Drive-In",
                        foodChainMoreLogo: require('../assets/restruants/sonic.svg')
                    },
                    {
                        foodChainMoreName: "Subway",
                        foodChainMoreSearch: "Subway Eat Fresh",
                        foodChainMoreLogo: require('../assets/restruants/subway.svg')
                    },
                    {
                        foodChainMoreName: "Swiss Chalet",
                        foodChainMoreSearch: "Swiss Chalet",
                        foodChainMoreLogo: require('../assets/restruants/swissChalet.svg')
                    },
                    {
                        foodChainMoreName: "Taco Bell",
                        foodChainMoreSearch: "Taco Bell",
                        foodChainMoreLogo: require('../assets/restruants/tacoBell.svg')
                    },
                    {
                        foodChainMoreName: "Thaï Express",
                        foodChainMoreSearch: "Thaï Express",
                        foodChainMoreLogo: require('../assets/restruants/thaiExpress.svg')
                    },
                    {
                        foodChainMoreName: "Tim Hortons",
                        foodChainMoreSearch: "Tim Hortons",
                        foodChainMoreLogo: require('../assets/restruants/timHortons.svg')
                    },
                    {
                        foodChainMoreName: "Wendy's",
                        foodChainMoreSearch: "Wendy's",
                        foodChainMoreLogo: require('../assets/restruants/wendys.svg')
                    },
                    {
                        foodChainMoreName: "Yogen Früz",
                        foodChainMoreSearch: "Yogen Früz",
                        foodChainMoreLogo: require('../assets/restruants/yogenFruz.svg')
                    },
                ],

            };
        },

        mounted() {
            this.geolocate();
        },

        methods: {
            addMarker: function(foodChain, foodChainSearch) {
                this.foodChainListState = false
                document.body.style.overflowY = "auto"

                this.optionsState = false

                this.$refs.mapRef.$mapPromise.then((map) => {

                    var service;
                    var currentLoc = new google.maps.LatLng(this.center.lat, this.center.lng);
                    var request = {
                        location: currentLoc,
                        radius: '1000',
                        name: foodChainSearch
                    };

                    service = new google.maps.places.PlacesService(map);
                    service.nearbySearch(request, callback.bind(this));

                    function callback(results, status) {
                        if (results.length > 0) {
                            this.mapPlaceState = true;
                            this.mapMarkerState = true;
                            this.placeName = results.length + " " + foodChain + " around your area."
                            this.zoom = 14
                        } else {
                            this.mapPlaceState = false;
                            this.mapMarkerState = false;
                            this.placeName = "No " + foodChain + " in the vicinity."
                        }

                        if (status == google.maps.places.PlacesServiceStatus.OK) {
                            for (var i = 0; i < results.length; i++) {
                                var place = results[i];
                                createMarker.bind(this)(results[i], i);
                                
                                var address;
                                var rating = [];
                                var ratingNumber;
                                var ratingCounter = 4;
                                var ratingFinal;
                                var ratingTimes;
                                var open;
                                var openClass;
                                var centerLat = this.center.lat;
                                var centerLng = this.center.lng;
                                var placeLat = place.geometry.location.lat();
                                var placeLng = place.geometry.location.lng();
                                var distance;
                                var url;

                                function createPlaceData() {
                                    if (place.hasOwnProperty("vicinity")) {
                                        address = place.vicinity

                                        url = "https://www.google.com/maps/dir/?api=1&origin=" + centerLat + "," + centerLng + "&destination=QVB&destination_place_id=" + place.place_id + "&travelmode=walking"
                                    } else {
                                        address = "Unknown Address"

                                        url = "https://maps.google.ca/maps"
                                    }

                                    if (place.hasOwnProperty("rating")) {
                                        ratingFinal = place.rating;
                                        ratingNumber = place.rating;
                                        ratingTimes = "(" + place.user_ratings_total + ")";

                                        function createRating() {
                                            if (ratingNumber >= 1) {
                                                rating.push("mapPlaceListRatingStarFull");
                                                ratingNumber--;
                                                ratingCounter--;
                                                createRating();
                                            } else if (ratingNumber > 0.7) {
                                                rating.push("mapPlaceListRatingStarFull");
                                                ratingNumber--;
                                                ratingCounter--;
                                                createRating();
                                            } else if (ratingNumber > 0.2) {
                                                rating.push("mapPlaceListRatingStarHalf");
                                                ratingNumber--;
                                                ratingCounter--;
                                                createRating();
                                            } else {
                                                for (var i = 0; i <= ratingCounter; i++) {
                                                    rating.push("mapPlaceListRatingStarHollow");
                                                }
                                            }
                                        }
                                        createRating();
                                    } else {
                                        ratingFinal = "No ratings";
                                    }

                                    if (place.hasOwnProperty("opening_hours")) {
                                        if (place.opening_hours.open_now === true) {
                                            open = "Open now"
                                            openClass = "mapPlaceListIsOpen mapPlaceListIsOpenOpen"
                                        } else if (place.opening_hours.open_now === false) {
                                            open = "Closed now"
                                            openClass = "mapPlaceListIsOpen mapPlaceListIsOpenClosed"
                                        } else {
                                            open = "No store hours recorded"
                                            openClass = "mapPlaceListIsOpen mapPlaceListIsOpenClosed"
                                        }
                                    } else {
                                        if (place.permanently_closed === true) {
                                            open = "Permanently Closed"
                                            openClass = "mapPlaceListIsOpen mapPlaceListIsOpenClosed"
                                        } else {
                                            open = "No store hours recorded"
                                            openClass = "mapPlaceListIsOpen mapPlaceListIsOpenClosed"
                                        }
                                    }

                                    var rad = function(x) {
                                        return x * Math.PI / 180;
                                    };

                                    function getDistance(p1Lat, p1Lng, p2Lat, p2Lng) {
                                        var R = 6378137;
                                        var dLat = rad(p2Lat - p1Lat);
                                        var dLong = rad(p2Lng - p1Lng);
                                        var a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
                                            Math.cos(rad(p1Lat)) * Math.cos(rad(p2Lat)) *
                                            Math.sin(dLong / 2) * Math.sin(dLong / 2);
                                        var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
                                        var d = (R * c) / 1000;
                                        distance = (Math.round(d * 10) / 10) + " Kilometers away"
                                    }

                                    getDistance(centerLat, centerLng, placeLat, placeLng)

                                }

                                createPlaceData();

                                this.places.push({
                                    address: address,
                                    rating1: rating[0],
                                    rating2: rating[1],
                                    rating3: rating[2],
                                    rating4: rating[3],
                                    rating5: rating[4],
                                    ratingFinal: ratingFinal,
                                    ratingTimes: ratingTimes,
                                    open: open,
                                    openClass: openClass,
                                    distance: distance,
                                    url: url
                                })
                            }
                        }
                    }

                    function createMarker(place, number) {
                        var marker = new google.maps.Marker({
                            icon: {
                                url: require('../assets/marker.svg'),
                                scaledSize: new google.maps.Size(32, 32),
                                anchor: new google.maps.Point(16, 32),
                            },
                            position: place.geometry.location,
                            label: {
                                text: (number + 1).toString(),
                                color: "#ffffff",
                            }
                        });
                        this.markers.push(marker);

                    }
                })
                this.mapState = true

                var scrollToMap = document.getElementById("mapWrapper");

                scrollToMap.scrollIntoView({
                    behavior: "smooth",
                    block: "start",
                    inline: "nearest"
                });
            },
            geolocate: function() {

                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition(position => {
                        this.center = {
                            lat: position.coords.latitude,
                            lng: position.coords.longitude
                        };
                        this.currentMarker = {
                            position: {
                                lat: position.coords.latitude,
                                lng: position.coords.longitude
                            }
                        };
                        this.$refs.mapRef.$mapPromise.then((map) => {
                            var marker = new google.maps.Marker({
                                position: this.currentMarker.position,
                                icon: {
                                    url: require('../assets/circle.svg'),
                                    scaledSize: new google.maps.Size(60, 60),
                                    anchor: new google.maps.Point(30, 30)
                                },
                                map: map,
                            });
                        })
                    }, checkError.bind(this));
                } else {
                    this.errorState = true
                    this.errorMessage = "Geolocation is not supported by this browser."
                    document.body.style.overflowY = "hidden"
                }

                function checkError(error) {
                    switch (error.code) {
                        case error.PERMISSION_DENIED:
                            this.errorState = true
                            this.errorMessage = "User denied the request for Geolocation. Please turn on Geolocation for this service."
                            document.body.style.overflowY = "hidden"
                            break;
                        case error.POSITION_UNAVAILABLE:
                            this.errorState = true
                            this.errorMessage = "Location information is unavailable."
                            document.body.style.overflowY = "hidden"
                            break;
                        case error.TIMEOUT:
                            this.errorState = true
                            this.errorMessage = "The request to get user location timed out."
                            document.body.style.overflowY = "hidden"
                            break;
                        case error.UNKNOWN_ERROR:
                            this.errorState = true
                            this.errorMessage = "An unknown error occurred."
                            document.body.style.overflowY = "hidden"
                            break;
                    }
                }

            },
            reloadPage() {
                window.location.reload()
            },
            showFoodList() {
                if (this.foodChainListState === false) {
                    this.foodChainListState = true
                    document.body.style.overflowY = "hidden"
                } else {
                    this.foodChainListState = false
                    document.body.style.overflowY = "auto"
                }
            },

            backToMenu() {
                this.markers.length = 0
                this.places.length = 0
                this.mapState = false
                this.optionsState = true
            }
        },
    }

</script>

<style scoped>
    /* animations */

    .foodMoreListFade-enter-active,
    .foodMoreListFade-leave-active {
        transition: opacity .3s;
    }

    .foodMoreListFade-enter,
    .foodMoreListFade-leave-to {
        opacity: 0;
    }

    .placeListFade-enter-active,
    .placeListFade-leave-active {
        transition: opacity .3s;
    }

    .placeListFade-enter,
    .placeListFade-leave-to {
        opacity: 0;
    }

    .foodOptionsFade-enter-active,
    .foodOptionsFade-leave-active {
        transition: opacity .3s;
    }

    .foodOptionsFade-enter,
    .foodOptionsFade-leave-to {
        opacity: 0;
    }



    /* animations */

    #mapWrapper {
        padding-top: 3vw;
    }

    #mapTheMap {
        width: 75vw;
        height: 40vw;
        margin: auto;
    }

    #mapErrorBlock {
        width: 100vw;
        height: 100vh;
        top: 0;
        position: fixed;
        z-index: 999;
        background-color: #ffffff;
    }

    #mapErrorBlockContent {
        width: 40vw;
        height: auto;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        text-align: center;
    }

    #mapErrorBlockContentImage {
        width: 37.5%;
        margin: auto;
    }

    #mapErrorBlockContentTitle {
        margin: 0;
        padding: 0;
        font-family: 'Aleo', serif;
        font-weight: 700;
        font-size: 4vw;
        letter-spacing: 0.15vw;
        margin-top: 1vw;
        margin-bottom: 1vw;
    }

    #mapErrorBlockContentMessage {
        margin: 0;
        padding: 0;
        font-family: 'Open Sans', sans-serif;
        font-weight: 400;
        font-size: 1.25vw;
        letter-spacing: 0.025vw;
    }

    #mapErrorBlockContentButton {
        margin: 0;
        padding: 0;
        font-family: 'Aleo', serif;
        letter-spacing: 0.075vw;
        background-color: #d2232a;
        color: #ffffff;
        font-size: 1.25vw;
        margin-top: 2.5vw;
        border: none;
        padding-top: 0.75vw;
        padding-bottom: 0.75vw;
        padding-left: 1.25vw;
        padding-right: 1.25vw;
        cursor: pointer;
        -webkit-box-shadow: 0.6vw 0.6vw #258fa1;
        -moz-box-shadow: 0.6vw 0.6vw #258fa1;
        box-shadow: 0.6vw 0.6vw #258fa1;
        -webkit-transition: all .2s linear;
        -moz-transition: all .2s linear;
        -o-transition: all .2s linear;
        transition: all .2s linear;
    }

    #mapErrorBlockContentButton:hover {
        background-color: #81b2ac;
        -webkit-box-shadow: 0.4vw 0.4vw #258fa1;
        -moz-box-shadow: 0.4vw 0.4vw #258fa1;
        box-shadow: 0.4vw 0.4vw #258fa1;
    }

    #mapFoodOptionsWrapper {
        width: 75vw;
        margin: auto;
        margin-top: 3vw;
        display: grid;
        grid-template-columns: auto auto auto auto;
    }

    #mapFoodMoreButton {
        font-family: 'Aleo', serif;
        text-transform: uppercase;
        font-size: 1.5vw;
        letter-spacing: 0.225vw;
        color: #ffffff;
        width: 30vw;
        margin: auto;
        margin-top: 3.25vw;
        margin-bottom: 6vw;
        cursor: pointer;
        text-align: center;
        padding-top: 1.5vw;
        padding-bottom: 1.5vw;
        background-color: #d2232a;
        -webkit-box-shadow: 1vw 1vw #258fa1;
        -moz-box-shadow: 1vw 1vw #258fa1;
        box-shadow: 1vw 1vw #258fa1;
        -webkit-transition: all .2s linear;
        -moz-transition: all .2s linear;
        -o-transition: all .2s linear;
        transition: all .2s linear;
    }

    #mapFoodMoreButton:hover {
        background-color: #81b2ac;
        -webkit-box-shadow: 0.6vw 0.6vw #258fa1;
        -moz-box-shadow: 0.6vw 0.6vw #258fa1;
        box-shadow: 0.6vw 0.6vw #258fa1;
    }

    #mapFoodListButton {
        font-family: 'Aleo', serif;
        text-transform: uppercase;
        font-size: 1.5vw;
        letter-spacing: 0.225vw;
        color: #ffffff;
        width: 30vw;
        margin: auto;
        margin-top: 5.25vw;
        cursor: pointer;
        text-align: center;
        padding-top: 1.5vw;
        padding-bottom: 1.5vw;
        background-color: #d2232a;
        -webkit-box-shadow: 1vw 1vw #258fa1;
        -moz-box-shadow: 1vw 1vw #258fa1;
        box-shadow: 1vw 1vw #258fa1;
        -webkit-transition: all .2s linear;
        -moz-transition: all .2s linear;
        -o-transition: all .2s linear;
        transition: all .2s linear;
    }

    #mapFoodListButton:hover {
        background-color: #81b2ac;
        -webkit-box-shadow: 0.6vw 0.6vw #258fa1;
        -moz-box-shadow: 0.6vw 0.6vw #258fa1;
        box-shadow: 0.6vw 0.6vw #258fa1;
    }

    #mapFoodMoreListWrapper {
        position: fixed;
        z-index: 900;
        background-color: rgba(0, 0, 0, 0.7);
        top: 0;
        width: 100vw;
        height: 100vh;
    }

    #mapFoodMoreList {
        width: 50vw;
        height: auto;
        max-height: 80vh;
        overflow-y: auto;
        background-color: #ffffff;
        position: relative;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        -webkit-border-radius: 5px;
        -moz-border-radius: 5px;
        border-radius: 5px;
    }

    #mapFoodMoreListCloseWrapper {
        position: sticky;
        width: 100%;
        top: 2vw;
        z-index: 901
    }

    #mapFoodMoreListCloseWrapper img {
        position: absolute;
        width: 1.5vw;
        height: 1.5vw;
        cursor: pointer;
        right: 2vw;
    }

    #mapFoodMoreListOptionsWrapper {
        width: 90%;
        margin: auto;
        display: grid;
        grid-template-columns: auto auto auto;
    }

    #mapPlaceName {
        width: 80vw;
        margin: auto;
        text-align: center;
        font-family: 'Open Sans', sans-serif;
        font-weight: 800;
        font-size: 3.25vw;
        letter-spacing: 0.075vw;
        margin-top: 6vw;
        margin-bottom: 6vw;
    }

    #mapPlaceList {
        width: 75vw;
        margin: auto;
        padding: 0;
        margin-bottom: 5vw;
        list-style: none;
    }

    #mapPlaceList li {
        margin-bottom: 5vw;
        display: flex;
        align-items: center;
    }

    .mapPlaceListRating {
        display: flex;
        align-items: center;
        margin-bottom: 0.25vw;
    }

    .mapPlaceListRatingStarFull {
        width: 1.75vw;
        height: 1.75vw;
        background-image: url("../assets/starFull.svg");
        background-size: 100% 100%;
    }

    .mapPlaceListRatingStarHalf {
        width: 1.75vw;
        height: 1.75vw;
        background-image: url("../assets/starHalf.svg");
        background-size: 100% 100%;
    }

    .mapPlaceListRatingStarHollow {
        width: 1.75vw;
        height: 1.75vw;
        background-image: url("../assets/starHollow.svg");
        background-size: 100% 100%;
    }

    .mapFoodOptions {
        cursor: pointer;
        height: 12.5vw;
        position: relative;
        -webkit-transition: all .15s linear;
        -moz-transition: all .15s linear;
        -o-transition: all .15s linear;
        transition: all .15s linear;
    }

    .mapFoodOptions:hover {
        background-color: #f2f2f2;
    }

    .mapFoodOptions img {
        width: 10vw;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    .mapFoodMoreListOptions {
        cursor: pointer;
        height: 15vw;
        position: relative;
        -webkit-transition: all .15s linear;
        -moz-transition: all .15s linear;
        -o-transition: all .15s linear;
        transition: all .15s linear;
    }

    .mapFoodMoreListOptions:hover {
        background-color: #f2f2f2;
    }

    .mapFoodMoreListOptions img {
        width: 9vw;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    .mapPlaceListAddress {
        margin: 0;
        padding: 0;
        font-family: 'Aleo', serif;
        font-weight: 700;
        font-size: 2.25vw;
        letter-spacing: 0.15vw;
        margin-bottom: 1.75vw;
    }

    .mapPlaceListRatingNumber,
    .mapPlaceListRatingTimes {
        margin: 0;
        padding: 0;
        font-family: 'Open Sans', sans-serif;
        font-size: 1.75vw;
        letter-spacing: 0.05vw;
    }

    .mapPlaceListRatingNumber {
        font-weight: 700;
        margin-right: 1vw;
    }

    .mapPlaceListRatingTimes {
        font-weight: 400;
        margin-left: 1vw;
    }

    .mapPlaceListDistance {
        margin: 0;
        padding: 0;
        font-family: 'Open Sans', sans-serif;
        font-size: 1.75vw;
        letter-spacing: 0.05vw;
        font-weight: 400;
        margin-bottom: 0.25vw;
    }

    .mapPlaceListIsOpen {
        margin: 0;
        padding: 0;
        font-family: 'Open Sans', sans-serif;
        font-size: 1.75vw;
        letter-spacing: 0.05vw;
        font-weight: 700;
    }

    .mapPlaceListIsOpenOpen {
        color: #d2232a;
    }

    .mapPlaceListIsOpenClosed {
        color: #258fa1;
    }

    .mapPlaceListOne {
        position: relative;
        width: 12.5%;
    }

    .mapPlaceListMarker {
        margin: 0;
        padding: 0;
        position: absolute;
        width: 50%;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    .mapPlaceListNumber {
        margin: 0;
        padding: 0;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-family: 'Open Sans', sans-serif;
        color: #ffffff;
        font-weight: 400;
        font-size: 1.85vw;
    }

    .mapPlaceListTwo {
        width: 75%;
    }

    .mapPlaceListThree {
        position: relative;
        width: 12.5%;
    }

    .mapPlaceListLink {
        text-decoration: none;
    }

    .mapPlaceListDirection {
        width: 40%;
        display: block;
        margin: auto;
        margin-bottom: 0.5vw;
    }

    .mapPlaceListDirectionWord {
        margin: 0;
        padding: 0;
        font-family: 'Open Sans', sans-serif;
        font-size: 1.35vw;
        letter-spacing: 0.05vw;
        font-weight: 700;
        color: #000000;
        text-align: center;
    }


    @media only screen and (max-width: 850px) {
        #mapWrapper {
            padding-top: 5vw;
        }

        #mapTheMap {
            width: 80vw;
            height: 45vw;
        }

        #mapErrorBlockContent {
            width: 70vw;
        }

        #mapErrorBlockContentImage {
            width: 40%;
        }

        #mapErrorBlockContentTitle {
            font-size: 7vw;
            margin-top: 1.75vw;
            margin-bottom: 1.75vw;
        }

        #mapErrorBlockContentMessage {
            font-size: 2.25vw;
        }

        #mapErrorBlockContentButton {
            font-size: 2.25vw;
            margin-top: 5vw;
            padding-top: 1vw;
            padding-bottom: 1vw;
            padding-left: 2vw;
            padding-right: 2vw;
            -webkit-box-shadow: 1vw 1vw #258fa1;
            -moz-box-shadow: 1vw 1vw #258fa1;
            box-shadow: 1vw 1vw #258fa1;
        }

        #mapErrorBlockContentButton:hover {
            -webkit-box-shadow: 0.6vw 0.6vw #258fa1;
            -moz-box-shadow: 0.6vw 0.6vw #258fa1;
            box-shadow: 0.6vw 0.6vw #258fa1;
        }

        #mapFoodOptionsWrapper {
            width: 80vw;
            margin-top: 4vw;
        }

        #mapFoodMoreButton {
            font-size: 2vw;
            letter-spacing: 0.25vw;
            width: 40vw;
            margin-top: 4vw;
            margin-bottom: 10vw;
            padding-top: 2vw;
            padding-bottom: 2vw;
            -webkit-box-shadow: 1.5vw 1.5vw #258fa1;
            -moz-box-shadow: 1.5vw 1.5vw #258fa1;
            box-shadow: 1.5vw 1.5vw #258fa1;
        }

        #mapFoodMoreButton:hover {
            -webkit-box-shadow: 1vw 1vw #258fa1;
            -moz-box-shadow: 1vw 1vw #258fa1;
            box-shadow: 1vw 1vw #258fa1;
        }

        #mapFoodListButton {
            font-size: 2vw;
            letter-spacing: 0.25vw;
            width: 40vw;
            margin-top: 6vw;
            padding-top: 2vw;
            padding-bottom: 2vw;
            -webkit-box-shadow: 1.5vw 1.5vw #258fa1;
            -moz-box-shadow: 1.5vw 1.5vw #258fa1;
            box-shadow: 1.5vw 1.5vw #258fa1;
        }

        #mapFoodListButton:hover {
            -webkit-box-shadow: 1vw 1vw #258fa1;
            -moz-box-shadow: 1vw 1vw #258fa1;
            box-shadow: 1vw 1vw #258fa1;
        }

        #mapFoodMoreList {
            width: 70vw;
            max-height: 80vh;
        }

        #mapFoodMoreListCloseWrapper {
            top: 3.5vw;
        }

        #mapFoodMoreListCloseWrapper img {
            width: 15px;
            height: 15px;
            right: 3.5vw;
        }

        #mapPlaceName {
            font-size: 4vw;
            letter-spacing: 0.1vw;
            margin-top: 7vw;
            margin-bottom: 7vw;
        }

        #mapPlaceList {
            width: 80vw;
            margin-bottom: 8vw;
        }

        #mapPlaceList li {
            margin-bottom: 7vw;
        }

        .mapPlaceListRating {
            margin-bottom: 0.15vw;
        }

        .mapFoodOptions {
            height: 15vw;
        }

        .mapFoodOptions img {
            width: 11.5vw;
        }

        .mapFoodMoreListOptions {
            height: 20vw;
        }

        .mapFoodMoreListOptions img {
            width: 12vw;
        }

        .mapPlaceListAddress {
            font-size: 2.75vw;
            letter-spacing: 0.175vw;
            margin-bottom: 2.5vw;
        }

        .mapPlaceListRatingNumber,
        .mapPlaceListRatingTimes {
            font-size: 2.25vw;
            letter-spacing: 0.1vw;
        }

        .mapPlaceListRatingNumber {
            margin-right: 1.5vw;
        }

        .mapPlaceListRatingTimes {
            margin-left: 1.5vw;
        }

        .mapPlaceListDistance {
            font-size: 2.25vw;
            letter-spacing: 0.1vw;
            margin-bottom: 0.15vw;
        }

        .mapPlaceListIsOpen {
            font-size: 2.25vw;
            letter-spacing: 0.1vw;
        }

        .mapPlaceListRatingStarFull {
            width: 2.25vw;
            height: 2.25vw;
        }

        .mapPlaceListRatingStarHalf {
            width: 2.25vw;
            height: 2.25vw;
        }

        .mapPlaceListRatingStarHollow {
            width: 2.25vw;
            height: 2.25vw;
        }

        .mapPlaceListOne {
            width: 15%;
        }

        .mapPlaceListMarker {
            width: 52.5%;
        }

        .mapPlaceListNumber {
            font-size: 2.25vw;
        }

        .mapPlaceListTwo {
            width: 70%;
        }

        .mapPlaceListThree {
            width: 15%;
        }

        .mapPlaceListDirection {
            width: 40%;
            margin-bottom: 0.5vw;
        }

        .mapPlaceListDirectionWord {
            font-size: 1.75vw;
            letter-spacing: 0.05vw;
        }

    }

    @media only screen and (max-width: 650px) {
        #mapWrapper {
            padding-top: 6.75vw;
        }
        
        #mapTheMap {
            width: 80vw;
            height: 60vw;
        }

        #mapErrorBlockContent {
            width: 80vw;
        }

        #mapErrorBlockContentImage {
            width: 40%;
        }

        #mapErrorBlockContentTitle {
            font-size: 9vw;
            margin-top: 2vw;
            margin-bottom: 2vw;
        }

        #mapErrorBlockContentMessage {
            font-size: 3.25vw;
        }

        #mapErrorBlockContentButton {
            font-size: 3.25vw;
            margin-top: 5vw;
            padding-top: 1.5vw;
            padding-bottom: 1.5vw;
            padding-left: 2.75vw;
            padding-right: 2.75vw;
        }

        #mapFoodOptionsWrapper {
            margin-top: 5vw;
        }

        #mapFoodMoreButton {
            font-size: 2.75vw;
            letter-spacing: 0.25vw;
            width: 70vw;
            margin-top: 5vw;
            margin-bottom: 12vw;
            padding-top: 2.5vw;
            padding-bottom: 2.5vw;
        }

        #mapFoodListButton {
            font-size: 2.75vw;
            letter-spacing: 0.25vw;
            width: 70vw;
            margin-top: 8vw;
            padding-top: 2.5vw;
            padding-bottom: 2.5vw;
        }

        #mapFoodMoreList {
            width: 80vw;
            max-height: 85vh;
        }

        #mapPlaceName {
            font-size: 5.25vw;
            letter-spacing: 0.075vw;
            margin-top: 8.5vw;
            margin-bottom: 8.5vw;
        }

        #mapPlaceList {
            width: 80vw;
            margin-bottom: 10.5vw;
        }

        #mapPlaceList li {
            margin-bottom: 8vw;
        }

        .mapPlaceListRating {
            margin-bottom: 0.05vw;
        }

        .mapFoodMoreListOptions {
            height: 24vw;
        }

        .mapFoodMoreListOptions img {
            width: 15vw;
        }

        .mapPlaceListAddress {
            font-size: 4vw;
            letter-spacing: 0.275vw;
            margin-bottom: 2.75vw;
        }

        .mapPlaceListRatingNumber,
        .mapPlaceListRatingTimes {
            font-size: 3.3vw;
            letter-spacing: 0.15vw;
        }

        .mapPlaceListRatingNumber {
            margin-right: 2vw;
        }

        .mapPlaceListRatingTimes {
            margin-left: 2vw;
        }

        .mapPlaceListDistance {
            font-size: 3.3vw;
            letter-spacing: 0.15vw;
            margin-bottom: 0.05vw;
        }

        .mapPlaceListIsOpen {
            font-size: 3.3vw;
            letter-spacing: 0.15vw;
        }

        .mapPlaceListRatingStarFull {
            width: 3.3vw;
            height: 3.3vw;
        }

        .mapPlaceListRatingStarHalf {
            width: 3.3vw;
            height: 3.3vw;
        }

        .mapPlaceListRatingStarHollow {
            width: 3.3vw;
            height: 3.3vw;
        }

        .mapPlaceListOne {
            width: 15%;
        }

        .mapPlaceListMarker {
            width: 70%;
        }

        .mapPlaceListNumber {
            font-size: 3.25vw;
        }

        .mapPlaceListTwo {
            width: 70%;
        }

        .mapPlaceListThree {
            width: 15%;
        }

        .mapPlaceListDirection {
            width: 50%;
            margin-bottom: 0.5vw;
        }

        .mapPlaceListDirectionWord {
            font-size: 1.75vw;
            letter-spacing: 0.05vw;
        }
    }

</style>
