# Seattle-Airbnb in Malloy

Working on this project gave me the opportunity to fully engage with a real-world dataset using Malloy. I explored Airbnb listings across Seattle from 23 December 2024, diving into everything from pricing to popularity. What stood out to me was how Malloy makes querying and visual storytelling seamless—it wasn’t just numbers, it was insight. I learned how to break down listings by neighborhood and room type, compare average prices, and even segment by host behavior. The scatter plots made it clear that expensive listings don’t necessarily mean better performance—affordability and availability drive reviews. This was also my first real introduction to Markdown in practice within the platform. I used it to organize sections, embed the Airbnb and Space Needle logos, etc., and create a clean, engaging flow between visuals and narrative. That visual formatting added structure and clarity, helping the data tell its story. Lastly, this project taught me that when you combine structured queries with clean design, you don’t just build a dashboard—you build a story. Hurdles were figuring out the image embedding and resizing but also making sure that the code would run. Hopefully it can inspire you to also make a great project. 

## Seattle ![Airbnb Logo](https://img.shields.io/badge/Airbnb-%23ff5a5f.svg?style=for-the-badge&logo=Airbnb&logoColor=white) Rental Data Insights (23 Dec 2024) from [Inside Airbnb](https://insideairbnb.com/get-the-data/)

source: listings is duckdb.table('c:/Users/.../Project 2/SeattleListings.csv')

query: listings_per_neighbourhood_group is listings -> {
  where: price is not null and price > 0
  group_by: neighbourhood_group, room_type
  aggregate: 
    count_listings is count(),
    avg_price is avg(price),
    min_price is min(price),
    max_price is max(price),
  order_by: count_listings desc
  limit: 20
}

## <img src="https://wallpapers.com/images/hd/space-needle-green-lights-hksuoqg46wceu1ky.jpg" alt="Space Needle" width="25" height="60"> Listings Per Neighborhood

# bar_chart
run: listings_per_neighbourhood_group -> {
  group_by: neighbourhood_group
  aggregate: count_listings is count()
  order_by: count_listings desc
  limit: 20
  }

  ## Average Price by Neighborhood

  # bar_chart
run: listings -> {
  where: price is not null and price > 0
  group_by: neighbourhood
  aggregate: 
    avg_price is avg(price),
    count_listings is count()
  order_by: avg_price desc
  limit: 20
}

##  Most Affordable Neighborhoods (with at least 20 listings)

# line_chart
run: listings -> {
  where: price is not null and price > 0
  group_by: neighbourhood
  aggregate: 
    avg_price is avg(price),
    count_listings is count()
  having: count_listings >= 20
  order_by: avg_price asc
  limit: 20
}

## Most Desirable Areas (by total reviews)

# line_chart
run: listings -> {
  where: number_of_reviews is not null
  group_by: neighbourhood
  aggregate: 
    total_reviews is sum(number_of_reviews),
    avg_price is avg(price)
  order_by: total_reviews desc
  limit: 20
}

## Average of Rooms Per Venue
* This chart shows the average price and total count of listings by room type.

# bar_chart
run: listings -> {
  where: price is not null and price > 0
  group_by: room_type
  aggregate: 
    avg_price is avg(price),
    count_listings is count()
  order_by: avg_price desc
}

## <img src=https://www.clker.com/cliparts/E/h/n/7/h/G/home-price-md.png alt="Home Pricing" width="50" height="50"> 1. Pricing & Affordability
* Average price by Neighborhood Group and Room Type

# bar_chart
run: listings -> {
  where: price is not null and price > 0
  group_by: neighbourhood_group, room_type
  aggregate: avg_price is avg(price)
  order_by: avg_price desc
}

* Areas with the widest price ranges

# bar_chart
run: listings -> {
  where: price is not null and price > 0
  group_by: neighbourhood
  aggregate: 
    price_range is max(price) - min(price),
    count_listings is count()
  having: count_listings > 10
  order_by: price_range desc
  limit: 20
}

* Price distribution (histogram style — by price buckets)
  
# bar_chart
run: listings -> {
  where: price is not null and price > 0 and price < 1000
  group_by: price_bucket is floor(price / 50) * 50
  aggregate: count_listings is count()
  order_by: price_bucket
}

## <img src=https://www.clker.com/cliparts/1/d/4/b/1194985555352626736home1.svg.med.png alt="Home Pricing" width="50" height="50"> 2. Location-Based Insights
* Top neighborhoods by listing count

# bar_chart
run: listings -> {
  group_by: neighbourhood
  aggregate: count_listings is count()
  order_by: count_listings desc
  limit: 20
}

* Top neighborhoods by average price

# bar_chart
run: listings -> {
  where: price > 0
  group_by: neighbourhood
  aggregate: avg_price is avg(price), count_listings is count()
  having: count_listings > 10
  order_by: avg_price desc
  limit: 20
}

* Top neighborhoods by availability
# bar_chart
run: listings -> {
  group_by: neighbourhood
  aggregate: avg_availability is avg(availability_365)
  order_by: avg_availability desc
  limit: 20
}

* Top neighborhoods by number of reviews
  
# bar_chart
run: listings -> {
  group_by: neighbourhood
  aggregate: total_reviews is sum(number_of_reviews)
  order_by: total_reviews desc
  limit: 20
}

* Neighbourhood group with most listings

# bar_chart
run: listings -> {
  group_by: neighbourhood_group
  aggregate: count_listings is count()
  order_by: count_listings desc
}

## <img src=https://www.clker.com/cliparts/8/b/T/0/D/n/hotel-md.png alt="Home Pricing" width="50" height="50"> 3. Room Type & Availability

* Most Common Room Type in Seattle

# bar_chart
run: listings -> {
  group_by: room_type
  aggregate: count_listings is count()
  order_by: count_listings desc
}

# bar_chart
run: listings -> {
  where: price > 0
  group_by: room_type
  aggregate: avg_price is avg(price)
  order_by: avg_price desc
}

* Room type breakdown by neighborhood

# bar_chart
run: listings -> {
  group_by: neighbourhood, room_type
  aggregate: count_listings is count()
  order_by: count_listings desc
  limit: 30
}

* Average availability by 365 days by neighborhood or room_type

# bar_chart
run: listings -> {
  group_by: room_type
  aggregate: avg_availability is avg(availability_365)
  order_by: avg_availability desc
}

## <img src=https://www.clker.com/cliparts/S/l/O/0/7/g/si-s-three-star-md.png alt="Home Pricing" width="150" height="30"> 4. Popularity & Activity
 * Listings/hosts with most reviews

# table
run: listings -> {
  group_by: name, host_name, number_of_reviews, price
  order_by: number_of_reviews desc
  limit: 20
}

* Average number of reviews per neighborhood
  
# bar_chart
run: listings -> {
  group_by: neighbourhood
  aggregate: avg_reviews is avg(number_of_reviews)
  order_by: avg_reviews desc
  limit: 20
}

* Compare reviews_per_month vs price

# scatter_chart
run: listings -> {
  where: price > 0 and reviews_per_month is not null
  group_by: price, reviews_per_month
  limit: 1000
}

* Correlation between reviews and availability

# scatter_chart
run: listings -> {
  where: number_of_reviews is not null
  group_by: number_of_reviews, availability_365
  limit: 1000
}

## <img src=https://www.clker.com/cliparts/3/3/6/a/119498454029858016people.svg.med.png alt="Home Pricing" width="50" height="50"> 5. Host & Listing Behavior
* Listings per host

# bar_chart
run: listings -> {
  group_by: host_name
  aggregate: total_listings is max(calculated_host_listings_count)
  order_by: total_listings desc
  limit: 20
}

* "Super hosts" — hosts with 5+ listings

# bar_chart
run: listings -> {
  group_by: host_name
  aggregate: listing_count is max(calculated_host_listings_count)
  having: listing_count >= 5
  order_by: listing_count desc
  limit: 20
}

* Listings available year-round (365 days)
# bar_chart
run: listings -> {
  group_by: neighbourhood
  aggregate: full_availability_count is count() { where: availability_365 = 365 }
  order_by: full_availability_count desc
  limit: 20
}

## <img src=https://www.clker.com/cliparts/m/t/a/Y/c/Y/3rd-brain-md.png alt="Home Pricing" width="50" height="50"> 6. Custom Segments & Value
* Best value area (high reviews + low price)

# bar_chart
run: listings -> {
  where: price > 0 and number_of_reviews > 10
  group_by: neighbourhood
  aggregate: 
    avg_price is avg(price),
    avg_reviews is avg(number_of_reviews)
  order_by: avg_reviews desc, avg_price
  limit: 20
}

* Do expensive listings get more reviews?

# scatter_chart
run: listings -> {
  where: price > 0 and number_of_reviews > 0
  group_by: price, number_of_reviews
  limit: 1000
}

* Long-term stays by neighborhood group (minimum_nights)

# bar_chart
run: listings -> {
  group_by: neighbourhood_group
  aggregate: avg_min_nights is avg(minimum_nights)
  order_by: avg_min_nights desc
}

## <img src=https://www.clker.com/cliparts/n/U/6/u/5/Z/price-tag-md.png alt="Home Pricing" width="75" height="50"> Prices Over Time 

# line_chart
run: listings -> {
  where: price is not null and price > 0
  group_by: listing_month is date_trunc!('month', last_review)
  aggregate: avg_price is avg(price)
  order_by: listing_month
}
