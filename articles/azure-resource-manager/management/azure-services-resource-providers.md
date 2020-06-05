---
title: Poskytovatelé prostředků podle služeb Azure
description: Vypíše všechny obory názvů poskytovatele prostředků pro Azure Resource Manager a zobrazí službu Azure pro tento obor názvů.
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 272e6cb27b01ed971025bd273b225d4823d04501
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424428"
---
# <a name="resource-providers-for-azure-services"></a>Poskytovatelé prostředků pro služby Azure

V tomto článku se dozvíte, jak se mapují obory názvů poskytovatele prostředků na služby Azure.

## <a name="match-resource-provider-to-service"></a>Spárovat poskytovatele prostředků se službou

| Obor názvů poskytovatele prostředků | Služba Azure |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft. addons | core |
| Microsoft. ADHybridHealthService<sup>1</sup> | [Azure Active Directory](/azure/active-directory/) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft. ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | core |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft. Attestation | Služba Azure Attestation |
| Microsoft. Authorization<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automation](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Autonomní systémy](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. Azureactivedirectory selhala | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureData | Registr SQL Server |
| Microsoft. AzureStack | core |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. fakturace<sup>1</sup> | [Cost Management a fakturace](/azure/billing/) |
| Microsoft. BingMaps | [Mapy Bingu](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. blockchain | [Služba Azure Blockchain](/azure/blockchain/workbench/) |
| Microsoft. BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. detail | [Azure Blueprint](/azure/governance/blueprints/) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft. Capacity | core |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [App Service certifikátů](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Virtuální počítač modelu nasazení Classic |
| Microsoft. ClassicInfrastructureMigrate | Migrace modelu nasazení Classic |
| Microsoft. ClassicNetwork | Virtuální síť modelu nasazení Classic |
| Microsoft. ClassicStorage | Úložiště modelu nasazení Classic |
| Microsoft. ClassicSubscription<sup>1</sup> | Model nasazení Classic |
| Microsoft. Cognitiveservices Account | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft. Commerce<sup>1</sup> | core |
| Microsoft.Compute | [Virtual Machines](/azure/virtual-machines/)<br />[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) |
| Microsoft. spotřeba<sup>1</sup> | [Správa nákladů](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft. ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft. ContainerService | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. CostManagement<sup>1</sup> | [Správa nákladů](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Správa nákladů](/azure/cost-management/) |
| Microsoft. CustomerLockbox | Customer Lockbox pro Microsoft Azure |
| Microsoft. CustomProviders | [Vlastní poskytovatelé Azure](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Cihly Microsoft. | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. datacatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft. DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft. DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Migrace Microsoft. | [Azure Database Migration Service](/azure/dms/) |
| Microsoft. DataProtection | Ochrana dat |
| Microsoft. datashare | [Azure Data Share](/azure/data-share/) |
| Microsoft. DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft. DeploymentManager | [Deployment Manager Azure](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Virtual Desktop](/azure/virtual-desktop/) |
| Microsoft. Devices | [Azure IoT Hub](/azure/iot-hub/)<br />[Služba Azure IoT Hub Device Provisioning](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft. DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft. DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Podnikový graf znalostní báze |
| Microsoft. EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. Features<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [SAP HANA ve velkých instancích Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Rezervované HSM Azure](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Azure API for FHIR](../../healthcare-apis/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft. ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| Microsoft. Insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Trezor Microsoft. | [Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.Kusto | [Průzkumník dat Azure](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Údržba Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft. Management | [Skupiny pro správu](/azure/governance/management-groups/) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | core |
| Microsoft. MarketplaceApps | core |
| Microsoft. MarketplaceOrdering<sup>1</sup> | core |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. migruje | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtuální síť WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Úložiště objektů |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal<sup>1</sup> | [portál Azure](/azure/azure-portal/) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. | [Azure](https://azure.microsoft.com/services/quantum/) |
| Microsoft. RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph<sup>1</sup> | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. Resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | core |
| Microsoft. Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft. Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft. SerialConsole<sup>1</sup> | [Sériová konzola Azure pro Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | core |
| Microsoft. SignalRService | [Služba Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licence |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Spravovaná instance Azure SQL](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [SQL Server na Azure Virtual Machines](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. Subscription | core |
| Microsoft. support<sup>1</sup> | core |
| Microsoft. synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Token |
| Microsoft. VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsESU | Rozšířené aktualizace zabezpečení |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. monitor zátěže byl<sup>1</sup> | [Azure Monitor](../../azure-monitor/index.yml) |

<sup>1</sup> registrováno ve výchozím nastavení

## <a name="next-steps"></a>Další kroky

Další informace o poskytovatelích prostředků, včetně toho, jak registrovat poskytovatele prostředků, najdete v tématu [poskytovatelé a typy prostředků Azure](resource-providers-and-types.md) .
