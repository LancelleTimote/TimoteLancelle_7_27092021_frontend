<template>
    <div class="container__vue">
        <Header/>
        <main class="container__vue--post">
            <form @submit.prevent="createPost" aria-label="Nouveau message" class="container__vue--post--newPost">
                <h2>Quoi de neuf, {{ firstName }} ?</h2>
                <textarea v-model="content" name="message" id="message" placeholder="Écrivez quelque chose..." aria-label="Rédiger un nouveau message"/>
                <img class="container__vue--post--newPost--imagePreview" v-if="imagePreview" :src="imagePreview" id="preview" alt="Prévisualisation de l'image ajoutée au message"/>
                <div class="container__vue--post--newPost--blockButton">
                    <input type="file" name="imageNewPost" @change="onFileSelected"  accept="image/*" id="imageNewPost">
                    <label for="imageNewPost">
                        Choisir une image
                    </label>
                    <button type="submit" aria-label="Publier le message">Publier</button>
                </div>
            </form>

            <h1>Dernières publications</h1>
            
            <div v-for="post in posts" :key="post.postId">
                <div class="container__vue--post--postsPublished">
                    <div class="container__vue--post--postsPublished--header">
                        <div class="container__vue--post--postsPublished--header--block">
                            <ProfileAvatar :src="post.User.profileAvatar" class="container__vue--post--postsPublished--header--block--profileAvatar"/>
                            <div class="container__vue--post--postsPublished--header--block--text">
                                <h2>{{ post.User.firstName }} {{ post.User.lastName }}</h2>
                                <p>Publié le {{ post.createdAt | moment("DD.MM.YYYY à HH:mm") }}</p>
                            </div>
                        </div>
                        <div class="container__vue--post--postsPublished--header--icons">
                            <button v-if="userId == post.UserId || admin === 'true'" @click="displayModifyPost(post.id)" aria-label="Modifier le message" class="animationZoomSimple container__vue--post--postsPublished--header--icons--edit"><i class="fas fa-edit"></i></button>
                            <button v-if="userId == post.UserId || admin === 'true'" v-on:click="deletePost(post.id)" aria-label="Supprimer le message" class="animationZoomSimple"><i class="fas fa-trash-alt"></i></button>
                        </div>
                    </div>
                    <div class="container__vue--post--postsPublished--content">
                        <p :contentPostId="post.id">{{ post.content }}</p>
                        <img v-if="post.imagePost" :imgPostId="post.id" :src="post.imagePost" alt="Image insérée dans le message" class="container__vue--post--postsPublished--content--image"/>
                    </div>
                    <div>
                        <div :inputId="post.id" v-bind:showInputModify="showInputModify" class="container__vue--post--postsPublished--edit">
                            <textarea v-model="contentmodifyPost" :placeholder="post.content" id="textarea" aria-label="Modifier le message"/>
                            <img v-if="imagePreviewModifyPost" :src="imagePreviewModifyPost" alt="Prévisualisation de l'image ajoutée au message modifié"/>
                            <div class="container__vue--post--postsPublished--edit--blockButton">
                                <input type="file" name="imageModifyPost" @change="onFileSelectedModifyPost"  accept="image/*" id="imageModifyPost">
                                <label for="imageModifyPost">
                                    Choisir une image
                                </label>
                                <button v-on:click="modifyPost(post.id)" aria-label="Enregistrer les modifications">Enregistrer</button>
                            </div>
                        </div>
                    </div>
                    <div class="container__vue--post--postsPublished--blockButtonPost">
                        <Likes v-bind:post="post"/>   
                        <div class="container__vue--post--postsPublished--blockButtonPost--buttonComment">
                            <button v-on:click="displayCreateComment(post.id)" aria-label="Commenter le message"><i class="far fa-comment-alt"></i>Commenter</button>
                        </div>
                    </div>
                    <div :formId="post.id" v-bind:showCreateComment="showCreateComment" class="container__vue--post--postsPublished--containerCreateComment">
                        <form @submit.prevent="createComment(post.id)" class="container__vue--post--postsPublished--containerCreateComment--form">
                            <textarea v-model="contentComment" name="comment" id="comment" placeholder="Écrivez un commentaire..." aria-label="Rédiger un nouveau commentaire"/>              
                            <button aria-label="Publier le commentaire">Publier</button>
                        </form>
                    </div>
                    <span @click="displayComment(post.id)" v-if="post.Comments.length > 1" class="container__vue--post--postsPublished--commentCount">{{ post.Comments.length }} commentaires</span>
                    <span @click="displayComment(post.id)" v-if="post.Comments.length === 1" class="container__vue--post--postsPublished--commentCount">{{ post.Comments.length }} commentaire</span>
                    <div v-for="comment in comments" :key="comment.commentId">
                        <div v-bind:showComment="showComment" v-if="showComment && post.id == comment.postId" class="container__vue--post--postsPublished--comments">
                            <div class="container__vue--post--postsPublished--comments--header">
                                <div class="container__vue--post--postsPublished--comments--header--block">
                                    <ProfileAvatar :src="comment.User.profileAvatar"/>
                                    <div class="container__vue--post--postsPublished--comments--header--block--text">
                                        <h2>{{comment.User.firstName}} {{ comment.User.lastName }}</h2>
                                        <p>Publié le {{ comment.createdAt | moment("DD.MM.YYYY à HH:mm") }}</p>
                                    </div>
                                </div>
                                <div class="container__vue--post--postsPublished--comments--header--button">
                                    <button v-if="userId == comment.UserId || admin === 'true'" @click="deleteComment(comment.id)" class="animationZoomSimple"><i class="fas fa-trash-alt"></i></button>
                                </div>
                            </div>
                            <p class="container__vue--post--postsPublished--comments--content">{{ comment.content }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </main>
        <Footer/>
    </div>
</template>

<script>
    import Header from '../components/Header.vue'
    import Footer from '../components/Footer.vue'
    import ProfileAvatar from '../components/ProfileAvatar.vue'
    import Likes from '../components/Likes.vue'

    import axios from 'axios'
    import { Notyf } from 'notyf'
    import 'notyf/notyf.min.css'

    export default {
        name: 'Post',
        components: {
            Header,
            Footer,
            ProfileAvatar,
            Likes,
        },
        data() {
            return {
                firstName: localStorage.getItem('firstName'),
                profileAvatar: localStorage.getItem('profileAvatar'),
                userId: localStorage.getItem('userId'),
                admin: localStorage.getItem('admin'),
                imagePost: '',
                imagePreview: null,
                imagePreviewModifyPost: null,
                content: '',
                posts: [],
                contentmodifyPost: '',
                showInputModify: false,
                comments: [],
                contentComment: '',
                showComment: false,
                showCreateComment: false,
                like: false,
                postLikes: [],
            }
        },
        created() {
            this.displayPost();
            this.notyf = new Notyf({
                duration: 3000,
                position: {
                    x: 'center',
                    y: 'bottom'
                }
            });
        },  
        methods: {
            //création d'un nouveau post
            onFileSelected(event) {
                this.imagePost = event.target.files[0];
                this.imagePreview = URL.createObjectURL(this.imagePost);
            },
            onFileSelectedModifyPost(event) {
                this.imagePost = event.target.files[0];
                this.imagePreviewModifyPost = URL.createObjectURL(this.imagePost);
            },       
            createPost() {
                const formData = new FormData();
                formData.append("content", this.content);
                formData.append("image", this.imagePost);
                axios.post('http://localhost:3000/api/post', formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                        'Authorization': 'Bearer ' + localStorage.getItem('token')
                    }
                })
                .then(() => {
                    window.location.reload()
                })
                .catch(error => {
                    const msgerror = error.response.data;
                    this.notyf.error(msgerror.error)
                })
            },
            //affichage des posts
            displayPost() {
                axios.get('http://localhost:3000/api/post', {
                    headers: {
                        'Content-Type' : 'application/json',
                        'Authorization': 'Bearer ' + localStorage.getItem('token')
                    }
                })
                .then(response => {
                    this.posts = response.data;
                })
                .catch(error => {
                    const msgerror = error.response.data
                    this.notyf.error(msgerror.error)
                })
            },
            //modification d'un post
            modifyPost(id) {
                const postId = id;
                const formData = new FormData();
                formData.append("content", this.contentmodifyPost);
                formData.append("image", this.imagePost);
                axios.put('http://localhost:3000/api/post/' + postId, formData, {
                    headers: {
                        'Authorization': 'Bearer ' + localStorage.getItem('token'),
                        'Content-Type': 'multipart/form-data'
                    }
                })
                .then(() => {
                    window.location.reload()
                })
                .catch(error => {
                    const msgerror = error.response.data
                    this.notyf.error(msgerror.error)
                })
            },
            //affichage du champ pour la modification d'un post
            displayModifyPost(id) {
                const postId = id;
                this.showInputModify === false
                let input = document.querySelector('div[inputId="'+id+'"]');
                let inputId = input.getAttribute('inputId');
                if(postId == inputId && this.showInputModify === false) {
                    input.style.display = "block";
                    this.showInputModify = !this.showInputModify
                } else if(postId == inputId && this.showInputModify === true) {
                    input.style.display = "none";
                    this.showInputModify = !this.showInputModify
                }
            },
            //suppression d'un post
            deletePost(id) {
                const postId = id;
                axios.delete('http://localhost:3000/api/post/' + postId, {
                    headers: {
                        'Content-Type' : 'application/json',
                        'Authorization': 'Bearer ' + localStorage.getItem('token')
                    }
                })
                .then(() => {
                    this.displayPost();
                })
                .catch(error => {
                    const msgerror = error.response.data
                    this.notyf.error(msgerror.error)
                })
            },
            //création d'un nouveau commentaire
            createComment(id) {
                const postId = id;
                axios.post('http://localhost:3000/api/comment/' + postId, {
                    content: this.contentComment,
                },
                {
                    headers: {
                        'Authorization': 'Bearer ' + localStorage.getItem('token')
                    }
                })
                .then(() => {
                    window.location.reload()                    
                })
                .catch(error => {
                    const msgerror = error.response.data
                    this.notyf.error(msgerror.error)
                })
            },
            //affichage du champ pour créer un nouveau commentaire
            displayCreateComment(id) {
                const postId = id;
                this.showCreateComment == false
                let form = document.querySelector('div[formId="'+id+'"]')
                let formId = form.getAttribute('formId');
                if(postId == formId && this.showCreateComment == false) {
                    form.style.display = "block";
                    this.showCreateComment = !this.showCreateComment
                } else if(postId == formId && this.showCreateComment == true) {
                    form.style.display = "none";
                    this.showCreateComment = !this.showCreateComment
                }
            },
            //affichage des commentaires d'un message
            displayComment(id) {
                this.showComment = !this.showComment
                const postId = id;
                axios.get('http://localhost:3000/api/comment/' + postId, {
                    headers: {
                        'Content-Type' : 'application/json',
                        'Authorization': 'Bearer ' + localStorage.getItem('token')
                    }
                })
                .then(response => {
                    this.comments = response.data;
                })
                .catch(error => {
                    const msgerror = error.response.data
                    this.notyf.error(msgerror.error)
                })
            },
            //suppression d'un commentaire
            deleteComment(id) {
                const commentId = id;
                axios.delete('http://localhost:3000/api/comment/' + commentId, {
                    headers: {
                        'Content-Type' : 'application/json',
                        'Authorization': 'Bearer ' + localStorage.getItem('token')
                    }
                })
                .then(() => {
                    window.location.reload()
                })
                .catch(error => {
                    const msgerror = error.response.data
                    this.notyf.error(msgerror.error)
                })
            }
        }
    }
</script>

<style scoped lang="scss">
    .container__vue--post--newPost, .container__vue--post--postsPublished--edit, .container__vue--post--postsPublished--containerCreateComment--form {
        textarea {
            height: 7em;
            resize: none;
            border: 3px solid rgba(39, 72, 128, 1);
            border-radius: 5px;
        }
    }
    .container__vue--post--newPost--blockButton, .container__vue--post--postsPublished--edit--blockButton, .container__vue--post--postsPublished--blockButtonPost--buttonComment, .container__vue--post--postsPublished--containerCreateComment--form {
        button {
            background-color: rgba(190, 209, 243, 1);
            border: none;
            border-radius: 5px;
        }
        button:hover {
            border: 3px solid rgba(39, 72, 128, 1);
        }
    }
    .container__vue--post--newPost--blockButton, .container__vue--post--postsPublished--edit--blockButton {
        margin-top: 25px;
        display: flex;
        justify-content: space-between;
        button {
            width: 100px;
            height: 40px;
        }
        input {
            display: none;
        }
        label:hover {
            border: 3px solid rgba(39, 72, 128, 1);
        }
        label {
            font-size: 14px;
            cursor: pointer;
            padding: 10px;
            width: 115px;
            height: 20px;
            background-color: rgba(190, 209, 243, 1);
            border-radius: 5px;
        }
    }
    .container__vue--post--postsPublished--header--icons, .container__vue--post--postsPublished--comments--header--button {
        button {
            border: none;
            background-color: rgba(255, 255, 255, 1);
            i {
                color: rgba(39, 72, 128, 1);
                font-size: 15px;
            }
        }
    }
    .container__vue--post--newPost--imagePreview, .container__vue--post--postsPublished--content img, .container__vue--post--postsPublished--edit img {
        display: flex;
        max-width: 38.5em;
        max-height: 21.5em;
    }
    .container__vue {
        main {
            background-image: none;
            background-color: rgba(107, 102, 102, 1);
        }
        &--post {
            h1 {
                color: rgba(255, 255, 255, 1);
            }
            display: flex;
            flex-direction: column;
            align-items: center;
            &--newPost {
                padding: 25px;
                margin-top: 20px;
                width: auto;
                height: auto;
                border: 3px solid rgba(39, 72, 128, 1);
                border-radius: 5px;
                display: flex;
                flex-direction: column;
                background-color: rgba(255, 255, 255, 1);
                h2 {
                    margin: 0 0 20px 0;
                    color: rgba(39, 72, 128, 1);
                }
                textarea {
                    width: 47.2em;
                }
                &--imagePreview {  
                    margin: 20px auto 0 auto;
                }
            }
            &--postsPublished {
                padding: 25px;
                width: 40em;
                border: 3px solid rgba(39, 72, 128, 1);
                border-radius: 5px;
                margin-bottom: 30px;
                background-color: rgba(255, 255, 255, 1);
                display: flex;
                flex-direction: column;
                p {
                    color: rgba(0, 0, 0, 1);
                }
                &--header {
                    display: flex;
                    justify-content: space-between;
                    margin-bottom: 10px;
                    &--block {
                        display: flex;
                        &--profileAvatar {
                            width: 8em;
                            height: 8em;
                        }
                        &--text {
                            margin: 10px 0 0 20px;
                            h2 {
                                margin-bottom: 10px;
                                color: rgba(72, 100, 147, 1);
                            }
                            p {
                                margin-bottom: 10px;
                                color: rgba(107, 102, 102, 1);
                                font-style: italic;
                            }
                        }
                    }
                    &--icons {
                        &--edit {
                            margin-right: 20px;
                        }
                    }
                }
                &--content {
                    border: 2px solid rgba(107, 102, 102, 1);
                    border-radius: 5px;
                    margin-bottom: 10px;
                    padding: 10px;
                    p {
                        margin-bottom: 10px;
                    }
                    img {
                        margin: 2px auto;
                    }
                }
                &--edit {
                    display:none;
                    textarea {
                        margin-top: 20px;
                        width: 47.4em;
                    }
                    img {
                        margin: 20px auto;
                    }
                }
                &--blockButtonPost {
                    display: flex;
                    justify-content: space-around;
                    margin-top: 15px;
                    &--buttonComment {
                        button {
                            width: 120px;
                            height: 35px;
                            display: flex;
                            align-items: center;
                            justify-content: center;
                        }
                        i {
                            margin-right: 10px;
                            color: rgba(39, 72, 128, 1);
                        }
                    }
                }
                &--commentCount {
                    cursor: pointer;
                    margin-top: 20px;
                }
                &--containerCreateComment {
                    display: none;
                    &--form {
                        margin-top: 20px;
                        display: flex;
                        flex-direction: column;
                        textarea {
                            margin-bottom: 20px;
                        }
                        button {
                            width: 120px;
                            height: 35px;
                        }
                    }
                }
                &--comments {
                    border: 3px solid rgba(39, 72, 128, 1);
                    border-radius: 5px;
                    margin: 20px 0;
                    padding: 20px;
                    &--header {
                        display: flex;
                        justify-content: space-between;
                        &--block {
                            display: flex;
                            img {
                                width: 8em;
                                height: 8em;
                            }
                            &--text {
                                margin: 10px 0 0 20px;
                                h2 {
                                    margin-bottom: 10px;
                                    color: rgba(72, 100, 147, 1);
                                }
                                p {
                                    color: rgba(107, 102, 102, 1);
                                    font-style: italic;
                                }
                            }
                        }
                    }
                    &--content {
                        border: 2px solid rgba(107, 102, 102, 1);
                        border-radius: 5px;
                        padding: 10px;
                    }
                }
            }
        }
    }
    @media (max-width: 576px) {
        .container__vue--post--newPost--blockButton, .container__vue--post--postsPublished--edit--blockButton {
            margin-top: 10px;
            button, label {
                width: 75px;
                font-size: 10px;
            }
            label {
                height: 20px;
                text-align: center;
            }
        }
        .container__vue--post {
            h1 {
                font-size: 1.5em;
            }
            &--newPost {
                width: 20em;
                padding: 10px;
                h2 {
                    text-align: center;
                    font-size: 20px;
                    margin-bottom: 10px;
                }
                textarea {
                    width: 23.3em;
                }
                &--imagePreview {
                    margin-top: 10px;
                    max-width: 19em;
                }
            }
            &--postsPublished {
                width: 20em;
                padding: 10px;
                margin-bottom: 10px;
                &--header {
                    flex-direction: column-reverse;
                    &--icons--edit {
                        margin-right: 0;
                    }
                    &--block {
                        flex-direction: column;
                        align-items: center;
                        &--profileAvatar {
                            width: 5em;
                            height: 5em;
                        }
                        &--text {
                            text-align: center;
                            margin: 0;
                            h2 {
                                margin-top: 0;
                                font-size: 20px;
                            }
                            p {
                                font-size: 14px;
                            }
                        }
                    }
                }
                &--content {
                    p {
                        font-size: 12px;
                    }
                    img {
                        max-width: 18.5em;
                    }
                }
                &--edit {
                    textarea {
                        margin-top: 10px;
                        width: 23.4em;
                    }
                    img {

                        margin: 10px auto 15px auto;
                        max-width: 19em;
                    }
                }
                &--blockButtonPost {
                    &--buttonComment button, .buttonLike {
                        font-size: 10px;
                        width: 95px;
                        height: 30px;
                    }
                }
                &--commentCount {
                    margin-top: 10px;
                    font-size: 12px;
                }
                &--containerCreateComment--form {
                    button {
                        font-size: 10px;
                        width: 100px;
                        height: 30px; 
                    }
                }
                &--comments {
                    margin: 10px 0;
                    padding: 10px;
                    &--header {
                        flex-direction: column-reverse;
                        &--block {
                            flex-direction: column;
                            align-items: center;
                            img {
                                width: 5em;
                                height: 5em;
                                margin: 0;
                            }
                            &--text {
                                margin: 0;
                                text-align: center;
                                h2 {
                                    margin-top: 10px;
                                    font-size: 20px;
                                }
                                p {
                                    margin-bottom: 10px;
                                    font-size: 14px;
                                }
                            }
                        }
                    }
                    &--content {
                        font-size: 12px;
                    }
                }
            }
        }
    }
    @media (max-width: 320px) {
        .container__vue--post {
            &--newPost {
                width: 17em;
                textarea {
                    width: 19.5em;
                }
                &--imagePreview {
                    width: 16em;
                }
            }
            &--postsPublished {
                width: 17em;
                &--content {
                    img {
                        width: 15em;
                    }
                }
                &--edit {
                    textarea {
                        width: 19.7em;
                    }
                    img {
                        margin-top: 10px;
                        width: 16em;
                    }
                }
            }
        }
    }
</style>