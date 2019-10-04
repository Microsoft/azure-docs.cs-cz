---
title: Přesunout podporu operace podle typu prostředku Azure
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: 81096a4299ec190cb4299f1b8ea4f9c2533d37ad
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948030"
---
# <a name="move-operation-support-for-resources"></a>Přesunout podporu operací pro prostředky
Tento článek uvádí, jestli typ prostředku Azure podporuje operaci přesunu. Poskytuje taky informace o zvláštních podmínkách, které je potřeba vzít v úvahu při přesunu prostředku.

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Backup](#microsoftbackup)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Cihly Microsoft.](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. dataexchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. datalake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Migrace Microsoft.](#microsoftdatamigration)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migruje](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft. ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | domainservices | Ne | Ne |
> | domainservices / replicasets | Ne | Ne |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | tenantů | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | actionrules | Ano | Ano |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | service | Ano | Ano |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ano | Ano |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | apiapps | Ne | Ne |
> | appidentities | Ne | Ne |
> | brány | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | policyassignments | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ano | Ano |
> | automationaccounts/konfigurace | Ano | Ano |
> | automationaccounts/Runbooky | Ano | Ano |

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ano | Ano |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | Ne | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | rozpoznávání | Ano | Ano |

## <a name="microsoftbackup"></a>Microsoft. Backup

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | backupvaultu | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft. Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ano | Ano |

## <a name="microsoftbatchai"></a>Microsoft. BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | servery | Ne | Ne |
> | úlohy | Ne | Ne |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | BizTalk | Ano | Ano |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Ano | Ano |
> | sledovacích procesů | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | Redis | Ano | Ano |

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Ne | Ne |
> | profily | Ano | Ano |
> | profily/koncové body | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft. ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | domainnames | Ano | Ne |
> | virtualmachines | Ano | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Ne | Ne |
> | reservedips | Ne | Ne |
> | virtualnetworks | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ano | Ano |

## <a name="microsoftcompute"></a>Microsoft. COMPUTE

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ano | Ano |
> | diskencryptionsets | Ne | Ne |
> | disků | Ano | Ano |
> | Galerie | Ne | Ne |
> | Galerie/image | Ne | Ne |
> | Galerie/image/verze | Ne | Ne |
> | hostgroups | Ne | Ne |
> | hostgroups/hostitelé | Ne | Ne |
> | obrázky | Ano | Ano |
> | proximityplacementgroups | Ne | Ne |
> | restorepointcollections | Ne | Ne |
> | sharedvmimages | Ne | Ne |
> | sharedvmimages/verze | Ne | Ne |
> | snímky | Ano | Ano |
> | virtualmachines | Ano | Ano |
> | VirtualMachines/rozšíření | Ano | Ano |
> | virtualmachinescalesets | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | containergroups | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | containergroups | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | registr | Ano | Ano |
> | Registry/BuildTasks | Ano | Ano |
> | Registry/replikace | Ano | Ano |
> | Registry/úlohy | Ano | Ano |
> | Registry a Webhooky | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | containerservices | Ne | Ne |
> | managedclusters | Ne | Ne |
> | openshiftmanagedclusters | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | konektory | Ano | Ano |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | Hubs | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | úlohy | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Ne | Ne |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | spustí | Ano | Ano |
> | datacatalogs | Ne | Ne |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Ne | Ne |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | balíčky | Ne | Ne |
> | plánují | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | objekty DataFactory | Ano | Ano |
> | továrny | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ano | Ano |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | služby | Ne | Ne |
> | služby a projekty | Ne | Ne |
> | otvory | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | servergroups | Ne | Ne |
> | servery | Ano | Ano |
> | serversv2 | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ano | Ano |
> | uvádění | Ano | Ano |
> | servicetopologies | Ano | Ano |
> | servicetopologies/služby | Ano | Ano |
> | servicetopologies/služby/serviceunits | Ano | Ano |
> | uvedené | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | elasticpools | Ne | Ne |
> | elasticpools / iothubtenants | Ne | Ne |
> | iothubs | Ano | Ano |
> | provisioningservices | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | labcenters | Ne | Ne |
> | labs | Ano | Ne |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/servicerunners | Ano | Ano |
> | Labs/VirtualMachines | Ano | Ne |
> | časových | Ano | Ano |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | domény | Ano | Ano |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | služby | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | domény | Ano | Ano |
> | popisující | Ano | Ano |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | obory názvů | Ano | Ano |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | hanainstances | Ne | Ne |
> | sapmonitors | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nemůžete se ale pohybovat mezi předplatnými síťových prostředků propojených s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nemůžete přesunout do nové skupiny prostředků síťovou kartu, která je připojená k virtuálnímu počítači pro daný cluster.
>
> Když přesunete cluster HDInsight do nového předplatného, napřed přesuňte další prostředky (třeba účet úložiště). Pak můžete cluster HDInsight přesunout sám o sobě.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | služby | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | virtuální | Ne | Ne |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | datamanagery | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | úlohy | Ano | Ano |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |
> | actiongroups | Ano | Ano |
> | upozorněníprotokoluaktivit | Ne | Ne |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | komponenty | Ano | Ano |
> | guestdiagnosticsettings | Ne | Ne |
> | metricalerts | Ne | Ne |
> | notificationgroups | Ne | Ne |
> | notificationrules | Ne | Ne |
> | scheduledqueryrules | Ano | Ano |
> | webové testy | Ano | Ano |
> | sešity | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | iotapps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ano | Ano |
> | zapisovací | Ano | Ano |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | hsmpools | Ne | Ne |
> | trezory | Ano | Ano |

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

## <a name="microsoftkusto"></a>Microsoft. Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | labaccounts | Ne | Ne |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Ne | Ne |
> | integrationaccounts | Ano | Ano |
> | integrationserviceenvironments | Ne | Ne |
> | isolatedenvironments | Ne | Ne |
> | pracovní postupy | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ano | Ano |
> | WebServices | Ano | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Ano | Ano |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |
> | účty/pracovní prostory | Ne | Ne |
> | účty/pracovní prostory/projekty | Ne | Ne |
> | teamaccounts | Ne | Ne |
> | teamaccounts/pracovní prostory | Ne | Ne |
> | teamaccounts/pracovní prostory/projekty | Ne | Ne |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Ne | Ne |

## <a name="microsoftmachinelearningservices"></a>Microsoft. MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ano | Ano |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | MediaServices | Ano | Ano |
> | MediaServices/liveevents | Ano | Ano |
> | MediaServices/starají | Ano | Ano |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | appclusters | Ne | Ne |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ne | Ne |
> | migrateprojects | Ne | Ne |
> | projekty | Ne | Ne |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Ne | Ne |
> | netappaccounts / capacitypools | Ne | Ne |
> | netappaccounts/capacitypools/svazky | Ne | Ne |
> | netappaccounts/capacitypools/svazky/mounttargets | Ne | Ne |
> | netappaccounts/capacitypools/svazky/snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft. Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Ne | Ne |
> | applicationgatewaywebapplicationfirewallpolicies | Ne | Ne |
> | applicationsecuritygroups | Ano | Ano |
> | azurefirewalls | Ano | Ano |
> | bastionhosts | Ne | Ne |
> | připojení | Ano | Ano |
> | ddoscustompolicies | Ano | Ano |
> | ddosprotectionplans | Ne | Ne |
> | dnszones | Ano | Ano |
> | expressroutecircuits | Ne | Ne |
> | expressroutecrossconnections | Ne | Ne |
> | expressroutegateways | Ne | Ne |
> | expressrouteports | Ne | Ne |
> | frontdoors | Ne | Ne |
> | frontdoorwebapplicationfirewallpolicies | Ne | Ne |
> | loadbalancers | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | localnetworkgateways | Ano | Ano |
> | natgateways | Ano | Ano |
> | networkintentpolicies | Ano | Ano |
> | networkinterfaces | Ano | Ano |
> | networkprofiles | Ne | Ne |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | Ano | Ano |
> | networkwatchers / connectionmonitors | Ano | Ano |
> | networkwatchers/čočky | Ano | Ano |
> | networkwatchers / pingmeshes | Ano | Ano |
> | p2svpngateways | Ne | Ne |
> | privatednszones | Ano | Ano |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privateendpoints | Ne | Ne |
> | privatelinkservices | Ne | Ne |
> | publicipaddresses | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ano | Ano |
> | routefilters | Ne | Ne |
> | routetables | Ano | Ano |
> | securegateways | Ano | Ano |
> | serviceendpointpolicies | Ano | Ano |
> | trafficmanagerprofiles | Ano | Ano |
> | virtualhubs | Ne | Ne |
> | virtualnetworkgateways | Ano | Ano |
> | virtualnetworks | Ano | Ano |
> | virtualnetworktaps | Ne | Ne |
> | virtualwans | Ne | Ne |
> | vpngateways (virtuální síť WAN) | Ne | Ne |
> | vpnsites (virtuální síť WAN) | Ne | Ne |
> | webapplicationfirewallpolicies | Ano | Ano |

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | obory názvů | Ano | Ano |
> | obory názvů/notificationhubs | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Ano | Ano |
> | řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | partnerské vztahy | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | řídicí panely | Ano | Ano |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | rootresources | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | kapacity | Ano | Ano |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | účty | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | trezory | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | obory názvů | Ano | Ano |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | dotazy | Ano | Ano |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ne |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | pokračuje | Ano | Ano |
> | jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | searchservices | Ano | Ano |

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Ano | Ano |
> | playbookconfigurations | Ne | Ne |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | brány | Ne | Ne |
> | uzly | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft. ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | obory názvů | Ano | Ano |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |
> | existující | Ano | Ano |
> | clustery/aplikace | Ne | Ne |
> | containergroups | Ne | Ne |
> | containergroupsets | Ne | Ne |
> | edgeclusters | Ne | Ne |
> | sítě | Ne | Ne |
> | secretstores | Ne | Ne |
> | svazků | Ne | Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | containergroups | Ne | Ne |
> | brány | Ano | Ano |
> | sítě | Ano | Ano |
> | záleží | Ano | Ano |
> | svazků | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | SignalR | Ano | Ano |

## <a name="microsoftsiterecovery"></a>Microsoft. SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Ne | Ne |
> | zařízení | Ne | Ne |
> | applicationdefinitions | Ne | Ne |
> | aplikace | Ne | Ne |
> | jitrequests | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | instancepools | Ne | Ne |
> | managedinstances | Ne | Ne |
> | managedinstances/databáze | Ne | Ne |
> | servery | Ano | Ano |
> | servery/databáze | Ano | Ano |
> | servery/elasticpools | Ano | Ano |
> | virtualclusters | Ano | Ano |

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ano | Ano |
> | sqlvirtualmachines | Ano | Ano |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | dwvm | Ne | Ne |

## <a name="microsoftstorage"></a>Microsoft. Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ano |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | mezipaměti | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | jednatel | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Ano | Ano |

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | prostředí | Ne | Ne |
> | prostředí/EventSources | Ne | Ne |
> | instance | Ne | Ne |
> | instance/prostředí | Ne | Ne |
> | instance/prostředí/EventSources | Ne | Ne |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Ne | Ne |
> | prostředky | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | prostředí | Ano | Ano |
> | prostředí/EventSources | Ano | Ano |
> | prostředí/referencedatasets | Ano | Ano |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | úložiště | Ne | Ne |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Ne | Ne |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | zohledňují | Ano | Ano |
> | účet/rozšíření | Ano | Ano |
> | účet/projekt | Ano | Ano |

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Ano | Ano |
> | dedicatedcloudservices | Ano | Ano |
> | virtualmachines | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | certifikáty | Ne | Ano |
> | connectiongateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | hostingenvironments | Ne | Ne |
> | serverových farem | Ano | Ano |
> | místa | Ano | Ano |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | deviceservices | Ne | Ne |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Formě |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ne | Ne |
> | hostpools | Ne | Ne |
> | pracovní prostory | Ne | Ne |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky
Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

Pokud chcete získat stejná data jako soubor hodnot oddělených čárkami, Stáhněte si [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
