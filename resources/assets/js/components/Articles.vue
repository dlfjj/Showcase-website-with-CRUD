        <!--suppress ALL -->
        <template>
            <div>
                <div class = "title p-5">
                    <!--Page title-->
                <h2>
                    Articles
                    <small class="text-muted">everything you need to know about</small>

                </h2>
                </div>


                <nav aria-label="Page navigation example">
                    <ul class="pagination">
                        <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
                        <li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{ pagination.current_page}} of {{ pagination.last_page}}</a></li>
                        <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a></li>
                    </ul>
                </nav>


                <div class = "card card-body mb-3" v-for="article in articles" v-bind:key="article.id">
                    <h3>{{ article.title }}</h3>
                    <p>{{ article.body }}</p>
                    <hr>

                    <!--edit and delete button-->
                    <h4>
                        <b-button-group size="sm">
                            <button @click="editArticle(article)" class="btn btn-warning col-lg-1">Edit</button>
                            <button @click="deleteArticle(article.id)" class="btn btn-danger col-lg-1">Delete</button>
                        </b-button-group>
                    </h4>
                </div>




                <!--<form @submit.prevent="addArticle" class="mb-4">-->
                    <!--<div class="form-group purple-border">-->
                    <!--<div class="row">-->
                        <!--<div class="form-group">-->

                            <!--<div class="form-group col-lg-12">-->
                                <!--&lt;!&ndash;title input&ndash;&gt;-->
                                <!--<input type="text" class="form-control" placeholder="Title" v-model="article.title">-->
                            <!--</div>-->

                            <!--&lt;!&ndash;text body input&ndash;&gt;-->
                            <!--<div class ="input-group">-->
                                <!--<div class="input-group-prepend">-->
                                    <!--<span class="input-group-text">Body</span>-->
                                <!--</div>-->
                                <!--<textarea class="form-control" aria-label="Body" v-model="article.body" rows="3"></textarea>-->

                            <!--</div>-->
                        <!--</div>-->
                    <!--</div>-->
                        <!--&lt;!&ndash;Save button&ndash;&gt;-->
                        <!--<button type="submit" class="btn btn-primary mb-2 col-lg-2">Save</button>-->
                    <!--</div>-->
                    <!---->
                <!--</form>-->

                    <div class="container1">
                        <span class="border purple-border">
                        <div class="contact">
                            <div class="col-md-6 col-md-offset-3">
                                <div class="form-area">
                                    <div class="text-left contact-h">Create New Posts</div>
                                    <form @submit.prevent="addArticle" class="mb-4">
                                        <div class="form-group group">
                                            <input type="text" class="form-control" id="title" name="title" v-model="article.title" required >
                                            <span class="highlight"></span>
                                            <span class="bar"></span>
                                            <label>Title</label>
                                        </div>
                                        <div class="form-group group">
                                            <div class="text-group">
                                                <textarea required="required" class="form-control" aria-label="Body" v-model="article.body" rows="6"></textarea>
                                                <label for="textarea" class="input-label">What is in your mind?</label><i class="bar"></i>
                                            </div>
                                            <button type="submit" id="submit" name="submit" class="btn btn-primary col-md-12">Save</button>
                                        </div>
                                    </form>
                                </div>
                            </div>
                        </div>
                        </span>
                    </div>

            </div>
        </template>

        <script>
            export default {
                data() {
                    return {
                        text: 'Welcome',
                        articles: [],
                        article: {
                            id: '',
                            title: '',
                            body: ''
                        },
                        article_id: '',
                        pagination: {},
                        edit: false
                    };
                },

                created() {
                    this.fetchArticles();
                },
                methods: {
                    fetchArticles(page_url) {
                        let vm = this;
                        page_url = page_url || '/api/articles';
                        fetch(page_url)
                            .then(res => res.json())
                            .then(res => {
                                this.articles = res.data;
                                vm.makePagination(res.meta, res.links);
                            })
                            .catch(err => console.log(err));
                    },
                    makePagination(meta, links) {
                        let pagination = {
                            current_page: meta.current_page,
                            last_page: meta.last_page,
                            next_page_url: links.next,
                            prev_page_url: links.prev
                        };
                        this.pagination = pagination;
                    },
                    deleteArticle(id) {
                        if (confirm('Are You Sure?')) {
                            fetch(`api/article/${id}`, {
                                method: 'delete'
                            })
                                .then(res => res.json())
                                .then(data => {
                                    alert('Article Removed');
                                    this.fetchArticles();
                                })
                                .catch(err => console.log(err));
                        }
                    },
                    addArticle() {
                        if (this.edit === false) {
                            // Add
                            fetch('api/article', {
                                method: 'post',
                                body: JSON.stringify(this.article),
                                headers: {
                                    'content-type': 'application/json'
                                }
                            })
                                .then(res => res.json())
                                .then(data => {
                                    this.article.title = '';
                                    this.article.body = '';
                                    alert('Article Added');
                                    this.fetchArticles();
                                })
                                .catch(err => console.log(err));
                        } else {
                            // Update
                            fetch('api/article', {
                                method: 'put',
                                body: JSON.stringify(this.article),
                                headers: {
                                    'content-type': 'application/json'
                                }
                            })
                                .then(res => res.json())
                                .then(data => {
                                    this.article.title = '';
                                    this.article.body = '';
                                    alert('Article Updated');
                                    this.fetchArticles();
                                })
                                .catch(err => console.log(err));
                        }

                    },
                    editArticle(article){
                        this.edit =true;
                        this.article.id = article.id;
                        this.article.article_id =article.id;
                        this.article.title = article.title;
                        this.article.body = article.body;
                    }
                }
            };

        </script>

        <style>
            /*textarea style*/
            textarea{
                resize: none;
                width: 100%;
            }

            .purple-border .form-control:focus {
                border: 1px solid #ba68c8;
                box-shadow: 0 0 0 0.2rem rgba(186, 104, 200, .25);
            }

            div.container1 {
                /*width: 50%;*/
                /*margin: 100px;*/
                background-color: #e9fffa;
                border: 5px solid #ff050a;
                overflow:auto;

            }


            /*textinput style*/
            .contact-h {
                font-size: 24px;
                text-transform: uppercase;
                font-weight: 700;
                margin-bottom: 40px;
                color: #EC407A;
            }
            .contact .form-group {
                margin-bottom: 40px;
            }
            .contact .form-control {
                border-radius: 0;
                border: none;
                border-bottom: 4px solid #00BCD4;
                box-shadow: none;
            }
            .contact .btn-primary {
                background-color: #EC407A;
                border-color: #EC407A;
                border-radius: 0;
                text-transform: uppercase;
                font-weight: bold;
            }
            .contact .btn {
                padding: 12px 15px;
            }
            .group {
                position: relative;
                margin-bottom: 45px;
            }
            input:focus {
                outline: none;
            }
            label {
                color: #999;
                font-size: 18px;
                font-weight: normal;
                position: absolute;
                pointer-events: none;
                left: 5px;
                top: 0;
                transition: 0.2s ease all;
                -moz-transition: 0.2s ease all;
                -webkit-transition: 0.2s ease all;
            }
            input:focus ~ label,
            input:valid ~ label {
                top: -20px;
                font-size: 14px;
                color: #96c93d;
            }
            .bar {
                position: relative;
                display: block;
                width: 100%;
            }
            .bar:before,
            .bar:after {
                content: '';
                height: 4px;
                width: 0;
                bottom: 0px;
                position: absolute;
                background: #96c93d;
                transition: 0.2s ease all;
                -moz-transition: 0.2s ease all;
                -webkit-transition: 0.2s ease all;
            }
            .bar:before {
                left: 50%;
            }
            .bar:after {
                right: 50%;
            }
            input:focus ~ .bar:before,
            input:focus ~ .bar:after {
                width: 50%;
            }
            .highlight {
                position: absolute;
                width: 100px;
                top: 25%;
                left: 0;
                pointer-events: none;
                opacity: 0.5;
            }
            /* active state */

            input:focus ~ .highlight {
                -webkit-animation: inputHighlighter 0.3s ease;
                -moz-animation: inputHighlighter 0.3s ease;
                animation: inputHighlighter 0.3s ease;
            }
            /* ANIMATIONS ================ */

            @-webkit-keyframes inputHighlighter {
                from {
                    background: #5264AE;
                }
                to {
                    width: 0;
                    background: transparent;
                }
            }
            @-moz-keyframes inputHighlighter {
                from {
                    background: #5264AE;
                }
                to {
                    width: 0;
                    background: transparent;
                }
            }
            @keyframes inputHighlighter {
                from {
                    background: #5264AE;
                }
                to {
                    width: 0;
                    background: transparent;
                }
            }
            .text-group textarea {
                display: block;
                background: none;
                padding: 0.125rem 0.125rem 0.0625rem;
                border-width: 0;
                border-color: transparent;
                line-height: 1.9;
                width: 100%;
                -webkit-transition: all 0.28s ease;
                transition: all 0.28s ease;
                box-shadow: none;
            }
            .text-group textarea:focus ~ .input-label,
            .text-group textarea:valid ~ .input-label,
            .text-group textarea.form-file ~ .input-label,
            .text-group textarea.has-value ~ .input-label {
                font-size: 14px;
            ;
                color: gray;
                top: -1rem;
                left: 0;
            }
            .text-group textarea:focus ~ .input-label {
                color: #96c93d;
            }
            .text-group textarea:focus ~ .bar::before {
                width: 100%;
                left: 0;
            }
            .text-group {
                position: relative;
                margin-top: 2.25rem;
                margin-bottom: 4.25rem;
            }
        </style>