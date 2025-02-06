# Capacity Estimation  

## Traffic  

- **MAU** (Monthly Active Users): 100M  
- **DAU** (Daily Active Users): 10M  
- Each user has approximately **4 photos** and **1 video** (~5 photos).  
- Each active user views **50 profiles per day**.  
- Each active user sends and receives **10 messages per day** from 3 users.  

**Calculations:**  
1. **250 image views per active user daily**  
2. **60 text messages per active user daily** (10 messages * 3 users * 2 interactions)  

## Load  

1. **Search operations**:  
   - 10M DAU / 100k searches per day = **100 searches per second**.  
   - Search duration: **5 seconds** per search.  
   - **500 concurrent searches** per second.  

2. **Session activities**:  
   - Average session duration: **30 minutes** (15 minutes browsing profiles and 15 minutes chatting).  
   - Every 10 minutes, the user updates their location.  
   - 30 / 10 = **3 location updates per active user daily**.  
   - 10M users * 3 updates / 100k s/d = **300 RPS** for location updates.  

3. **Likes/Dislikes**:  
   - 10M DAU * 50 likes per user / 100k s/d = **5k RPS** for writing likes/dislikes in DB.  

4. **Image retrieval**:  
   - 10M DAU * 250 images per user / 100k s/d = **25k RPS** for image reads.  

5. **Messages**:  
   - 10M DAU * 10 messages * 3 users / 100k s/d = **3k RPS** for reading messages.  
   - **3k RPS** for writing messages.  

#### Summary:  
- **Writes**:  
  - **5k RPS** for likes  
  - **3k RPS** for messages  
  - **0.3k RPS** for locations  
- **Real-time chats**: **100k** concurrent real-time chats  
- **Reads**:  
  - **25k RPS** for image reads  
  - **3k RPS** for message reads  

## Network  

1. **Image traffic**:  
   - 25k RPS * 500 kB = **12.5 GB/s** for images.  
   - **12.5 GB/s** * 100,000 s/d * 400 days = **500 PB** total image network traffic per year.  

2. **Likes/Dislikes**:  
   - Each like/dislike entry: 9B (User1, User2, T/F).  
   - 5k RPS * 9B = **45 kB/s** for like/dislike writes.  
   - **45 kB/s** * 100,000 s/d * 400 days = **1.8 PB** total like/dislike network traffic per year.  

3. **Location updates**:  
   - Each location record: 8B (latitude) + 8B (longitude) + 4B (user ID) = **16B**.  
   - 300 RPS * 16B = **5 kB/s** for location writes.  
   - **5 kB/s** * 100,000 s/d * 400 days = **0.2 PB** total location network traffic per year.  

4. **Message traffic**:  
   - 50B per message * (3k RPS + 3k RPS) = **300 kB/s** for message writes/reads.  
   - **300 kB/s** * 100,000 s/d * 400 days = **12 PB** total message network traffic per year.  

#### Summary:  
- **Total bandwidth usage**:  
  - **12.5 GB/s** for image reads  
  - **45 kB/s** for likes/dislikes  
  - **5 kB/s** for location writes  
  - **300 kB/s** for messages  

- **Total network traffic per year**:  
  - **500 PB** for images  
  - **1.8 PB** for likes  
  - **0.2 PB** for locations  
  - **12 PB** for messages  

## Storage  

- **User profiles**: 100 kB * 100M = **10 TB**  
- **Locations**: 20 B * 100M = **2 GB**  
- **Photos + Videos**: 500 kB * 5 * 100M = **250 TB**  
- **Likes** (assuming 6 months storage): 9B * 50 * 180 days * 10M = **810 GB**  
- **Chats** (assuming 10 chats with 50 messages per user): 50B * 10 * 50 * 100M = **2.5 TB**  

#### Summary:  
- **Total storage**:  
  - **250 TB** for photos/videos  
  - **10 TB** for profiles  
  - **2.5 TB** for chats  
  - **810 GB** for likes  
  - **2 GB** for locations  

- **Total storage required**: **270 TB**  

## Hardware  

- **Read/Write capacity**: **10-15 instances**  
- **Network capacity**: **>= 100 instances** (12.5 GB/s * 8)  
- **Storage capacity**: **<= 33 SSDs** or **14 HDDs**  

## Cost Estimation (1 year)  

- **Traffic**: $0.1 / GB * 500 M GB = **$50M**  
- **Storage**: $300 / TB * 270 TB = **~$100k**  
- **Compute/Bandwidth**: 15 instances * $1000 * 12 * 3 = **$540k**  

# Total Estimated Cost: **~$50M**
