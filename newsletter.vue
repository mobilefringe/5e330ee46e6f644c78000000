<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="inside_page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
                    <div class="main_container position_relative">
                        <h1>Newsletter</h1>
                    </div>
                </div>
                <div class="main_container">
                    <div class="row">
                        <div class="col-md-12">
                            <breadcrumb></breadcrumb>
                            <div v-if="pageContent" v-html="pageContent.body"></div>
                            <form class="js-cm-form" id="subForm" action="https://www.createsend.com/t/subscribeerror?description=" method="post" data-id="92D4C54F0FEC16E5ADC2B1904DE9ED1AD10EB25B1E23DC984139C444822ACBD6F30D2EE9E818178B0BA0C25B195ACAFAB30D0D4B61CF52CDF997A100C09CDCC7">
	<label>First Name </label>
	<input aria-label="First Name" id="fieldyulldlu" maxlength="200" name="cm-f-yulldlu">
	<label>Last Name </label>
	<input aria-label="Last Name" id="fieldyulldrl" maxlength="200" name="cm-f-yulldrl">
	<label>Email </label>
	<input autocomplete="Email" aria-label="Email" class="js-cm-email-input" id="fieldEmail" maxlength="200" name="cm-ulhikt-ulhikt" required="" type="email">
	<button type="submit">Subscribe</button>
</form>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
    define(["Vue", "vuex", "jquery", "vee-validate", "json!site.json"], function(Vue, Vuex, $, VeeValidate, site) {
        Vue.use(VeeValidate);
        return Vue.component("newsletter-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: true,
                    pageBanner: null,
                    pageContent: null,
                    siteInfo: site,
                    form_data : {},
                    formSuccess : false,
                    formError: false
                }
            },
            mounted () {
                this.form_data.first_name = this.$route.query.name;
                $("#fieldzkydut").val(this.form_data.first_name);
                this.form_data.email = this.$route.query.email;
                $("#newsletter_email").val(this.form_data.email);
            },
            watch : {
                $route () {
                    this.form_data.first_name = this.$route.query.name;
                    $("#fieldzkydut").val(this.form_data.first_name);
                    this.form_data.email = this.$route.query.email;
                    $("#newsletter_email").val(this.form_data.email);
                }
            },
            created() {
                this.loadData().then(response => {
                    var temp_repo = this.findRepoByName('Newsletter Banner').images;
                    if(temp_repo != null) {
                        this.pageBanner = temp_repo[0];
                    } else {
                        this.pageBanner = {
                            "image_url": this.siteInfo.insideBanner
                        }
                    }
                    if(response) {
                        this.pageContent = response[0].data;
                    }
                    
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'findRepoByName'
                ])
            },
            methods: {
                loadData: async function () {
                    this.property.mm_host = this.property.mm_host.replace("http:", "");
                    try {
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "/pages/" + this.siteInfo.subdomain + "-newsletter.json"}), this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>