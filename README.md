GitHub Public Events Streamer
This project leverages Apache Kafka to stream live data from the GitHub Public Events API, enabling real-time analytics on open-source contributions and development activities. By processing various GitHub events like commits, pull requests, issues, and forks, the system provides actionable insights into trends and patterns within the open-source community.

Project Workflow:
1. Data Ingestion:
The data pipeline begins with ingesting live events from the GitHub Public Events API, which provides a continuous feed of all public actions occurring on GitHub repositories. Events captured include:

PushEvent: Commits made to repositories.
PullRequestEvent: Creation and merging of pull requests.
IssuesEvent: Issue creation, updates, and closures.
ForkEvent: Forking of repositories.
These events are fetched at regular intervals and transformed into a structured format (JSON) for further processing.

2. Kafka Producer:
The Kafka producer component, built using kafka-python, is responsible for pushing the JSON-formatted GitHub event data to a Kafka topic in real time. This ensures scalability and fault-tolerance in data transmission.

Key Kafka Producer Details:
Topic: Events are published to a specific Kafka topic (e.g., github-events).
Partitioning: Kafka’s partitioning mechanism is used to distribute data across different partitions for load balancing.
Message Delivery: The producer sends JSON messages, where each message contains details of a GitHub event such as the repository, event type, timestamp, and user involved.
Kafka Producer Code Example:
python
Copy code
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers='broker_address:9092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

# Send a GitHub event to the 'github-events' topic
producer.send('github-events', {
    'repo': 'repo_name',
    'event_type': 'PushEvent',
    'timestamp': '2024-09-23T12:34:56Z',
    'user': 'username'
})
producer.flush()
3. Kafka Broker:
The Kafka broker serves as the middle layer, receiving and storing messages produced by the Kafka producer. It manages the distribution of messages across partitions and ensures messages are replicated to guarantee fault tolerance.

4. Kafka Consumer:
The Kafka consumer listens to the same topic, github-events, and processes the incoming stream of GitHub event data. This data is then transformed for analysis or forwarded to a storage system for persistence.

Key Kafka Consumer Details:
Real-time Processing: Processing and aggregating event data to generate insights, such as the number of commits per repository, or top contributors.
Batch Processing: Periodic aggregation of data to compute more complex analytics, such as contributor trends or pull request resolution times.
Kafka Consumer Code Example:
python
Copy code
from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
    'github-events',
    bootstrap_servers='broker_address:9092',
    auto_offset_reset='earliest',
    value_deserializer=lambda x: json.loads(x.decode('utf-8'))
)

for message in consumer:
    event_data = message.value
    process_event(event_data)  # Custom function to process and analyze event data
5. Data Storage:
After the consumer processes the data, it is stored in a persistent storage system for further analysis. The storage solutions can include:

NoSQL databases like MongoDB to store unstructured event data in JSON format.
Relational databases like PostgreSQL or MySQL for storing aggregated metrics and analytics results.
6. Real-time Analytics and Dashboards:
Using the streamed data, I performed analytics to uncover key patterns in open-source contributions. Some of the analytics include:

Top Contributors: Identifying the users who made the most contributions across repositories.
Event Frequency: Tracking the frequency of GitHub events over time to detect peaks in activity.
Time to Resolution: Analyzing the time taken to close issues or merge pull requests.
Repository Activity: Visualizing the most active repositories and understanding their contribution patterns.
The real-time analytics results are visualized on a dashboard using tools like Grafana or Streamlit, providing live insights into ongoing GitHub events.

Key Kafka Details:
Producer-Consumer Architecture: The project follows a classic producer-consumer model, where the producer streams data into Kafka and the consumer processes it in real time.
Fault Tolerance: Kafka ensures that the data stream is replicated across multiple brokers, enabling fault tolerance. This makes the system resilient to failures during data transmission.
Scalability: Kafka’s partitioning and replication mechanisms allow for scaling as the volume of GitHub events increases.
Real-Time Data Pipeline: By using Kafka, this project creates a real-time pipeline for continuously ingesting and analyzing GitHub events without delays.
