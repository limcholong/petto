<template>
  <div>
    <div>
      <v-container class="slide">
          <swiper class="swiper swiper-wrapper" :options="swiperOption">
              <swiper-slide v-for="image in images" :key="image.text">
                  <v-img :src="image.img" max-height="100%"></v-img>
                  <v-btn class="overlay-btn" outlined color="white" x-large width="300" style="border:3px solid white;"
                  router :to="image.link"><h4>{{ image.text}}</h4></v-btn>
              </swiper-slide>
              <div class="swiper-pagination" slot="pagination"></div>
              <div class="swiper-button-prev" slot="button-prev"></div>
              <div class="swiper-button-next" slot="button-next"></div>
          </swiper>
      </v-container>
    </div>
    <br>
    <div style="width: 80%; margin: auto;">
      <section>
          <div style="float:left; width: 22%; margin:1%; height:100%;">
              <v-card height="40%">
                  <v-row>
                      <v-card-title style="width:65%; font-size:20px; color: grey;" class="normalText">유기동물 뉴스</v-card-title>
                      <v-card-actions>
                          <v-btn plain @click="moreNews()"><v-icon>chevron_right</v-icon>더보기</v-btn>
                      </v-card-actions>
                  </v-row>
                  <div  v-for="mainNews in title" :key="mainNews">
                    <v-card-text class="texts-overflow" @click="links(mainNews)">{{mainNews}}</v-card-text>
                  </div>
              </v-card>
          </div>
          <div style="float:left ;width: 22%; margin:1%;">
            <v-card max-height="40%">
                <v-row>
                    <v-card-title style="width:65%; font-size:20px; color: grey;" class="normalText">공고만료</v-card-title>
                    <!-- <v-card-actions>
                        <v-btn plain  route :to="{ name: 'AbandonedAnimal' }"><v-icon>chevron_right</v-icon>더보기</v-btn>
                    </v-card-actions> -->
                </v-row>
                    <div v-for="oldAnimal in olderList" :key="oldAnimal.notice_no">
                        <v-card-text @click="toDetailPage(oldAnimal.id)"
                          class="texts-overflow"><b>{{oldAnimal.kind}}</b>(이/가) 입양을 기다립니다.</v-card-text>
                    </div>
            </v-card>
          </div>
          <div style="float:left; width: 22%; margin:1%;">
              <v-card height="40%">
                  <v-row style="margin-bottom:0%;">
                      <v-card-title style="width:65%; font-size:20px; padding-bottom:2%; color: grey;" class="texts-overflow normalText">오늘의 통계</v-card-title>
                  </v-row>
                      <div>
                          <v-card-actions style="padding:0%;">
                              <statistics/>
                          </v-card-actions>
                      </div>   
              </v-card>
          </div>
          <div style="float:left; width: 22%; margin:1%;">
            <v-card height="40%">
              <v-row style="margin-bottom:0%;">
                  <v-card-title style="width:65%; font-size:20px; padding-bottom:2%; color: grey;" class="texts-overflow normalText">지도 & 보호소</v-card-title>
              </v-row>
                <br>
                <div>
                  <v-card-actions style="padding:0%;">
                    <!-- -->
                    <v-btn text x-large class="normalText" style="width: 47%; height: 270px; font-size: 18px;"
                    @click="toMapPage">
                      유기동물<br>관련 지도
                    </v-btn>
                    &ensp;
                    <v-btn text x-large class="normalText" style="width: 47%; height: 270px; font-size: 20px;"
                    @click="toFacilListPage">
                      보호소
                    </v-btn>
                  </v-card-actions>
              </div>   
            </v-card>
          </div>
        </section>
    </div>
    <div style="padding-top: 100px;">
      <div id="youtube">
        <ul>
          <li v-for="(vid, idx) in videos" :key="idx">
            <img :src="vid.imgSrc" style="width: 250px;" @click="watchVid(vid.url)"/>
          </li>
        </ul>
      </div>
    </div>
      <div>
        <div>
            <v-card v-if="layers">
              <div id="layer">
                <img src="@/assets/img/popup.png" alt="자원봉사" />
                <v-btn class="close" @click="Close()" small icon
                  ><v-icon>close</v-icon></v-btn
                >
                <v-btn
                  class="img-link"
                  color="#feecae"
                  depressed
                  width="200"
                  height="40"
                  route :to="{name : 'AnimalVoluntaryListPage'}"
                  >자세히보기</v-btn>
                <v-btn class="today-close" @click="TodayClose()" plain>오늘 하루 보지 않기</v-btn>
              </div>
            </v-card>
          </div>
      </div>
    </div>
</template>

<script>
import axios from 'axios'
import { mapActions, mapState } from 'vuex';
import { Swiper, SwiperSlide } from 'vue-awesome-swiper'
import 'swiper/css/swiper.css'
import Statistics from "@/components/crawling/Statistics.vue";

export default {
  components: {
    Swiper,
    SwiperSlide,
    Statistics,
  },
  data () {
        return {
          layers: false,
            images: [
                {img:"https://images.pexels.com/photos/3609287/pexels-photo-3609287.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940",text:'입양하기', link:'/abandonedAnimal/list/page=0&place=none&kind=none'},
                {img:"https://images.pexels.com/photos/5938326/pexels-photo-5938326.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940",text:'제보하기', link: '/reportBoard'},
                {img: "https://images.pexels.com/photos/1453479/pexels-photo-1453479.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940",text:'봉사하기', link: '/voluntaryBoard'},
            ],
            swiperOption: {
                loopedSlides: 2,
                grabCursor: true,
                centeredSlides: true,
                slidesPerView: 'auto',
                effect: 'fade',
                pagination: {
                    el: '.swiper-pagination'
                },
                navigation: {
                    nextEl: '.swiper-button-next',
                    prevEl: '.swiper-button-prev'
                }
            },
            title: [],
            url:[],
            news:[],
        }
    },
  methods: {
    watchVid(url) {
      window.open(url, 'newWindow');
    },
    Close() {
      this.layers = false;
    },
    TodayClose() {
      this.$store.dispatch("TodayPopUp");
      this.layers = false;
    },
    ...mapActions(['fetchOlderAnimalList', 'fetchAlarmList', 'fetchVideoList']),
      toDetailPage(id) {
      this.$router.push({
        name: 'AnimalDetailPage',
        params: { "id": id }
      })
    },
    moreNews() {
        const url = 'https://search.naver.com/search.naver?where=news&sm=tab_jum&query=%EC%9C%A0%EA%B8%B0%EB%8F%99%EB%AC%BC+%EC%95%88%EB%9D%BD%EC%82%AC';
        window.open(url);
    },
    links(mainNews) {
        const num = this.title.indexOf(mainNews)
        const toLink = this.url[num]
        window.open(toLink);
    },
    toMapPage() {
      this.$router.push({
        name: 'KakaoMap'
      })
    },
    toFacilListPage() {
      this.$router.push({
        name: 'FacilityListPage'
      })
    }
  },
  mounted() {
    if (this.$cookies.isKey("TodayPopUpClose") == false) {
      this.layers = true;
    }
    this.fetchOlderAnimalList()

    if(this.$cookies.isKey("user")) {
      this.$store.state.session = this.$cookies.get("user");
      if(this.$store.state.session != null) this.$store.dispatch('fetchAlarmList', this.session.id)
    }

    this.fetchVideoList()

    //axios.post("http://localhost:5000/pythonCrawlNews")
    axios.get("http://localhost:8888/petto/admin/python_crawled_News")
      .then(res => {
        if(res.status == 200){
          for(var i=0; i<5; i++){
              this.title.push(res.data[i].title)
              this.url.push(res.data[i].url)
          }
        } else {
          alert('잠시후 다시 실행해 주세요');
        }
    });
  },
  computed: {
      ...mapState(['olderList', 'session', 'videos'])
  }
}
</script>

<style lang="scss" scoped>

.swiper-wrapper {
  position: relative;
  width: 100%;
  height: 100%;
  z-index: 1;
  display: flex;
  transition-property: transform;
  box-sizing: content-box;
}

.slide {
    padding-left: 0px;
    margin-left: 0px;
    padding-top: 0px;
}
.slide-3d {
    padding-top: 0px;
    padding-bottom: 0px;
}
.swiper {
    position: static;
    height: 94vh;
    width: 99.6vw;
    .swiper-slide {
        display: flex;
        justify-content: center;
        width: 100%;
        height: 100%;
        text-align: center;
        font-weight: bold;
        font-size: 3rem;
        background-position: center;
        background-size: center;
    }
}

.overlay-btn{
    position:absolute;
    top: 70%;
}

.texts-overflow{
    display: block;
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
}

#layer {
  position: fixed;
  left: 50px;
  top: 50px;
  width: 450px;
  border: 3px solid #fff;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.4);
}

#layer img {
  width: 100%;
  display: block;
}

#layer .close {
  position: absolute;
  right: 0.2em;
  top: 3px;
  font-size: 20px;
}
#layer .today-close {
  position: absolute;
  right: 0.2em;
  bottom: 0px;
  font-size: 12px;
}
#layer .img-link {
  position: absolute;
  right: 10em;
  bottom: 3em;
  font-size: 12px;
}

h4{
  font-family: "GowunDodum-Regular";
  font-size: 1.5vw;
  margin:0vw;
  letter-spacing: 4px;
}

.container3{
  width: 100%;
  
  margin-top:5%;
  margin-bottom: 5%;
}

#youtube{
  padding: 0% 5em 5% 5em;
  max-width: 100vw;
}

#youtube ul {
  white-space:nowrap;
  overflow-x: auto;
  text-align:center;
  overflow-x: scroll;
  width: 100%;
}

#youtube ul::-webkit-scrollbar{
    width: 1em;
    height: 0.8em;
}

#youtube ul::-webkit-scrollbar-thumb{
    height: 10%;
    background-color:#f7b43e;
    border-radius: 10px;
}

#youtube ul li{
    margin-left: 15px;
    display:inline-block;

    position: relative;
    margin-right: 10px;
    font-size: 20px;
    cursor: pointer;
    transform-origin: center left;

}

#youtube ul li iframe{
    text-decoration:none;
    color:inherit;
}

</style>
