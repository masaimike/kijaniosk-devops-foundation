Regions and Availability zones

Region Selection - Middle East  - Bahrain (AWS)

Reasoning
LATENCY
- Kijanikiosk users customer base is Primarily in Nairobi ,Kenya and East AFrica. Users need quick service and responses. In order to reduce latency which is the amount taken before a response - we should deploy in the region closest to us.
- If we consider AWS as our cloud provider - the Middle East would be the best region to deploy as it is the closest to Nairobi ,Kenya as compared to the second closest which is Capetown ,South Africa according to AWS Global infrastracture.

HIGH AVAILABILITY multi AZ deployment
- KijaniKiosk users also want to minimize outages of their application. We can ensure this by deploying a Multi-AZ architecture between multiple availability zones within a region.
- In case one data centre goes down, the aapplication would remain running in another AZ ensuring high availability and resilience.
- In the case of deploying in AWS  in the Middle East -we can deploy in Barhain regiion which has 3 availability zones.

- A multi-region deployment would not be recommended in this case as it would increase both costs and operational complexity.


