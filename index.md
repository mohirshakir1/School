<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.12"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.8.0/html2pdf.bundle.min.js" integrity="sha512-w3u9q/DeneCSwUDjhiMNibTRh/1i/gScBVp2imNVAMCt6cUHIw6xzhzcPFIaL3Q1EbI2l+nu17q2aLJJLo4ZYg==" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.21.0/axios.min.js" integrity="sha512-DZqqY3PiOvTP9HkjIWgjO6ouCbq+dxqWoJZ/Q+zPYNHmlnI2dQnbJ5bxAHpAMw+LXRm4D72EIRXzvcHQtE8/VQ==" crossorigin="anonymous"></script>
    <style>
        .container {
            max-width: 700px;
        }
        
        .paper {
            margin: 0 auto;
            margin-bottom: 1em;
            background-color: white;
            width: 60%;
            padding: 0.5em 0 0.5em 0;
            border-top: 1px solid #e0e0e0;
            border-left: 1px solid #e0e0e0;
            border-right: 1px solid #e0e0e0;
            border-bottom: 3px solid #e0e0e0;
            border-radius: 3px;
        }
        
        .card:hover,
        ul li:hover {
            background-color: #34568B;
            color: #e0e0e0;
        }
        
        .btn {
            background-color: #34568B;
        }
        
        @media print {
            .container {
                width: auto;
            }
            .paper {
                border-width: 0 !important;
            }
            button {
                display: none !important;
            }
        }
    </style>
</head>

<body>
    <main id="app" ref="resume" class="container paper p-5 mt-5">
        <section class="heading">
            <h1>{{resumeData.name}}</h1>
            <p>{{resumeData.personalStatement}}</p>
        </section>
        <section class="skills">
            <h4>Skills:</h4>
            <ul class="list-group">
                <li class="list-group-item" v-for="skill in resumeData.skills">{{skill}}</li>
            </ul>
        </section>

        <section class="heading pt-2 card  my-4 px-3">
            <h4>Relevant Experience</h4>
            <div v-for="job in resumeData.jobs">
                <div><em>{{job.title}}</em>
                </div>
                <p>{{job.company}} | {{job.timePeriod}}</p>
                <ul>
                    <li v-for="bullet in job.bullets">{{bullet}}</li>
                </ul>
            </div>
        </section>

        <section class="courses pt-2">
            <h4>Education:</h4>
            <div v-for="school in resumeData.education">

                <div><em>{{school.institutionName}}</em>
                    <p>Selected Courses:</p>
                    <ul class="list-group">
                        <li class="list-group-item" v-for="course in school.relevantCourses">{{course}}</li>
                    </ul>
                </div>
        </section>
        <button class="btn btn-info my-5" @click="dl">Print/Download as PDF</button>
    </main>
</body>

<script>
    var app = new Vue({
        el: '#app',
        data: {
            accessToken: '',
            // PUT YOUR APP ID HERE f
            realmAppID: 'resume-cyrxz', // <------- replace 'resumesite-inliw' with yours
            resumeData: {},
            options: {
                margin: 0,
                filename: 'Resume.pdf',

                html2canvas: {
                    scale: 4
                },
                jsPDF: {
                    unit: 'in',

                    orientation: 'portrait'
                },
                pagebreak: {
                    mode: ['avoid-all']
                }
            },
        },
        methods: {
            dl: function() {
                window.print()


            },
        },
        mounted() {
            axios({
                url: `https://realm.mongodb.com/api/client/v2.0/app/${this.realmAppID}/auth/providers/anon-user/login`,
                method: 'get'
            }).then(result => {

                this.accessToken = result.data.access_token

                axios({
                    url: `https://realm.mongodb.com/api/client/v2.0/app/${this.realmAppID}/graphql`,
                    method: 'post',
                    headers: {
                        authorization: `Bearer ${this.accessToken}`

                    },
                    data: {
                        query: `query {
                            resume {
                          
                                    education {
                                    institutionName
                                    relevantCourses
                                    }
                                    jobs {
                                    company
                                    timePeriod
                                    title
                                    }
                                    name
                                    personalStatement
                                    skills
                                }
                            }
                        `
                    }
                }).then((result) => {
                    this.resumeData = result.data.data.resume

                    console.log(this.resumeData)




                });
            })
        }
    })
</script>

</html>
