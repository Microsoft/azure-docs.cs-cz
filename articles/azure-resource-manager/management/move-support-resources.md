---
title: Přesunout podporu operace podle typu prostředku
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 81d545066ea6bcc1d3e2eecd884671324155d796
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124682"
---
# <a name="move-operation-support-for-resources"></a>Podpora operace přesunutí pro prostředky
Tento článek uvádí, jestli typ prostředku Azure podporuje operaci přesunu. Poskytuje taky informace o zvláštních podmínkách, které je potřeba vzít v úvahu při přesunu prostředku.

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. fakturace](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognition](#microsoftcognition)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. spotřeba](#microsoftconsumption)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. datashare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentování](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migruje](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft.](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. synapse](#microsoftsynapse)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. monitor zátěže byl](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | domainservices | Ne | Ne |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | tenantů | Ne | Ne |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konfiguračních | Ne | Ne |
> | doporučit | Ne | Ne |
> | potlačení | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actionrules | Ano | Ano |
> | výstrahy | Ne | Ne |
> | alertssummary | Ne | Ne |
> | smartdetectoralertrules | Ano | Ano |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | služba | Ano | Ano |

> [!IMPORTANT]
> Službu API Management, která je nastavená na SKU spotřeby, se nedá přesunout.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ano | Ano |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | návratu | Ano | Ano |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apiapps | Ne | Ne |
> | appidentities | Ne | Ne |
> | brány | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instanci | Ne | Ne |
> | denyassignments | Ne | Ne |
> | findorphanroleassignments | Ne | Ne |
> | počtu | Ne | Ne |
> | nastaven | Ne | Ne |
> | policyassignments | Ne | Ne |
> | policydefinitions | Ne | Ne |
> | policysetdefinitions | Ne | Ne |
> | RoleAssignments | Ne | Ne |
> | roleassignmentsusagemetrics | Ne | Ne |
> | roledefinitions | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ano | Ano |
> | automationaccounts/konfigurace | Ano | Ano |
> | automationaccounts/Runbooky | Ano | Ano |

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ano | Ano |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Ne | Ne |
> | postgresinstances | Ne | Ne |
> | sqlinstances | Ne | Ne |
> | sqlserverregistrations | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | rozpoznávání | Ano | Ano |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ano | Ano |

## <a name="microsoftbatchai"></a>Microsoft. BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | servery | Ne | Ne |
> | úlohy | Ne | Ne |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | billingperiods | Ne | Ne |
> | billingpermissions | Ne | Ne |
> | billingroleassignments | Ne | Ne |
> | billingroledefinitions | Ne | Ne |
> | createbillingroleassignment | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | biztalk | Ne | Ne |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Ne | Ne |
> | cordamembers | Ne | Ne |
> | sledovacích procesů | Ne | Ne |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | tokenservices | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Ne | Ne |
> | podrobné plány | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Redis | Ano | Ano |

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Ano | Ano |
> | uživatelů | Ano | Ano |
> | profily/koncové body | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | domainnames | Ano | Ne |
> | virtualmachines | Ano | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Ne | Ne |
> | reservedips | Ne | Ne |
> | virtualnetworks | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftcognition"></a>Microsoft. Cognition

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Ne | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ano | Ano |
> | diskencryptionsets | Ne | Ne |
> | disků | Ano | Ano |
> | Galerie | Ne | Ne |
> | Galerie/image | Ne | Ne |
> | Galerie/image/verze | Ne | Ne |
> | hostgroups | Ne | Ne |
> | hostgroups/hostitelé | Ne | Ne |
> | images | Ano | Ano |
> | proximityplacementgroups | Ano | Ano |
> | restorepointcollections | Ne | Ne |
> | sharedvmextensions | Ne | Ne |
> | sharedvmimages | Ne | Ne |
> | sharedvmimages/verze | Ne | Ne |
> | snímky | Ano | Ano |
> | sshpublickeys | Ne | Ne |
> | virtualmachines | Ano | Ano |
> | VirtualMachines/rozšíření | Ano | Ano |
> | virtualmachinescalesets | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Ne | Ne |
> | Přesun | Ne | Ne |
> | projektů | Ne | Ne |
> | poplatky za | Ne | Ne |
> | costtags | Ne | Ne |
> | dobropis | Ne | Ne |
> | stránka events | Ne | Ne |
> | prognózy | Ne | Ne |
> | ŠARŽ | Ne | Ne |
> | tržišť | Ne | Ne |
> | operationresults | Ne | Ne |
> | stav operationstatus | Ne | Ne |
> | pricesheets | Ne | Ne |
> | produktech | Ne | Ne |
> | reservationdetails | Ne | Ne |
> | reservationrecommendations | Ne | Ne |
> | reservationsummaries | Ne | Ne |
> | reservationtransactions | Ne | Ne |
> | tags | Ne | Ne |
> | tenantů | Ne | Ne |
> | uvedenými | Ne | Ne |
> | usagedetails | Ne | Ne |

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containergroups | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containergroups | Ne | Ne |
> | serviceassociationlinks | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | registr | Ano | Ano |
> | Registry/agentpools | Ne | Ne |
> | Registry/BuildTasks | Ano | Ano |
> | Registry/replikace | Ano | Ano |
> | Registry/taskruns | Ano | Ano |
> | Registry/úlohy | Ano | Ano |
> | Registry a Webhooky | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containerservices | Ne | Ne |
> | managedclusters | Ne | Ne |
> | openshiftmanagedclusters | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | výstrahy | Ne | Ne |
> | projektů | Ne | Ne |
> | konektory | Ano | Ano |
> | použijí | Ne | Ne |
> | vývozních | Ne | Ne |
> | externalsubscriptions | Ne | Ne |
> | forecast | Ne | Ne |
> | query | Ne | Ne |
> | reportconfigs | Ne | Ne |
> | reports | Ne | Ne |
> | showbackrules | Ne | Ne |
> | zobrazení | Ne | Ne |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Hubs | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | přidružení | Ne | Ne |
> | resourceproviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Ne | Ne |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | spustí | Ano | Ano |
> | datacatalogs | Ne | Ne |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Ne | Ne |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zásilk | Ne | Ne |
> | plánují | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | objekty DataFactory | Ano | Ano |
> | továrny | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ne | Ne |
> | služby a projekty | Ne | Ne |
> | otvory | Ne | Ne |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupvaults | Ne | Ne |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | servergroups | Ne | Ne |
> | servery | Ano | Ano |
> | serversv2 | Ano | Ano |
> | singleservers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ano | Ano |
> | uvádění | Ano | Ano |
> | servicetopologies | Ano | Ano |
> | servicetopologies/služby | Ano | Ano |
> | servicetopologies/služby/serviceunits | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ne | Ne |
> | hostpools | Ne | Ne |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | elasticpools | Ne | Ne |
> | elasticpools / iothubtenants | Ne | Ne |
> | iothubs | Ano | Ano |
> | provisioningservices | Ano | Ano |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kanály | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labcenters | Ne | Ne |
> | Labs | Ano | Ne |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/servicerunners | Ano | Ano |
> | Labs/VirtualMachines | Ano | Ne |
> | časových | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |
> | eventSubscriptions | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. |
> | eventsubscriptions | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. |
> | extensiontopics | Ne | Ne |
> | partnernamespaces | Ano | Ano |
> | partnerregistrations | Ne | Ne |
> | partnertopics | Ano | Ano |
> | systemtopics | Ano | Ano |
> | popisující | Ano | Ano |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | obsažené | Ano | Ano |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Ne | Ne |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Ne | Ne |
> | automanagedvmconfigurationprofiles | Ne | Ne |
> | guestconfigurationassignments | Ne | Ne |
> | Vybavení | Ne | Ne |
> | softwareupdateprofile | Ne | Ne |
> | softwareupdates | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hanainstances | Ne | Ne |
> | sapmonitors | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nemůžete se ale pohybovat mezi předplatnými síťových prostředků propojených s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nemůžete přesunout do nové skupiny prostředků síťovou kartu, která je připojená k virtuálnímu počítači pro daný cluster.
>
> Když přesunete cluster HDInsight do nového předplatného, napřed přesuňte další prostředky (třeba účet úložiště). Pak můžete cluster HDInsight přesunout sám o sobě.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | virtuální | Ano | Ano |
> | počítače/rozšíření | Ne | Ne |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datamanagery | Ano | Ano |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | Ne | Ne |
> | networkscopes | Ne | Ne |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ano | Ano |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ano | Ano |
> | upozorněníprotokoluaktivit | Ne | Ne |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | směrný | Ne | Ne |
> | calculatebaseline | Ne | Ne |
> | konstrukční | Ano | Ano |
> | datacollectionrules | Ne | Ne |
> | diagnosticsettings | Ne | Ne |
> | diagnosticsettingscategories | Ne | Ne |
> | EventType | Ne | Ne |
> | extendeddiagnosticsettings | Ne | Ne |
> | guestdiagnosticsettings | Ne | Ne |
> | logdefinitions | Ne | Ne |
> | Protokoly | Ne | Ne |
> | metricalerts | Ne | Ne |
> | metricbaselines | Ne | Ne |
> | metricdefinitions | Ne | Ne |
> | metricnamespaces | Ne | Ne |
> | metriky | Ne | Ne |
> | myworkbooks | Ne | Ne |
> | notificationgroups | Ne | Ne |
> | privatelinkscopes | Ano | Ano |
> | scheduledqueryrules | Ano | Ano |
> | topologie | Ne | Ne |
> | transactions | Ne | Ne |
> | vminsightsonboardingstatuses | Ne | Ne |
> | webové testy | Ano | Ano |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | iotapps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ano | Ano |
> | zapisovací | Ano | Ano |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hsmpools | Ne | Ne |
> | trezory | Ano | Ano |

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ne | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labaccounts | Ne | Ne |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Ne | Ne |
> | integrationaccounts | Ano | Ano |
> | integrationserviceenvironments | Ano | Ne |
> | integrationserviceenvironments/Inspirujte | Ano | Ne |
> | isolatedenvironments | Ne | Ne |
> | Zpracovávaný | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ne | Ne |
> | WebServices | Ano | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Ne | Ne |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | účty/pracovní prostory | Ne | Ne |
> | účty/pracovní prostory/projekty | Ne | Ne |
> | teamaccounts | Ne | Ne |
> | teamaccounts/pracovní prostory | Ne | Ne |
> | teamaccounts/pracovní prostory/projekty | Ne | Ne |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Ne | Ne |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |
> | pracovní prostory a výpočetní prostředky | Ne | Ne |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applyupdates | Ne | Ne |
> | configurationassignments | Ne | Ne |
> | maintenanceconfigurations | Ano | Ano |
> | aktualizovány | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | nebyly | Ne | Ne |
> | userassignedidentities | Ne | Ne |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managednetworks | Ne | Ne |
> | managednetworks / managednetworkgroups | Ne | Ne |
> | managednetworks / managednetworkpeeringpolicies | Ne | Ne |
> | oznámení | Ne | Ne |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Ne | Ne |
> | registrationdefinitions | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/privateatlases | Ano | Ano |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | MediaServices | Ano | Ano |
> | MediaServices/liveevents | Ano | Ano |
> | MediaServices/starají | Ano | Ano |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appclusters | Ne | Ne |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ano | Ano |
> | migrateprojects | Ano | Ano |
> | movecollections | Ne | Ne |
> | projekty | Ne | Ne |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Ne | Ne |
> | netappaccounts / backuppolicies | Ne | Ne |
> | netappaccounts / capacitypools | Ne | Ne |
> | netappaccounts/capacitypools/svazky | Ne | Ne |
> | netappaccounts/capacitypools/svazky/mounttargets | Ne | Ne |
> | netappaccounts/capacitypools/svazky/snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
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
> | expressroutegateways | Ne | Ne |
> | firewallpolicies | Ano | Ano |
> | frontdoors | Ne | Ne |
> | frontdoorwebapplicationfirewallpolicies | Ne | Ne |
> | ipgroups | Ano | Ano |
> | loadbalancers | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | localnetworkgateways | Ano | Ano |
> | natgateways | Ano | Ano |
> | networkexperimentprofiles | Ano | Ano |
> | networkintentpolicies | Ano | Ano |
> | networkinterfaces | Ano | Ano |
> | networkprofiles | Ne | Ne |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | Ano | Ne |
> | networkwatchers / connectionmonitors | Ano | Ne |
> | networkwatchers / flowlogs | Ano | Ne |
> | networkwatchers / pingmeshes | Ano | Ne |
> | p2svpngateways | Ne | Ne |
> | privatednszones | Ano | Ano |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privateendpointredirectmaps | Ne | Ne |
> | privateendpoints | Ano | Ano |
> | privatelinkservices | Ne | Ne |
> | publicipaddresses | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ano | Ano |
> | routefilters | Ne | Ne |
> | routetables | Ano | Ano |
> | serviceendpointpolicies | Ano | Ano |
> | trafficmanagerprofiles | Ano | Ano |
> | virtualhubs | Ne | Ne |
> | virtualnetworkgateways | Ano | Ano |
> | virtualnetworks | Ano | Ano |
> | virtualnetworktaps | Ne | Ne |
> | virtualrouters | Ano | Ano |
> | virtualwans | Ne | Ne |
> | vpngateways (virtuální síť WAN) | Ne | Ne |
> | vpnserverconfigurations | Ne | Ne |
> | vpnsites (virtuální síť WAN) | Ne | Ne |
> | webapplicationfirewallpolicies | Ano | Ano |

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |
> | obory názvů/notificationhubs | Ano | Ano |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | storageinsightconfigs | Ne | Ne |
> | pracovní prostory | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).
> 
> Pracovní prostory, které mají propojený účet Automation, nejde přesunout. Než začnete s přesunem, nezapomeňte zrušit propojení jakýchkoli účtů Automation.   

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managementassociations | Ne | Ne |
> | managementconfigurations | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | partnerské vztahy | Ano | Ano |
> | peeringservices | Ne | Ne |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | policyevents | Ne | Ne |
> | policystates | Ne | Ne |
> | policytrackedresources | Ne | Ne |
> | nápravy | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | řídicí panely | Ano | Ano |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | rootresources | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | schopností | Ano | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | uvádění | Ne | Ne |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Ne | Ne |
> | replicationeligibilityresults | Ne | Ne |
> | trezory | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Ne | Ne |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | odešle | Ano | Ano |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Ne | Ne |
> | childavailabilitystatuses | Ne | Ne |
> | childresources | Ne | Ne |
> | stránka events | Ne | Ne |
> | připomenutí | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Ne | Ne |
> | odkazy | Ne | Ne |
> | tags | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ne |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | searchservices | Ano | Ano |

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Ne | Ne |
> | advancedthreatprotectionsettings | Ne | Ne |
> | assessmentmetadata | Ne | Ne |
> | hodnocení | Ne | Ne |
> | automatizace | Ano | Ano |
> | complianceresults | Ne | Ne |
> | předpisů | Ne | Ne |
> | datacollectionagents | Ne | Ne |
> | devicesecuritygroups | Ne | Ne |
> | informationprotectionpolicies | Ne | Ne |
> | iotsecuritysolutions | Ano | Ano |
> | servervulnerabilityassessments | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | agregace | Ne | Ne |
> | alertrules | Ne | Ne |
> | alertruletemplates | Ne | Ne |
> | záložky | Ne | Ne |
> | věcech | Ne | Ne |
> | dataconnects | Ne | Ne |
> | dataconnectorscheckrequirements | Ne | Ne |
> | podnikům | Ne | Ne |
> | entityqueries | Ne | Ne |
> | incidenty | Ne | Ne |
> | officeconsents | Ne | Ne |
> | settings | Ne | Ne |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | brány | Ne | Ne |
> | sortiment | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |
> | existující | Ano | Ano |
> | clustery/aplikace | Ne | Ne |
> | containergroups | Ne | Ne |
> | containergroupsets | Ne | Ne |
> | edgeclusters | Ne | Ne |
> | managedclusters | Ne | Ne |
> | sítí | Ne | Ne |
> | secretstores | Ne | Ne |
> | volumes | Ne | Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | containergroups | Ne | Ne |
> | brány | Ano | Ano |
> | sítí | Ano | Ano |
> | záleží | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | uvádění | Ne | Ne |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | SignalR | Ano | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Ne | Ne |
> | aplikace | Ne | Ne |
> | jitrequests | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instancepools | Ne | Ne |
> | polohy | Ano | Ano |
> | managedinstances | Ne | Ne |
> | managedinstances/databáze | Ne | Ne |
> | servery | Ano | Ano |
> | servery/databáze | Ano | Ano |
> | servery/elasticpools | Ano | Ano |
> | servery/jobaccounts | Ano | Ano |
> | servery/jobagents | Ano | Ano |
> | virtualclusters | Ano | Ano |

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ano | Ano |
> | sqlvirtualmachines | Ano | Ano |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dwvm | Ne | Ne |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ano |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mezipaměti | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | jednatel | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Ano | Ano |

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | Ne | Ne |
> | prostředí/EventSources | Ne | Ne |
> | instance | Ne | Ne |
> | instance/prostředí | Ne | Ne |
> | instance/prostředí/EventSources | Ne | Ne |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | createsubscription | Ne | Ne |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | supporttickets | Ne | Ne |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |
> | pracovní prostory/bigdatapools | Ne | Ne |
> | pracovní prostory/sqlpools | Ne | Ne |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Ne | Ne |
> | resources | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | Ano | Ano |
> | prostředí/EventSources | Ano | Ano |
> | prostředí/referencedatasets | Ano | Ano |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Store | Ano | Ano |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Ne | Ne |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | account | Ne | Ne |
> | účet/rozšíření | Ne | Ne |
> | účet/projekt | Ne | Ne |

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Ne | Ne |
> | dedicatedcloudservices | Ne | Ne |
> | virtualmachines | Ne | Ne |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | Ne | Ne |
> | vnfs | Ne | Ne |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | plánují | Ne | Ne |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | certifikáty | Ne | Ano |
> | connectiongateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | hostingenvironments | Ne | Ne |
> | kubeenvironments | Ano | Ano |
> | serverových farem | Ano | Ano |
> | místa | Ano | Ano |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | staticsites | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Ne | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deviceservices | Ne | Ne |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | Ne | Ne |
> | monitorinstances | Ne | Ne |
> | Monitor | Ne | Ne |
> | notificationsettings | Ne | Ne |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky
Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

Pokud chcete získat stejná data jako soubor hodnot oddělených čárkami, Stáhněte si [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
