<template>
  <div v-if="events.length > 0" class="upcoming-events" :style="position + ': 0; text-align: ' + position">
    <h2>{{ heading }}</h2>
    <h3 v-for="event in events | limitBy 3">
      <aside>
        <a :href="event.links.self" target="_blank">{{ event.relationships.group.attributes.focus }}</a>
        <div>
          {{ formatDate(event) }}
          (in {{ event.attributes.time.relative }})
        </div>
      </aside>
      <dynamic-invisible-textarea
        :name="eventTitleName(event)"
        :style="'text-align: ' + position"
        :default-value="event.attributes.name"
      ></dynamic-invisible-textarea>
    </h3>
  </div>
</template>

<script>
  // VENDOR
  import moment from 'moment'

  // COMPONENTS
  import DynamicInvisibleTextarea from './dynamic-invisible-textarea'

  export default {
    // COMPONENTS
    components: {
      DynamicInvisibleTextarea
    },
    // PROPS
    props: {
      events: {
        type: Array,
        required: true
      },
      heading: {
        required: true,
        validator: value => {
          return typeof value === 'string' || value === null
        }
      },
      position: {
        type: String,
        required: true,
        validator: value => {
          return ['left', 'right'].some(validValue => {
            return value === validValue
          })
        }
      }
    },
    // HELPERS
    methods: {
      formatDate (event) {
        return moment(event.attributes.time.absolute).format('MMMM Do')
      },
      eventTitleName (event) {
        const uniqueIdentifier = event.attributes.id
        return `eventTitle${uniqueIdentifier}`
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '../scss/globals';

  // HEADING
  $events-heading-margin-bottom: 15px;
  $events-heading-font-weight: $default-font-weight;

  // EVENT
  $event-spacing: 10px;

  // EVENT META
  $event-meta-relative-font-size: 0.8em;

  // EVENT DATE
  $event-date-font-weight: $default-font-weight;

  // EVENT CATEGORY
  $event-category-font-weight: $bolded-font-weight;
  $event-category-text-tranform: uppercase;

  .upcoming-events {
    position: absolute;
    bottom: 0;

    h2 {
      margin-top: 0;
      margin-bottom: $events-heading-margin-bottom;
      font-weight: $events-heading-font-weight;
    }

    h3 {
      margin-top: $event-spacing;
      margin-bottom: 0;

      aside {
        font-size: $event-meta-relative-font-size;
        font-weight: $event-date-font-weight;

        a {
          display: block;
          font-weight: $event-category-font-weight;
          text-transform: $event-category-text-tranform;
        }
      }
    }

    textarea {
      width: $events-width;
    }
  }
</style>
