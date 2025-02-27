[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: Qiutong Li
### Student Id: 21112325
### Email: qlicv@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > Name of measurement tool: Sysbench
    >
    > CPU
    >
    > configuration
    >
    > ​	**--cpu-max-prime=20000**: This defines the highest prime number to be calculated. A higher value increases computation workload, making it useful for stress testing the CPU. We choose **20000** to ensure a substantial test without excessive execution time.
    >
    > ​	**--threads=4**: This specifies the number of concurrent threads. We set it to **4** to match a typical EC2 instance with **4 vCPUs**. Adjusting this value helps simulate real-world multi-threaded workloads.
    >
    > CPU speed:
    >
    >   events per second:  892.42
    >
    >   events (avg/stddev):      2231.7500/14.75
    >
    >   execution time (avg/stddev):  9.9890/0.01
    >
    > **Interpretation**:
    >
    > ​	Your CPU handled **~892 operations per second**, which represents how many prime number calculations were completed per second.
    >
    > ​	The total execution time is **10 seconds**, indicating that the benchmark ran for the expected duration.
    >
    > ​	A **higher events/sec value** means better computational performance.
    >
    > GPU
    >
    > configuration
    >
    > **--memory-block-size=1M**: This sets the size of each memory block to be processed in one operation. A larger block size (1MB) allows for a more efficient memory transfer test.
    >
    > **--memory-total-size=10G**: The total memory size to be tested. We choose **10GB** to ensure a thorough evaluation of memory bandwidth while keeping the test duration reasonable.
    >
    > **--threads=4**: The number of threads executing memory operations concurrently. Setting it to **4** allows us to evaluate parallel memory access efficiency.
    >
    > **Latency** (ms):
    >
    > ​     min:                  0.04
    >
    > ​     avg:                  0.20
    >
    > ​     max:                  8.31
    >
    > ​     95th percentile:            0.42
    >
    > ​     sum:                 2041.76
    >
    > **Interpretation**:
    >
    > ​	Low **average latency (0.20ms)** indicates fast memory access.
    >
    > ​	A **95th percentile of 0.42ms** means most memory operations were executed quickly.
    >
    > ​	A **maximum latency of 8.31ms** suggests occasional slow operations, but they are rare.
    >
    > **Throughput (Speed)**
    >
    > Total operations: 10240 (19168.75 per second)
    >
    > 10240.00 MiB transferred (19168.75 MiB/sec)
    >
    > General statistics:
    >
    >   total time:             0.5323s
    >
    >   total number of events:       10240
    >
    > **Interpretation**: Your EC2 instance is handling a high amount of memory read/write operations efficiently with a solid throughput of ~19GB/s.
    >
    > ​	

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.micro` | **~892.42 ops/sec** | **~19,168.75 MiB/sec (~19GB/s)** |
    | `t2.medium`  | **1,590.29 ops/sec** | **27,712.69 MiB/sec (~27GB/s)** |
    | `c5d.large` | **703.51 ops/sec** | **19,246.94 MiB/sec (~19GB/s)** |

    > **Memory performance increased from 19GB/s → 27GB/s** but did not **scale perfectly** with vCPU count.
    >
    > Latency decreased slightly but was still affected by **hardware memory architecture**.
    >
    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` | 3.85 Gbits/sec | 0.226    |
    | `m5.large` - `m5.large`   | 4.94           | 0.125    |
    | `c5n.large` - `c5n.large` | 4.95           | 0.154    |
    | `t3.medium` - `c5n.large` | 2.11           | 0.765    |
    | `m5.large` - `c5n.large`  | 4.96           | 0.157    |
    | `m5.large` - `t3.medium`  | 2.54           | 0.577    |

    > **TCP Bandwidth:**
    >
    > ​	For **same-instance-type pairs** (e.g., t3.medium to t3.medium), the maximum TCP bandwidth is determined by the instance’s network performance specifications.
    >
    > ​	t3.medium: Baseline up to 0.25 Gbps, burstable up to 5 Gbps.
    >
    > ​	m5.large: Up to 10 Gbps.
    >
    > ​	c5n.large: Up to 25 Gbps.
    >
    > ​	For **different-instance-type pairs** (e.g., t3.medium to c5n.large), the effective TCP bandwidth is typically limited by the instance with the lower network performance.
    >
    > ​	**Round-Trip Time (RTT):**
    >
    > ​	RTT values are approximate and can vary based on factors such as network load, physical distance within the region, and placement groups.
    >
    > ​	Placing instances within the same **cluster placement group** can reduce latency and improve throughput.
    >
    > 
    >
    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      | 367            | 65.235   |
    | N. Virginia - N. Virginia | 4934           | 0.257    |
    | Oregon - Oregon           | 4934           | 0.121    |

    > ​	**TCP Bandwidth:**
    >
    > ​	Inter-region connections (e.g., N. Virginia to Oregon) typically experience **lower bandwidth** compared to intra-region connections due to the increased distance and potential for traversing public internet pathways.
    >
    > ​	Intra-region connections (e.g., within N. Virginia) benefit from AWS’s high-speed internal network, offering **higher bandwidth**.
    >
    > ​	**Round-Trip Time (RTT):**
    >
    > ​	RTT increases with geographical distance. For example, the latency between N. Virginia and Oregon is higher than within the same region.
    >
    > ​	Intra-region RTTs are minimal due to the proximity of data centers and optimized AWS infrastructure.
    >
    > 
    >
    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.
