<template>
    <div id="layoutSidenav">
        <CommonSidebar />
        <div id="layoutSidenav_content">
            <CommonHeader />
            <main>
                <Loader v-if="loader" />
                <div class="container-fluid px-4">
                    <div class="card mb-4">
                        <div class="card-header icon-title">
                            <a href="#"><i class="fa fa-shield-alt"></i></a>
                            <h4>Category</h4>
                        </div>
                        <div class="card-body">
                            <ul class="sub-tab">
                                <li class=""><a href="#" class="active">Item Category</a></li>
                            </ul>
    
                            <div class="content-subtab">
                                <form class="g-3 mt-3" method="post" @submit.prevent="OnSubmit">
                                    <div class="row align-items-center">
                                        <div class="col-md-7 mb-4">
                                            <label for="category" class="form-label">Item Category</label>
                                            <input type="text" id="category" class="form-control" placeholder="Enter Category" v-model="category" />
                                        </div>
    
                                        <div class="col-lg-1 col-sm-2 mb-4">
                                            <label class="form-label">Index</label>
                                            <input type="text" class="form-control" placeholder="0" v-model="index" />
                                        </div>
    
                                        <div class="col-sm-3 mb-4">
                                        <label for="" class="form-label">Status</label>
                                        <select class="form-select" v-model="status">
                                        <option value="1">Enable</option>
                                        <option value="0">Disable</option>
                                        </select>
                                    </div>
                                    </div>
                                    <p v-if="errorList.length">
                                        <ul>
                                            <li style="color:red" v-for='err in errorList' :key='err'>
                                                {{ err }}
                                            </li>
                                        </ul>
                                    </p>
                                    <!-- close-row -->
                                    <div class="d-flex justify-content-center" id="sidebar" ref="sidebar">
                                        <button type="submit" class="btn btn-success btn-text" v-if="settingId">
                                            <i class="fa fa-save"></i>Update
                                        </button>
                                        <button type="submit" class="btn btn-success btn-text" v-if="!settingId">
                                            <i class="fa fa-plus"></i> Add New
                                        </button>
                                    </div>
                                </form>
                                <div class="table-title">
                                    <h3>List of Category</h3>
                                </div>
                                <table class="table table-striped data-table display nowrap" style="width: 100%">
                                    <thead>
                                        <tr>
                                            <th style="width:3%">No</th>
                                            <th>Category</th>
                                            <th>Index</th>
                                            <th>Status</th>
                                            <th style="width:3%">Action</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        <tr v-for="(setting, index) in settinglist" :key="index">
                                            <td>#{{index+1}}</td>
                                            <td>{{setting.section_value}}</td>
                                            <td>{{setting.section_order}}</td>
                                            <td>
                                                <p v-if="setting.status == 0" style="color:red">Disabled</p>
                                                <p v-if="setting.status == 1">Enabled</p>
                                            </td>
                                            <td class="td">
                                                <a class="view" @click="editsetting(setting)"><i class="fa fa-edit"></i></a>
                                          </td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </div>
            </main>
        </div>
    </div>
    </template>
    
    <script>
    import CommonHeader from '../../../components/CommonHeader.vue';
    import CommonSidebar from '../../../components/CommonSidebar.vue';
    export default {
        components: {
            CommonSidebar,
            CommonHeader
        },
        name: "category",
        data() {
            return {
                category: "",
                index: 0,
                errorList: [],
                userdetails: null,
                settinglist: [],
                settingId: 0,
                requesttype: "insert",
                loader: false,
                status: "1",
            };
        },
        beforeMount() {
            this.userdetails = JSON.parse(localStorage.getItem("userdetails"));
        },
        mounted() {
            this.loader = true;
            this.GetList();
           
        },
        methods: {
        async GetList(){
            const headers = {
                Authorization: "Bearer " + this.userdetails.access_token,
                Accept: "application/json",
                "Content-Type": "application/json",
            };
            const axios = require("axios").default;
            axios
                .get(`${this.$axios.defaults.baseURL}` + "general-setting/lists?section=" + "itemcategory", {headers})

                .then((resp) => {
                    this.settinglist = resp.data.list;
                    this.loader = false;
                   
                })
                .catch ((err) => {
                 this.loader = false;
                    this.$swal.fire({icon: 'error', title: 'Oops... Something Went Wrong!',text: 'the error is: ' + err, footer: ''});
    
                    this.loader = false
                    console.error(err);
                    this.$swal.fire({icon: 'error',title: 'Oops... Something Went Wrong!',text: 'the error is: ' + err,footer: ''});
                });
        },
            async OnSubmit() {
             
                this.errorList = [];
                try {
                    if (!this.category) {
                        this.errorList.push("Category is required");
                    }else {
                        this.loader = true;
                        const headers = {
                            Authorization: "Bearer " + this.userdetails.access_token,
                            Accept: "application/json",
                            "Content-Type": "application/json",
                        };
                        const response = await this.$axios.post(
                            "/general-setting/add", {
                                added_by: this.userdetails.user.id,
                                section: "itemcategory",
                                section_value: this.category,
                                section_order: this.index,
                                setting_id: this.settingId,
                                request_type: this.requesttype,
                                status: this.status,
                            }, {
                                headers
                            }
                        );
                        if (response.data.code == 200) {
                            this.loader = false;
                            if (this.settingId > 0) {
                                this.$swal.fire('Successfully Updated', '', 'success');
                            } else {
                                this.$swal.fire('Successfully Added', '', 'success');
                            }
                            this.GetSettingList();
                            this.index = 0;
                            this.category = "";
                            this.settingId = 0;
                            this.status= "1";
                            this.requesttype = "insert";
                        } else {
                            this.loader = false;
                            this.$swal.fire({
                                icon: 'error',
                                title: 'Oops... Something Went Wrong!',
                                text: 'the error is: ' + JSON.stringify(response.data.message),
                                footer: ''
                            });
                        }
                    }
                } catch (e) {
                    this.$swal.fire({
                        icon: 'error',
                        title: 'Oops... Something Went Wrong!',
                        text: 'the error is: ' + e,
                        footer: ''
                    });
                }
            },
            async GetSettingList() {
                const headers = {
                    Authorization: "Bearer " + this.userdetails.access_token,
                    Accept: "application/json",
                    "Content-Type": "application/json",
                };
                const response = await this.$axios.get(
                    "general-setting/lists?section=" + "itemcategory", {
                        headers
                    }
                );
                if (response.data.code == 200 || response.data.code == "200") {
                    this.settinglist = response.data.list;
                } else {
                    this.settinglist = [];
                }
            },
         
            async editsetting(data) {
                this.loader = true;
                const headers = {
                    Authorization: "Bearer " + this.userdetails.access_token,
                    Accept: "application/json",
                    "Content-Type": "application/json",
                };
                const response = await this.$axios.post(
                    "/general-setting/fetch", {
                        setting_id: data.id,
                    }, {
                        headers
                    }
                );
                if (response.data.code == 200) {
                    this.loader = false;
                    this.settingId = response.data.setting[0].id;
                    this.category = response.data.setting[0].section_value;
                    this.index = response.data.setting[0].section_order;
                    this.status=response.data.setting[0].status;
                    this.requesttype = "update";
                } else {
                    this.loader = false;
                    this.$swal.fire({
                        icon: 'error',
                        title: 'Oops... Something Went Wrong!',
                        text: 'the error is: ' + JSON.stringify(response.data.message),
                        footer: ''
                    });
                }
            },
        },
    };
    </script>
    