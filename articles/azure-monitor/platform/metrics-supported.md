---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484221"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Počet|Průměr|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Paměť|Bytes|Průměr|Memory. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Bytes|Průměr|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Bytes|Průměr|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests|Počet|Průměr|Total connection requests. These are arrivals.|ServerResourceType|
|SuccessfullConnectionsPerSec|Successful Connections Per Sec|CountPerSecond|Průměr|Rate of successful connection completions.|ServerResourceType|
|TotalConnectionFailures|Total Connection Failures|Počet|Průměr|Total failed connection attempts.|ServerResourceType|
|CurrentUserSessions|Current User Sessions|Počet|Průměr|Current number of user sessions established.|ServerResourceType|
|QueryPoolBusyThreads|Query Pool Busy Threads|Počet|Průměr|Number of busy threads in the query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Command Pool Job Queue Length|Počet|Průměr|Number of jobs in the queue of the command thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processing Pool Job Queue Length|Počet|Průměr|Number of non-I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|CurrentConnections|Connection: Current connections|Počet|Průměr|Current number of client connections established.|ServerResourceType|
|CleanerCurrentPrice|Memory: Cleaner Current Price|Počet|Průměr|Current price of memory, $/byte/time, normalized to 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memory: Cleaner Memory shrinkable|Bytes|Průměr|Amount of memory, in bytes, subject to purging by the background cleaner.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memory: Cleaner Memory nonshrinkable|Bytes|Průměr|Amount of memory, in bytes, not subject to purging by the background cleaner.|ServerResourceType|
|MemoryUsage|Memory: Memory Usage|Bytes|Průměr|Memory usage of the server process as used in calculating cleaner memory price. Equal to counter Process\PrivateBytes plus the size of memory-mapped data, ignoring any memory which was mapped or allocated by the xVelocity in-memory analytics engine (VertiPaq) in excess of the xVelocity engine Memory Limit.|ServerResourceType|
|MemoryLimitHard|Memory: Memory Limit Hard|Bytes|Průměr|Hard memory limit, from configuration file.|ServerResourceType|
|MemoryLimitHigh|Memory: Memory Limit High|Bytes|Průměr|High memory limit, from configuration file.|ServerResourceType|
|MemoryLimitLow|Memory: Memory Limit Low|Bytes|Průměr|Low memory limit, from configuration file.|ServerResourceType|
|MemoryLimitVertiPaq|Memory: Memory Limit VertiPaq|Bytes|Průměr|In-memory limit, from configuration file.|ServerResourceType|
|Kvóta|Memory: Quota|Bytes|Průměr|Current memory quota, in bytes. Memory quota is also known as a memory grant or memory reservation.|ServerResourceType|
|QuotaBlocked|Memory: Quota Blocked|Počet|Průměr|Current number of quota requests that are blocked until other memory quotas are freed.|ServerResourceType|
|VertiPaqNonpaged|Memory: VertiPaq Nonpaged|Bytes|Průměr|Bytes of memory locked in the working set for use by the in-memory engine.|ServerResourceType|
|VertiPaqPaged|Memory: VertiPaq Paged|Bytes|Průměr|Bytes of paged memory in use for in-memory data.|ServerResourceType|
|RowsReadPerSec|Processing: Rows read per sec|CountPerSecond|Průměr|Rate of rows read from all relational databases.|ServerResourceType|
|RowsConvertedPerSec|Processing: Rows converted per sec|CountPerSecond|Průměr|Rate of rows converted during processing.|ServerResourceType|
|RowsWrittenPerSec|Processing: Rows written per sec|CountPerSecond|Průměr|Rate of rows written during processing.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Command pool busy threads|Počet|Průměr|Number of busy threads in the command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Command pool idle threads|Počet|Průměr|Number of idle threads in the command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Long parsing busy threads|Počet|Průměr|Number of busy threads in the long parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Long parsing idle threads|Počet|Průměr|Number of idle threads in the long parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Long parsing job queue length|Počet|Průměr|Number of jobs in the queue of the long parsing thread pool.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Počet|Průměr|Number of threads running I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Processing pool busy non-I/O threads|Počet|Průměr|Number of threads running non-I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processing pool I/O job queue length|Počet|Průměr|Number of I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Počet|Průměr|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processing pool idle non-I/O threads|Počet|Průměr|Number of idle threads in the processing thread pool dedicated to non-I/O jobs.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Query pool idle threads|Počet|Průměr|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Počet|Průměr|Number of jobs in the queue of the query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Short parsing busy threads|Počet|Průměr|Number of busy threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Short parsing idle threads|Počet|Průměr|Number of idle threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Short parsing job queue length|Počet|Průměr|Number of jobs in the queue of the short parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Memory Thrashing|Procento|Průměr|Average memory thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Počet|Průměr|QPU usage by mashup engine processes|ServerResourceType|
|mashup_engine_memory_metric|M Engine Memory|Bytes|Průměr|Memory usage by mashup engine processes|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Průměr|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Bytes|Průměr|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Počet|Celkem|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Počet|Celkem|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Počet|Celkem|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests|Počet|Celkem| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Počet|Celkem|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Other Gateway Requests|Počet|Celkem|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Délka|Overall Duration of Gateway Requests|Milliseconds|Průměr|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Kapacita|Kapacita|Procento|Průměr|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Umístění|
|EventHubTotalEvents|Total EventHub Events|Počet|Celkem|The total number of events sent to EventHub from API Management in a given period.|Umístění|
|EventHubSuccessfulEvents|Successful EventHub Events|Počet|Celkem|The total number of successful EventHub events in a given period.|Umístění|
|EventHubTotalFailedEvents|Failed EventHub Events|Počet|Celkem|The total number of failed EventHub events in a given period.|Umístění|
|EventHubRejectedEvents|Rejected EventHub Events|Počet|Celkem|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Umístění|
|EventHubThrottledEvents|Throttled EventHub Events|Počet|Celkem|The total number of throttled EventHub events in a given period.|Umístění|
|EventHubTimedoutEvents|Timed Out EventHub Events|Počet|Celkem|The total number of timed out EventHub events in a given period.|Umístění|
|EventHubDroppedEvents|Dropped EventHub Events|Počet|Celkem|The total number of events skipped because of queue size limit reached in a given period.|Umístění|
|EventHubTotalBytesSent|Size of EventHub Events|Bytes|Celkem|The total size of EventHub events in bytes in a given period.|Umístění|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Total Jobs|Počet|Celkem|The total number of jobs|Runbook, Status|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Počet|Celkem|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Počet|Celkem|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Počet|Celkem|Total number of dedicated cores in the batch account|No Dimensions|
|TotalNodeCount|Dedicated Node Count|Počet|Celkem|Total number of dedicated nodes in the batch account|No Dimensions|
|LowPriorityCoreCount|LowPriority Core Count|Počet|Celkem|Total number of low-priority cores in the batch account|No Dimensions|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Počet|Celkem|Total number of low-priority nodes in the batch account|No Dimensions|
|CreatingNodeCount|Creating Node Count|Počet|Celkem|Number of nodes being created|No Dimensions|
|StartingNodeCount|Starting Node Count|Počet|Celkem|Number of nodes starting|No Dimensions|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Počet|Celkem|Number of nodes waiting for the Start Task to complete|No Dimensions|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Počet|Celkem|Number of nodes where the Start Task has failed|No Dimensions|
|IdleNodeCount|Idle Node Count|Počet|Celkem|Number of idle nodes|No Dimensions|
|OfflineNodeCount|Offline Node Count|Počet|Celkem|Number of offline nodes|No Dimensions|
|RebootingNodeCount|Rebooting Node Count|Počet|Celkem|Number of rebooting nodes|No Dimensions|
|ReimagingNodeCount|Reimaging Node Count|Počet|Celkem|Number of reimaging nodes|No Dimensions|
|RunningNodeCount|Running Node Count|Počet|Celkem|Number of running nodes|No Dimensions|
|LeavingPoolNodeCount|Leaving Pool Node Count|Počet|Celkem|Number of nodes leaving the Pool|No Dimensions|
|UnusableNodeCount|Unusable Node Count|Počet|Celkem|Number of unusable nodes|No Dimensions|
|PreemptedNodeCount|Preempted Node Count|Počet|Celkem|Number of preempted nodes|No Dimensions|
|TaskStartEvent|Task Start Events|Počet|Celkem|Total number of tasks that have started|No Dimensions|
|TaskCompleteEvent|Task Complete Events|Počet|Celkem|Total number of tasks that have completed|No Dimensions|
|TaskFailEvent|Task Fail Events|Počet|Celkem|Total number of tasks that have completed in a failed state|No Dimensions|
|PoolCreateEvent|Pool Create Events|Počet|Celkem|Total number of pools that have been created|No Dimensions|
|PoolResizeStartEvent|Pool Resize Start Events|Počet|Celkem|Total number of pool resizes that have started|No Dimensions|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Počet|Celkem|Total number of pool resizes that have completed|No Dimensions|
|PoolDeleteStartEvent|Pool Delete Start Events|Počet|Celkem|Total number of pool deletes that have started|No Dimensions|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Počet|Celkem|Total number of pool deletes that have completed|No Dimensions|
|JobDeleteCompleteEvent|Job Delete Complete Events|Počet|Celkem|Total number of jobs that have been successfully deleted.|No Dimensions|
|JobDeleteStartEvent|Job Delete Start Events|Počet|Celkem|Total number of jobs that have been requested to be deleted.|No Dimensions|
|JobDisableCompleteEvent|Job Disable Complete Events|Počet|Celkem|Total number of jobs that have been successfully disabled.|No Dimensions|
|JobDisableStartEvent|Job Disable Start Events|Počet|Celkem|Total number of jobs that have been requested to be disabled.|No Dimensions|
|JobStartEvent|Job Start Events|Počet|Celkem|Total number of jobs that have been successfully started.|No Dimensions|
|JobTerminateCompleteEvent|Job Terminate Complete Events|Počet|Celkem|Total number of jobs that have been successfully terminated.|No Dimensions|
|JobTerminateStartEvent|Job Terminate Start Events|Počet|Celkem|Total number of jobs that have been requested to be terminated.|No Dimensions|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Connected Clients|Počet|Maximum||ShardId|
|totalcommandsprocessed|Total Operations|Počet|Celkem||ShardId|
|cachehits|Cache Hits|Počet|Celkem||ShardId|
|cachemisses|Cache Misses|Počet|Celkem||ShardId|
|getcommands|Gets|Počet|Celkem||ShardId|
|setcommands|Sets|Počet|Celkem||ShardId|
|operationsPerSecond|Operations Per Second|Počet|Maximum||ShardId|
|evictedkeys|Evicted Keys|Počet|Celkem||ShardId|
|totalkeys|Total Keys|Počet|Maximum||ShardId|
|expiredkeys|Expired Keys|Počet|Celkem||ShardId|
|usedmemory|Used Memory|Bytes|Maximum||ShardId|
|usedmemorypercentage|Used Memory Percentage|Procento|Maximum||ShardId|
|usedmemoryRss|Used Memory RSS|Bytes|Maximum||ShardId|
|serverLoad|Server Load|Procento|Maximum||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Maximum||ShardId|
|cacheRead|Cache Read|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Procento|Maximum||ShardId|
|cacheLatency|Cache Latency Microseconds (Preview)|Počet|Průměr||ShardId, SampleType|
|chyby|Chyby|Počet|Maximum||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed0|Total Operations (Shard 0)|Počet|Celkem||No Dimensions|
|cachehits0|Cache Hits (Shard 0)|Počet|Celkem||No Dimensions|
|cachemisses0|Cache Misses (Shard 0)|Počet|Celkem||No Dimensions|
|getcommands0|Gets (Shard 0)|Počet|Celkem||No Dimensions|
|setcommands0|Sets (Shard 0)|Počet|Celkem||No Dimensions|
|operationsPerSecond0|Operations Per Second (Shard 0)|Počet|Maximum||No Dimensions|
|evictedkeys0|Evicted Keys (Shard 0)|Počet|Celkem||No Dimensions|
|totalkeys0|Total Keys (Shard 0)|Počet|Maximum||No Dimensions|
|expiredkeys0|Expired Keys (Shard 0)|Počet|Celkem||No Dimensions|
|usedmemory0|Used Memory (Shard 0)|Bytes|Maximum||No Dimensions|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Bytes|Maximum||No Dimensions|
|serverLoad0|Server Load (Shard 0)|Procento|Maximum||No Dimensions|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime0|CPU (Shard 0)|Procento|Maximum||No Dimensions|
|connectedclients1|Connected Clients (Shard 1)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed1|Total Operations (Shard 1)|Počet|Celkem||No Dimensions|
|cachehits1|Cache Hits (Shard 1)|Počet|Celkem||No Dimensions|
|cachemisses1|Cache Misses (Shard 1)|Počet|Celkem||No Dimensions|
|getcommands1|Gets (Shard 1)|Počet|Celkem||No Dimensions|
|setcommands1|Sets (Shard 1)|Počet|Celkem||No Dimensions|
|operationsPerSecond1|Operations Per Second (Shard 1)|Počet|Maximum||No Dimensions|
|evictedkeys1|Evicted Keys (Shard 1)|Počet|Celkem||No Dimensions|
|totalkeys1|Total Keys (Shard 1)|Počet|Maximum||No Dimensions|
|expiredkeys1|Expired Keys (Shard 1)|Počet|Celkem||No Dimensions|
|usedmemory1|Used Memory (Shard 1)|Bytes|Maximum||No Dimensions|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Bytes|Maximum||No Dimensions|
|serverLoad1|Server Load (Shard 1)|Procento|Maximum||No Dimensions|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime1|CPU (Shard 1)|Procento|Maximum||No Dimensions|
|connectedclients2|Connected Clients (Shard 2)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed2|Total Operations (Shard 2)|Počet|Celkem||No Dimensions|
|cachehits2|Cache Hits (Shard 2)|Počet|Celkem||No Dimensions|
|cachemisses2|Cache Misses (Shard 2)|Počet|Celkem||No Dimensions|
|getcommands2|Gets (Shard 2)|Počet|Celkem||No Dimensions|
|setcommands2|Sets (Shard 2)|Počet|Celkem||No Dimensions|
|operationsPerSecond2|Operations Per Second (Shard 2)|Počet|Maximum||No Dimensions|
|evictedkeys2|Evicted Keys (Shard 2)|Počet|Celkem||No Dimensions|
|totalkeys2|Total Keys (Shard 2)|Počet|Maximum||No Dimensions|
|expiredkeys2|Expired Keys (Shard 2)|Počet|Celkem||No Dimensions|
|usedmemory2|Used Memory (Shard 2)|Bytes|Maximum||No Dimensions|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Bytes|Maximum||No Dimensions|
|serverLoad2|Server Load (Shard 2)|Procento|Maximum||No Dimensions|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead2|Cache Read (Shard 2)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime2|CPU (Shard 2)|Procento|Maximum||No Dimensions|
|connectedclients3|Connected Clients (Shard 3)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed3|Total Operations (Shard 3)|Počet|Celkem||No Dimensions|
|cachehits3|Cache Hits (Shard 3)|Počet|Celkem||No Dimensions|
|cachemisses3|Cache Misses (Shard 3)|Počet|Celkem||No Dimensions|
|getcommands3|Gets (Shard 3)|Počet|Celkem||No Dimensions|
|setcommands3|Sets (Shard 3)|Počet|Celkem||No Dimensions|
|operationsPerSecond3|Operations Per Second (Shard 3)|Počet|Maximum||No Dimensions|
|evictedkeys3|Evicted Keys (Shard 3)|Počet|Celkem||No Dimensions|
|totalkeys3|Total Keys (Shard 3)|Počet|Maximum||No Dimensions|
|expiredkeys3|Expired Keys (Shard 3)|Počet|Celkem||No Dimensions|
|usedmemory3|Used Memory (Shard 3)|Bytes|Maximum||No Dimensions|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Bytes|Maximum||No Dimensions|
|serverLoad3|Server Load (Shard 3)|Procento|Maximum||No Dimensions|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime3|CPU (Shard 3)|Procento|Maximum||No Dimensions|
|connectedclients4|Connected Clients (Shard 4)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed4|Total Operations (Shard 4)|Počet|Celkem||No Dimensions|
|cachehits4|Cache Hits (Shard 4)|Počet|Celkem||No Dimensions|
|cachemisses4|Cache Misses (Shard 4)|Počet|Celkem||No Dimensions|
|getcommands4|Gets (Shard 4)|Počet|Celkem||No Dimensions|
|setcommands4|Sets (Shard 4)|Počet|Celkem||No Dimensions|
|operationsPerSecond4|Operations Per Second (Shard 4)|Počet|Maximum||No Dimensions|
|evictedkeys4|Evicted Keys (Shard 4)|Počet|Celkem||No Dimensions|
|totalkeys4|Total Keys (Shard 4)|Počet|Maximum||No Dimensions|
|expiredkeys4|Expired Keys (Shard 4)|Počet|Celkem||No Dimensions|
|usedmemory4|Used Memory (Shard 4)|Bytes|Maximum||No Dimensions|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Bytes|Maximum||No Dimensions|
|serverLoad4|Server Load (Shard 4)|Procento|Maximum||No Dimensions|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime4|CPU (Shard 4)|Procento|Maximum||No Dimensions|
|connectedclients5|Connected Clients (Shard 5)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed5|Total Operations (Shard 5)|Počet|Celkem||No Dimensions|
|cachehits5|Cache Hits (Shard 5)|Počet|Celkem||No Dimensions|
|cachemisses5|Cache Misses (Shard 5)|Počet|Celkem||No Dimensions|
|getcommands5|Gets (Shard 5)|Počet|Celkem||No Dimensions|
|setcommands5|Sets (Shard 5)|Počet|Celkem||No Dimensions|
|operationsPerSecond5|Operations Per Second (Shard 5)|Počet|Maximum||No Dimensions|
|evictedkeys5|Evicted Keys (Shard 5)|Počet|Celkem||No Dimensions|
|totalkeys5|Total Keys (Shard 5)|Počet|Maximum||No Dimensions|
|expiredkeys5|Expired Keys (Shard 5)|Počet|Celkem||No Dimensions|
|usedmemory5|Used Memory (Shard 5)|Bytes|Maximum||No Dimensions|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Bytes|Maximum||No Dimensions|
|serverLoad5|Server Load (Shard 5)|Procento|Maximum||No Dimensions|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime5|CPU (Shard 5)|Procento|Maximum||No Dimensions|
|connectedclients6|Connected Clients (Shard 6)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed6|Total Operations (Shard 6)|Počet|Celkem||No Dimensions|
|cachehits6|Cache Hits (Shard 6)|Počet|Celkem||No Dimensions|
|cachemisses6|Cache Misses (Shard 6)|Počet|Celkem||No Dimensions|
|getcommands6|Gets (Shard 6)|Počet|Celkem||No Dimensions|
|setcommands6|Sets (Shard 6)|Počet|Celkem||No Dimensions|
|operationsPerSecond6|Operations Per Second (Shard 6)|Počet|Maximum||No Dimensions|
|evictedkeys6|Evicted Keys (Shard 6)|Počet|Celkem||No Dimensions|
|totalkeys6|Total Keys (Shard 6)|Počet|Maximum||No Dimensions|
|expiredkeys6|Expired Keys (Shard 6)|Počet|Celkem||No Dimensions|
|usedmemory6|Used Memory (Shard 6)|Bytes|Maximum||No Dimensions|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Bytes|Maximum||No Dimensions|
|serverLoad6|Server Load (Shard 6)|Procento|Maximum||No Dimensions|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime6|CPU (Shard 6)|Procento|Maximum||No Dimensions|
|connectedclients7|Connected Clients (Shard 7)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed7|Total Operations (Shard 7)|Počet|Celkem||No Dimensions|
|cachehits7|Cache Hits (Shard 7)|Počet|Celkem||No Dimensions|
|cachemisses7|Cache Misses (Shard 7)|Počet|Celkem||No Dimensions|
|getcommands7|Gets (Shard 7)|Počet|Celkem||No Dimensions|
|setcommands7|Sets (Shard 7)|Počet|Celkem||No Dimensions|
|operationsPerSecond7|Operations Per Second (Shard 7)|Počet|Maximum||No Dimensions|
|evictedkeys7|Evicted Keys (Shard 7)|Počet|Celkem||No Dimensions|
|totalkeys7|Total Keys (Shard 7)|Počet|Maximum||No Dimensions|
|expiredkeys7|Expired Keys (Shard 7)|Počet|Celkem||No Dimensions|
|usedmemory7|Used Memory (Shard 7)|Bytes|Maximum||No Dimensions|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Bytes|Maximum||No Dimensions|
|serverLoad7|Server Load (Shard 7)|Procento|Maximum||No Dimensions|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime7|CPU (Shard 7)|Procento|Maximum||No Dimensions|
|connectedclients8|Connected Clients (Shard 8)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed8|Total Operations (Shard 8)|Počet|Celkem||No Dimensions|
|cachehits8|Cache Hits (Shard 8)|Počet|Celkem||No Dimensions|
|cachemisses8|Cache Misses (Shard 8)|Počet|Celkem||No Dimensions|
|getcommands8|Gets (Shard 8)|Počet|Celkem||No Dimensions|
|setcommands8|Sets (Shard 8)|Počet|Celkem||No Dimensions|
|operationsPerSecond8|Operations Per Second (Shard 8)|Počet|Maximum||No Dimensions|
|evictedkeys8|Evicted Keys (Shard 8)|Počet|Celkem||No Dimensions|
|totalkeys8|Total Keys (Shard 8)|Počet|Maximum||No Dimensions|
|expiredkeys8|Expired Keys (Shard 8)|Počet|Celkem||No Dimensions|
|usedmemory8|Used Memory (Shard 8)|Bytes|Maximum||No Dimensions|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Bytes|Maximum||No Dimensions|
|serverLoad8|Server Load (Shard 8)|Procento|Maximum||No Dimensions|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime8|CPU (Shard 8)|Procento|Maximum||No Dimensions|
|connectedclients9|Connected Clients (Shard 9)|Počet|Maximum||No Dimensions|
|totalcommandsprocessed9|Total Operations (Shard 9)|Počet|Celkem||No Dimensions|
|cachehits9|Cache Hits (Shard 9)|Počet|Celkem||No Dimensions|
|cachemisses9|Cache Misses (Shard 9)|Počet|Celkem||No Dimensions|
|getcommands9|Gets (Shard 9)|Počet|Celkem||No Dimensions|
|setcommands9|Sets (Shard 9)|Počet|Celkem||No Dimensions|
|operationsPerSecond9|Operations Per Second (Shard 9)|Počet|Maximum||No Dimensions|
|evictedkeys9|Evicted Keys (Shard 9)|Počet|Celkem||No Dimensions|
|totalkeys9|Total Keys (Shard 9)|Počet|Maximum||No Dimensions|
|expiredkeys9|Expired Keys (Shard 9)|Počet|Celkem||No Dimensions|
|usedmemory9|Used Memory (Shard 9)|Bytes|Maximum||No Dimensions|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Bytes|Maximum||No Dimensions|
|serverLoad9|Server Load (Shard 9)|Procento|Maximum||No Dimensions|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime9|CPU (Shard 9)|Procento|Maximum||No Dimensions|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|No Dimensions|
|Síťové vstupy|Síťové vstupy|Bytes|Celkem|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|No Dimensions|
|Síťové výstupy|Síťové výstupy|Bytes|Celkem|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|No Dimensions|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Průměr|Average bytes read from disk during monitoring period.|No Dimensions|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Průměr|Average bytes written to disk during monitoring period.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Průměr|Disk Read IOPS.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Průměr|Disk Write IOPS.|No Dimensions|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|Bytes|Celkem|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|Bytes|Celkem|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|RoleInstanceId|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Průměr|Average bytes read from disk during monitoring period.|RoleInstanceId|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Průměr|Average bytes written to disk during monitoring period.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Průměr|Disk Read IOPS.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Průměr|Disk Write IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Počet|Celkem|Total number of calls.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Počet|Celkem|Number of successful calls.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Počet|Celkem|Total number of calls with error response (HTTP response code 4xx or 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Počet|Celkem|Number of calls that exceeded rate or quota limit.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Počet|Celkem|Number of calls with service internal error (HTTP response code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Počet|Celkem|Number of calls with client side error (HTTP response code 4xx).|ApiName, OperationName, Region|
|DataIn|Data In|Bytes|Celkem|Size of incoming data in bytes.|ApiName, OperationName, Region|
|DataOut|Data Out|Bytes|Celkem|Size of outgoing data in bytes.|ApiName, OperationName, Region|
|Latence|Latence|MilliSeconds|Průměr|Latency in milliseconds.|ApiName, OperationName, Region|
|CharactersTranslated|Characters Translated|Počet|Celkem|Total number of characters in incoming text request.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Počet|Celkem|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Speech Session Duration|Sekund|Celkem|Total duration of speech session in seconds.|ApiName, OperationName, Region|
|TotalTransactions|Total Transactions|Počet|Celkem|Total number of transactions.|No Dimensions|
|TotalTokenCalls|Total Token Calls|Počet|Celkem|Total number of token calls.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|No Dimensions|
|Síťové vstupy|Network In Billable|Bytes|Celkem|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Síťové výstupy|Network Out Billable|Bytes|Celkem|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Celkem|Bytes read from disk during monitoring period|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Celkem|Bytes written to disk during monitoring period|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Průměr|Disk Read IOPS|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Průměr|Disk Write IOPS|No Dimensions|
|CPU Credits Remaining|CPU Credits Remaining|Počet|Průměr|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Počet|Průměr|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Počet|Průměr|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Počet|Průměr|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Počet|Průměr|Data Disk Queue Depth(or Queue Length)|LUN|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Počet|Průměr|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Inbound Flows|Inbound Flows (Preview)|Počet|Průměr|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|No Dimensions|
|Outbound Flows|Outbound Flows (Preview)|Počet|Průměr|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|No Dimensions|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Průměr|The maximum creation rate of inbound flows (traffic going into the VM)|No Dimensions|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Průměr|The maximum creation rate of outbound flows (traffic going out of the VM)|No Dimensions|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procento|Průměr|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procento|Průměr|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procento|Průměr|Premium OS Disk Cache Read Hit|No Dimensions|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procento|Průměr|Premium OS Disk Cache Read Miss|No Dimensions|
|Network In Total|Network In Total|Bytes|Celkem|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Network Out Total|Network Out Total|Bytes|Celkem|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|VMName|
|Síťové vstupy|Network In Billable|Bytes|Celkem|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Síťové výstupy|Network Out Billable|Bytes|Celkem|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Disk Read Bytes|Disk Read Bytes|Bytes|Celkem|Bytes read from disk during monitoring period|VMName|
|Disk Write Bytes|Disk Write Bytes|Bytes|Celkem|Bytes written to disk during monitoring period|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Průměr|Disk Read IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Průměr|Disk Write IOPS|VMName|
|CPU Credits Remaining|CPU Credits Remaining|Počet|Průměr|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Počet|Průměr|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Počet|Průměr|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Počet|Průměr|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Počet|Průměr|Data Disk Queue Depth(or Queue Length)|LUN, VMName|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Průměr|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Průměr|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Průměr|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Počet|Průměr|OS Disk Queue Depth(or Queue Length)|VMName|
|Inbound Flows|Inbound Flows (Preview)|Počet|Průměr|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Počet|Průměr|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Průměr|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Průměr|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procento|Průměr|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procento|Průměr|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procento|Průměr|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procento|Průměr|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Bytes|Celkem|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Network Out Total|Network Out Total|Bytes|Celkem|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|CPU Usage|Počet|Průměr|CPU usage on all cores in millicores.|containerName|
|MemoryUsage|Memory Usage|Bytes|Průměr|Total memory usage in byte.|containerName|
|NetworkBytesReceivedPerSecond|Network Bytes Received Per Second|Bytes|Průměr|The network bytes received per second.|No Dimensions|
|NetworkBytesTransmittedPerSecond|Network Bytes Transmitted Per Second|Bytes|Průměr|The network bytes transmitted per second.|No Dimensions|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Počet|Průměr|Number of image pulls in total|No Dimensions|
|SuccessfulPullCount|Successful Pull Count|Počet|Průměr|Number of successful image pulls|No Dimensions|
|TotalPushCount|Total Push Count|Počet|Průměr|Number of image pushes in total|No Dimensions|
|SuccessfulPushCount|Successful Push Count|Počet|Průměr|Number of successful image pushes|No Dimensions|
|RunDuration|Run Duration|Milliseconds|Celkem|Run Duration in milliseconds|No Dimensions|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster|Počet|Celkem|Total number of available cpu cores in a managed cluster|No Dimensions|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster|Bytes|Celkem|Total amount of available memory in a managed cluster|No Dimensions|
|kube_pod_status_ready|Number of pods in Ready state|Počet|Celkem|Number of pods in Ready state|namespace, pod|
|kube_node_status_condition|Statuses for various node conditions|Počet|Celkem|Statuses for various node conditions|condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase|Počet|Celkem|Number of pods by phase|phase, namespace, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Počet|Celkem||No Dimensions|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Počet|Celkem||No Dimensions|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Počet|Celkem||No Dimensions|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Počet|Celkem||No Dimensions|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Sekund|Celkem||No Dimensions|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Počet|Celkem||No Dimensions|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Počet|Celkem||No Dimensions|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Sekund|Celkem||No Dimensions|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Počet|Celkem||No Dimensions|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Počet|Celkem||No Dimensions|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Sekund|Celkem||No Dimensions|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Sekund|Celkem||No Dimensions|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Sekund|Celkem||No Dimensions|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Sekund|Celkem||No Dimensions|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Sekund|Celkem||No Dimensions|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Sekund|Celkem||No Dimensions|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Sekund|Celkem||No Dimensions|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Sekund|Celkem||No Dimensions|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Sekund|Celkem||No Dimensions|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Sekund|Celkem||No Dimensions|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Sekund|Celkem||No Dimensions|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Sekund|Celkem||No Dimensions|
|ImportASAValuesFailed|Import ASA Values Failed Count|Počet|Celkem||No Dimensions|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Počet|Celkem||No Dimensions|
|DCIProfilesCount|Profile Instance Count|Počet|Last||No Dimensions|
|DCIInteractionsPerMonthCount|Interactions per Month Count|Počet|Last||No Dimensions|
|DCIKpisCount|KPI Count|Počet|Last||No Dimensions|
|DCISegmentsCount|Segment Count|Počet|Last||No Dimensions|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count|Počet|Last||No Dimensions|
|DCIPredictionsCount|Prediction Count|Počet|Last||No Dimensions|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|BytesPerSecond|Průměr|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|BytesPerSecond|Průměr|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Průměr|The download throughput to Azure from a share during the reporting period.|Sdílení|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|BytesPerSecond|Průměr|The upload throughput to Azure from a share during the reporting period.|Sdílení|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Bytes|Průměr|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Sdílení|
|TotalCapacity|Total Capacity|Bytes|Průměr|Total Capacity|No Dimensions|
|AvailableCapacity|Available Capacity|Bytes|Průměr|The available capacity in bytes during the reporting period.|No Dimensions|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Průměr|The cloud upload throughput  to Azure during the reporting period.|No Dimensions|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Průměr|The cloud download throughput to Azure during the reporting period.|No Dimensions|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Bytes|Průměr|The total number of bytes that is uploaded to Azure from a device during the reporting period.|No Dimensions|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Procento|Průměr|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Procento|Průměr|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Failed Runs|Počet|Celkem||pipelineName, activityName|
|SuccessfulRuns|Successful Runs|Počet|Celkem||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics|Počet|Celkem||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics|Počet|Celkem||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics|Počet|Celkem||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics|Počet|Celkem||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics|Počet|Celkem||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics|Počet|Celkem||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU utilization|Procento|Průměr||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime available memory|Bytes|Průměr||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Počet|Maximum||No Dimensions|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Počet|Maximum||No Dimensions|
|ResourceCount|Total entities count|Počet|Maximum||No Dimensions|
|FactorySizeInGbUnits|Total factory size (GB unit)|Počet|Maximum||No Dimensions|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Počet|Celkem|Count of successful jobs.|No Dimensions|
|JobEndedFailure|Failed Jobs|Počet|Celkem|Count of failed jobs.|No Dimensions|
|JobEndedCancelled|Canceled Jobs|Počet|Celkem|Count of canceled jobs.|No Dimensions|
|JobAUEndedSuccess|Successful AU Time|Sekund|Celkem|Total AU time for successful jobs.|No Dimensions|
|JobAUEndedFailure|Failed AU Time|Sekund|Celkem|Total AU time for failed jobs.|No Dimensions|
|JobAUEndedCancelled|Canceled AU Time|Sekund|Celkem|Total AU time for canceled jobs.|No Dimensions|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Úložiště celkem|Bytes|Maximum|Total amount of data stored in the account.|No Dimensions|
|DataWritten|Data Written|Bytes|Celkem|Total amount of data written to the account.|No Dimensions|
|DataRead|Data Read|Bytes|Celkem|Total amount of data read from the account.|No Dimensions|
|WriteRequests|Write Requests|Počet|Celkem|Count of data write requests to the account.|No Dimensions|
|ReadRequests|Read Requests|Počet|Celkem|Count of data read requests to the account.|No Dimensions|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procento|Průměr|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procento|Průměr|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procento|Průměr|IO percent|No Dimensions|
|storage_percent|Storage percent|Procento|Průměr|Storage percent|No Dimensions|
|storage_used|Využité úložiště|Bytes|Průměr|Využité úložiště|No Dimensions|
|storage_limit|Storage limit|Bytes|Průměr|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procento|Průměr|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Průměr|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Průměr|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Počet|Průměr|Active Connections|No Dimensions|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Počet|Průměr|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Průměr|Backup Storage used|No Dimensions|
|network_bytes_egress|Síťové výstupy|Bytes|Celkem|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Síťové vstupy|Bytes|Celkem|Network In across active connections|No Dimensions|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procento|Průměr|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procento|Průměr|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procento|Průměr|IO percent|No Dimensions|
|storage_percent|Storage percent|Procento|Průměr|Storage percent|No Dimensions|
|storage_used|Využité úložiště|Bytes|Průměr|Využité úložiště|No Dimensions|
|storage_limit|Storage limit|Bytes|Průměr|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procento|Průměr|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Průměr|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Průměr|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Počet|Průměr|Active Connections|No Dimensions|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Počet|Průměr|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Průměr|Backup Storage used|No Dimensions|
|network_bytes_egress|Síťové výstupy|Bytes|Celkem|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Síťové vstupy|Bytes|Celkem|Network In across active connections|No Dimensions|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procento|Průměr|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procento|Průměr|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procento|Průměr|IO percent|No Dimensions|
|storage_percent|Storage percent|Procento|Průměr|Storage percent|No Dimensions|
|storage_used|Využité úložiště|Bytes|Průměr|Využité úložiště|No Dimensions|
|storage_limit|Storage limit|Bytes|Průměr|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procento|Průměr|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Průměr|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Průměr|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Počet|Průměr|Active Connections|No Dimensions|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Průměr|Backup Storage used|No Dimensions|
|network_bytes_egress|Síťové výstupy|Bytes|Celkem|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Síťové vstupy|Bytes|Celkem|Network In across active connections|No Dimensions|
|pg_replica_log_delay_in_seconds|Replica Lag|Sekund|Maximum|Replica lag in seconds|No Dimensions|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Bytes|Maximum|Lag in bytes of the most lagging replica|No Dimensions|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procento|Průměr|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procento|Průměr|Memory percent|No Dimensions|
|iops|IOPS|Počet|Průměr|IO Operations per second|No Dimensions|
|storage_percent|Storage percent|Procento|Průměr|Storage percent|No Dimensions|
|storage_used|Využité úložiště|Bytes|Průměr|Využité úložiště|No Dimensions|
|active_connections|Active Connections|Počet|Průměr|Active Connections|No Dimensions|
|network_bytes_egress|Síťové výstupy|Bytes|Celkem|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Síťové vstupy|Bytes|Celkem|Network In across active connections|No Dimensions|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetry message send attempts|Počet|Celkem|Number of device-to-cloud telemetry messages attempted to be sent to your IoT hub|No Dimensions|
|d2c.telemetry.ingress.success|Telemetry messages sent|Počet|Celkem|Number of device-to-cloud telemetry messages sent successfully to your IoT hub|No Dimensions|
|c2d.commands.egress.complete.success|Commands completed|Počet|Celkem|Number of cloud-to-device commands completed successfully by the device|No Dimensions|
|c2d.commands.egress.abandon.success|Commands abandoned|Počet|Celkem|Number of cloud-to-device commands abandoned by the device|No Dimensions|
|c2d.commands.egress.reject.success|Commands rejected|Počet|Celkem|Number of cloud-to-device commands rejected by the device|No Dimensions|
|devices.totalDevices|Total devices (deprecated)|Počet|Celkem|Number of devices registered to your IoT hub|No Dimensions|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) |Počet|Celkem|Number of devices connected to your IoT hub|No Dimensions|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered|Počet|Celkem|The number of times messages were successfully delivered to all endpoints using IoT Hub routing. If a message is routed to multiple endpoints, this value increases by one for each successful delivery. If a message is delivered to the same endpoint multiple times, this value increases by one for each successful delivery.|No Dimensions|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped |Počet|Celkem|The number of times messages were dropped by IoT Hub routing due to dead endpoints. This value does not count messages delivered to fallback route as dropped messages are not delivered there.|No Dimensions|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned |Počet|Celkem|The number of times messages were orphaned by IoT Hub routing because they didn't match any routing rules (including the fallback rule). |No Dimensions|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible|Počet|Celkem|The number of times IoT Hub routing failed to deliver messages due to an incompatibility with the endpoint. This value does not include retries.|No Dimensions|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback|Počet|Celkem|The number of times IoT Hub routing delivered messages to the endpoint associated with the fallback route.|No Dimensions|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub|Počet|Celkem|The number of times IoT Hub routing successfully delivered messages to Event Hub endpoints.|No Dimensions|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub|Milliseconds|Průměr|The average latency (milliseconds) between message ingress to IoT Hub and message ingress into an Event Hub endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue|Počet|Celkem|The number of times IoT Hub routing successfully delivered messages to Service Bus queue endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue|Milliseconds|Průměr|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus queue endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic|Počet|Celkem|The number of times IoT Hub routing successfully delivered messages to Service Bus topic endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic|Milliseconds|Průměr|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus topic endpoint.|No Dimensions|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events|Počet|Celkem|The number of times IoT Hub routing successfully delivered messages to the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events|Milliseconds|Průměr|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.egress.storage|Routing: messages delivered to storage|Počet|Celkem|The number of times IoT Hub routing successfully delivered messages to storage endpoints.|No Dimensions|
|d2c.endpoints.latency.storage|Routing: message latency for storage|Milliseconds|Průměr|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a storage endpoint.|No Dimensions|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage|Bytes|Celkem|The amount of data (bytes) IoT Hub routing delivered to storage endpoints.|No Dimensions|
|d2c.endpoints.egress.storage.blobs|Routing: blobs delivered to storage|Počet|Celkem|The number of times IoT Hub routing delivered blobs to storage endpoints.|No Dimensions|
|EventGridDeliveries|Event Grid deliveries (preview)|Počet|Celkem|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Successful twin reads from devices|Počet|Celkem|The count of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.read.failure|Failed twin reads from devices|Počet|Celkem|The count of all failed device-initiated twin reads.|No Dimensions|
|d2c.twin.read.size|Response size of twin reads from devices|Bytes|Průměr|The average, min, and max of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.update.success|Successful twin updates from devices|Počet|Celkem|The count of all successful device-initiated twin updates.|No Dimensions|
|d2c.twin.update.failure|Failed twin updates from devices|Počet|Celkem|The count of all failed device-initiated twin updates.|No Dimensions|
|d2c.twin.update.size|Size of twin updates from devices|Bytes|Průměr|The average, min, and max size of all successful device-initiated twin updates.|No Dimensions|
|c2d.methods.success|Successful direct method invocations|Počet|Celkem|The count of all successful direct method calls.|No Dimensions|
|c2d.methods.failure|Failed direct method invocations|Počet|Celkem|The count of all failed direct method calls.|No Dimensions|
|c2d.methods.requestSize|Request size of direct method invocations|Bytes|Průměr|The average, min, and max of all successful direct method requests.|No Dimensions|
|c2d.methods.responseSize|Response size of direct method invocations|Bytes|Průměr|The average, min, and max of all successful direct method responses.|No Dimensions|
|c2d.twin.read.success|Successful twin reads from back end|Počet|Celkem|The count of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.failure|Failed twin reads from back end|Počet|Celkem|The count of all failed back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.size|Response size of twin reads from back end|Bytes|Průměr|The average, min, and max of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.update.success|Successful twin updates from back end|Počet|Celkem|The count of all successful back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.failure|Failed twin updates from back end|Počet|Celkem|The count of all failed back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.size|Size of twin updates from back end|Bytes|Průměr|The average, min, and max size of all successful back-end-initiated twin updates.|No Dimensions|
|twinQueries.success|Successful twin queries|Počet|Celkem|The count of all successful twin queries.|No Dimensions|
|twinQueries.failure|Failed twin queries|Počet|Celkem|The count of all failed twin queries.|No Dimensions|
|twinQueries.resultSize|Twin queries result size|Bytes|Průměr|The average, min, and max of the result size of all successful twin queries.|No Dimensions|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs|Počet|Celkem|The count of all successful creation of twin update jobs.|No Dimensions|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs|Počet|Celkem|The count of all failed creation of twin update jobs.|No Dimensions|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs|Počet|Celkem|The count of all successful creation of direct method invocation jobs.|No Dimensions|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs|Počet|Celkem|The count of all failed creation of direct method invocation jobs.|No Dimensions|
|jobs.listJobs.success|Successful calls to list jobs|Počet|Celkem|The count of all successful calls to list jobs.|No Dimensions|
|jobs.listJobs.failure|Failed calls to list jobs|Počet|Celkem|The count of all failed calls to list jobs.|No Dimensions|
|jobs.cancelJob.success|Successful job cancellations|Počet|Celkem|The count of all successful calls to cancel a job.|No Dimensions|
|jobs.cancelJob.failure|Failed job cancellations|Počet|Celkem|The count of all failed calls to cancel a job.|No Dimensions|
|jobs.queryJobs.success|Successful job queries|Počet|Celkem|The count of all successful calls to query jobs.|No Dimensions|
|jobs.queryJobs.failure|Failed job queries|Počet|Celkem|The count of all failed calls to query jobs.|No Dimensions|
|jobs.completed|Completed jobs|Počet|Celkem|The count of all completed jobs.|No Dimensions|
|jobs.failed|Failed jobs|Počet|Celkem|The count of all failed jobs.|No Dimensions|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors|Počet|Celkem|Number of throttling errors due to device throughput throttles|No Dimensions|
|dailyMessageQuotaUsed|Total number of messages used|Počet|Průměr|Number of total messages used today. This is a cumulative value that is reset to zero at 00:00 UTC every day.|No Dimensions|
|deviceDataUsage|Total device data usage|Bytes|Celkem|Bytes transferred to and from any devices connected to IotHub|No Dimensions|
|totalDeviceCount|Total devices (preview)|Počet|Průměr|Number of devices registered to your IoT hub|No Dimensions|
|connectedDeviceCount|Connected devices (preview)|Počet|Průměr|Number of devices connected to your IoT hub|No Dimensions|
|configurations|Configuration Metrics|Počet|Celkem|Metrics for Configuration Operations|No Dimensions|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts|Počet|Celkem|Number of device registrations attempted|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Devices assigned|Počet|Celkem|Number of devices assigned to an IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation attempts|Počet|Celkem|Number of device attestations attempted|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|AvailableStorage|Available Storage|Bytes|Celkem|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra Connection Closures|Počet|Celkem|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Počet|Celkem|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Počet|Počet|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Využití dat|Bytes|Celkem|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Document Count|Počet|Celkem|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Document Quota|Bytes|Celkem|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Index Usage|Bytes|Celkem|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Počet|Počet|Count of metadata requests. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge|Počet|Celkem|Mongo Request Units Consumed|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Requests|Počet|Počet|Number of Mongo Requests Made|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Zřízená propustnost|Počet|Maximum|Zřízená propustnost|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Průměr|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServiceAvailability|Service Availability|Procento|Průměr|Account requests availability at one hour, day or month granularity|No Dimensions|
|TotalRequestUnits|Total Request Units|Počet|Celkem|Request Units consumed|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Požadavky celkem|Počet|Počet|Number of requests made|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Počet|Celkem|Total events published to this topic|No Dimensions|
|PublishFailCount|Publish Failed Events|Počet|Celkem|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Počet|Celkem|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Počet|Celkem|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Matched Events|Počet|Celkem|Total events matched to this event subscription|No Dimensions|
|DeliveryAttemptFailCount|Delivery Failed Events|Počet|Celkem|Total events failed to deliver to this event subscription|Error, ErrorType|
|DeliverySuccessCount|Delivered Events|Počet|Celkem|Total events delivered to this event subscription|No Dimensions|
|DestinationProcessingDurationInMs|Destination Processing Duration|Milliseconds|Průměr|Destination processing duration in milliseconds|No Dimensions|
|DroppedEventCount|Dropped Events|Počet|Celkem|Total dropped events matching to this event subscription|DropReason|
|DeadLetteredCount|Dead Lettered Events|Počet|Celkem|Total dead lettered events matching to this event subscription|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Počet|Celkem|Total events published to this topic|No Dimensions|
|PublishFailCount|Failed Events|Počet|Celkem|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Počet|Celkem|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Počet|Celkem|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests|Počet|Celkem|Successful Requests for Microsoft.EventHub.|EntityName, |
|ServerErrors|Server Errors.|Počet|Celkem|Server Errors for Microsoft.EventHub.|EntityName, |
|UserErrors|User Errors.|Počet|Celkem|User Errors for Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Quota Exceeded Errors.|Počet|Celkem|Quota Exceeded Errors for Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Throttled Requests.|Počet|Celkem|Throttled Requests for Microsoft.EventHub.|EntityName, |
|IncomingRequests|Incoming Requests|Počet|Celkem|Incoming Requests for Microsoft.EventHub.|EntityName|
|IncomingMessages|Incoming Messages|Počet|Celkem|Incoming Messages for Microsoft.EventHub.|EntityName|
|OutgoingMessages|Outgoing Messages|Počet|Celkem|Outgoing Messages for Microsoft.EventHub.|EntityName|
|IncomingBytes|Incoming Bytes.|Bytes|Celkem|Incoming Bytes for Microsoft.EventHub.|EntityName|
|OutgoingBytes|Outgoing Bytes.|Bytes|Celkem|Outgoing Bytes for Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Počet|Průměr|Total Active Connections for Microsoft.EventHub.|No Dimensions|
|ConnectionsOpened|Connections Opened.|Počet|Průměr|Connections Opened for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connections Closed.|Počet|Průměr|Connections Closed for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Počet|Celkem|Capture Backlog for Microsoft.EventHub.|EntityName|
|CapturedMessages|Captured Messages.|Počet|Celkem|Captured Messages for Microsoft.EventHub.|EntityName|
|CapturedBytes|Captured Bytes.|Bytes|Celkem|Captured Bytes for Microsoft.EventHub.|EntityName|
|Velikost|Velikost|Bytes|Průměr|Size of an EventHub in Bytes.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Počet|Celkem|Total incoming send requests for a namespace (Deprecated)|No Dimensions|
|SUCCREQ|Successful Requests (Deprecated)|Počet|Celkem|Total successful requests for a namespace (Deprecated)|No Dimensions|
|FAILREQ|Failed Requests (Deprecated)|Počet|Celkem|Total failed requests for a namespace (Deprecated)|No Dimensions|
|SVRBSY|Server Busy Errors (Deprecated)|Počet|Celkem|Total server busy errors for a namespace (Deprecated)|No Dimensions|
|INTERR|Internal Server Errors (Deprecated)|Počet|Celkem|Total internal server errors for a namespace (Deprecated)|No Dimensions|
|MISCERR|Other Errors (Deprecated)|Počet|Celkem|Total failed requests for a namespace (Deprecated)|No Dimensions|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Počet|Celkem|Total incoming messages for a namespace. This metric is deprecated. Please use Incoming Messages metric instead (Deprecated)|No Dimensions|
|EHINMSGS|Incoming Messages (Deprecated)|Počet|Celkem|Total incoming messages for a namespace (Deprecated)|No Dimensions|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Počet|Celkem|Total outgoing messages for a namespace. This metric is deprecated. Please use Outgoing Messages metric instead (Deprecated)|No Dimensions|
|EHOUTMSGS|Outgoing Messages (Deprecated)|Počet|Celkem|Total outgoing messages for a namespace (Deprecated)|No Dimensions|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Bytes|Celkem|Event Hub incoming message throughput for a namespace. This metric is deprecated. Please use Incoming bytes metric instead (Deprecated)|No Dimensions|
|EHINBYTES|Incoming bytes (Deprecated)|Bytes|Celkem|Event Hub incoming message throughput for a namespace (Deprecated)|No Dimensions|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Bytes|Celkem|Event Hub outgoing message throughput for a namespace. This metric is deprecated. Please use Outgoing bytes metric instead (Deprecated)|No Dimensions|
|EHOUTBYTES|Outgoing bytes (Deprecated)|Bytes|Celkem|Event Hub outgoing message throughput for a namespace (Deprecated)|No Dimensions|
|EHABL|Archive backlog messages (Deprecated)|Počet|Celkem|Event Hub archive messages in backlog for a namespace (Deprecated)|No Dimensions|
|EHAMSGS|Archive messages (Deprecated)|Počet|Celkem|Event Hub archived messages in a namespace (Deprecated)|No Dimensions|
|EHAMBS|Archive message throughput (Deprecated)|Bytes|Celkem|Event Hub archived message throughput in a namespace (Deprecated)|No Dimensions|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Počet|Celkem|Successful Requests for Microsoft.EventHub. (Preview)|No Dimensions|
|ServerErrors|Server Errors. (Preview)|Počet|Celkem|Server Errors for Microsoft.EventHub. (Preview)|No Dimensions|
|UserErrors|User Errors. (Preview)|Počet|Celkem|User Errors for Microsoft.EventHub. (Preview)|No Dimensions|
|QuotaExceededErrors|Quota Exceeded Errors. (Preview)|Počet|Celkem|Quota Exceeded Errors for Microsoft.EventHub. (Preview)|No Dimensions|
|ThrottledRequests|Throttled Requests. (Preview)|Počet|Celkem|Throttled Requests for Microsoft.EventHub. (Preview)|No Dimensions|
|IncomingRequests|Incoming Requests (Preview)|Počet|Celkem|Incoming Requests for Microsoft.EventHub. (Preview)|No Dimensions|
|IncomingMessages|Incoming Messages (Preview)|Počet|Celkem|Incoming Messages for Microsoft.EventHub. (Preview)|No Dimensions|
|OutgoingMessages|Outgoing Messages (Preview)|Počet|Celkem|Outgoing Messages for Microsoft.EventHub. (Preview)|No Dimensions|
|IncomingBytes|Incoming Bytes. (Preview)|Bytes|Celkem|Incoming Bytes for Microsoft.EventHub. (Preview)|No Dimensions|
|OutgoingBytes|Outgoing Bytes. (Preview)|Bytes|Celkem|Outgoing Bytes for Microsoft.EventHub. (Preview)|No Dimensions|
|ActiveConnections|ActiveConnections (Preview)|Počet|Průměr|Total Active Connections for Microsoft.EventHub. (Preview)|No Dimensions|
|ConnectionsOpened|Connections Opened. (Preview)|Počet|Průměr|Connections Opened for Microsoft.EventHub. (Preview)|No Dimensions|
|ConnectionsClosed|Connections Closed. (Preview)|Počet|Průměr|Connections Closed for Microsoft.EventHub. (Preview)|No Dimensions|
|CaptureBacklog|Capture Backlog. (Preview)|Počet|Celkem|Capture Backlog for Microsoft.EventHub. (Preview)|No Dimensions|
|CapturedMessages|Captured Messages. (Preview)|Počet|Celkem|Captured Messages for Microsoft.EventHub. (Preview)|No Dimensions|
|CapturedBytes|Captured Bytes. (Preview)|Bytes|Celkem|Captured Bytes for Microsoft.EventHub. (Preview)|No Dimensions|
|Procesor|CPU (Preview)|Procento|Maximum|CPU utilization for the Event Hub Cluster as a percentage|Role|
|AvailableMemory|Available Memory (Preview)|Počet|Maximum|Available memory for the Event Hub Cluster in bytes|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Gateway Requests|Počet|Celkem|Number of gateway requests|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Categorized Gateway Requests|Počet|Celkem|Number of gateway requests by categories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Počet|Maximum|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Observed Metric Value|Počet|Průměr|The value computed by autoscale when executed|MetricTriggerSource|
|MetricThreshold|Metric Threshold|Počet|Průměr|The configured autoscale threshold when autoscale ran.|MetricTriggerRule|
|ObservedCapacity|Observed Capacity|Počet|Průměr|The capacity reported to autoscale when it executed.|No Dimensions|
|ScaleActionsInitiated|Scale Actions Initiated|Počet|Celkem|The direction of the scale operation.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Průměr|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Testy dostupnosti|Počet|Počet|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Průměr|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Page load network connect time|MilliSeconds|Průměr|Time between user request and network connection. Includes DNS lookup and transport connection.|No Dimensions|
|browserTimings/processingDuration|Client processing time|MilliSeconds|Průměr|Time between receiving the last byte of a document until the DOM is loaded. Async requests may still be processing.|No Dimensions|
|browserTimings/receiveDuration|Receiving response time|MilliSeconds|Průměr|Time between the first and last bytes, or until disconnection.|No Dimensions|
|browserTimings/sendDuration|Send request time|MilliSeconds|Průměr|Time between network connection and receiving the first byte.|No Dimensions|
|browserTimings/totalDuration|Browser page load time|MilliSeconds|Průměr|Time from user request until DOM, stylesheets, scripts and images are loaded.|No Dimensions|
|dependencies/count|Dependency calls|Počet|Počet|Count of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dependency duration|MilliSeconds|Průměr|Duration of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Počet|Počet|Count of failed dependency calls made by the application to external resources.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Page views|Počet|Počet|Count of page views.|operation/synthetic|
|pageViews/duration|Page view load time|MilliSeconds|Průměr|Page view load time|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP request execution time|MilliSeconds|Průměr|Execution time of the most recent request.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue|Počet|Průměr|Length of the application request queue.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP request rate|CountPerSecond|Průměr|Rate of all requests to the application per second from ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Exception rate|CountPerSecond|Průměr|Count of handled and unhandled exceptions reported to windows, including .NET exceptions and unmanaged exceptions that are converted into .NET exceptions.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process IO rate|BytesPerSecond|Průměr|Total bytes per second read and written to files, network and devices.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Process CPU|Procento|Průměr|The percentage of elapsed time that all process threads used the processor to execute instructions. This can vary between 0 to 100. This metric indicates the performance of w3wp process alone.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor time|Procento|Průměr|The percentage of time that the processor spends in non-idle threads.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bytes|Průměr|Physical memory immediately available for allocation to a process or for system use.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Process private bytes|Bytes|Průměr|Memory exclusively assigned to the monitored application's processes.|cloud/roleInstance|
|requests/duration|Server response time|MilliSeconds|Průměr|Time between receiving an HTTP request and finishing sending the response.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Server requests|Počet|Počet|Count of HTTP requests completed.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Neúspěšné požadavky|Počet|Počet|Count of HTTP requests marked as failed. In most cases these are requests with a response code >= 400 and not equal to 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|CountPerSecond|Průměr|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Výjimky|Počet|Počet|Combined count of all uncaught exceptions.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Výjimky prohlížečů|Počet|Počet|Count of uncaught exceptions thrown in the browser.|No Dimensions|
|exceptions/server|Server exceptions|Počet|Počet|Count of uncaught exceptions thrown in the server application.|cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Počet|Počet|Trace document count|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Total Service Api Hits|Počet|Počet|Number of total service api hits|ActivityType, ActivityName|
|ServiceApiLatency|Overall Service Api Latency|Milliseconds|Průměr|Overall latency of service api requests|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total Service Api Results|Počet|Počet|Number of total service api results|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization|Procento|Průměr|Utilization level in the cluster scope|Žádné|
|QueryDuration|Query Duration|Milliseconds|Průměr|Queries’ duration in seconds|Query Status|
|IngestionUtilization|Ingestion Utilization|Procento|Průměr|Ratio of used ingestion slots in the cluster|Žádné|
|KeepAlive|Keep Alive|Počet|Průměr|Sanity check indicates the cluster responds to queries|Žádné|
|IngestionVolumeInMB|Ingestion Volume (In MB)|Počet|Celkem|Overall volume of ingested data to the cluster (in MB)|Databáze|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds)|Sekund|Průměr|Ingestion time from the source (e.g. message is in EventHub) to the cluster in seconds|Žádné|
|EventProcessedForEventHubs|Events Processed (for Event Hubs)|Počet|Celkem|Number of events processed by the cluster when ingesting from Event Hub|Žádné|
|IngestionResult|Ingestion Result|Počet|Počet|Number of ingestion operations|Stav|
|Procesor|Procesor|Procento|Průměr|CPU utilization level|Žádné|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Počet | Celkem | Number of records exported for every storage artifact written during the export operation  | Žádné |
| ExportUtilization | Export Utilization | Procento | Maximum | Export utilization | Žádné |
| ContinuousExportPendingCount | Continuous Export Pending Count | Počet | Maximum | The number of pending continuous export jobs ready for execution | Žádné |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Počet | Maximum | The max time in minutes of all continuous exports which are pending and ready for execution | Žádné |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Počet|Počet|Count of API calls|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Počet|Celkem|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Počet|Celkem|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Počet|Celkem|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Počet|Celkem|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Počet|Celkem|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Sekund|Průměr|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Sekund|Průměr|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Počet|Celkem|Number of workflow action or trigger throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procento|Celkem|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Počet|Celkem|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Počet|Celkem|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Počet|Celkem|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed|Počet|Celkem|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Počet|Celkem|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Sekund|Průměr|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Sekund|Průměr|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Počet|Celkem|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Počet|Celkem|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Počet|Celkem|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Počet|Celkem|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Počet|Celkem|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Počet|Celkem|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Počet|Celkem|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Sekund|Průměr|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Sekund|Průměr|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Sekund|Průměr|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Počet|Celkem|Number of workflow trigger throttled events.|No Dimensions|
|BillableActionExecutions|Billable Action Executions|Počet|Celkem|Number of workflow action executions getting billed.|No Dimensions|
|BillableTriggerExecutions|Billable Trigger Executions|Počet|Celkem|Number of workflow trigger executions getting billed.|No Dimensions|
|TotalBillableExecutions|Total Billable Executions|Počet|Celkem|Number of workflow executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Počet|Celkem|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Počet|Celkem|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Počet|Celkem|Number of storage consumption executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Počet|Celkem|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Počet|Celkem|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Počet|Celkem|Number of storage consumption executions getting billed.|No Dimensions|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Počet|Celkem|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Počet|Celkem|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Počet|Celkem|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Počet|Celkem|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Počet|Celkem|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Sekund|Průměr|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Sekund|Průměr|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Počet|Celkem|Number of workflow action or trigger throttled events.|No Dimensions|
|RunStartThrottledEvents|Run Start Throttled Events|Počet|Celkem|Number of workflow run start throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procento|Celkem|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Počet|Celkem|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Počet|Celkem|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Počet|Celkem|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed |Počet|Celkem|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Počet|Celkem|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Sekund|Průměr|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Sekund|Průměr|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Počet|Celkem|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Počet|Celkem|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Počet|Celkem|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Počet|Celkem|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Počet|Celkem|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Počet|Celkem|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Počet|Celkem|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Sekund|Průměr|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Sekund|Průměr|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Sekund|Průměr|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Počet|Celkem|Number of workflow trigger throttled events.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Procento|Průměr|Workflow processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Procento|Průměr|Workflow memory usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Procento|Průměr|Connector processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Procento|Průměr|Connector memory usage for integration service environment.|No Dimensions|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Počet|Celkem|Number of runs completed successfully for this workspace|Scénář|
|Started Runs|Started Runs|Počet|Celkem|Number of runs started for this workspace|Scénář|
|Failed Runs|Failed Runs|Počet|Celkem|Number of runs failed for this workspace|Scénář|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Počet|Počet|Count of API calls|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostupnost|Dostupnost|Procento|Průměr|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency|ms/op|Průměr|Average other latency (that is not read or write) in milliseconds per operation|No Dimensions|
|AverageReadLatency|Average read latency|ms/op|Průměr|Average read latency in milliseconds per operation|No Dimensions|
|AverageTotalLatency|Average total latency|ms/op|Průměr|Average total latency in milliseconds per operation|No Dimensions|
|AverageWriteLatency|Average write latency|ms/op|Průměr|Average write latency in milliseconds per operation|No Dimensions|
|FilesystemOtherOps|Filesystem other ops|ops|Průměr|Number of filesystem other operations (that is not read or write)|No Dimensions|
|FilesystemReadOps|Filesystem read ops|ops|Průměr|Number of filesystem read operations|No Dimensions|
|FilesystemTotalOps|Filesystem total ops|ops|Průměr|Sum of all filesystem operations|No Dimensions|
|FilesystemWriteOps|Filesystem write ops|ops|Průměr|Number of filesystem write operations|No Dimensions|
|IoBytesPerOtherOps|Io bytes per other ops|bytes/op|Průměr|Number of In/out bytes per other operations (that is not read or write)|No Dimensions|
|IoBytesPerReadOps|Io bytes per read ops|bytes/op|Průměr|Number of In/out bytes per read operation|No Dimensions|
|IoBytesPerTotalOps|Io bytes per op across all operations|bytes/op|Průměr|Sum of all In/out bytes operation|No Dimensions|
|IoBytesPerWriteOps|Io bytes per write ops|bytes/op|Průměr|Number of In/out bytes per write operation|No Dimensions|
|OtherIops|Other iops|operations/second|Průměr|Other In/out operation per second|No Dimensions|
|OtherThroughput|Other throughput|MBps|Průměr|Other throughput (that is not read or write) in megabytes per second|No Dimensions|
|ReadIops|Read iops|operations/second|Průměr|Read In/out operations per second|No Dimensions|
|ReadThroughput|Read throughput|MBps|Průměr|Read throughput in megabytes per second|No Dimensions|
|TotalIops|Total iops|operations/second|Průměr|Sum of all In/out operations per second|No Dimensions|
|TotalThroughput|Total throughput|MBps|Průměr|Sum of all throughput in megabytes per second|No Dimensions|
|VolumeAllocatedSize|Volume allocated size|bytes|Průměr|Allocated size of the volume (Not the actual used bytes)|No Dimensions|
|VolumeLogicalSize|Volume logical size|bytes|Průměr|Logical size of the volume (used bytes)|No Dimensions|
|VolumeSnapshotSize|Volume snapshot size|bytes|Průměr|Size of all snapshots in volume|No Dimensions|
|WriteIops|Write iops|operations/second|Průměr|Write In/out operations per second|No Dimensions|
|WriteThroughput|Write throughput|MBps|Průměr|Write throughput in megabytes per second|No Dimensions|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size|bytes|Průměr|Allocated size of the pool (Not the actual used bytes)|No Dimensions|
|VolumePoolAllocatedUsed|Volume pool allocated used|bytes|Průměr|Allocated used size of the pool|No Dimensions|
|VolumePoolTotalLogicalSize|Volume pool total logical size|bytes|Průměr|Sum of the logical size of all the volumes belonging to the pool|No Dimensions|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size|bytes|Průměr|Sum of all snapshots in pool|No Dimensions|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Sent|Počet|Celkem|Number of bytes the Network Interface sent|No Dimensions|
|BytesReceivedRate|Bytes Received|Počet|Celkem|Number of bytes the Network Interface received|No Dimensions|
|PacketsSentRate|Packets Sent|Počet|Celkem|Number of packets the Network Interface sent|No Dimensions|
|PacketsReceivedRate|Packets Received|Počet|Celkem|Number of packets the Network Interface received|No Dimensions|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability|Počet|Průměr|Average Load Balancer data path availability per time duration|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status|Počet|Průměr|Average Load Balancer health probe status per time duration|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byte Count|Počet|Celkem|Total number of Bytes transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Packet Count|Počet|Celkem|Total number of Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN Count|Počet|Celkem|Total number of SYN Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Počet|Celkem|Total number of new SNAT connections created within time period|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview)|Počet|Celkem|Total number of SNAT ports allocated within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview)|Počet|Celkem|Total number of SNAT ports used within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Query Volume|Počet|Celkem|Number of queries served for a DNS zone|No Dimensions|
|RecordSetCount|Record Set Count|Počet|Maximum|Number of Record Sets in a DNS zone|No Dimensions|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Procento|Maximum|Percent of Record Set capacity utilized by a DNS zone|No Dimensions|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|Inbound packets DDoS|CountPerSecond|Maximum|Inbound packets DDoS|No Dimensions|
|PacketsDroppedDDoS|Inbound packets dropped DDoS|CountPerSecond|Maximum|Inbound packets dropped DDoS|No Dimensions|
|PacketsForwardedDDoS|Inbound packets forwarded DDoS|CountPerSecond|Maximum|Inbound packets forwarded DDoS|No Dimensions|
|TCPPacketsInDDoS|Inbound TCP packets DDoS|CountPerSecond|Maximum|Inbound TCP packets DDoS|No Dimensions|
|TCPPacketsDroppedDDoS|Inbound TCP packets dropped DDoS|CountPerSecond|Maximum|Inbound TCP packets dropped DDoS|No Dimensions|
|TCPPacketsForwardedDDoS|Inbound TCP packets forwarded DDoS|CountPerSecond|Maximum|Inbound TCP packets forwarded DDoS|No Dimensions|
|UDPPacketsInDDoS|Inbound UDP packets DDoS|CountPerSecond|Maximum|Inbound UDP packets DDoS|No Dimensions|
|UDPPacketsDroppedDDoS|Inbound UDP packets dropped DDoS|CountPerSecond|Maximum|Inbound UDP packets dropped DDoS|No Dimensions|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS|CountPerSecond|Maximum|Inbound UDP packets forwarded DDoS|No Dimensions|
|BytesInDDoS|Inbound bytes DDoS|BytesPerSecond|Maximum|Inbound bytes DDoS|No Dimensions|
|BytesDroppedDDoS|Inbound bytes dropped DDoS|BytesPerSecond|Maximum|Inbound bytes dropped DDoS|No Dimensions|
|BytesForwardedDDoS|Inbound bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound bytes forwarded DDoS|No Dimensions|
|TCPBytesInDDoS|Inbound TCP bytes DDoS|BytesPerSecond|Maximum|Inbound TCP bytes DDoS|No Dimensions|
|TCPBytesDroppedDDoS|Inbound TCP bytes dropped DDoS|BytesPerSecond|Maximum|Inbound TCP bytes dropped DDoS|No Dimensions|
|TCPBytesForwardedDDoS|Inbound TCP bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound TCP bytes forwarded DDoS|No Dimensions|
|UDPBytesInDDoS|Inbound UDP bytes DDoS|BytesPerSecond|Maximum|Inbound UDP bytes DDoS|No Dimensions|
|UDPBytesDroppedDDoS|Inbound UDP bytes dropped DDoS|BytesPerSecond|Maximum|Inbound UDP bytes dropped DDoS|No Dimensions|
|UDPBytesForwardedDDoS|Inbound UDP bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound UDP bytes forwarded DDoS|No Dimensions|
|IfUnderDDoSAttack|Under DDoS attack or not|Počet|Maximum|Under DDoS attack or not|No Dimensions|
|DDoSTriggerTCPPackets|Inbound TCP packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound TCP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerUDPPackets|Inbound UDP packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound UDP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound SYN packets to trigger DDoS mitigation|No Dimensions|
|VipAvailability|Data Path Availability|Počet|Průměr|Average IP Address availability per time duration|Port|
|ByteCount|Byte Count|Počet|Celkem|Total number of Bytes transmitted within time period|Port, Direction|
|PacketCount|Packet Count|Počet|Celkem|Total number of Packets transmitted within time period|Port, Direction|
|SynCount|SYN Count|Počet|Celkem|Total number of SYN Packets transmitted within time period|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Počet|Celkem|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Počet|Celkem|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|Celkem|Number of bytes per second the Application Gateway has served|No Dimensions|
|UnhealthyHostCount|Unhealthy Host Count|Počet|Průměr|Number of unhealthy backend hosts|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count|Počet|Průměr|Number of healthy backend hosts|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Počet|Celkem|Count of successful requests that Application Gateway has served|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Počet|Celkem|Count of failed requests that Application Gateway has served|BackendSettingsPool|
|ResponseStatus|Response Status|Počet|Celkem|Http response status returned by Application Gateway|HttpStatusGroup|
|CurrentConnections|Current Connections|Počet|Celkem|Count of current connections established with Application Gateway|No Dimensions|
|CapacityUnits|Current Capacity Units|Počet|Průměr|Capacity Units consumed|No Dimensions|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth|BytesPerSecond|Průměr|Average site-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SBandwidth|Gateway P2S Bandwidth|BytesPerSecond|Průměr|Average point-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SConnectionCount|P2S Connection Count|Počet|Maximum|Point-to-site connection count of a gateway|Protocol (Protokol)|
|TunnelAverageBandwidth|Tunnel Bandwidth|BytesPerSecond|Průměr|Average bandwidth of a tunnel in bytes per second|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes|Bytes|Celkem|Outgoing bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Celkem|Incoming bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets|Počet|Celkem|Outgoing packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets|Počet|Celkem|Incoming packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Počet|Celkem|Outgoing packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Počet|Celkem|Incoming packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bits ingressing Azure per second|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bits egressing Azure per second|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned|Počet|Celkem|Number of times a Traffic Manager endpoint was returned in the given time frame|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint|Počet|Maximum|1 if an endpoint's probe status is "Enabled", 0 otherwise.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Probes Failed|Procento|Průměr|% of connectivity monitoring probes failed|No Dimensions|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Průměr|Average network round-trip time (ms) for connectivity monitoring probes sent between source and destination|No Dimensions|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Počet|Celkem|The number of client requests served by the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Request Size|Bytes|Celkem|The number of bytes sent as requests from clients to the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Response Size|Bytes|Celkem|The number of bytes sent as responses from HTTP/S proxy to clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Backend Request Count|Počet|Celkem|The number of requests sent from the HTTP/S proxy to backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency|MilliSeconds|Průměr|The time calculated from when the request was sent by the HTTP/S proxy to the backend until the HTTP/S proxy received the last response byte from the backend|Backend|
|TotalLatency|Total Latency|MilliSeconds|Průměr|The time calculated from when the client request was received by the HTTP/S proxy until the client acknowledged the last response byte from the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Backend Health Percentage|Procento|Průměr|The percentage of successful health probes from the HTTP/S proxy to backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count|Počet|Celkem|The number of client requests processed by the Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|registration.all|Registration Operations|Počet|Celkem|The count of all successful registration operations (creations updates queries and deletions). |No Dimensions|
|registration.create|Registration Create Operations|Počet|Celkem|The count of all successful registration creations.|No Dimensions|
|registration.update|Registration Update Operations|Počet|Celkem|The count of all successful registration updates.|No Dimensions|
|registration.get|Registration Read Operations|Počet|Celkem|The count of all successful registration queries.|No Dimensions|
|registration.delete|Registration Delete Operations|Počet|Celkem|The count of all successful registration deletions.|No Dimensions|
|incoming|Incoming Messages|Počet|Celkem|The count of all successful send API calls. |No Dimensions|
|incoming.scheduled|Scheduled Push Notifications Sent|Počet|Celkem|Scheduled Push Notifications Canceled|No Dimensions|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Počet|Celkem|Scheduled Push Notifications Canceled|No Dimensions|
|scheduled.pending|Pending Scheduled Notifications|Počet|Celkem|Pending Scheduled Notifications|No Dimensions|
|installation.all|Installation Management Operations|Počet|Celkem|Installation Management Operations|No Dimensions|
|installation.get|Get Installation Operations|Počet|Celkem|Get Installation Operations|No Dimensions|
|installation.upsert|Create or Update Installation Operations|Počet|Celkem|Create or Update Installation Operations|No Dimensions|
|installation.patch|Patch Installation Operations|Počet|Celkem|Patch Installation Operations|No Dimensions|
|installation.delete|Delete Installation Operations|Počet|Celkem|Delete Installation Operations|No Dimensions|
|outgoing.allpns.success|Successful notifications|Počet|Celkem|The count of all successful notifications.|No Dimensions|
|outgoing.allpns.invalidpayload|Payload Errors|Počet|Celkem|The count of pushes that failed because the PNS returned a bad payload error.|No Dimensions|
|outgoing.allpns.pnserror|External Notification System Errors|Počet|Celkem|The count of pushes that failed because there was a problem communicating with the PNS (excludes authentication problems).|No Dimensions|
|outgoing.allpns.channelerror|Channel Errors|Počet|Celkem|The count of pushes that failed because the channel was invalid not associated with the correct app throttled or expired.|No Dimensions|
|outgoing.allpns.badorexpiredchannel|Bad or Expired Channel Errors|Počet|Celkem|The count of pushes that failed because the channel/token/registrationId in the registration was expired or invalid.|No Dimensions|
|outgoing.wns.success|WNS Successful Notifications|Počet|Celkem|The count of all successful notifications.|No Dimensions|
|outgoing.wns.invalidcredentials|WNS Authorization Errors (Invalid Credentials)|Počet|Celkem|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked. (Windows Live does not recognize the credentials).|No Dimensions|
|outgoing.wns.badchannel|WNS Bad Channel Error|Počet|Celkem|The count of pushes that failed because the ChannelURI in the registration was not recognized (WNS status: 404 not found).|No Dimensions|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Počet|Celkem|The count of pushes that failed because the ChannelURI is expired (WNS status: 410 Gone).|No Dimensions|
|outgoing.wns.throttled|WNS Throttled Notifications|Počet|Celkem|The count of pushes that failed because WNS is throttling this app (WNS status: 406 Not Acceptable).|No Dimensions|
|outgoing.wns.tokenproviderunreachable|WNS Authorization Errors (Unreachable)|Počet|Celkem|Windows Live is not reachable.|No Dimensions|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Počet|Celkem|The token provided to WNS is not valid (WNS status: 401 Unauthorized).|No Dimensions|
|outgoing.wns.wrongtoken|WNS Authorization Errors (Wrong Token)|Počet|Celkem|The token provided to WNS is valid but for another application (WNS status: 403 Forbidden). This can happen if the ChannelURI in the registration is associated with another app. Check that the client app is associated with the same app whose credentials are in the notification hub.|No Dimensions|
|outgoing.wns.invalidnotificationformat|WNS Invalid Notification Format|Počet|Celkem|The format of the notification is invalid (WNS status: 400). Note that WNS does not reject all invalid payloads.|No Dimensions|
|outgoing.wns.invalidnotificationsize|WNS Invalid Notification Size Error|Počet|Celkem|The notification payload is too large (WNS status: 413).|No Dimensions|
|outgoing.wns.channelthrottled|WNS Channel Throttled|Počet|Celkem|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-NotificationStatus:channelThrottled).|No Dimensions|
|outgoing.wns.channeldisconnected|WNS Channel Disconnected|Počet|Celkem|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.dropped|WNS Dropped Notifications|Počet|Celkem|The notification was dropped because the ChannelURI in the registration is throttled (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.pnserror|WNS Errors|Počet|Celkem|Notification not delivered because of errors communicating with WNS.|No Dimensions|
|outgoing.wns.authenticationerror|WNS Authentication Errors|Počet|Celkem|Notification not delivered because of errors communicating with Windows Live invalid credentials or wrong token.|No Dimensions|
|outgoing.apns.success|APNS Successful Notifications|Počet|Celkem|The count of all successful notifications.|No Dimensions|
|outgoing.apns.invalidcredentials|APNS Authorization Errors|Počet|Celkem|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.apns.badchannel|APNS Bad Channel Error|Počet|Celkem|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|No Dimensions|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Počet|Celkem|The count of token that were invalidated by the APNS feedback channel.|No Dimensions|
|outgoing.apns.invalidnotificationsize|APNS Invalid Notification Size Error|Počet|Celkem|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|No Dimensions|
|outgoing.apns.pnserror|APNS Errors|Počet|Celkem|The count of pushes that failed because of errors communicating with APNS.|No Dimensions|
|outgoing.gcm.success|GCM Successful Notifications|Počet|Celkem|The count of all successful notifications.|No Dimensions|
|outgoing.gcm.invalidcredentials|GCM Authorization Errors (Invalid Credentials)|Počet|Celkem|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.gcm.badchannel|GCM Bad Channel Error|Počet|Celkem|The count of pushes that failed because the registrationId in the registration was not recognized (GCM result: Invalid Registration).|No Dimensions|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Počet|Celkem|The count of pushes that failed because the registrationId in the registration was expired (GCM result: NotRegistered).|No Dimensions|
|outgoing.gcm.throttled|GCM Throttled Notifications|Počet|Celkem|The count of pushes that failed because GCM throttled this app (GCM status code: 501-599 or result:Unavailable).|No Dimensions|
|outgoing.gcm.invalidnotificationformat|GCM Invalid Notification Format|Počet|Celkem|The count of pushes that failed because the payload was not formatted correctly (GCM result: InvalidDataKey or InvalidTtl).|No Dimensions|
|outgoing.gcm.invalidnotificationsize|GCM Invalid Notification Size Error|Počet|Celkem|The count of pushes that failed because the payload was too large (GCM result: MessageTooBig).|No Dimensions|
|outgoing.gcm.wrongchannel|GCM Wrong Channel Error|Počet|Celkem|The count of pushes that failed because the registrationId in the registration is not associated to the current app (GCM result: InvalidPackageName).|No Dimensions|
|outgoing.gcm.pnserror|GCM Errors|Počet|Celkem|The count of pushes that failed because of errors communicating with GCM.|No Dimensions|
|outgoing.gcm.authenticationerror|GCM Authentication Errors|Počet|Celkem|The count of pushes that failed because the PNS did not accept the provided credentials the credentials are blocked or the SenderId is not correctly configured in the app (GCM result: MismatchedSenderId).|No Dimensions|
|outgoing.mpns.success|MPNS Successful Notifications|Počet|Celkem|The count of all successful notifications.|No Dimensions|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Počet|Celkem|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Počet|Celkem|The count of pushes that failed because the ChannelURI in the registration was not recognized (MPNS status: 404 not found).|No Dimensions|
|outgoing.mpns.throttled|MPNS Throttled Notifications|Počet|Celkem|The count of pushes that failed because MPNS is throttling this app (WNS MPNS: 406 Not Acceptable).|No Dimensions|
|outgoing.mpns.invalidnotificationformat|MPNS Invalid Notification Format|Počet|Celkem|The count of pushes that failed because the payload of the notification was too large.|No Dimensions|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Počet|Celkem|The count of pushes that failed because the ChannelURI in the registration was disconnected (MPNS status: 412 not found).|No Dimensions|
|outgoing.mpns.dropped|MPNS Dropped Notifications|Počet|Celkem|The count of pushes that were dropped by MPNS (MPNS response header: X-NotificationStatus: QueueFull or Suppressed).|No Dimensions|
|outgoing.mpns.pnserror|MPNS Errors|Počet|Celkem|The count of pushes that failed because of errors communicating with MPNS.|No Dimensions|
|outgoing.mpns.authenticationerror|MPNS Authentication Errors|Počet|Celkem|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|notificationhub.pushes|All Outgoing Notifications|Počet|Celkem|All outgoing notifications of the notification hub|No Dimensions|
|incoming.all.requests|All Incoming Requests|Počet|Celkem|Total incoming requests for a notification hub|No Dimensions|
|incoming.all.failedrequests|All Incoming Failed Requests|Počet|Celkem|Total incoming failed requests for a notification hub|No Dimensions|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Počet|Průměr|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Počet|Průměr|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Počet|Průměr|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Počet|Průměr|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Počet|Průměr|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Počet|Průměr|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Počet|Průměr|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Počet|Průměr|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Počet|Průměr|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Počet|Průměr|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Počet|Průměr|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Počet|Průměr|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Počet|Průměr|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Počet|Průměr|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Počet|Průměr|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Počet|Průměr|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Počet|Průměr|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Počet|Průměr|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Počet|Průměr|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Počet|Průměr|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Počet|Průměr|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Počet|Průměr|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Počet|Průměr|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Počet|Průměr|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Počet|Průměr|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Počet|Průměr|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Počet|Průměr|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Počet|Průměr|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Počet|Průměr|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Počet|Průměr|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Počet|Průměr|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Počet|Průměr|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Počet|Průměr|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Počet|Průměr|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Počet|Průměr|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Počet|Průměr|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Počet|Průměr|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Počet|Průměr|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Počet|Průměr|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Počet|Průměr|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Počet|Průměr|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Počet|Průměr|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Počet|Průměr|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Počet|Průměr|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Počet|Průměr|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Počet|Průměr|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Počet|Průměr|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Počet|Průměr|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Počet|Průměr|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Počet|Průměr|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Počet|Průměr|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Počet|Průměr|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Uživatelé|Počet|Průměr|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Počet|Průměr|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Počet|Průměr|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Počet|Průměr|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Počet|Průměr|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Počet|Průměr|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Počet|Průměr|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Počet|Průměr|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Počet|Průměr|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|Počet|Průměr|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Počet|Průměr|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Počet|Průměr|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Počet|Průměr|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Počet|Průměr|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Počet|Průměr|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Processor Queue Length|Počet|Průměr|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Počet|Celkem|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aktualizovat|Aktualizovat|Počet|Průměr|Aktualizovat|Computer, Product, Classification, UpdateState, Optional, Approved|
|Událost|Událost|Počet|Průměr|Událost|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Query Duration|Milliseconds|Průměr|DAX Query duration in last interval|No Dimensions|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Počet|Průměr|Number of jobs in the queue of the query thread pool.|No Dimensions|
|qpu_high_utilization_metric|QPU High Utilization|Počet|Celkem|QPU High Utilization In Last Minute, 1 For High QPU Utilization, Otherwise 0|No Dimensions|
|memory_metric|Paměť|Bytes|Průměr|Memory. Range 0-3 GB for A1, 0-5 GB for A2, 0-10 GB for A3, 0-25 GB for A4, 0-50 GB for A5 and 0-100 GB for A6|No Dimensions|
|memory_thrashing_metric|Memory Thrashing|Procento|Průměr|Average memory thrashing.|No Dimensions|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Počet|Celkem|Successful ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Počet|Celkem|ClientError on ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Počet|Celkem|ServerError on ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Počet|Celkem|Successful SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Počet|Celkem|ClientError on SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Počet|Celkem|ServerError on SenderConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Počet|Celkem|Total ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Počet|Celkem|Total SenderConnections requests for Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Počet|Celkem|Total ActiveConnections for Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Počet|Celkem|Total ActiveListeners for Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Počet|Celkem|Total BytesTransferred for Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Počet|Celkem|Total ListenerDisconnects for Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Počet|Celkem|Total SenderDisconnects for Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Sekund|Průměr|Average search latency for the search service|No Dimensions|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Průměr|Search queries per second for the search service|No Dimensions|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Procento|Průměr|Percentage of search queries that were throttled for the search service|No Dimensions|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Počet|Celkem|Total successful requests for a namespace (Preview)|EntityName|
|ServerErrors|Server Errors. (Preview)|Počet|Celkem|Server Errors for Microsoft.ServiceBus. (Preview)|EntityName|
|UserErrors|User Errors. (Preview)|Počet|Celkem|User Errors for Microsoft.ServiceBus. (Preview)|EntityName|
|ThrottledRequests|Throttled Requests. (Preview)|Počet|Celkem|Throttled Requests for Microsoft.ServiceBus. (Preview)|EntityName|
|IncomingRequests|Incoming Requests (Preview)|Počet|Celkem|Incoming Requests for Microsoft.ServiceBus. (Preview)|EntityName|
|IncomingMessages|Incoming Messages (Preview)|Počet|Celkem|Incoming Messages for Microsoft.ServiceBus. (Preview)|EntityName|
|OutgoingMessages|Outgoing Messages (Preview)|Počet|Celkem|Outgoing Messages for Microsoft.ServiceBus. (Preview)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Počet|Celkem|Total Active Connections for Microsoft.ServiceBus. (Preview)|No Dimensions|
|Velikost|Size (Preview)|Bytes|Průměr|Size of an Queue/Topic in Bytes. (Preview)|EntityName|
|Zprávy|Count of messages in a Queue/Topic. (Preview)|Počet|Průměr|Count of messages in a Queue/Topic. (Preview)|EntityName|
|ActiveMessages|Count of active messages in a Queue/Topic. (Preview)|Počet|Průměr|Count of active messages in a Queue/Topic. (Preview)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (Preview)|Počet|Průměr|Count of dead-lettered messages in a Queue/Topic. (Preview)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (Preview)|Počet|Průměr|Count of scheduled messages in a Queue/Topic. (Preview)|EntityName|
|CPUXNS|CPU usage per namespace|Procento|Maximum|Service bus premium namespace CPU usage metric|No Dimensions|
|WSXNS|Memory size usage per namespace|Procento|Maximum|Service bus premium namespace memory usage metric|No Dimensions|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Počet|Průměr|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Průměr|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Počet|Průměr|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Průměr|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procento|Průměr|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procento|Průměr|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Počet|Průměr|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Počet|Průměr|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Počet|Průměr|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Počet|Průměr|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Počet|Průměr|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count|Počet|Maximum|The amount of user connection.|Koncový bod|
|MessageCount|Message Count|Počet|Celkem|The total amount of messages.|No Dimensions|
|InboundTraffic|Příchozí provoz|Bytes|Celkem|The inbound traffic of service|No Dimensions|
|OutboundTraffic|Odchozí provoz|Bytes|Celkem|The outbound traffic of service|No Dimensions|
|UserErrors|User Errors|Procento|Maximum|The percentage of user errors|No Dimensions|
|SystemErrors|System Errors|Procento|Maximum|The percentage of system errors|No Dimensions|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Průměr|Data space allocated. Not applicable to data warehouses.|No Dimensions|
|app_cpu_billed|App CPU billed|Počet|Celkem|App CPU billed. Applies to serverless databases.|No Dimensions|
|app_cpu_percent|App CPU percentage|Procento|Průměr|App CPU percentage. Applies to serverless databases.|No Dimensions|
|app_memory_percent|App memory used percentage|Procento|Průměr|App memory used percentage. Applies to serverless databases.|No Dimensions|
|blocked_by_firewall|Blocked by Firewall|Počet|Celkem|Blocked by Firewall|No Dimensions|
|cache_hit_percent|Cache hit percentage|Procento|Maximum|Cache hit percentage. Applies only to data warehouses.|No Dimensions|
|cache_used_percent|Cache used percentage|Procento|Maximum|Cache used percentage. Applies only to data warehouses.|No Dimensions|
|connection_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|No Dimensions|
|connection_successful|Successful Connections|Počet|Celkem|Successful Connections|No Dimensions|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|No Dimensions|
|cpu_limit|CPU limit|Počet|Průměr|CPU limit. Applies to vCore-based databases.|No Dimensions|
|cpu_used|CPU used|Počet|Průměr|CPU used. Applies to vCore-based databases.|No Dimensions|
|deadlock|Deadlocks|Počet|Celkem|Deadlocks. Not applicable to data warehouses.|No Dimensions|
|diff_backup_size_bytes|Differential backup storage size|Bytes|Maximum|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|dtu_limit|DTU Limit|Počet|Průměr|DTU Limit. Applies to DTU-based databases.|No Dimensions|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|DTU percentage. Applies to DTU-based databases.|No Dimensions|
|dtu_used|DTU used|Počet|Průměr|DTU used. Applies to DTU-based databases.|No Dimensions|
|dw_cpu_percent|DW node level CPU percentage|Procento|Průměr|DW node level CPU percentage|DwLogicalNodeId|
|dw_physical_data_read_percent|DW node level Data IO percentage|Procento|Průměr|DW node level Data IO percentage|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Procento|Maximum|DWU percentage. Applies only to data warehouses.|No Dimensions|
|dwu_limit|DWU limit|Počet|Maximum|DWU limit. Applies only to data warehouses.|No Dimensions|
|dwu_used|DWU used|Počet|Maximum|DWU used. Applies only to data warehouses.|No Dimensions|
|full_backup_size_bytes|Full backup storage size|Bytes|Maximum|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|local_tempdb_usage_percent|Local tempdb percentage|Procento|Průměr|Local tempdb percentage. Applies only to data warehouses.|No Dimensions|
|log_backup_size_bytes|Log backup storage size|Bytes|Maximum|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|log_write_percent|Log IO percentage|Procento|Průměr|Log IO percentage. Not applicable to data warehouses.|No Dimensions|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|No Dimensions|
|sessions_percent|Sessions percentage|Procento|Průměr|Sessions percentage. Not applicable to data warehouses.|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procento|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procento|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|úložiště|Data space used|Bytes|Maximum|Total database size. Not applicable to data warehouses.|No Dimensions|
|storage_percent|Data space used percent|Procento|Maximum|Database size percentage. Not applicable to data warehouses or hyperscale databases.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Počet|Maximum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Počet|Maximum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procento|Maximum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procento|Průměr|Workers percentage. Not applicable to data warehouses.|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procento|Průměr|In-Memory OLTP storage percent. Not applicable to data warehouses.|No Dimensions|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Průměr|Data space allocated|No Dimensions|
|allocated_data_storage_percent|Data space allocated percent|Procento|Maximum|Data space allocated percent|No Dimensions|
|cpu_limit|CPU limit|Počet|Průměr|CPU limit. Applies to vCore-based elastic pools.|No Dimensions|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|No Dimensions|
|cpu_used|CPU used|Počet|Průměr|CPU used. Applies to vCore-based elastic pools.|No Dimensions|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|DTU percentage. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_limit|eDTU limit|Počet|Průměr|eDTU limit. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_used|eDTU used|Počet|Průměr|eDTU used. Applies to DTU-based elastic pools.|No Dimensions|
|log_write_percent|Log IO percentage|Procento|Průměr|Log IO percentage|No Dimensions|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|No Dimensions|
|sessions_percent|Sessions percentage|Procento|Průměr|Sessions percentage|No Dimensions|
|storage_limit|Data max size|Bytes|Průměr|Storage limit|No Dimensions|
|storage_percent|Data space used percent||Procento|Průměr|Storage percentage|No Dimensions|
|storage_used|Data space used|Bytes|Průměr|Využité úložiště|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procento|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procento|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Počet|Maximum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Počet|Maximum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procento|Maximum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procento|Průměr|Workers percentage|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procento|Průměr|In-Memory OLTP storage percent|No Dimensions|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|avg_cpu_percent|Average CPU percentage|Procento|Průměr|Average CPU percentage|No Dimensions|
|io_bytes_read|IO bytes read|Bytes|Průměr|IO bytes read|No Dimensions|
|io_requests|IO requests count|Počet|Průměr|IO requests count|No Dimensions|
|io_bytes_written|IO bytes written|Bytes|Průměr|IO bytes written|No Dimensions|
|reserved_storage_mb|Storage space reserved|Počet|Průměr|Storage space reserved|No Dimensions|
|storage_space_used_mb|Storage space used|Počet|Průměr|Storage space used|No Dimensions|
|virtual_core_count|Virtual core count|Počet|Průměr|Virtual core count|No Dimensions|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity|Bytes|Průměr|Account used capacity|No Dimensions|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Příchozí přenos dat|Příchozí přenos dat|Bytes|Celkem|The amount of ingress data, in bytes. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName, Authentication|
|Výchozí přenos dat|Výchozí přenos dat|Bytes|Celkem|The amount of egress data, in bytes. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Průměr|The average latency used by Azure Storage to process a successful request, in milliseconds. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Průměr|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName, Authentication|
|Dostupnost|Dostupnost|Procento|Průměr|The percentage of availability for the storage service or the specified API operation. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity|Bytes|Průměr|The amount of storage used by the storage account’s Blob service in bytes.|BlobType, Tier|
|BlobCount|Blob Count|Počet|Celkem|The number of Blob in the storage account’s Blob service.|BlobType|       |BlobCount|Blob Count|Počet|Průměr|The number of Blob in the storage account’s Blob service.|BlobType, Tier|
|ContainerCount|Blob Container Count|Počet|Průměr|The number of containers in the storage account’s Blob service.|No Dimensions|
|IndexCapacity|Index Capacity|Bytes|Průměr|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|No Dimensions|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Příchozí přenos dat|Příchozí přenos dat|Bytes|Celkem|The amount of ingress data, in bytes. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName, Authentication|
|Výchozí přenos dat|Výchozí přenos dat|Bytes|Celkem|The amount of egress data, in bytes. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Průměr|The average latency used by Azure Storage to process a successful request, in milliseconds. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Průměr|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName, Authentication|
|Dostupnost|Dostupnost|Procento|Průměr|The percentage of availability for the storage service or the specified API operation. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|FileCapacity|File Capacity|Bytes|Průměr|The amount of storage used by the storage account’s File service in bytes.|No Dimensions|
|FileCount|File Count|Počet|Průměr|The number of file in the storage account’s File service.|No Dimensions|
|FileShareCount|File Share Count|Počet|Průměr|The number of file shares in the storage account’s File service.|No Dimensions|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Příchozí přenos dat|Příchozí přenos dat|Bytes|Celkem|The amount of ingress data, in bytes. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName, Authentication|
|Výchozí přenos dat|Výchozí přenos dat|Bytes|Celkem|The amount of egress data, in bytes. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Průměr|The average latency used by Azure Storage to process a successful request, in milliseconds. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Průměr|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName, Authentication|
|Dostupnost|Dostupnost|Procento|Průměr|The percentage of availability for the storage service or the specified API operation. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity|Bytes|Průměr|The amount of storage used by the storage account’s Queue service in bytes.|No Dimensions|
|QueueCount|Queue Count|Počet|Průměr|The number of queue in the storage account’s Queue service.|No Dimensions|
|QueueMessageCount|Queue Message Count|Počet|Průměr|The approximate number of queue messages in the storage account’s Queue service.|No Dimensions|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Příchozí přenos dat|Příchozí přenos dat|Bytes|Celkem|The amount of ingress data, in bytes. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName, Authentication|
|Výchozí přenos dat|Výchozí přenos dat|Bytes|Celkem|The amount of egress data, in bytes. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Průměr|The average latency used by Azure Storage to process a successful request, in milliseconds. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Průměr|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName, Authentication|
|Dostupnost|Dostupnost|Procento|Průměr|The percentage of availability for the storage service or the specified API operation. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity|Bytes|Průměr|The amount of storage used by the storage account’s Table service in bytes.|No Dimensions|
|TableCount|Table Count|Počet|Průměr|The number of table in the storage account’s Table service.|No Dimensions|
|TableEntityCount|Table Entity Count|Počet|Průměr|The number of table entities in the storage account’s Table service.|No Dimensions|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Příchozí přenos dat|Příchozí přenos dat|Bytes|Celkem|The amount of ingress data, in bytes. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName, Authentication|
|Výchozí přenos dat|Výchozí přenos dat|Bytes|Celkem|The amount of egress data, in bytes. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Průměr|The average latency used by Azure Storage to process a successful request, in milliseconds. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Průměr|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName, Authentication|
|Dostupnost|Dostupnost|Procento|Průměr|The percentage of availability for the storage service or the specified API operation. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Počet|Průměr|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Počet|Celkem|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Files not syncing|Počet|Celkem|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synced|Bytes|Celkem|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Počet|Maximum|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud tiering recall|Bytes|Celkem|Total size of data recalled by the server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|SU % Utilization|Procento|Maximum|SU % Utilization|LogicalName, PartitionId|
|InputEvents|Input Events|Počet|Celkem|Input Events|LogicalName, PartitionId|
|InputEventBytes|Input Event Bytes|Bytes|Celkem|Input Event Bytes|LogicalName, PartitionId|
|LateInputEvents|Late Input Events|Počet|Celkem|Late Input Events|LogicalName, PartitionId|
|OutputEvents|Output Events|Počet|Celkem|Output Events|LogicalName, PartitionId|
|ConversionErrors|Data Conversion Errors|Počet|Celkem|Data Conversion Errors|LogicalName, PartitionId|
|Chyby|Runtime Errors|Počet|Celkem|Runtime Errors|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Out of order Events|Počet|Celkem|Out of order Events|LogicalName, PartitionId|
|AMLCalloutRequests|Function Requests|Počet|Celkem|Function Requests|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Failed Function Requests|Počet|Celkem|Failed Function Requests|LogicalName, PartitionId|
|AMLCalloutInputEvents|Function Events|Počet|Celkem|Function Events|LogicalName, PartitionId|
|DeserializationError|Input Deserialization Errors|Počet|Celkem|Input Deserialization Errors|LogicalName, PartitionId|
|EarlyInputEvents|Early Input Events|Počet|Celkem|Early Input Events|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermark Delay|Sekund|Maximum|Watermark Delay|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Backlogged Input Events|Počet|Maximum|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Input Sources Received|Počet|Celkem|Input Sources Received|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Počet|Celkem|Count of messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Počet|Celkem|Count of invalid messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Celkem|Count of bytes read from all event sources|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Celkem|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Počet|Celkem|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Sekund|Maximum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Počet|Průměr|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Počet|Maximum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Počet|Maximum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Počet|Celkem|Count of messages read from the event source|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Počet|Celkem|Count of invalid messages read from the event source|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Celkem|Count of bytes read from the event source|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Celkem|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Počet|Celkem|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Sekund|Maximum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Počet|Průměr|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Počet|Maximum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Počet|Maximum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Průměr|Average disk throughput due to read operations over the sample period.|No Dimensions|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Průměr|Average disk throughput due to write operations over the sample period.|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Celkem|Total disk throughput due to read operations over the sample period.|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Celkem|Total disk throughput due to write operations over the sample period.|No Dimensions|
|DiskReadOperations|Disk Read Operations|Počet|Celkem|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskWriteOperations|Disk Write Operations|Počet|Celkem|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Průměr|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Průměr|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskReadLatency|Disk Read Latency|Milliseconds|Průměr|Total read latency. The sum of the device and kernel read latencies.|No Dimensions|
|DiskWriteLatency|Disk Write Latency|Milliseconds|Průměr|Total write latency. The sum of the device and kernel write latencies.|No Dimensions|
|NetworkInBytesPerSecond|Network In Bytes/Sec|BytesPerSecond|Průměr|Average network throughput for received traffic.|No Dimensions|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|BytesPerSecond|Průměr|Average network throughput for transmitted traffic.|No Dimensions|
|Síťové vstupy|Síťové vstupy|Bytes|Celkem|Total network throughput for received traffic.|No Dimensions|
|Síťové výstupy|Síťové výstupy|Bytes|Celkem|Total network throughput for transmitted traffic.|No Dimensions|
|MemoryUsed|Memory Used|Bytes|Průměr|The amount of machine memory that is in use by the VM.|No Dimensions|
|MemoryGranted|Memory Granted|Bytes|Průměr|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|No Dimensions|
|MemoryActive|Memory Active|Bytes|Průměr|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|No Dimensions|
|Procento CPU|Procento CPU|Procento|Průměr|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|No Dimensions|
|PercentageCpuReady|Percentage CPU Ready|Milliseconds|Celkem|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|No Dimensions|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Memory Percentage|Procento|Průměr|Memory Percentage|Instance|
|DiskQueueLength|Disk Queue Length|Počet|Průměr|Disk Queue Length|Instance|
|HttpQueueLength|Http Queue Length|Počet|Průměr|Http Queue Length|Instance|
|BytesReceived|Data In|Bytes|Celkem|Data In|Instance|
|BytesSent|Data Out|Bytes|Celkem|Data Out|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluding functions)

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Sekund|Celkem|CPU Time|Instance|
|Požadavky|Požadavky|Počet|Celkem|Požadavky|Instance|
|BytesReceived|Data In|Bytes|Celkem|Data In|Instance|
|BytesSent|Data Out|Bytes|Celkem|Data Out|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|Http 2xx|Počet|Celkem|Http 2xx|Instance|
|Http3xx|Http 3xx|Počet|Celkem|Http 3xx|Instance|
|Http401|Http 401|Počet|Celkem|Http 401|Instance|
|Http403|Http 403|Počet|Celkem|Http 403|Instance|
|Http404|Http 404|Počet|Celkem|Http 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|Http 4xx|Počet|Celkem|Http 4xx|Instance|
|Http5xx|Http Server Errors|Počet|Celkem|Http Server Errors|Instance|
|MemoryWorkingSet|Memory working set|Bytes|Průměr|Memory working set|Instance|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Průměr|Average memory working set|Instance|
|AverageResponseTime|Average Response Time|Sekund|Průměr|Average Response Time|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Handles|Handle Count|Počet|Průměr|Handle Count|Instance|
|Threads|Thread Count|Počet|Průměr|Thread Count|Instance|
|PrivateBytes|Private Bytes|Bytes|Průměr|Private Bytes|Instance|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Celkem|IO Read Bytes Per Second|Instance|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Celkem|IO Write Bytes Per Second|Instance|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Celkem|IO Other Bytes Per Second|Instance|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Celkem|IO Read Operations Per Second|Instance|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Celkem|IO Write Operations Per Second|Instance|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Celkem|IO Other Operations Per Second|Instance|
|RequestsInApplicationQueue|Requests In Application Queue|Počet|Průměr|Requests In Application Queue|Instance|
|CurrentAssemblies|Current Assemblies|Počet|Průměr|Current Assemblies|Instance|
|TotalAppDomains|Total App Domains|Počet|Průměr|Total App Domains|Instance|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Počet|Průměr|Total App Domains Unloaded|Instance|
|Gen0Collections|Gen 0 Garbage Collections|Počet|Celkem|Gen 0 Garbage Collections|Instance|
|Gen1Collections|Gen 1 Garbage Collections|Počet|Celkem|Gen 1 Garbage Collections|Instance|
|Gen2Collections|Gen 2 Garbage Collections|Počet|Celkem|Gen 2 Garbage Collections|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Data In|Bytes|Celkem|Data In|Instance|
|BytesSent|Data Out|Bytes|Celkem|Data Out|Instance|
|Http5xx|Http Server Errors|Počet|Celkem|Http Server Errors|Instance|
|MemoryWorkingSet|Memory working set|Bytes|Průměr|Memory working set|Instance|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Průměr|Average memory working set|Instance|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Celkem|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Function Execution Count|Počet|Celkem|Function Execution Count|Instance|
|PrivateBytes|Private Bytes|Bytes|Průměr|Private Bytes|Instance|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Celkem|IO Read Bytes Per Second|Instance|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Celkem|IO Write Bytes Per Second|Instance|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Celkem|IO Other Bytes Per Second|Instance|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Celkem|IO Read Operations Per Second|Instance|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Celkem|IO Write Operations Per Second|Instance|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Celkem|IO Other Operations Per Second|Instance|
|RequestsInApplicationQueue|Requests In Application Queue|Počet|Průměr|Requests In Application Queue|Instance|
|CurrentAssemblies|Current Assemblies|Počet|Průměr|Current Assemblies|Instance|
|TotalAppDomains|Total App Domains|Počet|Průměr|Total App Domains|Instance|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Počet|Průměr|Total App Domains Unloaded|Instance|
|Gen0Collections|Gen 0 Garbage Collections|Počet|Celkem|Gen 0 Garbage Collections|Instance|
|Gen1Collections|Gen 1 Garbage Collections|Počet|Celkem|Gen 1 Garbage Collections|Instance|
|Gen2Collections|Gen 2 Garbage Collections|Počet|Celkem|Gen 2 Garbage Collections|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Sekund|Celkem|CPU Time|Instance|
|Požadavky|Požadavky|Počet|Celkem|Požadavky|Instance|
|BytesReceived|Data In|Bytes|Celkem|Data In|Instance|
|BytesSent|Data Out|Bytes|Celkem|Data Out|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|Http 2xx|Počet|Celkem|Http 2xx|Instance|
|Http3xx|Http 3xx|Počet|Celkem|Http 3xx|Instance|
|Http401|Http 401|Počet|Celkem|Http 401|Instance|
|Http403|Http 403|Počet|Celkem|Http 403|Instance|
|Http404|Http 404|Počet|Celkem|Http 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|Http 4xx|Počet|Celkem|Http 4xx|Instance|
|Http5xx|Http Server Errors|Počet|Celkem|Http Server Errors|Instance|
|MemoryWorkingSet|Memory working set|Bytes|Průměr|Memory working set|Instance|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Průměr|Average memory working set|Instance|
|AverageResponseTime|Average Response Time|Sekund|Průměr|Average Response Time|Instance|
|FunctionExecutionUnits|Function Execution Units|Počet|Celkem|Function Execution Units|Instance|
|FunctionExecutionCount|Function Execution Count|Počet|Celkem|Function Execution Count|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Handles|Handle Count|Počet|Průměr|Handle Count|Instance|
|Threads|Thread Count|Počet|Průměr|Thread Count|Instance|
|PrivateBytes|Private Bytes|Bytes|Průměr|Private Bytes|Instance|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Celkem|IO Read Bytes Per Second|Instance|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Celkem|IO Write Bytes Per Second|Instance|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Celkem|IO Other Bytes Per Second|Instance|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Celkem|IO Read Operations Per Second|Instance|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Celkem|IO Write Operations Per Second|Instance|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Celkem|IO Other Operations Per Second|Instance|
|RequestsInApplicationQueue|Requests In Application Queue|Počet|Průměr|Requests In Application Queue|Instance|
|CurrentAssemblies|Current Assemblies|Počet|Průměr|Current Assemblies|Instance|
|TotalAppDomains|Total App Domains|Počet|Průměr|Total App Domains|Instance|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Počet|Průměr|Total App Domains Unloaded|Instance|
|Gen0Collections|Gen 0 Garbage Collections|Počet|Celkem|Gen 0 Garbage Collections|Instance|
|Gen1Collections|Gen 1 Garbage Collections|Počet|Celkem|Gen 1 Garbage Collections|Instance|
|Gen2Collections|Gen 2 Garbage Collections|Počet|Celkem|Gen 2 Garbage Collections|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Počet|Celkem|Požadavky|Instance|
|BytesReceived|Data In|Bytes|Celkem|Data In|Instance|
|BytesSent|Data Out|Bytes|Celkem|Data Out|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|Http 2xx|Počet|Celkem|Http 2xx|Instance|
|Http3xx|Http 3xx|Počet|Celkem|Http 3xx|Instance|
|Http401|Http 401|Počet|Celkem|Http 401|Instance|
|Http403|Http 403|Počet|Celkem|Http 403|Instance|
|Http404|Http 404|Počet|Celkem|Http 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|Http 4xx|Počet|Celkem|Http 4xx|Instance|
|Http5xx|Http Server Errors|Počet|Celkem|Http Server Errors|Instance|
|AverageResponseTime|Average Response Time|Sekund|Průměr|Average Response Time|Instance|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Memory Percentage|Procento|Průměr|Memory Percentage|Instance|
|DiskQueueLength|Disk Queue Length|Počet|Průměr|Disk Queue Length|Instance|
|HttpQueueLength|Http Queue Length|Počet|Průměr|Http Queue Length|Instance|
|ActiveRequests|Active Requests|Počet|Celkem|Active Requests|Instance|
|TotalFrontEnds|Total Front Ends|Počet|Průměr|Total Front Ends|No Dimensions|
|SmallAppServicePlanInstances|Small App Service Plan Workers|Počet|Průměr|Small App Service Plan Workers|No Dimensions|
|MediumAppServicePlanInstances|Medium App Service Plan Workers|Počet|Průměr|Medium App Service Plan Workers|No Dimensions|
|LargeAppServicePlanInstances|Large App Service Plan Workers|Počet|Průměr|Large App Service Plan Workers|No Dimensions|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metric Display Name|Jednotka|Aggregation Type|Popis|Dimenze|
|---|---|---|---|---|---|
|WorkersTotal|Total Workers|Počet|Průměr|Total Workers|No Dimensions|
|WorkersAvailable|Available Workers|Počet|Průměr|Available Workers|No Dimensions|
|WorkersUsed|Used Workers|Počet|Průměr|Used Workers|No Dimensions|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Memory Percentage|Procento|Průměr|Memory Percentage|Instance|

## <a name="next-steps"></a>Další kroky
* [Read about metrics in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Export metrics to storage, Event Hub, or Log Analytics](resource-logs-overview.md)
