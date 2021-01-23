---
title: Poskytovatelé prostředků podle služeb Azure
description: Vypíše všechny obory názvů poskytovatele prostředků pro Azure Resource Manager a zobrazí službu Azure pro tento obor názvů.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 581c25b384bf6e2865a21f083ba12eef2e5e8bcd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733783"
---
# <a name="resource-providers-for-azure-services"></a>Poskytovatelé prostředků pro služby Azure

V tomto článku se dozvíte, jak se mapují obory názvů poskytovatele prostředků na služby Azure.

## <a name="match-resource-provider-to-service"></a>Spárovat poskytovatele prostředků se službou

Poskytovatelé prostředků označených jako **registrované** jsou ve výchozím nastavení registrováni pro vaše předplatné. Další informace najdete v tématu [registrace](#registration).

| Obor názvů poskytovatele prostředků | Služba Azure |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft. addons | Core |
| Microsoft. ADHybridHealthService – [registrováno](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft. ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft. Attestation | Služba Azure Attestation |
| Microsoft. Authorization – [zaregistrováno](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automation](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Autonomní systémy](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. Azureactivedirectory selhala | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureArcData | Registr datových služeb s podporou ARC Azure |
| Microsoft. AzureData | Registr SQL Server |
| Microsoft. AzureStack | Core |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. fakturace – [zaregistrováno](#registration) | [Cost Management a fakturace](/azure/billing/) |
| Microsoft. BingMaps | [Mapy Bingu](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. blockchain | [Služba Azure Blockchain](../../blockchain/workbench/index.yml) |
| Microsoft. BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. detail | [Azure Blueprint](../../governance/blueprints/index.yml) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft. Capacity | Core |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [App Service certifikátů](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Virtuální počítač modelu nasazení Classic |
| Microsoft. ClassicInfrastructureMigrate | Migrace modelu nasazení Classic |
| Microsoft. ClassicNetwork | Virtuální síť modelu nasazení Classic |
| Microsoft. ClassicStorage | Úložiště modelu nasazení Classic |
| Microsoft. ClassicSubscription – [registrováno](#registration) | Model nasazení Classic |
| Microsoft. Cognitiveservices Account | [Cognitive Services](../../cognitive-services/index.yml) |
| Microsoft. Commerce – [zaregistrováno](#registration) | Core |
| Microsoft.Compute | [Virtual Machines](../../virtual-machines/index.yml)<br />[Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. spotřeba – [zaregistrováno](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft. ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft. ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. CostManagement – [registrováno](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft. CustomerLockbox | [Customer Lockbox pro Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Vlastní poskytovatelé Azure](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Cihly Microsoft. | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. datacatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft. DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft. DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Migrace Microsoft. | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft. DataProtection | Ochrana dat |
| Microsoft. datashare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft. DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Microsoft. DeploymentManager | [Správce nasazení Azure](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Služba Azure IoT Hub Device Provisioning](../../iot-dps/index.yml) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft. DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft. DynamicsLcs | [Služby životního cyklu](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft. EnterpriseKnowledgeGraph | Podnikový graf znalostní báze |
| Microsoft. EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. Features – [zaregistrováno](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [SAP HANA ve velkých instancích Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Azure API for FHIR](../../healthcare-apis/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Zóny privátního okraje](../../networking/edge-zones-overview.md) |
| Microsoft. ImportExport | [Azure Import/Export](../../import-export/storage-import-export-service.md) |
| Microsoft. Insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Trezor Microsoft. | [Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Údržba Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedNetwork | Virtuální sítě spravované službou PaaS Services |
| Microsoft. ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft. Management | [Skupiny pro správu](../../governance/management-groups/index.yml) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | Core |
| Microsoft. MarketplaceApps | Core |
| Microsoft. MarketplaceOrdering – [registrováno](#registration) | Core |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft. migruje | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtual WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. poznámkové bloky | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Úložiště objektů |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft. PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal – [zaregistrováno](#registration) | [Azure Portal](../../azure-portal/index.yml) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft. | [Azure](https://azure.microsoft.com/services/quantum/) |
| Microsoft. RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph – [registrováno](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. Resources – [zaregistrováno](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | Core |
| Microsoft. Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft. Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Centrum zabezpečení](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole – [registrováno](#registration) | [Sériová konzola Azure pro Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | Core |
| Microsoft. SignalRService | [Služba Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licence |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Spravovaná instance Azure SQL](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [SQL Server na Azure Virtual Machines](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. Subscription | Core |
| Microsoft. support – [zaregistrováno](#registration) | Core |
| Microsoft. synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Token |
| Microsoft. VirtualMachineImages | [Azure Image Builder](../../virtual-machines/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/) |
| Microsoft. VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft. Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsDefenderATP | [Rozšířená ochrana před internetovými útoky v programu Microsoft Defender](../../security-center/security-center-wdatp.md) |
| Microsoft. WindowsESU | Rozšířené aktualizace zabezpečení |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. monitor zátěže byl | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registrace

Poskytovatelé prostředků, které jsou označeni jako **registrované** , jsou ve výchozím nastavení registrovány pro vaše předplatné. Chcete-li použít jiné poskytovatele prostředků, je nutné [je zaregistrovat](resource-providers-and-types.md). Řada poskytovatelů prostředků se ale zaregistrovala za vás, když provádíte určité akce. Pokud například vytvoříte prostředek prostřednictvím portálu, portál automaticky zaregistruje všechny neregistrované poskytovatele prostředků, které jsou potřeba. Když nasadíte prostředky prostřednictvím [šablony Azure Resource Manager](../templates/overview.md), zaregistrují se taky další vyžadovaná poskytovatelé prostředků.

> [!IMPORTANT]
> Pokud jste připraveni ho použít, zaregistrujte pouze poskytovatele prostředků. Krok registrace umožňuje zachovat v rámci předplatného nejnižší oprávnění. Uživatel se zlými úmysly nemůže použít poskytovatele prostředků, kteří nejsou registrováni.

## <a name="next-steps"></a>Další kroky

Další informace o poskytovatelích prostředků, včetně toho, jak registrovat poskytovatele prostředků, najdete v tématu [poskytovatelé a typy prostředků Azure](resource-providers-and-types.md).