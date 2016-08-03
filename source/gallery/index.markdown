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
  var photoElements = [];


  $(document).ready(function() {
    $( window ).resize(function() {
      clearPhotoTable();
      populatePhotoTable();
    });

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
      after: processNewPhotos,
    });

    $(window).scroll(function() {
     if($(window).scrollTop() + $(window).height() == $(document).height()) {

        // call feed.next() once the page reaches the bottom
        if(feed.hasNext())
          feed.next();
    }
    });

    feed.run();
  });

  function processNewPhotos()
  {
    $('#instafeed').children('table').each(function () {
      photoElements.push($(this));
      $(this).showInPhotoTable = false;
      $(this).detach();
    });

    populatePhotoTable();
  }

  function clearPhotoTable()
  {
    for(var i = 0; i < photoElements.length; i++)
    {
      photoElements[i].detach();
      photoElements[i].showInPhotoTable = false;
    }

    $('#photo_table').empty();
  }

  function populatePhotoTable()
  {
    var counter = 0;
    var currentRow;
    var cell;
    var numPhotosPerRow;
    var photoWidth = 306;
    var viewPortWidth;

    viewPortWidth = $(window).width();

    if(viewPortWidth >= (photoWidth * 3))
    {
      numPhotosPerRow = 3;
    }
    else
    {
      if(viewPortWidth >= (photoWidth * 2))  numPhotosPerRow = 2;
      else numPhotosPerRow = 1;
    }

    for(var i = 0; i < photoElements.length; i++)
    {
      if(photoElements[i].showInPhotoTable)
        continue;

      if((counter % numPhotosPerRow) == 0)
      {
        currentRow = $('<tr>');
        currentRow.appendTo($('#photo_table'));
      }

      cell = $('<td style="background-color: white;">');
      cell.appendTo(currentRow);

      photoElements[i].appendTo(cell);
      photoElements[i].showInPhotoTable = true;

      counter++;
    }
  }

</script>

<div id='instafeed' style='display:none;'></div>

<div style="overflow:auto; text-align:center;">
    <table id='photo_table' style='margin:auto; border: none; border-collapse: separate; border-spacing: 15px;'></table>
</div>



