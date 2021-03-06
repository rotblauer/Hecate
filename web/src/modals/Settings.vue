<template>
    <div class='absolute top left bottom right z3' style="pointer-events: none;">
        <div class='flex-parent flex-parent--center-main flex-parent--center-cross h-full' style="pointer-events: auto;">
            <div class="flex-child px12 py12 w600 h80 bg-white round-ml shadow-darken10">
                <template v-if='error'>
                    <div class='grid w-full col'>
                        <div class='col--12'>
                            <h3 class='w-full py6 txt-m txt-bold align-center'>ERROR!</h3>
                        </div>

                        <div class='col--12 py12' v-text='error'></div>

                        <div class='col--12 py12'>
                            <div class='grid grid--gut12'>
                                <div class='col col--6'></div>
                                <div class='col col--6'>
                                    <button @click='error = false' class='btn round w-full'>Ok</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </template>
                <template v-else-if='mode === "addLayer"'>
                    <div class='grid w-full col'>

                        <template v-if='addLayerData.error'>
                            <div class='col--12 color-white px12 bg-red round align-center'>
                                <h3 class='w-full py6 txt-m txt-bold' v-text='addLayerData.error'></h3>
                            </div>
                        </template>

                        <div class='col--12'>
                            <template v-if='addLayerData.exists === false'>
                                <h3 class='w-full py6 txt-m txt-bold'>Add A New Base Layer</h3>
                            </template>
                            <template v-else>
                                <h3 class='w-full py6 txt-m txt-bold' v-text='"Modify the " + addLayerData.name + " layer"'></h3>
                            </template>
                        </div>

                        <div class='col--12 py12 px12'>
                            <div class='grid grid--gut12'>
                                <div class='col col--6'>
                                    <label>Layer Name</label>
                                    <input v-model='addLayerData.name' class='input' placeholder='Layer Name' v-bind:class="{ 'input--border-red': addLayerData.nameError }"/>
                                </div>
                                <div class='col col--6'>
                                    <label >Layer Type</label>
                                    <div class='select-container w-full'>
                                        <select v-model='addLayerData.type' class='select' v-bind:class="{ 'input--border-red': addLayerData.typeError }">
                                            <option>Vector</option>
                                            <option>Raster</option>
                                        </select>
                                        <div class='select-arrow'></div>
                                    </div>
                                </div>
                                <div class='col col--12 py12'>
                                    <label>Mapbox:// Style</label>
                                    <input v-model='addLayerData.url' class='input w-full' placeholder='mapbox://' v-bind:class="{ 'input--border-red': addLayerData.urlError }" />
                                </div>
                            </div>
                        </div>

                        <div class='col--12 py12'>
                            <div class='grid grid--gut12'>
                                <div class='col col--6'>
                                    <button @click='close' class='btn btn--red round w-full'>Cancel</button>
                                </div>
                                <div class='col col--6'>
                                    <template v-if='addLayerData.exists === false'>
                                        <button @click='addLayer' class='btn round w-full'>Create Layer</button>
                                    </template>
                                    <template v-else>
                                        <button @click='updateLayer(addLayerData.exists)' class='btn round w-full'>Update Layer</button>
                                    </template>
                                </div>
                            </div>
                        </div>
                    </div>
                </template>
                <template v-else-if='mode === "helpBase"'>
                    <div class='grid w-full col'>
                        <div class='col--12'>
                            <h3 class='w-full py6 txt-m txt-bold align-center'>Base Layers Help</h3>
                        </div>

                        <div class='col--12 py12'>
                            <p class='py3'>
                                Base Layers are the background map that the data in the Hecate instance is displayed on.
                            </p>
                            <p class='py3'>
                                Common options include Satellite Imagery, Street, Political Boundaries, etc.
                            </p>
                            <p class='py3'>
                                Hecate currently supports adding any mapbox:// prefixed map. See documentation on creating
                                a style url <a href="https://www.mapbox.com/help/define-style-url/">here</a>. Prebuilt
                                mapbox style URLs can be obtained <a href="https://www.mapbox.com/api-documentation/#styles">here</a>.
                            </p>
                        </div>

                        <div class='col--12 py12'>
                            <div class='grid grid--gut12'>
                                <div class='col col--6'></div>
                                <div class='col col--6'>
                                    <button @click='close' class='btn round w-full'>Got It!</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </template>
                <template v-else-if='mode === "delLayer"'>
                    <div class='grid w-full col'>
                        <div class='col--12'>
                            <h3 class='w-full py6 txt-m txt-bold align-center'>Are you sure you want to delete this layer?</h3>

                            <div class='w120 mx-auto relative color-gray-light my12'>
                                <div class='w-full h120 round border border--gray-light'>
                                    <template v-if='delLayerData.type === "Raster"'>
                                        <svg class='icon w-full h-full'><use href='#icon-satellite'/></svg>
                                    </template>
                                    <template v-else>
                                        <svg class='icon w-full h-full'><use href='#icon-paint'/></svg>
                                    </template>
                                </div>

                                <div class='w-full color-black align-center' v-text='delLayerData.name'></div>
                            </div>
                        </div>

                        <div class='col--12 py12'>
                            <div class='grid grid--gut12'>
                                <div class='col col--6'>
                                    <button @click='close' class='btn round w-full'>Cancel</button>
                                </div>
                                <div class='col col--6'>
                                    <button @click='deleteLayer' class='btn btn--red round w-full'>Delete</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </template>
                <template v-else>
                    <div class='fl h360' style='width: 44px; padding-right: 8px;'>
                        <button @click='submode = "server"' class='fl btn btn--stroke round w36 px12 my6'>
                            <svg class='icon'><use href='#icon-sprocket'/></svg>
                        </button>
    
                        <button @click='submode = "users"' class='fl btn btn--stroke round w36 px12 my6'>
                            <svg class='icon'><use href='#icon-user'/></svg>
                        </button>
                    </div>

                    <div class='fl pl6' style='width: calc(600px - 68px);'>
                        <template v-if='submode === "server"'>
                            <div class='col col--12 txt-m txt-bold'>
                                Server Settings
                                <button @click='close' class='fr btn round bg-white color-black bg-darken25-on-hover'><svg class='icon'><use href='#icon-close'/></svg></button>
                            </div>

                            <div class='py6 col col--12 border--gray-light border-b'>
                                <span class='txt-m'>Base Layers</span>
                                <span @click='helpBaseClick' class='fr cursor-pointer'><svg class='icon'><use href='#icon-info'/></svg></span>
                            </div>

                            <div class='col col--12 hmin120 hmax180 clearfix'>
                                <template v-for='(layer, layer_idx) of layers'>
                                    <div class='w120 fl relative color-gray-light my12 mx12 cursor-pointer'>
                                        <div @click='delLayerClick(layer_idx)' class='absolute bg-red-light bg-red-on-hover round color-white w18 h18' style='top: -9px; right: -9px;'>
                                            <svg class='icon w-full pt3'><use href='#icon-close'/></svg>
                                        </div>

                                        <div @click='editLayerClick(layer_idx)'  class='w-full h120 color-gray-on-hover round border border--gray-light border--gray-on-hover'>
                                            <template v-if='layer.type === "Raster"'>
                                                <svg class='icon w-full h-full'><use href='#icon-satellite'/></svg>
                                            </template>
                                            <template v-else>
                                                <svg class='icon w-full h-full'><use href='#icon-paint'/></svg>
                                            </template>
                                        </div>

                                        <div class='w-full color-black align-center' v-text='layer.name'></div>
                                    <div>
                                </template>

                                <!-- Add a new Base Layer -->
                                <div @click='newLayerClick' class='h120 w120 fl round border border--gray-light border--gray-on-hover color-gray-light color-gray-on-hover my12 mx12 cursor-pointer'>
                                    <svg class='icon w-full h-full'><use href='#icon-plus'/></svg>
                                </div>
                            </div>

                            <div class='py6 col col--12 border--gray-light border-b'>
                                <span class='txt-m'>Miscellaneous</span>
                            </div>

                            <div class='col col--12 my12'>
                                Wipe the cached vector tiles, forcing regen of all tiles
                                <button :disabled='tilecache' @click='clearCache' class='fr btn btn--red round'>
                                    <template v-if='tilecache'>Cache Cleared</template>
                                    <template v-else>Clear Cache</template>
                                </button>
                            </div>
                        </template>
                        <template v-if='submode === "users"'>
                            <div class='col col--12 txt-m txt-bold'>
                                User Settings
                                <button @click='close' class='fr btn round bg-white color-black bg-darken25-on-hover'><svg class='icon'><use href='#icon-close'/></svg></button>
                            </div>

                            <div class='py6 col col--12 border--gray-light border-b mb12'>
                                <span class='txt-m'>Users</span>
                            </div>

                            <div class='relative mb12'>
                                <div class='absolute flex-parent flex-parent--center-cross flex-parent--center-main w36 h36'><svg class='icon'><use href='#icon-search'></use></svg></div>
                                <input v-model='userFilter' class='input pl36' placeholder='Filter Users'>
                            </div>

                            <div class='col col--12 h240 scroll-auto'>
                                <template v-for='(user, user_idx) of users'>
                                    <div class='col col--12'>
                                       <div class='grid col h30 bg-gray-faint-on-hover cursor-pointer round'>
                                            <div class='col--2'>
                                                <span class='ml6 bg-blue-faint color-blue inline-block px6 py3 my3 my3 txt-xs txt-bold round' v-text="user.id"></span>
                                            </div>
                                            <div class='col--8' v-text='user.username'></div>
                                            <div class='col--2' v-text='user.access'></div>
                                        </div>
                                    </div>
                                </template>
                            </div>
                        </template>
                    </div>
                </template>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'settings',
    data: function() {
        return {
            mode: 'settings',
            submode: 'server',
            tilecache: false, //Store if the cache has been cleared or not
            layers: [],
            users: [],
            userFilter: '',
            error: '',
            delLayerData: {
                idx: false,
                name: '',
                type: ''
            },
            addLayerData: {
                exists: false,
                error: '',
                name: '',
                nameError: false,
                type: '',
                typeError: false,
                url: '',
                urlError: false
            }
        }
    },
    mounted: function() {
        this.getLayers();
        this.getUsers();
    },
    watch: {
        userFilter: function() {
            this.getUsers();
        },
        error: function() {
            this.getLayers();
        }
    },
    methods: {
        close: function() {
            this.clearLayer();

            if (this.mode !== 'settings') {
                this.mode = 'settings';
            } else {
                this.$emit('close');
            }
        },
        clearCache: function() {
            fetch(`${window.location.protocol}//${window.location.host}/api/tiles`, {
                method: 'DELETE',
                credentials: 'same-origin'
            }).then((response) => {
                if (response.status !== 200) {
                    this.error = response.status + ':' + response.statusText;
                    return;
                }
                this.tilecache = true;
            }).catch((err) => {
                this.error = err.message;
            });
        },
        getLayers: function() {
            fetch(`${window.location.protocol}//${window.location.host}/api/meta/layers`, {
                method: 'GET',
                credentials: 'same-origin'
            }).then((response) => {
                if (response.status !== 200) {
                    this.error = response.status + ':' + response.statusText;
                }

                return response.json();
            }).then((layers) => {
                if (!layers || !layers.length) layers = [];

                this.layers = layers;
            }).catch((err) => {
                this.error = err.message;
            });
        },
        getUsers: function() {
            fetch(`${window.location.protocol}//${window.location.host}/api/users?filter=${this.userFilter}`, {
                method: 'GET',
                credentials: 'same-origin'
            }).then((response) => {
                if (response.status !== 200) {
                    this.error = response.status + ':' + response.statusText;
                }

                return response.json();
            }).then((users) => {
                if (!users || !users.length) users = [];

                this.users = users;
            }).catch((err) => {
                this.error = err.message;
            });
        },
        putLayers: function() {
            fetch(`${window.location.protocol}//${window.location.host}/api/meta/layers`, {
                method: 'POST',
                credentials: 'same-origin',
                headers: new Headers({ 'Content-Type': 'application/json' }),
                body: JSON.stringify(this.layers)
            }).then((response) => {
                if (response.status !== 200) {
                    this.error = response.status + ':' + response.statusText;
                }

                return response.json();
            }).then((layers) => {
                if (!layers)
                this.layers = layers;
            }).catch((err) => {
                this.error = err.message;
            });
        },
        deleteLayer: function() {
            if (isNaN(this.delLayerData.idx)) return;

            this.layers.splice(this.delLayerData.idx, 1);
            this.putLayers();

            this.close();
        },
        validateLayer: function() {
            let error = false;

            if (this.addLayerData.name.length === 0) {
                this.addLayerData.nameError = true;
                error = true;
            } else {
                this.addLayerData.nameError = false;
            }

            if (['Vector', 'Raster'].indexOf(this.addLayerData.type) === -1) {
                this.addLayerData.typeError = true;
                error = true;
            } else {
                this.addLayerData.typeError = false;
            }

            if (!this.addLayerData.url.match(/^mapbox:\/\//)) {
                this.addLayerData.urlError = true;
                error = true;
            } else {
                this.addLayerData.urlError = false;
            }

            if (this.addLayerData.urlError || this.addLayerData.nameError || this.addLayerData.typeError) {
                this.addLayerData.error = 'All Fields Are Required!';
                return;
            } else {
                this.addLayerData.error = false;
            }

            return error;
        },
        updateLayer: function(layer_idx) {
            if (isNaN(layer_idx)) return;
            if (this.validateLayer()) return;

            this.layers[layer_idx].name = this.addLayerData.name;
            this.layers[layer_idx].type = this.addLayerData.type;
            this.layers[layer_idx].url = this.addLayerData.url;

            this.putLayers();
            this.close();
        },
        clearLayer: function() {
            this.addLayerData.exists = false;
            this.addLayerData.error = false;
            this.addLayerData.name = '';
            this.addLayerData.type = '';
            this.addLayerData.url = '';
        },
        addLayer: function() {
            if (this.validateLayer()) return;

            this.layers.push({
                name: this.addLayerData.name,
                type: this.addLayerData.type,
                url: this.addLayerData.url
            });

            this.putLayers();
            this.close();
        },
        editLayerClick: function(layer_idx) {
            if (isNaN(layer_idx)) return;

            this.mode = 'addLayer';
            this.addLayerData.exists = layer_idx;
            this.addLayerData.name = this.layers[layer_idx].name;
            this.addLayerData.url = this.layers[layer_idx].url;
            this.addLayerData.type = this.layers[layer_idx].type;
        },
        newLayerClick: function() {
            this.mode = 'addLayer';
        },
        helpBaseClick: function() {
            this.mode = 'helpBase';
        },
        delLayerClick: function(layer_idx) {
            if (isNaN(Number(layer_idx))) return;

            this.mode = 'delLayer';

            this.delLayerData.idx = Number(layer_idx);
            this.delLayerData.name = this.layers[layer_idx].name;
            this.delLayerData.type = this.layers[layer_idx].type;
        }
    },
    render: h => h(App),
    props: ['auth']
}
</script>
