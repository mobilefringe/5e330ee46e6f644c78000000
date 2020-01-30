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
                            <form class="newsletter_form form-horizontal" action="https://www.createsend.com/t/subscribeerror?description=" method="post"
                                data-id="92D4C54F0FEC16E5ADC2B1904DE9ED1AD10EB25B1E23DC984139C444822ACBD6F30D2EE9E818178B0BA0C25B195ACAFAB30D0D4B61CF52CDF997A100C09CDCC7">
                                <div class="row">
                                    <div class="col-sm-6" >
                                        <label for="fieldyulldlu" class="visuallyhidden">First Name</label>
                                        <input v-model="form_data.first_name" required class="margin_20 form-control" id="fieldyulldlu" name="cm-f-yulldlu" type="text" placeholder="First Name">
                                    </div>
                                    <div class="col-sm-6" >
                                        <label for="fieldyulldrl" class="visuallyhidden">Last Name</label>
                                        <input v-model="form_data.last_name" required class="margin_20 form-control" id="fieldyulldrl" name="cm-f-yulldrl" type="text" placeholder="Last Name">
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-sm-12">
                                        <label for="newsletter_email" class="visuallyhidden">Email</label>
                                        <input v-model="form_data.email" required class="margin_20 form-control" name="cm-ulhikt-ulhikt" type="email" placeholder="Email" id="newsletter_email">
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-sm-12">
                                        <div style="margin-left: 20px">
                                            <label class="checkbox">
                                                <input name="agree_newsletter" required  type="checkbox">
                                                I agree to receive communications from {{ property.name }}.
                                            </label>
                                        </div>
            					    </div>
            					</div>
        					    <div class="margin_40 clearfix"></div>
        					    <div class="row">
                                    <div class="col-xs-12">
                                        <button class="animated_btn" type="submit" :disabled="formSuccess">Subscribe</button>
                                    </div>
                                </div>
                            </form> 
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
    define(["Vue", "vuex", "jquery", "vee-validate", "json!site.json", "campaign-monitor"], function(Vue, Vuex, $, VeeValidate, site, campaign-monitor) {
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