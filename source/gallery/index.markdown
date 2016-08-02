---
layout: page
title: "gallery"
date: 2016-04-29 00:59
comments: false
sharing: true
footer: true
---
<script type="text/javascript" src="/javascripts/instafeed.min.js"></script>

<script type="text/javascript">
  $(document).ready(function(){
    var feed = new Instafeed({
      get: 'user',
      userId: '346023829',
      clientId: 'f4653956090540d59c5d049d465f526e',
      accessToken:'346023829.1677ed0.e55531cbd8644496bb80827cfadddbf0',
      resolution: 'low_resolution',
      sortBy: 'most-recent',
      target: 'instafeed',
      limit: 33,
      template: '<table style="border: none;"><tr><td><a href="\{\{link\}\}" target="_blank"><img src="\{\{image\}\}"/></a></td></tr><tr><td style="background-color: white;">Location: \{\{location\}\}</td></tr></table>',
      after: populatePhotoTable,
    });

    $(window).scroll(function() {
     if($(window).scrollTop() + $(window).height() == $(document).height()) {

        // call feed.next() once the page reaches the bottom
        if(feed.hasNext())
          feed.next();
    }
    });

    feed.run();
  })


  function populatePhotoTable()
  {
    var counter = 0;
    var currentRow;
    var cell;

    $('#instafeed').children('table').each(function () {
      // "this" is the current element in the loop

      if((counter % 3) == 0)
      {
        currentRow = $('<tr>');
        currentRow.appendTo($('#photo_table'));
      }

      cell = $('<td style="background-color: white;">');
      cell.appendTo(currentRow);


      $(this).detach().appendTo(cell);

      counter++;
    });
  }

</script>



<div id='instafeed' style='display: none;'></div>

<div style="overflow:auto">
  <table id='photo_table' style='border: none; border-collapse: separate; border-spacing: 8px;'></table>
</div>



