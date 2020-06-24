---
title: Přesunout podporu operace podle typu prostředku
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 3e27c319493613464dd6f1360171db6154064b0a
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781035"
---
# <a name="move-operation-support-for-resources"></a>Podpora operace přesunutí pro prostředky

Tento článek uvádí, jestli typ prostředku Azure podporuje operaci přesunu. Poskytuje taky informace o zvláštních podmínkách, které je potřeba vzít v úvahu při přesunu prostředku.

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturace](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. spotřeba](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
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
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentování](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migruje](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
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
> - [Microsoft. SerialConsole](#microsoftserialconsole)
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
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. monitor zátěže byl](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | domainservices | Ne | Ne |
> | domainservices / oucontainer | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Ne | Ne |
> | diagnosticsettingscategories | Ne | Ne |
> | Operace | Ne | Ne |
> | privatelinkforazuread | Ano | Ano |
> | tenantů | Ano | Ano |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | supportproviders | Ne | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Ne | Ne |
> | addsservices | Ne | Ne |
> | technici | Ne | Ne |
> | anonymousapiusers | Ne | Ne |
> | konfigurace | Ne | Ne |
> | Protokoly | Ne | Ne |
> | Operace | Ne | Ne |
> | sestavy | Ne | Ne |
> | servicehealthmetrics | Ne | Ne |
> | services | Ne | Ne |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konfiguračních | Ne | Ne |
> | generaterecommendations | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | Operace | Ne | Ne |
> | doporučit | Ne | Ne |
> | potlačení | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actionrules | Ano | Ano |
> | výstrahy | Ne | Ne |
> | alertslist | Ne | Ne |
> | alertsmetadata | Ne | Ne |
> | alertssummary | Ne | Ne |
> | alertssummarylist | Ne | Ne |
> | Operace | Ne | Ne |
> | smartdetectoralertrules | Ano | Ano |
> | smartgroups | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | checkservicenameavailability | Ne | Ne |
> | Operace | Ne | Ne |
> | reportfeedback | Ne | Ne |
> | služba | Ano | Ano |
> | validateservicename | Ne | Ne |

> [!IMPORTANT]
> Službu API Management, která je nastavená na SKU spotřeby, se nedá přesunout.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | configurationstores | Ano | Ano |
> | configurationstores / eventgridfilters | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/stav operationstatus | Ne | Ne |
> | Operace | Ne | Ne |
> | návratu | Ano | Ano |
> | pružina/aplikace | Ne | Ne |
> | Jarní/aplikace/nasazení | Ne | Ne |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apiapps | Ne | Ne |
> | appidentities | Ne | Ne |
> | brány | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instanci | Ne | Ne |
> | classicadministrators | Ne | Ne |
> | dataaliasy | Ne | Ne |
> | denyassignments | Ne | Ne |
> | elevateaccess | Ne | Ne |
> | findorphanroleassignments | Ne | Ne |
> | počtu | Ne | Ne |
> | Operace | Ne | Ne |
> | stav operationstatus | Ne | Ne |
> | oprávnění | Ne | Ne |
> | policyassignments | Ne | Ne |
> | policydefinitions | Ne | Ne |
> | policysetdefinitions | Ne | Ne |
> | privatelinkassociations | Ne | Ne |
> | provideroperations | Ne | Ne |
> | resourcemanagementprivatelinks | Ne | Ne |
> | RoleAssignments | Ne | Ne |
> | roleassignmentsusagemetrics | Ne | Ne |
> | roledefinitions | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ano | Ano |
> | automationaccounts/konfigurace | Ano | Ano |
> | automationaccounts/úlohy | Ne | Ne |
> | automationaccounts / privateendpointconnectionproxies | Ne | Ne |
> | automationaccounts / privateendpointconnections | Ne | Ne |
> | automationaccounts / privatelinkresources | Ne | Ne |
> | automationaccounts/Runbooky | Ano | Ano |
> | automationaccounts / softwareupdateconfigurations | Ne | Ne |
> | automationaccounts nebo Webhooky | Ne | Ne |
> | Operace | Ne | Ne |

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/checkquotaavailability | Ne | Ne |
> | umístění/checktrialavailability | Ne | Ne |
> | Operace | Ne | Ne |
> | privateclouds | Ano | Ano |
> | privateclouds/clustery | Ne | Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ano | Ano |
> | b2ctenants | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datacontrollery | Ne | Ne |
> | hybriddatamanagers | Ne | Ne |
> | Operace | Ne | Ne |
> | postgresinstances | Ne | Ne |
> | sqlinstances | Ne | Ne |
> | sqlmanagedinstances | Ne | Ne |
> | sqlserverinstances | Ne | Ne |
> | sqlserverregistrations | Ano | Ano |
> | sqlserverregistrations/SQLservers | Ne | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Ne | Ne |
> | Operace | Ne | Ne |
> | rozpoznávání | Ano | Ano |
> | registrace/customersubscriptions | Ne | Ne |
> | registrace/produkty | Ne | Ne |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/accountoperationresults | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/kvóty | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Ne | Ne |
> | billingaccounts/smlouvy | Ne | Ne |
> | billingaccounts / billingpermissions | Ne | Ne |
> | billingaccounts / billingprofiles | Ne | Ne |
> | billingaccounts / billingprofiles / availablebalance | Ne | Ne |
> | billingaccounts / billingprofiles / billingpermissions | Ne | Ne |
> | billingaccounts / billingprofiles / billingroleassignments | Ne | Ne |
> | billingaccounts / billingprofiles / billingroledefinitions | Ne | Ne |
> | billingaccounts / billingprofiles / billingsubscriptions | Ne | Ne |
> | billingaccounts / billingprofiles / createbillingroleassignment | Ne | Ne |
> | billingaccounts/billingprofiles/zákazníci | Ne | Ne |
> | billingaccounts/billingprofiles/– pokyny | Ne | Ne |
> | billingaccounts/billingprofiles/faktury | Ne | Ne |
> | billingaccounts/billingprofiles/faktury/pricesheet | Ne | Ne |
> | billingaccounts/billingprofiles/faktury/transakce | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Ne | Ne |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Ne | Ne |
> | billingaccounts/billingprofiles/invoicesections/Products | Ne | Ne |
> | billingaccounts/billingprofiles/invoicesections/Products/Transfer | Ne | Ne |
> | billingaccounts/billingprofiles/invoicesections/Products/updateautorenew | Ne | Ne |
> | billingaccounts/billingprofiles/invoicesections/transakce | Ne | Ne |
> | billingaccounts/billingprofiles/invoicesections/transfery | Ne | Ne |
> | billingaccounts / billingprofiles / patchoperations | Ne | Ne |
> | billingaccounts / billingprofiles / paymentmethods | Ne | Ne |
> | billingaccounts/billingprofiles/– zásady | Ne | Ne |
> | billingaccounts/billingprofiles/pricesheet | Ne | Ne |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Ne | Ne |
> | billingaccounts/billingprofiles/produkty | Ne | Ne |
> | billingaccounts/billingprofiles/transakcí | Ne | Ne |
> | billingaccounts / billingroleassignments | Ne | Ne |
> | billingaccounts / billingroledefinitions | Ne | Ne |
> | billingaccounts / billingsubscriptions | Ne | Ne |
> | billingaccounts/billingsubscriptions/faktury | Ne | Ne |
> | billingaccounts / createbillingroleassignment | Ne | Ne |
> | billingaccounts / createinvoicesectionoperations | Ne | Ne |
> | billingaccounts/zákazníci | Ne | Ne |
> | billingaccounts/Customers/billingpermissions | Ne | Ne |
> | billingaccounts/Customers/billingsubscriptions | Ne | Ne |
> | billingaccounts/Customers/initiatetransfer | Ne | Ne |
> | billingaccounts/Customers/policies | Ne | Ne |
> | billingaccounts/zákazníci/produkty | Ne | Ne |
> | billingaccounts/zákazníci/transakce | Ne | Ne |
> | billingaccounts/zákazníci/přenosy | Ne | Ne |
> | billingaccounts/oddělení | Ne | Ne |
> | billingaccounts / enrollmentaccounts | Ne | Ne |
> | billingaccounts/faktury | Ne | Ne |
> | billingaccounts / invoicesections | Ne | Ne |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Ne | Ne |
> | billingaccounts / invoicesections / billingsubscriptions | Ne | Ne |
> | billingaccounts/invoicesections/billingsubscriptions/Transfer | Ne | Ne |
> | billingaccounts/invoicesections/zvýšení oprávnění | Ne | Ne |
> | billingaccounts / invoicesections / initiatetransfer | Ne | Ne |
> | billingaccounts / invoicesections / patchoperations | Ne | Ne |
> | billingaccounts / invoicesections / productmoveoperations | Ne | Ne |
> | billingaccounts/invoicesections/produkty | Ne | Ne |
> | billingaccounts/invoicesections/produkty/přenos | Ne | Ne |
> | billingaccounts/invoicesections/Products/updateautorenew | Ne | Ne |
> | billingaccounts / invoicesections / producttransfersresults | Ne | Ne |
> | billingaccounts/invoicesections/transakcí | Ne | Ne |
> | billingaccounts/invoicesections/transfery | Ne | Ne |
> | billingaccounts / lineofcredit | Ne | Ne |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Ne | Ne |
> | billingaccounts / operationresults | Ne | Ne |
> | billingaccounts / patchoperations | Ne | Ne |
> | billingaccounts / paymentmethods | Ne | Ne |
> | billingaccounts/produkty | Ne | Ne |
> | billingaccounts/transakce | Ne | Ne |
> | billingperiods | Ne | Ne |
> | billingpermissions | Ne | Ne |
> | billingproperty | Ne | Ne |
> | billingroleassignments | Ne | Ne |
> | billingroledefinitions | Ne | Ne |
> | createbillingroleassignment | Ne | Ne |
> | oddělení | Ne | Ne |
> | enrollmentaccounts | Ne | Ne |
> | faktury | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | stav operationstatus | Ne | Ne |
> | Převede | Ne | Ne |
> | přenosy/accepttransfer | Ne | Ne |
> | přenosy/declinetransfer | Ne | Ne |
> | přenosy/stav operationstatus | Ne | Ne |
> | přenosy/validatetransfer | Ne | Ne |
> | validateaddress | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Ne | Ne |
> | mapapis | Ne | Ne |
> | Operace | Ne | Ne |
> | updatecommunicationpreference | Ne | Ne |

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
> | polohy | Ne | Ne |
> | umístění/blockchainmemberoperationresults | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/listconsortiums | Ne | Ne |
> | umístění/watcheroperationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | sledovacích procesů | Ne | Ne |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |
> | tokenservices | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Ne | Ne |
> | blueprintassignments / assignmentoperations | Ne | Ne |
> | blueprintassignments/operace | Ne | Ne |
> | podrobné plány | Ne | Ne |
> | modrotisky/artefakty | Ne | Ne |
> | plány/verze | Ne | Ne |
> | modrotisky/verze/artefakty | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | botservices | Ano | Ano |
> | botservices/kanály | Ne | Ne |
> | botservices/připojení | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | listauthserviceproviders | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | Operace | Ne | Ne |
> | Redis | Ano | Ano |
> | Redis/eventgridfilters | Ne | Ne |
> | Redis/privatelinkresources | Ne | Ne |
> | redisenterprise | Ne | Ne |

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Ne | Ne |
> | calculateexchange | Ne | Ne |
> | calculateprice | Ne | Ne |
> | calculatepurchaseprice | Ne | Ne |
> | spustí | Ne | Ne |
> | checkoffers | Ne | Ne |
> | checkpurchasestatus | Ne | Ne |
> | checkscopes | Ne | Ne |
> | commercialreservationorders | Ne | Ne |
> | výměn | Ne | Ne |
> | listbenefits | Ne | Ne |
> | Operace | Ne | Ne |
> | placepurchaseorder | Ne | Ne |
> | reservationorders | Ne | Ne |
> | reservationorders / availablescopes | Ne | Ne |
> | reservationorders / calculaterefund | Ne | Ne |
> | reservationorders/sloučit | Ne | Ne |
> | reservationorders/rezervace | Ne | Ne |
> | reservationorders/rezervací/availablescopes | Ne | Ne |
> | reservationorders/rezervace/Revize | Ne | Ne |
> | reservationorders/vrátit | Ne | Ne |
> | reservationorders/Split | Ne | Ne |
> | reservationorders/swap | Ne | Ne |
> | rezervace | Ne | Ne |
> | resources | Ne | Ne |
> | validatereservationorder | Ne | Ne |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Ne | Ne |
> | cdnwebapplicationfirewallpolicies | Ano | Ano |
> | checknameavailability | Ne | Ne |
> | checkresourceusage | Ne | Ne |
> | edgenodes | Ne | Ne |
> | operationresults | Ne | Ne |
> | operationresults / profileresults | Ne | Ne |
> | operationresults / profileresults / endpointresults | Ne | Ne |
> | operationresults / profileresults / endpointresults / customdomainresults | Ne | Ne |
> | operationresults / profileresults / endpointresults / origingroupresults | Ne | Ne |
> | operationresults / profileresults / endpointresults / originresults | Ne | Ne |
> | Operace | Ne | Ne |
> | uživatelů | Ano | Ano |
> | profily/koncové body | Ano | Ano |
> | profily/koncové body/customdomains | Ne | Ne |
> | profily/koncové body/origingroups | Ne | Ne |
> | profily/koncové body/zdroje | Ne | Ne |
> | validateprobe | Ne | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ano | Ano |
> | certificateorders/certifikáty | Ne | Ne |
> | Operace | Ne | Ne |
> | validatecertificateregistrationinformation | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | vestavěn | Ne | Ne |
> | checkdomainnameavailability | Ne | Ne |
> | domainnames | Ano | Ne |
> | DomainNames/možnosti | Ne | Ne |
> | domainnames / internalloadbalancers | Ne | Ne |
> | domainnames / servicecertificates | Ne | Ne |
> | DomainNames/sloty | Ne | Ne |
> | DomainNames/sloty/role | Ne | Ne |
> | DomainNames/sloty/role/metricdefinitions | Ne | Ne |
> | DomainNames/sloty/role/metriky | Ne | Ne |
> | movesubscriptionresources | Ne | Ne |
> | operatingsystemfamilies | Ne | Ne |
> | operatingsystems | Ne | Ne |
> | Operace | Ne | Ne |
> | operationstatuses | Ne | Ne |
> | quotas | Ne | Ne |
> | typ prostředků | Ne | Ne |
> | validatesubscriptionmoveavailability | Ne | Ne |
> | virtualmachines | Ano | Ne |
> | virtualmachines / diagnosticsettings | Ne | Ne |
> | virtualmachines / metricdefinitions | Ne | Ne |
> | VirtualMachines/metriky | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | vestavěn | Ne | Ne |
> | expressroutecrossconnections | Ne | Ne |
> | expressroutecrossconnections/partnerské vztahy | Ne | Ne |
> | gatewaysupporteddevices | Ne | Ne |
> | networksecuritygroups | Ne | Ne |
> | Operace | Ne | Ne |
> | quotas | Ne | Ne |
> | reservedips | Ne | Ne |
> | virtualnetworks | Ne | Ne |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Ne | Ne |
> | virtualnetworks/virtualnetworkpeerings | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | vestavěn | Ne | Ne |
> | checkstorageaccountavailability | Ne | Ne |
> | disků | Ne | Ne |
> | images | Ne | Ne |
> | Operace | Ne | Ne |
> | OSImage | Ne | Ne |
> | osplatformimages | Ne | Ne |
> | publicimages | Ne | Ne |
> | quotas | Ne | Ne |
> | storageaccounts | Ano | Ne |
> | storageaccounts/blobservices | Ne | Ne |
> | storageaccounts/služby | Ne | Ne |
> | storageaccounts/metricdefinitions | Ne | Ne |
> | storageaccounts/metriky | Ne | Ne |
> | storageaccounts/queueservices | Ne | Ne |
> | storageaccounts/služby | Ne | Ne |
> | storageaccounts/služby/diagnosticsettings | Ne | Ne |
> | storageaccounts/služby/metricdefinitions | Ne | Ne |
> | storageaccounts/služby/metriky | Ne | Ne |
> | storageaccounts/tableservices | Ne | Ne |
> | storageaccounts/vmimages | Ne | Ne |
> | vmimages | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | checkdomainavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/checkskuavailability | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |
> | ratecard | Ne | Ne |
> | usageaggregates | Ne | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ano | Ano |
> | diskaccesses | Ne | Ne |
> | diskencryptionsets | Ne | Ne |
> | disků | Ano | Ano |
> | Galerie | Ne | Ne |
> | Galerie/image | Ne | Ne |
> | Galerie/image/verze | Ne | Ne |
> | hostgroups | Ne | Ne |
> | hostgroups/hostitelé | Ne | Ne |
> | images | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/artifactpublishers | Ne | Ne |
> | umístění/capsoperations | Ne | Ne |
> | umístění/diskoperations | Ne | Ne |
> | umístění/loganalytics | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/vydavatelé | Ne | Ne |
> | umístění/runcommands | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | umístění/VirtualMachines | Ne | Ne |
> | umístění/povolených velikostí | Ne | Ne |
> | umístění/vsmoperations | Ne | Ne |
> | Operace | Ne | Ne |
> | proximityplacementgroups | Ano | Ano |
> | restorepointcollections | Ne | Ne |
> | restorepointcollections / restorepoints | Ne | Ne |
> | sharedvmextensions | Ne | Ne |
> | sharedvmimages | Ne | Ne |
> | sharedvmimages/verze | Ne | Ne |
> | snímky | Ano | Ano |
> | sshpublickeys | Ne | Ne |
> | virtualmachines | Ano | Ano |
> | VirtualMachines/rozšíření | Ano | Ano |
> | virtualmachines / metricdefinitions | Ne | Ne |
> | virtualmachines / runcommands | Ne | Ne |
> | virtualmachinescalesets | Ano | Ano |
> | virtualmachinescalesets/rozšíření | Ne | Ne |
> | virtualmachinescalesets/networkinterfaces | Ne | Ne |
> | virtualmachinescalesets/publicipaddresses | Ne | Ne |
> | virtualmachinescalesets/VirtualMachines | Ne | Ne |
> | virtualmachinescalesets/VirtualMachines/networkinterfaces | Ne | Ne |

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
> | Operace | Ne | Ne |
> | stav operationstatus | Ne | Ne |
> | pricesheets | Ne | Ne |
> | produktech | Ne | Ne |
> | reservationdetails | Ne | Ne |
> | reservationrecommendationdetails | Ne | Ne |
> | reservationrecommendations | Ne | Ne |
> | reservationsummaries | Ne | Ne |
> | reservationtransactions | Ne | Ne |
> | tags | Ne | Ne |
> | tenantů | Ne | Ne |
> | uvedenými | Ne | Ne |
> | usagedetails | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containergroups | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/cachedimages | Ne | Ne |
> | umístění/možnosti | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | Operace | Ne | Ne |
> | serviceassociationlinks | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/autorizace | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/setupauth | Ne | Ne |
> | Operace | Ne | Ne |
> | registr | Ano | Ano |
> | Registry/agentpools | Ano | Ano |
> | Registry/agentpools/listqueuestatus | Ne | Ne |
> | Registry a sestavení | Ne | Ne |
> | Registry/buildy/zrušit | Ne | Ne |
> | Registry/buildy/getloglink | Ne | Ne |
> | Registry/BuildTasks | Ano | Ano |
> | Registry/BuildTasks/listsourcerepositoryproperties | Ne | Ne |
> | Registry/BuildTasks/kroky | Ne | Ne |
> | Registry/BuildTasks/Steps/listbuildarguments | Ne | Ne |
> | Registry/eventgridfilters | Ne | Ne |
> | Registry/exportpipelines | Ne | Ne |
> | Registry/generatecredentials | Ne | Ne |
> | Registry/getbuildsourceuploadurl | Ne | Ne |
> | Registry/getpřihlašovací údaje | Ne | Ne |
> | Registry/importimage | Ne | Ne |
> | Registry/importpipelines | Ne | Ne |
> | Registry/listbuildsourceuploadurl | Ne | Ne |
> | Registry/listcredentials | Ne | Ne |
> | Registry/ListPolicies | Ne | Ne |
> | Registry/listusages | Ne | Ne |
> | Registry/pipelineruns | Ne | Ne |
> | Registry/privateendpointconnectionproxies | Ne | Ne |
> | Registry/privateendpointconnectionproxies/ověřit | Ne | Ne |
> | Registry/privateendpointconnections | Ne | Ne |
> | Registry/privatelinkresources | Ne | Ne |
> | Registry/QueueBuild | Ne | Ne |
> | Registry/regeneratecredential | Ne | Ne |
> | Registry/regeneratecredentials | Ne | Ne |
> | Registry/replikace | Ano | Ano |
> | Registry/běhy | Ne | Ne |
> | Registry/spuštění/zrušit | Ne | Ne |
> | Registry/běhy/listlogsasurl | Ne | Ne |
> | Registry/schedulerun | Ne | Ne |
> | Registry/scopemaps | Ne | Ne |
> | Registry/taskruns | Ne | Ne |
> | Registry/taskruns/ListDetails | Ne | Ne |
> | Registry/úlohy | Ano | Ano |
> | Registry/úlohy/ListDetails | Ne | Ne |
> | Registry a tokeny | Ne | Ne |
> | Registry/updatepolicies | Ne | Ne |
> | Registry a Webhooky | Ano | Ano |
> | Registry/Webhooky/getcallbackconfig | Ne | Ne |
> | Registry/Webhooky/listevents | Ne | Ne |
> | Registry/Webhooky/příkazy k odeslání | Ne | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containerservices | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/openshiftclusters | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/orchestrace | Ne | Ne |
> | managedclusters | Ne | Ne |
> | openshiftmanagedclusters | Ne | Ne |
> | Operace | Ne | Ne |

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
> | billingaccounts | Ne | Ne |
> | projektů | Ne | Ne |
> | cloudconnectors | Ne | Ne |
> | konektory | Ano | Ano |
> | oddělení | Ne | Ne |
> | použijí | Ne | Ne |
> | enrollmentaccounts | Ne | Ne |
> | vývozních | Ne | Ne |
> | externalbillingaccounts | Ne | Ne |
> | externalbillingaccounts/výstrahy | Ne | Ne |
> | externalbillingaccounts/dimenzí | Ne | Ne |
> | externalbillingaccounts/prognóza | Ne | Ne |
> | externalbillingaccounts/dotaz | Ne | Ne |
> | externalsubscriptions | Ne | Ne |
> | externalsubscriptions/výstrahy | Ne | Ne |
> | externalsubscriptions/dimenzí | Ne | Ne |
> | externalsubscriptions/prognóza | Ne | Ne |
> | externalsubscriptions/dotaz | Ne | Ne |
> | forecast | Ne | Ne |
> | Operace | Ne | Ne |
> | query | Ne | Ne |
> | register | Ne | Ne |
> | reportconfigs | Ne | Ne |
> | sestavy | Ne | Ne |
> | settings | Ne | Ne |
> | showbackrules | Ne | Ne |
> | zobrazení | Ne | Ne |

## <a name="microsoftcostmanagementexports"></a>Microsoft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Hubs | Ne | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |
> | požádal | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | přidružení | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | resourceproviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/availableskus | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/regionconfiguration | Ne | Ne |
> | umístění/validateaddress | Ne | Ne |
> | umístění/ValidateInputs | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableskus | Ne | Ne |
> | databoxedgedevices | Ano | Ano |
> | databoxedgedevices / checknameavailability | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/getnetworkpolicies | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | pracovní prostory | Ne | Ne |
> | pracovní prostory/dbworkspaces | Ne | Ne |
> | pracovní prostory/virtualnetworkpeerings | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | spustí | Ano | Ano |
> | checknameavailability | Ne | Ne |
> | datacatalogs | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/úlohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |

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
> | checkazuredatafactorynameavailability | Ne | Ne |
> | checkdatafactorynameavailability | Ne | Ne |
> | objekty DataFactory | Ano | Ano |
> | DataFactory/diagnosticsettings | Ne | Ne |
> | DataFactory/metricdefinitions | Ne | Ne |
> | datafactoryschema | Ne | Ne |
> | továrny | Ano | Ano |
> | továrny/integrationruntimes | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/configurefactoryrepo | Ne | Ne |
> | umístění/getfeaturevalue | Ne | Ne |
> | Operace | Ne | Ne |

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
> | účty/datalakestoreaccounts | Ne | Ne |
> | účty/storageaccounts | Ne | Ne |
> | účty/storageaccounts/kontejnery | Ne | Ne |
> | účty/storageaccounts/kontejnery/listsastokens | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/schopnost | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/eventgridfilters | Ne | Ne |
> | účty/firewallrules | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/schopnost | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | services | Ne | Ne |
> | služby a projekty | Ne | Ne |
> | otvory | Ne | Ne |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupvaults | Ne | Ne |
> | polohy | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/sdílené složky | Ne | Ne |
> | účty/akcie/datové sady | Ne | Ne |
> | účty/akcie/pozvánky | Ne | Ne |
> | účty/akcie/providersharesubscriptions | Ne | Ne |
> | účty/akcie/synchronizationsettings | Ne | Ne |
> | účty/sharesubscriptions | Ne | Ne |
> | účty/sharesubscriptions/consumersourcedatasets | Ne | Ne |
> | účty/sharesubscriptions/datasetmappings | Ne | Ne |
> | účty/sharesubscriptions/triggery | Ne | Ne |
> | listinvitations | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/consumerinvitations | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/rejectinvitation | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/azureasyncoperation | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/performancetiers | Ne | Ne |
> | umístění/privateendpointconnectionazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionoperationresults | Ne | Ne |
> | umístění/privateendpointconnectionproxyazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionproxyoperationresults | Ne | Ne |
> | umístění/recommendedactionsessionsazureasyncoperation | Ne | Ne |
> | umístění/recommendedactionsessionsoperationresults | Ne | Ne |
> | umístění/securityalertpoliciesazureasyncoperation | Ne | Ne |
> | umístění/securityalertpoliciesoperationresults | Ne | Ne |
> | umístění/serverkeyazureasyncoperation | Ne | Ne |
> | umístění/serverkeyoperationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | servery | Ano | Ano |
> | servery/poradci | Ne | Ne |
> | servery/privateendpointconnectionproxies | Ne | Ne |
> | servery/privateendpointconnections | Ne | Ne |
> | servery/privatelinkresources | Ne | Ne |
> | servery/querytexts | Ne | Ne |
> | servery/recoverableservers | Ne | Ne |
> | servery/topquerystatistics | Ne | Ne |
> | servery/virtualnetworkrules | Ne | Ne |
> | servery/waitstatistics | Ne | Ne |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/administratorazureasyncoperation | Ne | Ne |
> | umístění/administratoroperationresults | Ne | Ne |
> | umístění/azureasyncoperation | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/performancetiers | Ne | Ne |
> | umístění/privateendpointconnectionazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionoperationresults | Ne | Ne |
> | umístění/privateendpointconnectionproxyazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionproxyoperationresults | Ne | Ne |
> | umístění/recommendedactionsessionsazureasyncoperation | Ne | Ne |
> | umístění/recommendedactionsessionsoperationresults | Ne | Ne |
> | umístění/securityalertpoliciesazureasyncoperation | Ne | Ne |
> | umístění/securityalertpoliciesoperationresults | Ne | Ne |
> | umístění/serverkeyazureasyncoperation | Ne | Ne |
> | umístění/serverkeyoperationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | servery | Ano | Ano |
> | servery/poradci | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/privateendpointconnectionproxies | Ne | Ne |
> | servery/privateendpointconnections | Ne | Ne |
> | servery/privatelinkresources | Ne | Ne |
> | servery/querytexts | Ne | Ne |
> | servery/recoverableservers | Ne | Ne |
> | servery/topquerystatistics | Ne | Ne |
> | servery/virtualnetworkrules | Ne | Ne |
> | servery/waitstatistics | Ne | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/administratorazureasyncoperation | Ne | Ne |
> | umístění/administratoroperationresults | Ne | Ne |
> | umístění/azureasyncoperation | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/performancetiers | Ne | Ne |
> | umístění/privateendpointconnectionazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionoperationresults | Ne | Ne |
> | umístění/privateendpointconnectionproxyazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionproxyoperationresults | Ne | Ne |
> | umístění/recommendedactionsessionsazureasyncoperation | Ne | Ne |
> | umístění/recommendedactionsessionsoperationresults | Ne | Ne |
> | umístění/securityalertpoliciesazureasyncoperation | Ne | Ne |
> | umístění/securityalertpoliciesoperationresults | Ne | Ne |
> | umístění/serverkeyazureasyncoperation | Ne | Ne |
> | umístění/serverkeyoperationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | servergroups | Ne | Ne |
> | servery | Ano | Ano |
> | servery/poradci | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/privateendpointconnectionproxies | Ne | Ne |
> | servery/privateendpointconnections | Ne | Ne |
> | servery/privatelinkresources | Ne | Ne |
> | servery/querytexts | Ne | Ne |
> | servery/recoverableservers | Ne | Ne |
> | servery/topquerystatistics | Ne | Ne |
> | servery/virtualnetworkrules | Ne | Ne |
> | servery/waitstatistics | Ne | Ne |
> | serversv2 | Ano | Ano |
> | singleservers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ano | Ano |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | uvádění | Ano | Ano |
> | servicetopologies | Ano | Ano |
> | servicetopologies/služby | Ano | Ano |
> | servicetopologies/služby/serviceunits | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ano | Ano |
> | applicationgroups/aplikace | Ne | Ne |
> | applicationgroups/desktopy | Ne | Ne |
> | applicationgroups / startmenuitems | Ne | Ne |
> | hostpools | Ano | Ano |
> | hostpools / sessionhosts | Ne | Ne |
> | hostpools / sessionhosts / usersessions | Ne | Ne |
> | hostpools / usersessions | Ne | Ne |
> | Operace | Ne | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | checkprovisioningservicenameavailability | Ne | Ne |
> | elasticpools | Ne | Ne |
> | elasticpools / iothubtenants | Ne | Ne |
> | iothubs | Ano | Ano |
> | iothubs/eventgridfilters | Ne | Ne |
> | iothubs/SecuritySettings | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | provisioningservices | Ano | Ano |
> | použití | Ne | Ne |

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
> | řadiče/listconnectiondetails | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/checkcontainerhostmapping | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labcenters | Ne | Ne |
> | Labs | Ano | Ne |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/servicerunners | Ano | Ano |
> | Labs/VirtualMachines | Ano | Ne |
> | polohy | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | Operace | Ne | Ne |
> | časových | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Ne | Ne |
> | digitaltwinsinstances / operationresults | Ne | Ne |
> | polohy | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Ne | Ne |
> | databaseaccounts | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Ne | Ne |
> | doménu | Ano | Ano |
> | domény/domainownershipidentifiers | Ne | Ne |
> | generatessorequest | Ne | Ne |
> | listdomainrecommendations | Ne | Ne |
> | Operace | Ne | Ne |
> | topleveldomains | Ne | Ne |
> | validatedomainregistrationinformation | Ne | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |
> | domény a témata | Ne | Ne |
> | eventsubscriptions | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. |
> | extensiontopics | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/EventSubscriptions | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | umístění/topictypes | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | operationsstatus | Ne | Ne |
> | partnernamespaces | Ano | Ano |
> | partnernamespaces/eventchannels | Ne | Ne |
> | partnerregistrations | Ne | Ne |
> | partnertopics | Ano | Ano |
> | partnertopics / eventsubscriptions | Ne | Ne |
> | systemtopics | Ano | Ano |
> | systemtopics / eventsubscriptions | Ne | Ne |
> | popisující | Ano | Ano |
> | topictypes | Ne | Ne |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | checknamespaceavailability | Ne | Ne |
> | existující | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | Ne | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne | Ne |
> | obory názvů/disasterrecoveryconfigs/checknameavailability | Ne | Ne |
> | obory názvů/eventhubs | Ne | Ne |
> | obory názvů/eventhubs/autorizačních pravidel | Ne | Ne |
> | obory názvů/eventhubs/consumergroups | Ne | Ne |
> | obory názvů/networkrulesets | Ne | Ne |
> | Operace | Ne | Ne |
> | skladové | Ne | Ne |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operace | Ne | Ne |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | featureproviders | Ne | Ne |
> | funkce | Ne | Ne |
> | Operace | Ne | Ne |
> | dodavateli | Ne | Ne |
> | subscriptionfeatureregistrations | Ne | Ne |

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
> | Operace | Ne | Ne |
> | Vybavení | Ne | Ne |
> | softwareupdateprofile | Ne | Ne |
> | softwareupdates | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hanainstances | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | Operace | Ne | Ne |
> | sapmonitors | Ano | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Ne | Ne |
> | polohy | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | clustery/aplikace | Ne | Ne |
> | clustery/operationresults | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/azureasyncoperations | Ne | Ne |
> | umístění/billingspecs | Ne | Ne |
> | umístění/možnosti | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | umístění/validatecreaterequest | Ne | Ne |
> | Operace | Ne | Ne |

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nemůžete se ale pohybovat mezi předplatnými síťových prostředků propojených s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nemůžete přesunout do nové skupiny prostředků síťovou kartu, která je připojená k virtuálnímu počítači pro daný cluster.
>
> Když přesunete cluster HDInsight do nového předplatného, napřed přesuňte další prostředky (třeba účet úložiště). Pak můžete cluster HDInsight přesunout sám o sobě.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | services | Ano | Ano |
> | služby/privateendpointconnections | Ne | Ne |
> | služby/privatelinkresources | Ne | Ne |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/stav operationstatus | Ne | Ne |
> | virtuální | Ano | Ano |
> | počítače/rozšíření | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datamanagery | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | vnfs | Ne | Ne |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | Ne | Ne |
> | polohy | Ne | Ne |
> | networkscopes | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ano | Ano |
> | upozorněníprotokoluaktivit | Ne | Ne |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | standardní hodnoty | Ne | Ne |
> | calculatebaseline | Ne | Ne |
> | konstrukční | Ano | Ano |
> | komponenty/události | Ne | Ne |
> | komponenty/linkedstorageaccounts | Ne | Ne |
> | komponenty/metadata | Ne | Ne |
> | komponenty/metriky | Ne | Ne |
> | komponenty/pricingplans | Ne | Ne |
> | komponenty a dotazy | Ne | Ne |
> | datacollectionrules | Ne | Ne |
> | diagnosticsettings | Ne | Ne |
> | diagnosticsettingscategories | Ne | Ne |
> | eventcategories | Ne | Ne |
> | EventType | Ne | Ne |
> | extendeddiagnosticsettings | Ne | Ne |
> | guestdiagnosticsettings | Ne | Ne |
> | listmigrationdate | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | logdefinitions | Ne | Ne |
> | logprofiles | Ne | Ne |
> | Protokoly | Ne | Ne |
> | metricalerts | Ne | Ne |
> | metricbaselines | Ne | Ne |
> | metricbatch | Ne | Ne |
> | metricdefinitions | Ne | Ne |
> | metricnamespaces | Ne | Ne |
> | metriky | Ne | Ne |
> | migratealertrules | Ne | Ne |
> | migratetonewpricingmodel | Ne | Ne |
> | myworkbooks | Ne | Ne |
> | notificationgroups | Ne | Ne |
> | Operace | Ne | Ne |
> | privatelinkscopeoperationstatuses | Ne | Ne |
> | privatelinkscopes | Ne | Ne |
> | privatelinkscopes / privateendpointconnectionproxies | Ne | Ne |
> | privatelinkscopes / privateendpointconnections | Ne | Ne |
> | privatelinkscopes / scopedresources | Ne | Ne |
> | rollbacktolegacypricingmodel | Ne | Ne |
> | scheduledqueryrules | Ano | Ano |
> | topologie | Ne | Ne |
> | transactions | Ne | Ne |
> | vminsightsonboardingstatuses | Ne | Ne |
> | webové testy | Ano | Ano |
> | webtests/gettestresultfile | Ne | Ne |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apptemplates | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | checksubdomainavailability | Ne | Ne |
> | iotapps | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ano | Ano |
> | zapisovací | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | deletedvaults | Ne | Ne |
> | hsmpools | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/deletedvaults | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | managedhsms | Ne | Ne |
> | Operace | Ne | Ne |
> | trezory | Ano | Ano |
> | trezory/accesspolicies | Ne | Ne |
> | trezory/eventgridfilters | Ne | Ne |
> | trezory/tajné klíče | Ne | Ne |

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | registeredsubscriptions | Ne | Ne |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Ne | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | clustery/attacheddatabaseconfigurations | Ne | Ne |
> | clustery/databáze | Ne | Ne |
> | clustery, databáze/datapřipojení | Ne | Ne |
> | clustery/databáze/eventhubconnections | Ne | Ne |
> | clustery/databáze/principalassignments | Ne | Ne |
> | clustery/principalassignments | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labaccounts | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | Operace | Ne | Ne |
> | uživatelé | Ne | Ne |

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
> | polohy | Ne | Ne |
> | umístění/pracovní postupy | Ne | Ne |
> | Operace | Ne | Ne |
> | Zpracovávaný | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | Operace | Ne | Ne |
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

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/computeoperationsstatus | Ne | Ne |
> | umístění/kvóty | Ne | Ne |
> | umístění/updatequotas | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | umístění/povolených velikostí | Ne | Ne |
> | umístění/workspaceoperationsstatus | Ne | Ne |
> | Operace | Ne | Ne |
> | pracovní prostory | Ne | Ne |
> | pracovní prostory a výpočetní prostředky | Ne | Ne |
> | pracovní prostory/eventgridfilters | Ne | Ne |

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
> | Operace | Ne | Ne |
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
> | marketplaceregistrationdefinitions | Ne | Ne |
> | Operace | Ne | Ne |
> | operationstatuses | Ne | Ne |
> | registrationassignments | Ne | Ne |
> | registrationdefinitions | Ne | Ne |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | getentities | Ne | Ne |
> | managementgroups | Ne | Ne |
> | managementgroups/nastavení | Ne | Ne |
> | operationresults | Ne | Ne |
> | operationresults / asyncoperation | Ne | Ne |
> | Operace | Ne | Ne |
> | resources | Ne | Ne |
> | starttenantbackfill | Ne | Ne |
> | tenantbackfillstatus | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/eventgridfilters | Ne | Ne |
> | účty/privateatlases | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Ne | Ne |
> | Rozsah | Ne | Ne |
> | offertypes | Ne | Ne |
> | offertypes/vydavatelé | Ne | Ne |
> | offertypes/vydavatelé/nabídky | Ne | Ne |
> | offertypes/vydavatelé/nabídky/plány | Ne | Ne |
> | offertypes/vydavatelé/nabídky/plány/smlouvy | Ne | Ne |
> | offertypes/vydavatelé/nabídky/plány/konfigurace | Ne | Ne |
> | offertypes/vydavatelé/nabídky/plány/konfigurace/importimage | Ne | Ne |
> | Operace | Ne | Ne |
> | privategalleryitems | Ne | Ne |
> | privatestoreclient | Ne | Ne |
> | privatestores | Ne | Ne |
> | privatestores/nabídky | Ne | Ne |
> | produktech | Ne | Ne |
> | zdrojů | Ne | Ne |
> | Vydavatelé/nabídky | Ne | Ne |
> | Vydavatelé/nabídky/změny | Ne | Ne |
> | register | Ne | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Ne | Ne |
> | listcommunicationpreference | Ne | Ne |
> | Operace | Ne | Ne |
> | updatecommunicationpreference | Ne | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | o | Ne | Ne |
> | offertypes | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | MediaServices | Ano | Ano |
> | MediaServices/accountfilters | Ne | Ne |
> | MediaServices/assety | Ne | Ne |
> | MediaServices/assety/assetfilters | Ne | Ne |
> | MediaServices/contentkeypolicies | Ne | Ne |
> | MediaServices/eventgridfilters | Ne | Ne |
> | MediaServices/liveeventoperations | Ne | Ne |
> | MediaServices/liveevents | Ano | Ano |
> | MediaServices/liveevents/liveoutputs | Ne | Ne |
> | MediaServices/liveoutputoperations | Ne | Ne |
> | MediaServices/streamingendpointoperations | Ne | Ne |
> | MediaServices/starají | Ano | Ano |
> | MediaServices/streaminglocators | Ne | Ne |
> | MediaServices/streamingpolicies | Ne | Ne |
> | MediaServices/transformace | Ne | Ne |
> | MediaServices/transformace/úlohy | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appclusters | Ne | Ne |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/assessmentoptions | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | migrateprojects | Ne | Ne |
> | movecollections | Ne | Ne |
> | Operace | Ne | Ne |
> | projekty | Ne | Ne |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | objectunderstandingaccounts | Ne | Ne |
> | Operace | Ne | Ne |
> | remoterenderingaccounts | Ano | Ano |
> | spatialanchorsaccounts | Ano | Ano |

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
> | Operace | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Ne | Ne |
> | applicationgatewayavailableresponseheaders | Ne | Ne |
> | applicationgatewayavailableservervariables | Ne | Ne |
> | applicationgatewayavailablessloptions | Ne | Ne |
> | applicationgatewayavailablewafrulesets | Ne | Ne |
> | applicationgateways | Ne | Ne |
> | applicationgatewaywebapplicationfirewallpolicies | Ne | Ne |
> | applicationsecuritygroups | Ano | Ano |
> | azurefirewallfqdntags | Ne | Ne |
> | azurefirewalls | Ne | Ne |
> | bastionhosts | Ne | Ne |
> | bgpservicecommunities | Ne | Ne |
> | checkfrontdoornameavailability | Ne | Ne |
> | checktrafficmanagernameavailability | Ne | Ne |
> | připojení | Ano | Ano |
> | ddoscustompolicies | Ano | Ano |
> | ddosprotectionplans | Ne | Ne |
> | dnsoperationresults | Ne | Ne |
> | dnsoperationstatuses | Ne | Ne |
> | dnszones | Ano | Ano |
> | dnszones/a | Ne | Ne |
> | dnszones/AAAA | Ne | Ne |
> | dnszones/vše | Ne | Ne |
> | dnszones/CAA | Ne | Ne |
> | dnszones/CNAME | Ne | Ne |
> | dnszones/MX | Ne | Ne |
> | dnszones/NS | Ne | Ne |
> | dnszones/PTR | Ne | Ne |
> | dnszones/sady záznamů | Ne | Ne |
> | dnszones/SOA | Ne | Ne |
> | dnszones/SRV | Ne | Ne |
> | dnszones/txt | Ne | Ne |
> | expressroutecircuits | Ne | Ne |
> | expressroutegateways | Ne | Ne |
> | expressrouteserviceproviders | Ne | Ne |
> | firewallpolicies | Ano | Ano |
> | frontdooroperationresults | Ne | Ne |
> | frontdoors | Ne | Ne |
> | frontdoors / frontendendpoints | Ne | Ne |
> | frontdoorwebapplicationfirewallmanagedrulesets | Ne | Ne |
> | frontdoorwebapplicationfirewallpolicies | Ne | Ne |
> | getdnsresourcereference | Ne | Ne |
> | internalnotify | Ne | Ne |
> | ipallocations | Ano | Ano |
> | ipgroups | Ano | Ano |
> | loadbalancers | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | localnetworkgateways | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/autoapprovedprivatelinkservices | Ne | Ne |
> | umístění/availabledelegations | Ne | Ne |
> | umístění/availableprivateendpointtypes | Ne | Ne |
> | umístění/availableservicealiases | Ne | Ne |
> | umístění/baremetaltenants | Ne | Ne |
> | umístění/batchnotifyprivateendpointsforresourcemove | Ne | Ne |
> | umístění/batchvalidateprivateendpointsforresourcemove | Ne | Ne |
> | umístění/checkacceleratednetworkingsupport | Ne | Ne |
> | umístění/checkdnsnameavailability | Ne | Ne |
> | umístění/checkprivatelinkservicevisibility | Ne | Ne |
> | umístění/commitinternalazurenetworkmanagerconfiguration | Ne | Ne |
> | umístění/effectiveresourceownership | Ne | Ne |
> | umístění/nfvoperationresults | Ne | Ne |
> | umístění/nfvoperations | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/servicetags | Ne | Ne |
> | umístění/setresourceownership | Ne | Ne |
> | umístění/supportedvirtualmachinesizes | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | umístění/validateresourceownership | Ne | Ne |
> | umístění/virtualnetworkavailableendpointservices | Ne | Ne |
> | natgateways | Ano | Ano |
> | networkexperimentprofiles | Ne | Ne |
> | networkintentpolicies | Ano | Ano |
> | networkinterfaces | Ano | Ano |
> | networkprofiles | Ne | Ne |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | Ano | Ne |
> | networkwatchers / connectionmonitors | Ano | Ne |
> | networkwatchers / flowlogs | Ano | Ne |
> | networkwatchers / pingmeshes | Ano | Ne |
> | Operace | Ne | Ne |
> | p2svpngateways | Ne | Ne |
> | privatednsoperationresults | Ne | Ne |
> | privatednsoperationstatuses | Ne | Ne |
> | privatednszones | Ano | Ano |
> | privatednszones/a | Ne | Ne |
> | privatednszones/AAAA | Ne | Ne |
> | privatednszones/vše | Ne | Ne |
> | privatednszones/CNAME | Ne | Ne |
> | privatednszones/MX | Ne | Ne |
> | privatednszones/PTR | Ne | Ne |
> | privatednszones/SOA | Ne | Ne |
> | privatednszones/SRV | Ne | Ne |
> | privatednszones/txt | Ne | Ne |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privatednszonesinternal | Ne | Ne |
> | privateendpointredirectmaps | Ne | Ne |
> | privateendpoints | Ano | Ano |
> | privatelinkservices | Ne | Ne |
> | publicipaddresses | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ano | Ano |
> | routefilters | Ne | Ne |
> | routetables | Ano | Ano |
> | securitypartnerproviders | Ano | Ano |
> | serviceendpointpolicies | Ano | Ano |
> | trafficmanagergeographichierarchies | Ne | Ne |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/Heat mapy | Ne | Ne |
> | trafficmanagerusermetricskeys | Ne | Ne |
> | virtualhubs | Ne | Ne |
> | virtualnetworkgateways | Ano | Ano |
> | virtualnetworks | Ano | Ano |
> | virtualnetworktaps | Ne | Ne |
> | virtualrouters | Ano | Ano |
> | virtualwans | Ne | Ne |
> | vpngateways (virtuální síť WAN) | Ne | Ne |
> | vpnserverconfigurations | Ne | Ne |
> | vpnsites (virtuální síť WAN) | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | checknamespaceavailability | Ne | Ne |
> | obsažené | Ano | Ano |
> | obory názvů/notificationhubs | Ano | Ano |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hypervsites | Ne | Ne |
> | importsites | Ne | Ne |
> | Operace | Ne | Ne |
> | serversites | Ne | Ne |
> | vmwaresites | Ne | Ne |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | deletedworkspaces | Ne | Ne |
> | linktargets | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | storageinsightconfigs | Ne | Ne |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/zdroje dat | Ne | Ne |
> | pracovní prostory/linkedservices | Ne | Ne |
> | pracovní prostory/linkedstorageaccounts | Ne | Ne |
> | pracovní prostory/metadata | Ne | Ne |
> | pracovní prostory a dotazy | Ne | Ne |
> | pracovní prostory/scopedprivatelinkproxies | Ne | Ne |

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
> | Operace | Ne | Ne |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Ne | Ne |
> | legacypeerings | Ne | Ne |
> | Operace | Ne | Ne |
> | peerasns | Ne | Ne |
> | peeringlocations | Ne | Ne |
> | partnerské vztahy | Ne | Ne |
> | peeringservicecountries | Ne | Ne |
> | peeringservicelocations | Ne | Ne |
> | peeringserviceproviders | Ne | Ne |
> | peeringservices | Ne | Ne |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | policyevents | Ne | Ne |
> | policystates | Ne | Ne |
> | policytrackedresources | Ne | Ne |
> | nápravy | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Konzoly nástroje | Ne | Ne |
> | řídicí panely | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/konzoly | Ne | Ne |
> | umístění/UserSettings | Ne | Ne |
> | Operace | Ne | Ne |
> | usersettings | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | schopností | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Ne | Ne |
> | providerregistrations | Ne | Ne |
> | providerregistrations / resourcetyperegistrations | Ne | Ne |
> | uvádění | Ne | Ne |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/allocatedstamp | Ne | Ne |
> | umístění/allocatestamp | Ne | Ne |
> | umístění/backupaadproperties | Ne | Ne |
> | umístění/backupcrossregionrestore | Ne | Ne |
> | umístění/backupcrrjob | Ne | Ne |
> | umístění/backupcrrjobs | Ne | Ne |
> | umístění/backupcrroperationresults | Ne | Ne |
> | umístění/backupcrroperationsstatus | Ne | Ne |
> | umístění/backupprevalidateprotection | Ne | Ne |
> | umístění/backupstatus | Ne | Ne |
> | umístění/backupvalidatefeatures | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | Operace | Ne | Ne |
> | replicationeligibilityresults | Ne | Ne |
> | trezory | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationsstatus | Ne | Ne |
> | openshiftclusters | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | Ne | Ne |
> | obory názvů/hybridconnections | Ne | Ne |
> | obory názvů/hybridconnections/autorizačních pravidel | Ne | Ne |
> | obory názvů/privateendpointconnections | Ne | Ne |
> | obory názvů/wcfrelays | Ne | Ne |
> | obory názvů/wcfrelays/autorizačních pravidel | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |
> | odešle | Ano | Ano |
> | resourcechangedetails | Ne | Ne |
> | resourcechanges | Ne | Ne |
> | resources | Ne | Ne |
> | resourceshistory | Ne | Ne |
> | subscriptionsstatus | Ne | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Ne | Ne |
> | childavailabilitystatuses | Ne | Ne |
> | childresources | Ne | Ne |
> | emergingissues | Ne | Ne |
> | stránka events | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | připomenutí | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | Ne | Ne |
> | checkpolicycompliance | Ne | Ne |
> | checkresourcename | Ne | Ne |
> | nasazení | Ne | Ne |
> | nasazení/operace | Ne | Ne |
> | deploymentscripts | Ne | Ne |
> | deploymentscripts/protokoly | Ne | Ne |
> | odkazy | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/deploymentscriptoperationresults | Ne | Ne |
> | notifyresourcejobs | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | dodavateli | Ne | Ne |
> | ResourceGroups | Ne | Ne |
> | resources | Ne | Ne |
> | odběru | Ne | Ne |
> | předplatná/umístění | Ne | Ne |
> | předplatná/operationresults | Ne | Ne |
> | předplatná/poskytovatelé | Ne | Ne |
> | předplatná/ResourceGroups | Ne | Ne |
> | předplatná/ResourceGroups/prostředky | Ne | Ne |
> | předplatná/prostředky | Ne | Ne |
> | předplatná/TagNames | Ne | Ne |
> | předplatná/TagNames/tagvalues | Ne | Ne |
> | tags | Ne | Ne |
> | templatespecs | Ne | Ne |
> | templatespecs/verze | Ne | Ne |
> | tenantů | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ne |
> | checkmoderneligibility | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | saasresources | Ne | Ne |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | checkservicenameavailability | Ne | Ne |
> | Operace | Ne | Ne |
> | resourcehealthmetadata | Ne | Ne |
> | searchservices | Ano | Ano |

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Ne | Ne |
> | advancedthreatprotectionsettings | Ne | Ne |
> | výstrahy | Ne | Ne |
> | allowedconnections | Ne | Ne |
> | applicationwhitelistings | Ne | Ne |
> | assessmentmetadata | Ne | Ne |
> | hodnocení | Ne | Ne |
> | autodismissalertsrules | Ne | Ne |
> | automatizace | Ano | Ano |
> | autoprovisioningsettings | Ne | Ne |
> | complianceresults | Ne | Ne |
> | předpisů | Ne | Ne |
> | datacollectionagents | Ne | Ne |
> | devicesecuritygroups | Ne | Ne |
> | discoveredsecuritysolutions | Ne | Ne |
> | externalsecuritysolutions | Ne | Ne |
> | informationprotectionpolicies | Ne | Ne |
> | iotsecuritysolutions | Ano | Ano |
> | iotsecuritysolutions / analyticsmodels | Ne | Ne |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Ne | Ne |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Ne | Ne |
> | jitnetworkaccesspolicies | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/výstrahy | Ne | Ne |
> | umístění/allowedconnections | Ne | Ne |
> | umístění/applicationwhitelistings | Ne | Ne |
> | umístění/discoveredsecuritysolutions | Ne | Ne |
> | umístění/externalsecuritysolutions | Ne | Ne |
> | umístění/jitnetworkaccesspolicies | Ne | Ne |
> | umístění/securitysolutions | Ne | Ne |
> | umístění/securitysolutionsreferencedata | Ne | Ne |
> | umístění/úkoly | Ne | Ne |
> | umístění/topologie | Ne | Ne |
> | Operace | Ne | Ne |
> | Zásady | Ne | Ne |
> | ceny | Ne | Ne |
> | regulatorycompliancestandards | Ne | Ne |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Ne | Ne |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Ne | Ne |
> | securitycontacts | Ne | Ne |
> | securitysolutions | Ne | Ne |
> | securitysolutionsreferencedata | Ne | Ne |
> | securitystatuses | Ne | Ne |
> | securitystatusessummaries | Ne | Ne |
> | servervulnerabilityassessments | Ne | Ne |
> | settings | Ne | Ne |
> | podhodnocení | Ne | Ne |
> | úlohy | Ne | Ne |
> | topologie | Ne | Ne |
> | workspacesettings | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | agregace | Ne | Ne |
> | alertrules | Ne | Ne |
> | alertruletemplates | Ne | Ne |
> | automationrules | Ne | Ne |
> | záložky | Ne | Ne |
> | věcech | Ne | Ne |
> | dataconnects | Ne | Ne |
> | dataconnectorscheckrequirements | Ne | Ne |
> | podnikům | Ne | Ne |
> | entityqueries | Ne | Ne |
> | incidenty | Ne | Ne |
> | officeconsents | Ne | Ne |
> | Operace | Ne | Ne |
> | settings | Ne | Ne |
> | threatintelligence | Ne | Ne |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | consoleservices | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/consoleservices | Ne | Ne |
> | Operace | Ne | Ne |

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
> | checknameavailability | Ne | Ne |
> | checknamespaceavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | Ne | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne | Ne |
> | obory názvů/disasterrecoveryconfigs/checknameavailability | Ne | Ne |
> | obory názvů/eventgridfilters | Ne | Ne |
> | obory názvů/networkrulesets | Ne | Ne |
> | obory názvů/fronty | Ne | Ne |
> | obory názvů/fronty/autorizačních pravidel | Ne | Ne |
> | obory názvů/témata | Ne | Ne |
> | obory názvů/témata/autorizačních pravidel | Ne | Ne |
> | obory názvů/témata/předplatná | Ne | Ne |
> | obory názvů/témata/předplatná/pravidla | Ne | Ne |
> | Operace | Ne | Ne |
> | premiummessagingregions | Ne | Ne |
> | skladové | Ne | Ne |

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
> | polohy | Ne | Ne |
> | umístění/clusterversions | Ne | Ne |
> | umístění/prostředí | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | managedclusters | Ne | Ne |
> | sítí | Ne | Ne |
> | Operace | Ne | Ne |
> | secretstores | Ne | Ne |
> | volumes | Ne | Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | containergroups | Ne | Ne |
> | brány | Ano | Ano |
> | polohy | Ne | Ne |
> | umístění/applicationoperations | Ne | Ne |
> | umístění/gatewayoperations | Ne | Ne |
> | umístění/networkoperations | Ne | Ne |
> | umístění/secretoperations | Ne | Ne |
> | umístění/volumeoperations | Ne | Ne |
> | sítí | Ano | Ano |
> | Operace | Ne | Ne |
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
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | Operace | Ne | Ne |
> | SignalR | Ano | Ano |
> | signál/eventgridfilters | Ne | Ne |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Ne | Ne |
> | aplikace | Ne | Ne |
> | jitrequests | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | instancepools | Ne | Ne |
> | polohy | Ano | Ano |
> | umístění/administratorazureasyncoperation | Ne | Ne |
> | umístění/administratoroperationresults | Ne | Ne |
> | umístění/auditingsettingsazureasyncoperation | Ne | Ne |
> | umístění/auditingsettingsoperationresults | Ne | Ne |
> | umístění/možnosti | Ne | Ne |
> | umístění/databaseazureasyncoperation | Ne | Ne |
> | umístění/databaseoperationresults | Ne | Ne |
> | umístění/databaserestoreazureasyncoperation | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnetsazureasyncoperation | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnetsoperationresults | Ne | Ne |
> | umístění/dnsaliasasyncoperation | Ne | Ne |
> | umístění/dnsaliasoperationresults | Ne | Ne |
> | umístění/elasticpoolazureasyncoperation | Ne | Ne |
> | umístění/elasticpooloperationresults | Ne | Ne |
> | umístění/encryptionprotectorazureasyncoperation | Ne | Ne |
> | umístění/encryptionprotectoroperationresults | Ne | Ne |
> | umístění/extendedauditingsettingsazureasyncoperation | Ne | Ne |
> | umístění/extendedauditingsettingsoperationresults | Ne | Ne |
> | umístění/failovergroupazureasyncoperation | Ne | Ne |
> | umístění/failovergroupoperationresults | Ne | Ne |
> | umístění/firewallrulesazureasyncoperation | Ne | Ne |
> | umístění/firewallrulesoperationresults | Ne | Ne |
> | umístění/instancefailovergroupazureasyncoperation | Ne | Ne |
> | umístění/instancefailovergroupoperationresults | Ne | Ne |
> | umístění/instancefailovergroups | Ne | Ne |
> | umístění/instancepoolazureasyncoperation | Ne | Ne |
> | umístění/instancepooloperationresults | Ne | Ne |
> | umístění/jobagentazureasyncoperation | Ne | Ne |
> | umístění/jobagentoperationresults | Ne | Ne |
> | umístění/longtermretentionbackupazureasyncoperation | Ne | Ne |
> | umístění/longtermretentionbackupoperationresults | Ne | Ne |
> | umístění/longtermretentionbackups | Ne | Ne |
> | umístění/longtermretentionmanagedinstancebackupazureasyncoperation | Ne | Ne |
> | umístění/longtermretentionmanagedinstancebackupoperationresults | Ne | Ne |
> | umístění/longtermretentionmanagedinstancebackups | Ne | Ne |
> | umístění/longtermretentionmanagedinstances | Ne | Ne |
> | umístění/longtermretentionpolicyazureasyncoperation | Ne | Ne |
> | umístění/longtermretentionpolicyoperationresults | Ne | Ne |
> | umístění/longtermretentionservers | Ne | Ne |
> | umístění/manageddatabaseazureasyncoperation | Ne | Ne |
> | umístění/manageddatabasecompleterestoreazureasyncoperation | Ne | Ne |
> | umístění/manageddatabasecompleterestoreoperationresults | Ne | Ne |
> | umístění/manageddatabaseoperationresults | Ne | Ne |
> | umístění/manageddatabaserestoreazureasyncoperation | Ne | Ne |
> | umístění/manageddatabaserestoreoperationresults | Ne | Ne |
> | umístění/managedinstanceazureasyncoperation | Ne | Ne |
> | umístění/managedinstanceencryptionprotectorazureasyncoperation | Ne | Ne |
> | umístění/managedinstanceencryptionprotectoroperationresults | Ne | Ne |
> | umístění/managedinstancekeyazureasyncoperation | Ne | Ne |
> | umístění/managedinstancekeyoperationresults | Ne | Ne |
> | umístění/managedinstancelongtermretentionpolicyazureasyncoperation | Ne | Ne |
> | umístění/managedinstancelongtermretentionpolicyoperationresults | Ne | Ne |
> | umístění/managedinstanceoperationresults | Ne | Ne |
> | umístění/managedinstancetdecertazureasyncoperation | Ne | Ne |
> | umístění/managedinstancetdecertoperationresults | Ne | Ne |
> | umístění/managedserversecurityalertpoliciesazureasyncoperation | Ne | Ne |
> | umístění/managedserversecurityalertpoliciesoperationresults | Ne | Ne |
> | umístění/managedshorttermretentionpolicyazureasyncoperation | Ne | Ne |
> | umístění/managedshorttermretentionpolicyoperationresults | Ne | Ne |
> | umístění/notifyazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionoperationresults | Ne | Ne |
> | umístění/privateendpointconnectionproxyazureasyncoperation | Ne | Ne |
> | umístění/privateendpointconnectionproxyoperationresults | Ne | Ne |
> | umístění/securityalertpoliciesazureasyncoperation | Ne | Ne |
> | umístění/securityalertpoliciesoperationresults | Ne | Ne |
> | umístění/serveradministratorazureasyncoperation | Ne | Ne |
> | umístění/serveradministratoroperationresults | Ne | Ne |
> | umístění/serverazureasyncoperation | Ne | Ne |
> | umístění/serverkeyazureasyncoperation | Ne | Ne |
> | umístění/serverkeyoperationresults | Ne | Ne |
> | umístění/serveroperationresults | Ne | Ne |
> | umístění/shorttermretentionpolicyazureasyncoperation | Ne | Ne |
> | umístění/shorttermretentionpolicyoperationresults | Ne | Ne |
> | umístění/syncagentoperationresults | Ne | Ne |
> | umístění/syncdatabaseids | Ne | Ne |
> | umístění/syncgroupoperationresults | Ne | Ne |
> | umístění/syncmemberoperationresults | Ne | Ne |
> | umístění/tdecertazureasyncoperation | Ne | Ne |
> | umístění/tdecertoperationresults | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | umístění/virtualclusterazureasyncoperation | Ne | Ne |
> | umístění/virtualclusteroperationresults | Ne | Ne |
> | umístění/virtualnetworkrulesazureasyncoperation | Ne | Ne |
> | umístění/virtualnetworkrulesoperationresults | Ne | Ne |
> | umístění/vulnerabilityassessmentscanazureasyncoperation | Ne | Ne |
> | umístění/vulnerabilityassessmentscanoperationresults | Ne | Ne |
> | managedinstances | Ne | Ne |
> | managedinstances/Administrators | Ne | Ne |
> | managedinstances/databáze | Ne | Ne |
> | managedinstances/databáze/backuplongtermretentionpolicies | Ne | Ne |
> | managedinstances/databáze/vulnerabilityassessments | Ne | Ne |
> | managedinstances / metricdefinitions | Ne | Ne |
> | managedinstances/metriky | Ne | Ne |
> | managedinstances / recoverabledatabases | Ne | Ne |
> | managedinstances / tdecertificates | Ne | Ne |
> | managedinstances / vulnerabilityassessments | Ne | Ne |
> | Operace | Ne | Ne |
> | servery | Ano | Ano |
> | servery/administratoroperationresults | Ne | Ne |
> | servery/správci | Ne | Ne |
> | servery/poradci | Ne | Ne |
> | servery/aggregateddatabasemetrics | Ne | Ne |
> | servery/auditingpolicies | Ne | Ne |
> | servery/auditingsettings | Ne | Ne |
> | servery/automatictuning | Ne | Ne |
> | servery/communicationlinks | Ne | Ne |
> | servery/connectionpolicies | Ne | Ne |
> | servery/databáze | Ano | Ano |
> | servery/databáze/poradci | Ne | Ne |
> | servery/databáze/auditingpolicies | Ne | Ne |
> | servery/databáze/auditingsettings | Ne | Ne |
> | servery/databáze/AuditRecords | Ne | Ne |
> | servery/databáze/automatictuning | Ne | Ne |
> | servery/databáze/backuplongtermretentionpolicies | Ne | Ne |
> | servery/databáze/backupshorttermretentionpolicies | Ne | Ne |
> | servery/databáze/connectionpolicies | Ne | Ne |
> | servery/databáze/datamaskingpolicies | Ne | Ne |
> | servery/databáze/datamaskingpolicies/pravidla | Ne | Ne |
> | servery/databáze/rozšíření | Ne | Ne |
> | servery/databáze/geobackuppolicies | Ne | Ne |
> | servery/databáze/metricdefinitions | Ne | Ne |
> | servery/databáze/metriky | Ne | Ne |
> | servery/databáze/recommendedsensitivitylabels | Ne | Ne |
> | servery/databáze/securityalertpolicies | Ne | Ne |
> | servery/databáze/syncgroups | Ne | Ne |
> | servery/databáze/syncgroups/syncmembers | Ne | Ne |
> | servery/databáze/topqueries | Ne | Ne |
> | servery/databáze/topqueries/QUERYTEXT | Ne | Ne |
> | servery/databáze/transparentdataencryption | Ne | Ne |
> | servery/databáze/vulnerabilityassessment | Ne | Ne |
> | servery/databáze/vulnerabilityassessments | Ne | Ne |
> | servery/databáze/vulnerabilityassessmentscans | Ne | Ne |
> | servery/databáze/vulnerabilityassessmentsettings | Ne | Ne |
> | servery/databáze/workloadgroups | Ne | Ne |
> | servery/databasesecuritypolicies | Ne | Ne |
> | servery/disasterrecoveryconfiguration | Ne | Ne |
> | servery/dnsaliases | Ne | Ne |
> | servery/elasticpoolestimates | Ne | Ne |
> | servery/elasticpools | Ano | Ano |
> | servery/elasticpools/poradci | Ne | Ne |
> | servery/elasticpools/metricdefinitions | Ne | Ne |
> | servery/elasticpools/metriky | Ne | Ne |
> | servery/encryptionprotector | Ne | Ne |
> | servery/extendedauditingsettings | Ne | Ne |
> | servery/failovergroups | Ne | Ne |
> | servery/import | Ne | Ne |
> | servery/importexportoperationresults | Ne | Ne |
> | servery/jobaccounts | Ano | Ano |
> | servery/jobagents | Ano | Ano |
> | servery/jobagents/úlohy | Ne | Ne |
> | servery/jobagents/úlohy/spuštění | Ne | Ne |
> | servery/jobagents/úlohy/kroky | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/operationresults | Ne | Ne |
> | servery/recommendedelasticpools | Ne | Ne |
> | servery/recoverabledatabases | Ne | Ne |
> | servery/restorabledroppeddatabases | Ne | Ne |
> | servery/securityalertpolicies | Ne | Ne |
> | servery/serviceobjectives | Ne | Ne |
> | servery/syncagents | Ne | Ne |
> | servery/tdecertificates | Ne | Ne |
> | servery/použití | Ne | Ne |
> | servery/virtualnetworkrules | Ne | Ne |
> | servery/vulnerabilityassessments | Ne | Ne |
> | virtualclusters | Ano | Ano |

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/availabilitygrouplisteneroperationresults | Ne | Ne |
> | umístění/operationtypes | Ne | Ne |
> | umístění/sqlvirtualmachinegroupoperationresults | Ne | Ne |
> | umístění/sqlvirtualmachineoperationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | sqlvirtualmachinegroups | Ano | Ano |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Ne | Ne |
> | sqlvirtualmachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/asyncoperations | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | Operace | Ne | Ne |
> | storageaccounts | Ano | Ano |
> | storageaccounts/blobservices | Ne | Ne |
> | storageaccounts/služby | Ne | Ne |
> | storageaccounts/listaccountsas | Ne | Ne |
> | storageaccounts/listservicesas | Ne | Ne |
> | storageaccounts/queueservices | Ne | Ne |
> | storageaccounts/služby | Ne | Ne |
> | storageaccounts/služby/metricdefinitions | Ne | Ne |
> | storageaccounts/tableservices | Ne | Ne |
> | použití | Ne | Ne |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mezipaměti | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/checknameavailability | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/pracovní postupy | Ne | Ne |
> | Operace | Ne | Ne |
> | storagesyncservices | Ano | Ano |
> | storagesyncservices/registeredserver | Ne | Ne |
> | storagesyncservices / syncgroups | Ne | Ne |
> | storagesyncservices / syncgroups / cloudendpoints | Ne | Ne |
> | storagesyncservices / syncgroups / serverendpoints | Ne | Ne |
> | storagesyncservices/pracovní postupy | Ne | Ne |

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
> | Operace | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/kvóty | Ne | Ne |
> | Operace | Ne | Ne |
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
> | cancel | Ne | Ne |
> | createsubscription | Ne | Ne |
> | aby | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | přejmenovat | Ne | Ne |
> | subscriptiondefinitions | Ne | Ne |
> | subscriptionoperations | Ne | Ne |
> | odběru | Ne | Ne |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | operationresults | Ne | Ne |
> | Operace | Ne | Ne |
> | operationsstatus | Ne | Ne |
> | services | Ne | Ne |
> | služby/problemclassifications | Ne | Ne |
> | supporttickets | Ne | Ne |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ne | Ne |
> | Operace | Ne | Ne |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/bigdatapools | Ano | Ano |
> | pracovní prostory/operationresults | Ne | Ne |
> | pracovní prostory/operationstatuses | Ne | Ne |
> | pracovní prostory/sqlpools | Ano | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | Ano | Ano |
> | prostředí/accesspolicies | Ne | Ne |
> | prostředí/EventSources | Ano | Ano |
> | prostředí/referencedatasets | Ano | Ano |
> | Operace | Ne | Ne |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Store | Ano | Ano |
> | obchody/accesspolicies | Ne | Ne |
> | obchody/služby | Ne | Ne |
> | úložiště/služby/tokeny | Ne | Ne |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Ne | Ne |
> | imagetemplates / runoutputs | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | account | Ne | Ne |
> | účet/rozšíření | Ne | Ne |
> | účet/projekt | Ne | Ne |
> | checknameavailability | Ne | Ne |
> | Operace | Ne | Ne |

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | arczones | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | resourcepools | Ne | Ne |
> | servery vCenter | Ne | Ne |
> | virtualmachines | Ne | Ne |
> | virtualmachinetemplates | Ne | Ne |
> | virtualnetworks | Ne | Ne |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Ne | Ne |
> | dedicatedcloudservices | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/dostupnosti | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/privateclouds | Ne | Ne |
> | umístění/privateclouds/resourcepools | Ne | Ne |
> | umístění/privateclouds/virtualmachinetemplates | Ne | Ne |
> | umístění/privateclouds/virtualnetworks | Ne | Ne |
> | umístění/použití | Ne | Ne |
> | Operace | Ne | Ne |
> | virtualmachines | Ne | Ne |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | vnfs | Ne | Ne |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | Operace | Ne | Ne |
> | plánují | Ne | Ne |
> | registeredsubscriptions | Ne | Ne |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availablestacks | Ne | Ne |
> | billingmeters | Ne | Ne |
> | certifikáty | No | Ano |
> | checknameavailability | Ne | Ne |
> | connectiongateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | deletedsites | Ne | Ne |
> | deploymentlocations | Ne | Ne |
> | geografická umístění | Ne | Ne |
> | hostingenvironments | Ne | Ne |
> | hostingenvironments / eventgridfilters | Ne | Ne |
> | hostingenvironments / multirolepools | Ne | Ne |
> | hostingenvironments / workerpools | Ne | Ne |
> | ishostingenvironmentnameavailable | Ne | Ne |
> | ishostnameavailable | Ne | Ne |
> | isusernameavailable | Ne | Ne |
> | kubeenvironments | Ano | Ano |
> | listsitesassignedtohostname | Ne | Ne |
> | polohy | Ne | Ne |
> | umístění/apioperations | Ne | Ne |
> | umístění/connectiongatewayinstallations | Ne | Ne |
> | umístění/deletedsites | Ne | Ne |
> | umístění/deletevirtualnetworkorsubnets | Ne | Ne |
> | umístění/extractapidefinitionfromwsdl | Ne | Ne |
> | umístění/getnetworkpolicies | Ne | Ne |
> | umístění/listwsdlinterfaces | Ne | Ne |
> | umístění/Inspirujte | Ne | Ne |
> | umístění/operationresults | Ne | Ne |
> | umístění/operace | Ne | Ne |
> | umístění/moduly runtime | Ne | Ne |
> | Operace | Ne | Ne |
> | publishingusers | Ne | Ne |
> | doporučit | Ne | Ne |
> | resourcehealthmetadata | Ne | Ne |
> | moduly runtime | Ne | Ne |
> | serverových farem | Ano | Ano |
> | serverových farem/eventgridfilters | Ne | Ne |
> | místa | Ano | Ano |
> | lokality/eventgridfilters | Ne | Ne |
> | lokality/hostnamebindings | Ne | Ne |
> | lokality/networkconfig | Ne | Ne |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | lokality/sloty/eventgridfilters | Ne | Ne |
> | lokality/sloty/hostnamebindings | Ne | Ne |
> | lokality/sloty/networkconfig | Ne | Ne |
> | sourcecontrols | Ne | Ne |
> | staticsites | Ne | Ne |
> | oproti | Ne | Ne |
> | verifyhostingenvironmentvnet | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | multipleactivationkeys | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deviceservices | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | Ne | Ne |
> | umístění/operationstatuses | Ne | Ne |
> | Operace | Ne | Ne |
> | úlohy | Ne | Ne |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | Ne | Ne |
> | componentssummary | Ne | Ne |
> | monitorinstances | Ne | Ne |
> | monitorinstancessummary | Ne | Ne |
> | Monitor | Ne | Ne |
> | notificationsettings | Ne | Ne |
> | Operace | Ne | Ne |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky
Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
