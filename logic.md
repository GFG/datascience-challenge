The final goal is to infer gender of each customer basing on the rich lists of purchase behavior data. The problem turns to be cluster customer into groups then inferring gender of each group. I take advantage of a common K-means for clustering then decision tree to infer gender from each group. 
1. Clean Data
Data is loaded and check for missing value(s). Luckily, there is no missing data in dataset there. 
Furthermore, I have checked descriptive statistics for detecting some suspicious values. 
The descriptive statistics indicates value of 2 columns "coupon_discount_applied" & "revenue" get error when min value of these columns receive negative value (-155 & -1.13 for coupon_discount_applied & revenue sequently). Furthermore, the max value of coupon_discount_applied is too big (341). There are only 1 row with negative value of revenue & coupon_discount_applied then this row is removed. 
I assumed that coupon_discount_applied is not in percentage unit due to over 99% of value <=1. Hence, value of this feature ought to lie in range [0,1]. Digging deeper indicates that when coupon_discount_applied>1, value of revenue is too little. Therefore it is likely messesed up value of two columns in some rows. That is why i swap value of two columns when coupon_discount_applied >1. After removing the negative coupon_discount_applied and swapping value between 2 columns, some coupon_discount_applied still receive value >1. I assume that these coupon_discount_applied fill in percentage value instead of real value. That is why I divide these value to 100 to align unit with the remaining rows. 

2. Engineer features
There is only one feature is category feature - is_newsletter_subscriber. This column is converted into binary value (0/1) and keep as-is for clustering step. 
Features are used as-is including: coupon_discount_applied, is_newsletter_subscriber, days_since_first_order, days_since_last_order. 
Other features are manipulated as the relative instead of absolute values. Intuitively, each gender might prefer different payment methods or finding it more comfortable shipping to home instead of work address etc. In relative value, the features allow to see the gap between customer in the same scale. 
 To be specific: 
   * shopping_age = days_since_first_order - days_since_last_order. It represents the shopping period of consumer with your platform
   * Avarage_items = items/orders. It presents the average number of items per each order

   * Return_rate = returns/orders. It presents the return order rate of each customer over the total number of orders
   * different_addresses_rate = different_addresses/orders. It represents the rate of different addresses each customer use over the total number of order.
   * shipping_addresses_rate = shipping_addresses/orders. It represents the rate of different shipping addresses each customer use over the total number of order

   * vouchers_rate = vouchers/orders. It represent the rate of using applying voucher in total order of each customer. 

   * cc_payment_rate = cc_payment/orders. It represents the rate of using credit card in total order of each customer.
   * paypal_payment_rate = paypal_payment/orders. It represents the rate of using paypal in total order of each customer. 
   * afterpay_payments_rate = afterpay_payment/orders. It represents the rate of using Afterpay method in total order of each customer.

   * female_items_rate = female_items/items. It represents the rate of purchasing female items in total items of each customer. 
   * male_items_rate = male_items/items. It represents the rate of purchasing male items in total items of each customer. 
   * unisex_items_rate = unisex_items/items. It represents the rate of purchasing unisex items in total items of each customer. 
   * wapp_items_rate = wapp_items/items. It represents the rate of purchasing women apparel items in total items of each customer. 
   * wftw_items_rate = wftw_items/items. It represents the rate of purchasing women footwear items in total items of each customer. 
   * mapp_items_rate = mapp_items/items. It represents the rate of purchasing men apparel items in total items of each customer. 
   * mftw_items_rate = mftw_items/items. It represents the rate of purchasing men footwear items in total items of each customer. 
   * wacc_items_rate = wacc_items_rate/items. It represents the rate of purchasing women accessories items in total items of each customer. 
   * macc_items_rate = macc_items_rate/items. It represents the rate of purchasing men accessories items in total items of each customer. 
   * sprt_items_rate = sprt_items/items. It represents the rate of purchasing sport items in total items of each customer. 

   * msite_orders_rate = msite_orders/orders. It represents the rate of purchasing via mobile site in total order of each customer. 
   * desktop_orders_rate = desktop_orders/orders. It represents the rate of purchasing via desktop in total order of each customer.
   * android_orders_rate = android_orders/orders. It represents the rate of purchasing via android app in total order of each customer. 
   * ios_orders_rate = ios_orders/orders. It represents the rate of purchasing via ios app in total order of each customer. 

   * work_orders_rate = work_orders/orders. It represents the rate of shipping to work place in total order of each customer. 
   * home_orders_rate = home_orders/orders. It represents the rate of shipping to home address in total order of each customer.
   * parcelpoint_orders_rate = parcelpoint_orders/orders. It represents the rate of shipping to parcelpoint in total order of each customer. 

   * aov = revenue/orders. It represents the average value of each order of each customer. 

3. KMeans clustering
Except categorial feature - is_newsletter_subscriber is kept. Other 29 features are rescaled via standard scale. 
3.1. Estimate number of cluster:
In order to train a Kmeans model, an estimated number of clusters is required. The number of clusters is estimated by exploring the silhouette values of different Kmeans model executions. From 2 to 6, k = 2 return the highest silhouette value (0.211). Kmeans is implemented with number of cluster = 2. The cluster label will be assigned to each customer along with behaviioral features for further analysis. 

4. Infer gender. 
Once we construct the 2 clusters, we can produce a list of all customer_id and which cluster they belong to. I take each cluster and study the customer behavior along known dimensions. I come up with decion tree approach to see which dimension contribute most to separate clusters then make guesses on what the clusters will represent. 
The result of decision tree points out that days_since_last_order, female_items_rate, male_items_rate, wftw_items_rate (women footware items rate), macc_items_rate (men accessories items rate),sprt_items_rate (sport item rate) are contributes the most to classify 2 clusters. 

1. Other possible features:
The provided dataset includes purchasing behavior features, it is hence missing features of visit behaviors. I recommend 5 more features which might robust the result: 
   * Most frequently online time 
   * Average time on site
   * Number of reviews
   * Frequent kind of traffic source bring customer to your site
   * Number of different purchased brand(s)