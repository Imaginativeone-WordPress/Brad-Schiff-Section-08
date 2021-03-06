See WordPress Startup Activities

- [ ] Section 8: Events Post Type 7/8 | 2hr 4min
  - [ ] 29. Custom Post Types 20min
    - Regular Post Types
      - Posts
      - Pages (Posts with the Type of "Page")
      ![Custom Post Type](https://user-images.githubusercontent.com/1640067/93379941-8f05e880-f82c-11ea-90c2-e7fda1363d53.png)
      
      ```php
      <?php 
        // Post Types
        function university_post_types() {
          register_post_type('event', array(
            'show_in_rest' => true, // Modern Editor
            'supports' => array('title', 'editor', 'excerpt'), // CPT Excerpts now happen, 'editor' is mandatory
            // 'custom-fields' removed, using ACF now
            'rewrite' => array('slug' => 'events'),
            'has_archive' => true,
            'public' => true,
            'labels' => array(
              'name' => 'Events', // Menu Item
              'add_new_item' => 'Add New Event',
              'edit_item' => 'Edit Event',
              'all_items' => 'All Events',
              'singular_name' => 'Event'
            ),
            'menu_icon' => 'dashicons-calendar'
          ));
        }

        add_action('init', 'university_post_types');
      ?>
      ```

  - [ ] 30. Displaying Custom Post Types 20min
  - [ ] 31. Quick Timeout: Misc Updates 14min
  - [ ] 32. Custom Fields 19min
  - [ ] 33. A Note About the WordPress Post Edit Screen 1min
  - [ ] 34. Ordering (Sorting) Custom Queries 14min
  - [ ] 35. Manipulating Default URL Based Queries 18min
  - [ ] 36. Past Events Page (Custom Query Pagination) 19min
  
  ```php
  <?php 

    $today = date('Ymd');

    // Custom Query with Ordering and Sorting
    $pastEvents = new WP_Query(
      array(
        // 'posts_per_page' => 2,             // This doesn't work for pagination, only works for default queries. This is a custom query.
                                              // paginate_links() is working with the default query. See lines 99-103
        'paged'          => get_query_var('paged', 1),
        'post_type'      => 'event',
        'meta_key'       => 'event_date',
        'orderby'        => 'meta_value_num', // formerly 'post_date', 'rand', meta_value !event_date
        'order'          => 'ASC',
        'meta_query'     => array( // eliminate non-adherents to sub-query
          array(
            'key' => 'event_date',
            'compare' => '<',
            'value' => $today,
            'type' => 'numeric'
          )
        )
      )
    );

    while($pastEvents->have_posts()) {
      $pastEvents->the_post(); ?>
      <!-- Beg of HTML for an Event on the Home Page -->
      <div class="event-summary">
        <!-- <a class="event-summary__date t-center" href="<?php the_permalink(); ?>">
          <span class="event-summary__month">Mar</span>
          <span class="event-summary__day">25</span>
        </a> -->
        <a class="event-summary__date t-center" href="<?php the_permalink(); ?>">
          <span class="event-summary__month">
            <!-- <?php the_field('event_date'); ?></span> --><!-- CPT Date -->
            <?php 
              $eventDate = new DateTime(get_field('event_date')); // the_field >> get_field
              echo $eventDate->format('M');
            ?>
          <span class="event-summary__day"><?php echo $eventDate->format('d'); ?></span>
        </a>
        <div class="event-summary__content">
          <h5 class="event-summary__title headline headline--tiny">
            <a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h5>
          <p><?php echo wp_trim_words(get_the_content(), 18); ?>
            <a href="<?php the_permalink(); ?>" class="nu gray">Learn more</a>
          </p>
        </div>
      </div>
      <!-- End of HTML for an Event on the Home Page -->
    <?php }
  ?>

  <!-- Pagination -->
  <?php echo paginate_links(
    array(
      'total' => $pastEvents->max_num_pages
    )
  ); ?>
  ```
  **archive-events.php**
  ```php
  <hr class="section-break">

  <p>Looking for a recap of past events?
    <a href="<?php echo site_url('/past-events') ?>">Check out our past events archive.</a>
  </p>
  ```
