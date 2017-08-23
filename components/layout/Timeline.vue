<template>
  <div class="column is-6 timeline content">
    <hc-box top="true" bottom="true">
      <hc-title>Timeline</hc-title>
    </hc-box>
    <div class="timeline-intro">
      <p>Hallo Dennis, was sind deine Pläne für heute?</p>
      <p v-if="contributions.length == 0 && loadingFinished">
        Keine Beträge, schreibe doch etwas.
      </p>
      <hc-button color="primary" size="large" type="nuxt" to="/contributions/write" circle>
        <hc-icon icon="plus"/>
      </hc-button>
    </div>
    <div>
      <a class="button is-loading" v-if="loading">Loading ...</a>
      <div v-if="contributions.length > 0" class="timeline-missing-line"></div>
      <div class="timeline-post-wrapper is-clearfix">
        <div class="timeline-post-direction" v-if="contributions.length > 0"
                                             v-for="(contribution, index) in contributions">
          <contribution-card class="card timeline arrow"
            :post="contribution" :key="contribution.slug">
            <small slot="category">{{ contribution.type }}</small>
          </contribution-card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { mapGetters } from 'vuex'
  import feathers from '~plugins/feathers'
  import ContributionCard from '~components/Contributions/ContributionCard'

  export default {
    data () {
      return {
        loading: false,
        loadingFinished: false,
        contributions: []
      }
    },
    computed: {
      ...mapGetters({
        user: 'auth/user'
      })
    },
    components: {
      ContributionCard
    },
    mounted () {
      console.log('mounted')
      this.loading = true
      this.loadingFinished = false;

      // get the contributions by the userId and sort it
      // by createdAt DESC
      (async () => {
        let user = this.user
        try {
          let res = await feathers.service('contributions').find({
            query: {
              userId: user._id,
              $sort: {
                createdAt: -1
              }
            }
          })
          this.loading = false
          this.loadingFinished = true
          console.log({res})
          let contributions = Array.isArray(res.data) ? res.data : []
          this.contributions = contributions
        } catch (err) {
          // this just displays nothing
          // @todo implement some user feedback
          console.log(err)
          this.loading = false
        }
      })()
    }
  }
</script>
<style scoped lang="scss">

// @todo maybe move this to global css
$green: hsl(78, 71%, 41%);
.timeline {
  .timeline-missing-line {
    width: 50%;
    height: 20px;
    border-right: 2px solid $green;
    margin-left: 1px;
    margin-top: -10px;
  }
  .timeline-post-wrapper {
    position: relative;
    width: 100%;
  }
  .timeline-post-direction {
    .arrow {
      position: relative;
    }
    .arrow:after,
    .arrow:before {
      top: 50%;
      z-index: 10;
      border: solid transparent;
      content: " ";
      height: 0;
      width: 0;
      position: absolute;
      border-color: rgba(255, 255, 255, 0);
      border-width: 10px;
      margin-top: -10px;
      pointer-events: none;
    }

    width: 50%;
    &:nth-child(even) {
      float: right;
      border-left: 2px solid $green;
      margin-right: 1px;
      .card {
        &.timeline {
          float: right;
        }
      }
      .arrow:after, .arrow:before {
        right: 100%;
      }
      .arrow:after {
        border-right-color: #ffffff;
      }
      .arrow:before {
        border-right-color: #ffffff;
      }
    }
    &:nth-child(odd) {
      border-right: 2px solid $green;
      margin-left: 1px;
      .arrow:after, .arrow:before {
        left: 100%
      }
      .arrow:after {
        border-left-color: #ffffff;
      }
      .arrow:before {
        border-left-color: #ffffff;
      }
    }
  }
  .timeline-intro {
    text-transform: uppercase;
    text-align: center;
    color: grey;
    padding: 10px 0;
    font-size: 14px;
    letter-spacing: 1px;

    .add-post {
      display: block;
      margin: 10px auto;
      border: 0;
      border-radius: 50px;
      width: 50px;
      height: 50px;
      background-color: #9c3;
      hc-box-shadow: 1px 1px 3px grey;
      font-size: 30px;
      color: white;
      font-weight: lighter;
    }
  }
}
</style>