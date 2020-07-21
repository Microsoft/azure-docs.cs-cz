---
title: Přesunout podporu operace podle typu prostředku
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7b7f6f05c9cae80cffb64245a3fc8a6b0890d577
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539223"
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
> - [Microsoft.Devices](#microsoftdevices)
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
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
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
> | domainservices | No | No |
> | domainservices / oucontainer | No | No |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | No | No |
> | diagnosticsettingscategories | No | No |
> | Operace | No | No |
> | privatelinkforazuread | Ano | Ano |
> | tenantů | Ano | Ano |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operationresults | No | No |
> | Operace | No | No |
> | supportproviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | No | No |
> | addsservices | No | No |
> | technici | No | No |
> | anonymousapiusers | No | No |
> | konfigurace | No | No |
> | Protokoly | No | No |
> | Operace | No | No |
> | sestavy | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konfiguračních | No | No |
> | generaterecommendations | No | No |
> | zprostředkovatele identity | No | No |
> | Operace | No | No |
> | doporučit | No | No |
> | potlačení | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actionrules | Ano | Ano |
> | výstrahy | No | No |
> | alertslist | No | No |
> | alertsmetadata | No | No |
> | alertssummary | No | No |
> | alertssummarylist | No | No |
> | Operace | No | No |
> | smartdetectoralertrules | Ano | Ano |
> | smartgroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | No | No |
> | checknameavailability | No | No |
> | checkservicenameavailability | No | No |
> | Operace | No | No |
> | reportfeedback | No | No |
> | service | Ano | Ano |
> | validateservicename | No | No |

> [!IMPORTANT]
> Službu API Management, která je nastavená na SKU spotřeby, se nedá přesunout.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | configurationstores | Ano | Ano |
> | configurationstores / eventgridfilters | No | No |
> | polohy | No | No |
> | umístění/operationsstatus | No | No |
> | Operace | No | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/stav operationstatus | No | No |
> | Operace | No | No |
> | návratu | Ano | Ano |
> | pružina/aplikace | No | No |
> | Jarní/aplikace/nasazení | No | No |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apiapps | No | No |
> | appidentities | No | No |
> | brány | No | No |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ano | Ano |
> | Operace | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instanci | No | No |
> | classicadministrators | No | No |
> | dataaliasy | No | No |
> | denyassignments | No | No |
> | elevateaccess | No | No |
> | findorphanroleassignments | No | No |
> | počtu | No | No |
> | Operace | No | No |
> | stav operationstatus | No | No |
> | oprávnění | No | No |
> | policyassignments | No | No |
> | policydefinitions | No | No |
> | policysetdefinitions | No | No |
> | privatelinkassociations | No | No |
> | provideroperations | No | No |
> | resourcemanagementprivatelinks | No | No |
> | RoleAssignments | No | No |
> | roleassignmentsusagemetrics | No | No |
> | roledefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ano | Ano |
> | automationaccounts/konfigurace | Ano | Ano |
> | automationaccounts/úlohy | No | No |
> | automationaccounts / privateendpointconnectionproxies | No | No |
> | automationaccounts / privateendpointconnections | No | No |
> | automationaccounts / privatelinkresources | No | No |
> | automationaccounts/Runbooky | Ano | Ano |
> | automationaccounts / softwareupdateconfigurations | No | No |
> | automationaccounts nebo Webhooky | No | No |
> | Operace | No | No |

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.
>
> Informace najdete v tématu [přesunutí účtu Azure Automation do jiného předplatného](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checkquotaavailability | No | No |
> | umístění/checktrialavailability | No | No |
> | Operace | No | No |
> | privateclouds | Ano | Ano |
> | privateclouds/clustery | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ano | Ano |
> | b2ctenants | No | No |
> | checknameavailability | No | No |
> | Operace | No | No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datacontrollery | No | No |
> | hybriddatamanagers | No | No |
> | Operace | No | No |
> | postgresinstances | No | No |
> | sqlinstances | No | No |
> | sqlmanagedinstances | No | No |
> | sqlserverinstances | No | No |
> | sqlserverregistrations | Ano | Ano |
> | sqlserverregistrations/SQLservers | No | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | No | No |
> | Operace | No | No |
> | rozpoznávání | Ano | Ano |
> | registrace/customersubscriptions | No | No |
> | registrace/produkty | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | No | No |
> | Operace | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ano | Ano |
> | polohy | No | No |
> | umístění/accountoperationresults | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/kvóty | No | No |
> | Operace | No | No |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | billingaccounts | No | No |
> | billingaccounts/smlouvy | No | No |
> | billingaccounts / billingpermissions | No | No |
> | billingaccounts / billingprofiles | No | No |
> | billingaccounts / billingprofiles / availablebalance | No | No |
> | billingaccounts / billingprofiles / billingpermissions | No | No |
> | billingaccounts / billingprofiles / billingroleassignments | No | No |
> | billingaccounts / billingprofiles / billingroledefinitions | No | No |
> | billingaccounts / billingprofiles / billingsubscriptions | No | No |
> | billingaccounts / billingprofiles / createbillingroleassignment | No | No |
> | billingaccounts/billingprofiles/zákazníci | No | No |
> | billingaccounts/billingprofiles/– pokyny | No | No |
> | billingaccounts/billingprofiles/faktury | No | No |
> | billingaccounts/billingprofiles/faktury/pricesheet | No | No |
> | billingaccounts/billingprofiles/faktury/transakce | No | No |
> | billingaccounts / billingprofiles / invoicesections | No | No |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | No | No |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | No | No |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | No | No |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | No | No |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | No | No |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | No | No |
> | billingaccounts/billingprofiles/invoicesections/Products | No | No |
> | billingaccounts/billingprofiles/invoicesections/Products/Transfer | No | No |
> | billingaccounts/billingprofiles/invoicesections/Products/updateautorenew | No | No |
> | billingaccounts/billingprofiles/invoicesections/transakce | No | No |
> | billingaccounts/billingprofiles/invoicesections/transfery | No | No |
> | billingaccounts / billingprofiles / patchoperations | No | No |
> | billingaccounts / billingprofiles / paymentmethods | No | No |
> | billingaccounts/billingprofiles/– zásady | No | No |
> | billingaccounts/billingprofiles/pricesheet | No | No |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | No | No |
> | billingaccounts/billingprofiles/produkty | No | No |
> | billingaccounts/billingprofiles/transakcí | No | No |
> | billingaccounts / billingroleassignments | No | No |
> | billingaccounts / billingroledefinitions | No | No |
> | billingaccounts / billingsubscriptions | No | No |
> | billingaccounts/billingsubscriptions/faktury | No | No |
> | billingaccounts / createbillingroleassignment | No | No |
> | billingaccounts / createinvoicesectionoperations | No | No |
> | billingaccounts/zákazníci | No | No |
> | billingaccounts/Customers/billingpermissions | No | No |
> | billingaccounts/Customers/billingsubscriptions | No | No |
> | billingaccounts/Customers/initiatetransfer | No | No |
> | billingaccounts/Customers/policies | No | No |
> | billingaccounts/zákazníci/produkty | No | No |
> | billingaccounts/zákazníci/transakce | No | No |
> | billingaccounts/zákazníci/přenosy | No | No |
> | billingaccounts/oddělení | No | No |
> | billingaccounts / enrollmentaccounts | No | No |
> | billingaccounts/faktury | No | No |
> | billingaccounts / invoicesections | No | No |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | No | No |
> | billingaccounts / invoicesections / billingsubscriptions | No | No |
> | billingaccounts/invoicesections/billingsubscriptions/Transfer | No | No |
> | billingaccounts/invoicesections/zvýšení oprávnění | No | No |
> | billingaccounts / invoicesections / initiatetransfer | No | No |
> | billingaccounts / invoicesections / patchoperations | No | No |
> | billingaccounts / invoicesections / productmoveoperations | No | No |
> | billingaccounts/invoicesections/produkty | No | No |
> | billingaccounts/invoicesections/produkty/přenos | No | No |
> | billingaccounts/invoicesections/Products/updateautorenew | No | No |
> | billingaccounts / invoicesections / producttransfersresults | No | No |
> | billingaccounts/invoicesections/transakcí | No | No |
> | billingaccounts/invoicesections/transfery | No | No |
> | billingaccounts / lineofcredit | No | No |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | No | No |
> | billingaccounts / operationresults | No | No |
> | billingaccounts / patchoperations | No | No |
> | billingaccounts / paymentmethods | No | No |
> | billingaccounts/produkty | No | No |
> | billingaccounts/transakce | No | No |
> | billingperiods | No | No |
> | billingpermissions | No | No |
> | billingproperty | No | No |
> | billingroleassignments | No | No |
> | billingroledefinitions | No | No |
> | createbillingroleassignment | No | No |
> | oddělení | No | No |
> | enrollmentaccounts | No | No |
> | faktury | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | stav operationstatus | No | No |
> | Převede | No | No |
> | přenosy/accepttransfer | No | No |
> | přenosy/declinetransfer | No | No |
> | přenosy/stav operationstatus | No | No |
> | přenosy/validatetransfer | No | No |
> | validateaddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | No | No |
> | mapapis | No | No |
> | Operace | No | No |
> | updatecommunicationpreference | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | biztalk | No | No |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | No | No |
> | cordamembers | No | No |
> | polohy | No | No |
> | umístění/blockchainmemberoperationresults | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/listconsortiums | No | No |
> | umístění/watcheroperationresults | No | No |
> | Operace | No | No |
> | sledovacích procesů | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |
> | tokenservices | No | No |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |
> | blueprintassignments / assignmentoperations | No | No |
> | blueprintassignments/operace | No | No |
> | podrobné plány | No | No |
> | modrotisky/artefakty | No | No |
> | plány/verze | No | No |
> | modrotisky/verze/artefakty | No | No |
> | Operace | No | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | botservices | Ano | Ano |
> | botservices/kanály | No | No |
> | botservices/připojení | No | No |
> | checknameavailability | No | No |
> | listauthserviceproviders | No | No |
> | Operace | No | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationsstatus | No | No |
> | Operace | No | No |
> | Redis | Ano | Ano |
> | Redis/eventgridfilters | No | No |
> | Redis/privatelinkresources | No | No |
> | redisenterprise | No | No |

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appliedreservations | No | No |
> | calculateexchange | No | No |
> | calculateprice | No | No |
> | calculatepurchaseprice | No | No |
> | spustí | No | No |
> | checkoffers | No | No |
> | checkpurchasestatus | No | No |
> | checkscopes | No | No |
> | commercialreservationorders | No | No |
> | výměn | No | No |
> | listbenefits | No | No |
> | Operace | No | No |
> | placepurchaseorder | No | No |
> | reservationorders | No | No |
> | reservationorders / availablescopes | No | No |
> | reservationorders / calculaterefund | No | No |
> | reservationorders/sloučit | No | No |
> | reservationorders/rezervace | No | No |
> | reservationorders/rezervací/availablescopes | No | No |
> | reservationorders/rezervace/Revize | No | No |
> | reservationorders/vrátit | No | No |
> | reservationorders/Split | No | No |
> | reservationorders/swap | No | No |
> | rezervace | No | No |
> | resources | No | No |
> | validatereservationorder | No | No |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | No | No |
> | cdnwebapplicationfirewallpolicies | Ano | Ano |
> | checknameavailability | No | No |
> | checkresourceusage | No | No |
> | edgenodes | No | No |
> | operationresults | No | No |
> | operationresults / profileresults | No | No |
> | operationresults / profileresults / endpointresults | No | No |
> | operationresults / profileresults / endpointresults / customdomainresults | No | No |
> | operationresults / profileresults / endpointresults / origingroupresults | No | No |
> | operationresults / profileresults / endpointresults / originresults | No | No |
> | Operace | No | No |
> | uživatelů | Ano | Ano |
> | profily/koncové body | Ano | Ano |
> | profily/koncové body/customdomains | No | No |
> | profily/koncové body/origingroups | No | No |
> | profily/koncové body/zdroje | No | No |
> | validateprobe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ano | Ano |
> | certificateorders/certifikáty | No | No |
> | Operace | No | No |
> | validatecertificateregistrationinformation | No | No |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | možnosti | No | No |
> | checkdomainnameavailability | No | No |
> | domainnames | Ano | No |
> | DomainNames/možnosti | No | No |
> | domainnames / internalloadbalancers | No | No |
> | domainnames / servicecertificates | No | No |
> | DomainNames/sloty | No | No |
> | DomainNames/sloty/role | No | No |
> | DomainNames/sloty/role/metricdefinitions | No | No |
> | DomainNames/sloty/role/metriky | No | No |
> | movesubscriptionresources | No | No |
> | operatingsystemfamilies | No | No |
> | operatingsystems | No | No |
> | Operace | No | No |
> | operationstatuses | No | No |
> | quotas | No | No |
> | typ prostředků | No | No |
> | validatesubscriptionmoveavailability | No | No |
> | virtualmachines | Ano | No |
> | virtualmachines / diagnosticsettings | No | No |
> | virtualmachines / metricdefinitions | No | No |
> | VirtualMachines/metriky | No | No |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | No | No |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | možnosti | No | No |
> | expressroutecrossconnections | No | No |
> | expressroutecrossconnections/partnerské vztahy | No | No |
> | gatewaysupporteddevices | No | No |
> | networksecuritygroups | No | No |
> | Operace | No | No |
> | quotas | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | No | No |
> | virtualnetworks/virtualnetworkpeerings | No | No |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | možnosti | No | No |
> | checkstorageaccountavailability | No | No |
> | disků | No | No |
> | images | No | No |
> | Operace | No | No |
> | OSImage | No | No |
> | osplatformimages | No | No |
> | publicimages | No | No |
> | quotas | No | No |
> | storageaccounts | Ano | No |
> | storageaccounts/blobservices | No | No |
> | storageaccounts/služby | No | No |
> | storageaccounts/metricdefinitions | No | No |
> | storageaccounts/metriky | No | No |
> | storageaccounts/queueservices | No | No |
> | storageaccounts/služby | No | No |
> | storageaccounts/služby/diagnosticsettings | No | No |
> | storageaccounts/služby/metricdefinitions | No | No |
> | storageaccounts/služby/metriky | No | No |
> | storageaccounts/tableservices | No | No |
> | storageaccounts/vmimages | No | No |
> | vmimages | No | No |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | checkdomainavailability | No | No |
> | polohy | No | No |
> | umístění/checkskuavailability | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |
> | ratecard | No | No |
> | usageaggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ano | Ano |
> | diskaccesses | No | No |
> | diskencryptionsets | No | No |
> | disků | Ano | Ano |
> | Galerie | No | No |
> | Galerie/image | No | No |
> | Galerie/image/verze | No | No |
> | hostgroups | No | No |
> | hostgroups/hostitelé | No | No |
> | images | Ano | Ano |
> | polohy | No | No |
> | umístění/artifactpublishers | No | No |
> | umístění/capsoperations | No | No |
> | umístění/diskoperations | No | No |
> | umístění/loganalytics | No | No |
> | umístění/operace | No | No |
> | umístění/vydavatelé | No | No |
> | umístění/runcommands | No | No |
> | umístění/použití | No | No |
> | umístění/VirtualMachines | No | No |
> | umístění/povolených velikostí | No | No |
> | umístění/vsmoperations | No | No |
> | Operace | No | No |
> | proximityplacementgroups | Ano | Ano |
> | restorepointcollections | No | No |
> | restorepointcollections / restorepoints | No | No |
> | sharedvmextensions | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages/verze | No | No |
> | snímky | Ano | Ano |
> | sshpublickeys | No | No |
> | virtualmachines | Ano | Ano |
> | VirtualMachines/rozšíření | Ano | Ano |
> | virtualmachines / metricdefinitions | No | No |
> | virtualmachines / runcommands | No | No |
> | virtualmachinescalesets | Ano | Ano |
> | virtualmachinescalesets/rozšíření | No | No |
> | virtualmachinescalesets/networkinterfaces | No | No |
> | virtualmachinescalesets/publicipaddresses | No | No |
> | virtualmachinescalesets/VirtualMachines | No | No |
> | virtualmachinescalesets/VirtualMachines/networkinterfaces | No | No |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | No | No |
> | Přesun | No | No |
> | projektů | No | No |
> | poplatky za | No | No |
> | costtags | No | No |
> | dobropis | No | No |
> | stránka events | No | No |
> | prognózy | No | No |
> | ŠARŽ | No | No |
> | tržišť | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | stav operationstatus | No | No |
> | pricesheets | No | No |
> | produktech | No | No |
> | reservationdetails | No | No |
> | reservationrecommendationdetails | No | No |
> | reservationrecommendations | No | No |
> | reservationsummaries | No | No |
> | reservationtransactions | No | No |
> | tags | No | No |
> | tenantů | No | No |
> | uvedenými | No | No |
> | usagedetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |
> | polohy | No | No |
> | umístění/cachedimages | No | No |
> | umístění/možnosti | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/operace | No | No |
> | umístění/použití | No | No |
> | Operace | No | No |
> | serviceassociationlinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/autorizace | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/operationresults | No | No |
> | umístění/setupauth | No | No |
> | Operace | No | No |
> | registr | Ano | Ano |
> | Registry/agentpools | Ano | Ano |
> | Registry/agentpools/listqueuestatus | No | No |
> | Registry a sestavení | No | No |
> | Registry/buildy/zrušit | No | No |
> | Registry/buildy/getloglink | No | No |
> | Registry/BuildTasks | Ano | Ano |
> | Registry/BuildTasks/listsourcerepositoryproperties | No | No |
> | Registry/BuildTasks/kroky | No | No |
> | Registry/BuildTasks/Steps/listbuildarguments | No | No |
> | Registry/eventgridfilters | No | No |
> | Registry/exportpipelines | No | No |
> | Registry/generatecredentials | No | No |
> | Registry/getbuildsourceuploadurl | No | No |
> | Registry/getpřihlašovací údaje | No | No |
> | Registry/importimage | No | No |
> | Registry/importpipelines | No | No |
> | Registry/listbuildsourceuploadurl | No | No |
> | Registry/listcredentials | No | No |
> | Registry/ListPolicies | No | No |
> | Registry/listusages | No | No |
> | Registry/pipelineruns | No | No |
> | Registry/privateendpointconnectionproxies | No | No |
> | Registry/privateendpointconnectionproxies/ověřit | No | No |
> | Registry/privateendpointconnections | No | No |
> | Registry/privatelinkresources | No | No |
> | Registry/QueueBuild | No | No |
> | Registry/regeneratecredential | No | No |
> | Registry/regeneratecredentials | No | No |
> | Registry/replikace | Ano | Ano |
> | Registry/běhy | No | No |
> | Registry/spuštění/zrušit | No | No |
> | Registry/běhy/listlogsasurl | No | No |
> | Registry/schedulerun | No | No |
> | Registry/scopemaps | No | No |
> | Registry/taskruns | No | No |
> | Registry/taskruns/ListDetails | No | No |
> | Registry/úlohy | Ano | Ano |
> | Registry/úlohy/ListDetails | No | No |
> | Registry a tokeny | No | No |
> | Registry/updatepolicies | No | No |
> | Registry a Webhooky | Ano | Ano |
> | Registry/Webhooky/getcallbackconfig | No | No |
> | Registry/Webhooky/listevents | No | No |
> | Registry/Webhooky/příkazy k odeslání | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containerservices | No | No |
> | polohy | No | No |
> | umístění/openshiftclusters | No | No |
> | umístění/operationresults | No | No |
> | umístění/operace | No | No |
> | umístění/orchestrace | No | No |
> | managedclusters | No | No |
> | openshiftmanagedclusters | No | No |
> | Operace | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | výstrahy | No | No |
> | billingaccounts | No | No |
> | projektů | No | No |
> | cloudconnectors | No | No |
> | konektory | Ano | Ano |
> | oddělení | No | No |
> | použijí | No | No |
> | enrollmentaccounts | No | No |
> | vývozních | No | No |
> | externalbillingaccounts | No | No |
> | externalbillingaccounts/výstrahy | No | No |
> | externalbillingaccounts/dimenzí | No | No |
> | externalbillingaccounts/prognóza | No | No |
> | externalbillingaccounts/dotaz | No | No |
> | externalsubscriptions | No | No |
> | externalsubscriptions/výstrahy | No | No |
> | externalsubscriptions/dimenzí | No | No |
> | externalsubscriptions/prognóza | No | No |
> | externalsubscriptions/dotaz | No | No |
> | forecast | No | No |
> | Operace | No | No |
> | query | No | No |
> | register | No | No |
> | reportconfigs | No | No |
> | sestavy | No | No |
> | nastavení | No | No |
> | showbackrules | No | No |
> | zobrazení | No | No |

## <a name="microsoftcostmanagementexports"></a>Microsoft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Hubs | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |
> | požádal | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | přidružení | No | No |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | resourceproviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | No | No |
> | polohy | No | No |
> | umístění/availableskus | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/regionconfiguration | No | No |
> | umístění/validateaddress | No | No |
> | umístění/ValidateInputs | No | No |
> | Operace | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableskus | No | No |
> | databoxedgedevices | Ano | Ano |
> | databoxedgedevices / checknameavailability | No | No |
> | Operace | No | No |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/getnetworkpolicies | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | pracovní prostory | No | No |
> | pracovní prostory/dbworkspaces | No | No |
> | pracovní prostory/virtualnetworkpeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | spustí | Ano | Ano |
> | checknameavailability | No | No |
> | datacatalogs | No | No |
> | polohy | No | No |
> | umístění/úlohy | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zásilk | No | No |
> | plánují | No | No |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | No | No |
> | checkdatafactorynameavailability | No | No |
> | objekty DataFactory | Ano | Ano |
> | DataFactory/diagnosticsettings | No | No |
> | DataFactory/metricdefinitions | No | No |
> | datafactoryschema | No | No |
> | továrny | Ano | Ano |
> | továrny/integrationruntimes | No | No |
> | polohy | No | No |
> | umístění/configurefactoryrepo | No | No |
> | umístění/getfeaturevalue | No | No |
> | Operace | No | No |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/datalakestoreaccounts | No | No |
> | účty/storageaccounts | No | No |
> | účty/storageaccounts/kontejnery | No | No |
> | účty/storageaccounts/kontejnery/listsastokens | No | No |
> | polohy | No | No |
> | umístění/schopnost | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/použití | No | No |
> | Operace | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/eventgridfilters | No | No |
> | účty/firewallrules | No | No |
> | polohy | No | No |
> | umístění/schopnost | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/operationresults | No | No |
> | umístění/použití | No | No |
> | Operace | No | No |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | services | No | No |
> | služby a projekty | No | No |
> | otvory | No | No |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupvaults | No | No |
> | polohy | No | No |
> | Operace | No | No |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/sdílené složky | No | No |
> | účty/akcie/datové sady | No | No |
> | účty/akcie/pozvánky | No | No |
> | účty/akcie/providersharesubscriptions | No | No |
> | účty/akcie/synchronizationsettings | No | No |
> | účty/sharesubscriptions | No | No |
> | účty/sharesubscriptions/consumersourcedatasets | No | No |
> | účty/sharesubscriptions/datasetmappings | No | No |
> | účty/sharesubscriptions/triggery | No | No |
> | listinvitations | No | No |
> | polohy | No | No |
> | umístění/consumerinvitations | No | No |
> | umístění/operationresults | No | No |
> | umístění/rejectinvitation | No | No |
> | Operace | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/azureasyncoperation | No | No |
> | umístění/operationresults | No | No |
> | umístění/performancetiers | No | No |
> | umístění/privateendpointconnectionazureasyncoperation | No | No |
> | umístění/privateendpointconnectionoperationresults | No | No |
> | umístění/privateendpointconnectionproxyazureasyncoperation | No | No |
> | umístění/privateendpointconnectionproxyoperationresults | No | No |
> | umístění/recommendedactionsessionsazureasyncoperation | No | No |
> | umístění/recommendedactionsessionsoperationresults | No | No |
> | umístění/securityalertpoliciesazureasyncoperation | No | No |
> | umístění/securityalertpoliciesoperationresults | No | No |
> | umístění/serverkeyazureasyncoperation | No | No |
> | umístění/serverkeyoperationresults | No | No |
> | Operace | No | No |
> | servery | Ano | Ano |
> | servery/poradci | No | No |
> | servery/privateendpointconnectionproxies | No | No |
> | servery/privateendpointconnections | No | No |
> | servery/privatelinkresources | No | No |
> | servery/querytexts | No | No |
> | servery/recoverableservers | No | No |
> | servery/topquerystatistics | No | No |
> | servery/virtualnetworkrules | No | No |
> | servery/waitstatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/administratorazureasyncoperation | No | No |
> | umístění/administratoroperationresults | No | No |
> | umístění/azureasyncoperation | No | No |
> | umístění/operationresults | No | No |
> | umístění/performancetiers | No | No |
> | umístění/privateendpointconnectionazureasyncoperation | No | No |
> | umístění/privateendpointconnectionoperationresults | No | No |
> | umístění/privateendpointconnectionproxyazureasyncoperation | No | No |
> | umístění/privateendpointconnectionproxyoperationresults | No | No |
> | umístění/recommendedactionsessionsazureasyncoperation | No | No |
> | umístění/recommendedactionsessionsoperationresults | No | No |
> | umístění/securityalertpoliciesazureasyncoperation | No | No |
> | umístění/securityalertpoliciesoperationresults | No | No |
> | umístění/serverkeyazureasyncoperation | No | No |
> | umístění/serverkeyoperationresults | No | No |
> | Operace | No | No |
> | servery | Ano | Ano |
> | servery/poradci | No | No |
> | servery/klíče | No | No |
> | servery/privateendpointconnectionproxies | No | No |
> | servery/privateendpointconnections | No | No |
> | servery/privatelinkresources | No | No |
> | servery/querytexts | No | No |
> | servery/recoverableservers | No | No |
> | servery/topquerystatistics | No | No |
> | servery/virtualnetworkrules | No | No |
> | servery/waitstatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/administratorazureasyncoperation | No | No |
> | umístění/administratoroperationresults | No | No |
> | umístění/azureasyncoperation | No | No |
> | umístění/operationresults | No | No |
> | umístění/performancetiers | No | No |
> | umístění/privateendpointconnectionazureasyncoperation | No | No |
> | umístění/privateendpointconnectionoperationresults | No | No |
> | umístění/privateendpointconnectionproxyazureasyncoperation | No | No |
> | umístění/privateendpointconnectionproxyoperationresults | No | No |
> | umístění/recommendedactionsessionsazureasyncoperation | No | No |
> | umístění/recommendedactionsessionsoperationresults | No | No |
> | umístění/securityalertpoliciesazureasyncoperation | No | No |
> | umístění/securityalertpoliciesoperationresults | No | No |
> | umístění/serverkeyazureasyncoperation | No | No |
> | umístění/serverkeyoperationresults | No | No |
> | Operace | No | No |
> | servergroups | No | No |
> | servery | Ano | Ano |
> | servery/poradci | No | No |
> | servery/klíče | No | No |
> | servery/privateendpointconnectionproxies | No | No |
> | servery/privateendpointconnections | No | No |
> | servery/privatelinkresources | No | No |
> | servery/querytexts | No | No |
> | servery/recoverableservers | No | No |
> | servery/topquerystatistics | No | No |
> | servery/virtualnetworkrules | No | No |
> | servery/waitstatistics | No | No |
> | serversv2 | Ano | Ano |
> | singleservers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ano | Ano |
> | operationresults | No | No |
> | Operace | No | No |
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
> | applicationgroups/aplikace | No | No |
> | applicationgroups/desktopy | No | No |
> | applicationgroups / startmenuitems | No | No |
> | hostpools | Ano | Ano |
> | hostpools / sessionhosts | No | No |
> | hostpools / sessionhosts / usersessions | No | No |
> | hostpools / usersessions | No | No |
> | Operace | No | No |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checkprovisioningservicenameavailability | No | No |
> | elasticpools | No | No |
> | elasticpools / iothubtenants | No | No |
> | iothubs | Ano | Ano |
> | iothubs/eventgridfilters | No | No |
> | iothubs/SecuritySettings | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | provisioningservices | Ano | Ano |
> | použití | No | No |

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
> | řadiče/listconnectiondetails | No | No |
> | polohy | No | No |
> | umístění/checkcontainerhostmapping | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labcenters | No | No |
> | Labs | Ano | No |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/servicerunners | Ano | Ano |
> | Labs/VirtualMachines | Ano | No |
> | polohy | No | No |
> | umístění/operace | No | No |
> | Operace | No | No |
> | časových | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | No | No |
> | digitaltwinsinstances / operationresults | No | No |
> | polohy | No | No |
> | Operace | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | No | No |
> | databaseaccounts | Ano | Ano |
> | polohy | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationsstatus | No | No |
> | operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | No | No |
> | doménu | Ano | Ano |
> | domény/domainownershipidentifiers | No | No |
> | generatessorequest | No | No |
> | listdomainrecommendations | No | No |
> | Operace | No | No |
> | topleveldomains | No | No |
> | validatedomainregistrationinformation | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |
> | domény a témata | No | No |
> | eventsubscriptions | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. |
> | extensiontopics | No | No |
> | polohy | No | No |
> | umístění/EventSubscriptions | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationsstatus | No | No |
> | umístění/topictypes | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | operationsstatus | No | No |
> | partnernamespaces | Ano | Ano |
> | partnernamespaces/eventchannels | No | No |
> | partnerregistrations | No | No |
> | partnertopics | Ano | Ano |
> | partnertopics / eventsubscriptions | No | No |
> | systemtopics | Ano | Ano |
> | systemtopics / eventsubscriptions | No | No |
> | popisující | Ano | Ano |
> | topictypes | No | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | No | No |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | existující | Ano | Ano |
> | polohy | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/disasterrecoveryconfigs | No | No |
> | obory názvů/disasterrecoveryconfigs/checknameavailability | No | No |
> | obory názvů/eventhubs | No | No |
> | obory názvů/eventhubs/autorizačních pravidel | No | No |
> | obory názvů/eventhubs/consumergroups | No | No |
> | obory názvů/networkrulesets | No | No |
> | Operace | No | No |
> | skladové | No | No |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | No | No |
> | polohy | No | No |
> | umístění/operace | No | No |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | featureproviders | No | No |
> | funkce | No | No |
> | Operace | No | No |
> | dodavateli | No | No |
> | subscriptionfeatureregistrations | No | No |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | No | No |
> | automanagedvmconfigurationprofiles | No | No |
> | guestconfigurationassignments | No | No |
> | Operace | No | No |
> | Vybavení | No | No |
> | softwareupdateprofile | No | No |
> | softwareupdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | polohy | No | No |
> | umístění/operace | No | No |
> | umístění/operationsstatus | No | No |
> | Operace | No | No |
> | sapmonitors | Ano | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | No | No |
> | polohy | No | No |
> | Operace | No | No |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | clustery/aplikace | No | No |
> | clustery/operationresults | No | No |
> | polohy | No | No |
> | umístění/azureasyncoperations | No | No |
> | umístění/billingspecs | No | No |
> | umístění/možnosti | No | No |
> | umístění/operationresults | No | No |
> | umístění/použití | No | No |
> | umístění/validatecreaterequest | No | No |
> | Operace | No | No |

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nemůžete se ale pohybovat mezi předplatnými síťových prostředků propojených s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nemůžete přesunout do nové skupiny prostředků síťovou kartu, která je připojená k virtuálnímu počítači pro daný cluster.
>
> Když přesunete cluster HDInsight do nového předplatného, napřed přesuňte další prostředky (třeba účet úložiště). Pak můžete cluster HDInsight přesunout sám o sobě.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |
> | services | Ano | Ano |
> | služby/privateendpointconnections | No | No |
> | služby/privatelinkresources | No | No |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | umístění/stav operationstatus | No | No |
> | virtuální | Ano | Ano |
> | počítače/rozšíření | Ano | Ano |
> | Operace | No | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datamanagery | Ano | Ano |
> | Operace | No | No |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | No | No |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | vnfs | No | No |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | No | No |
> | polohy | No | No |
> | networkscopes | No | No |
> | Operace | No | No |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ano | Ano |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ano | Ano |
> | upozorněníprotokoluaktivit | No | No |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | standardní hodnoty | No | No |
> | calculatebaseline | No | No |
> | konstrukční | Ano | Ano |
> | komponenty/události | No | No |
> | komponenty/linkedstorageaccounts | No | No |
> | komponenty/metadata | No | No |
> | komponenty/metriky | No | No |
> | komponenty/pricingplans | No | No |
> | komponenty a dotazy | No | No |
> | datacollectionrules | No | No |
> | diagnosticsettings | No | No |
> | diagnosticsettingscategories | No | No |
> | eventcategories | No | No |
> | EventType | No | No |
> | extendeddiagnosticsettings | No | No |
> | guestdiagnosticsettings | No | No |
> | listmigrationdate | No | No |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | logdefinitions | No | No |
> | logprofiles | No | No |
> | Protokoly | No | No |
> | metricalerts | No | No |
> | metricbaselines | No | No |
> | metricbatch | No | No |
> | metricdefinitions | No | No |
> | metricnamespaces | No | No |
> | metriky | No | No |
> | migratealertrules | No | No |
> | migratetonewpricingmodel | No | No |
> | myworkbooks | No | No |
> | notificationgroups | No | No |
> | Operace | No | No |
> | privatelinkscopeoperationstatuses | No | No |
> | privatelinkscopes | No | No |
> | privatelinkscopes / privateendpointconnectionproxies | No | No |
> | privatelinkscopes / privateendpointconnections | No | No |
> | privatelinkscopes / scopedresources | No | No |
> | rollbacktolegacypricingmodel | No | No |
> | scheduledqueryrules | Ano | Ano |
> | topologie | No | No |
> | transactions | No | No |
> | vminsightsonboardingstatuses | No | No |
> | webové testy | Ano | Ano |
> | webtests/gettestresultfile | No | No |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apptemplates | No | No |
> | checknameavailability | No | No |
> | checksubdomainavailability | No | No |
> | iotapps | Ano | Ano |
> | Operace | No | No |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ano | Ano |
> | zapisovací | Ano | Ano |
> | Operace | No | No |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | deletedvaults | No | No |
> | hsmpools | No | No |
> | polohy | No | No |
> | umístění/deletedvaults | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/operationresults | No | No |
> | managedhsms | No | No |
> | Operace | No | No |
> | trezory | Ano | Ano |
> | trezory/accesspolicies | No | No |
> | trezory/eventgridfilters | No | No |
> | trezory/tajné klíče | No | No |

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ano | Ano |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | registeredsubscriptions | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | clustery/attacheddatabaseconfigurations | No | No |
> | clustery/databáze | No | No |
> | clustery, databáze/datapřipojení | No | No |
> | clustery/databáze/eventhubconnections | No | No |
> | clustery/databáze/principalassignments | No | No |
> | clustery/principalassignments | No | No |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labaccounts | No | No |
> | polohy | No | No |
> | umístění/operace | No | No |
> | Operace | No | No |
> | uživatelé | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | No | No |
> | integrationaccounts | Ano | Ano |
> | integrationserviceenvironments | Ano | No |
> | integrationserviceenvironments/Inspirujte | Ano | No |
> | isolatedenvironments | No | No |
> | polohy | No | No |
> | umístění/pracovní postupy | No | No |
> | Operace | No | No |
> | Zpracovávaný | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | commitmentplans | No | No |
> | polohy | No | No |
> | umístění/operace | No | No |
> | umístění/operationsstatus | No | No |
> | Operace | No | No |
> | WebServices | Ano | No |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | účty/pracovní prostory | No | No |
> | účty/pracovní prostory/projekty | No | No |
> | teamaccounts | No | No |
> | teamaccounts/pracovní prostory | No | No |
> | teamaccounts/pracovní prostory/projekty | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/computeoperationsstatus | No | No |
> | umístění/kvóty | No | No |
> | umístění/updatequotas | No | No |
> | umístění/použití | No | No |
> | umístění/povolených velikostí | No | No |
> | umístění/workspaceoperationsstatus | No | No |
> | Operace | No | No |
> | pracovní prostory | No | No |
> | pracovní prostory a výpočetní prostředky | No | No |
> | pracovní prostory/eventgridfilters | No | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applyupdates | No | No |
> | configurationassignments | No | No |
> | maintenanceconfigurations | Ano | Ano |
> | aktualizovány | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | nebyly | No | No |
> | Operace | No | No |
> | userassignedidentities | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managednetworks | No | No |
> | managednetworks / managednetworkgroups | No | No |
> | managednetworks / managednetworkpeeringpolicies | No | No |
> | oznámení | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | No | No |
> | Operace | No | No |
> | operationstatuses | No | No |
> | registrationassignments | No | No |
> | registrationdefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | getentities | No | No |
> | managementgroups | No | No |
> | managementgroups/nastavení | No | No |
> | operationresults | No | No |
> | operationresults / asyncoperation | No | No |
> | Operace | No | No |
> | resources | No | No |
> | starttenantbackfill | No | No |
> | tenantbackfillstatus | No | No |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/eventgridfilters | No | No |
> | účty/privateatlases | Ano | Ano |
> | Operace | No | No |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | No | No |
> | Rozsah | No | No |
> | offertypes | No | No |
> | offertypes/vydavatelé | No | No |
> | offertypes/vydavatelé/nabídky | No | No |
> | offertypes/vydavatelé/nabídky/plány | No | No |
> | offertypes/vydavatelé/nabídky/plány/smlouvy | No | No |
> | offertypes/vydavatelé/nabídky/plány/konfigurace | No | No |
> | offertypes/vydavatelé/nabídky/plány/konfigurace/importimage | No | No |
> | Operace | No | No |
> | privategalleryitems | No | No |
> | privatestoreclient | No | No |
> | privatestores | No | No |
> | privatestores/nabídky | No | No |
> | produktech | No | No |
> | zdrojů | No | No |
> | Vydavatelé/nabídky | No | No |
> | Vydavatelé/nabídky/změny | No | No |
> | register | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |
> | listcommunicationpreference | No | No |
> | Operace | No | No |
> | updatecommunicationpreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | o | No | No |
> | offertypes | No | No |
> | Operace | No | No |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | MediaServices | Ano | Ano |
> | MediaServices/accountfilters | No | No |
> | MediaServices/assety | No | No |
> | MediaServices/assety/assetfilters | No | No |
> | MediaServices/contentkeypolicies | No | No |
> | MediaServices/eventgridfilters | No | No |
> | MediaServices/liveeventoperations | No | No |
> | MediaServices/liveevents | Ano | Ano |
> | MediaServices/liveevents/liveoutputs | No | No |
> | MediaServices/liveoutputoperations | No | No |
> | MediaServices/streamingendpointoperations | No | No |
> | MediaServices/starají | Ano | Ano |
> | MediaServices/streaminglocators | No | No |
> | MediaServices/streamingpolicies | No | No |
> | MediaServices/transformace | No | No |
> | MediaServices/transformace/úlohy | No | No |
> | Operace | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | No | No |
> | polohy | No | No |
> | umístění/assessmentoptions | No | No |
> | umístění/checknameavailability | No | No |
> | migrateprojects | No | No |
> | movecollections | No | No |
> | Operace | No | No |
> | projekty | No | No |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | No |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | objectunderstandingaccounts | No | No |
> | Operace | No | No |
> | remoterenderingaccounts | Ano | Ano |
> | spatialanchorsaccounts | Ano | Ano |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | netappaccounts | No | No |
> | netappaccounts / backuppolicies | No | No |
> | netappaccounts / capacitypools | No | No |
> | netappaccounts/capacitypools/svazky | No | No |
> | netappaccounts/capacitypools/svazky/mounttargets | No | No |
> | netappaccounts/capacitypools/svazky/snímky | No | No |
> | Operace | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | No | No |
> | applicationgatewayavailableresponseheaders | No | No |
> | applicationgatewayavailableservervariables | No | No |
> | applicationgatewayavailablessloptions | No | No |
> | applicationgatewayavailablewafrulesets | No | No |
> | applicationgateways | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | applicationsecuritygroups | Ano | Ano |
> | azurefirewallfqdntags | No | No |
> | azurefirewalls | No | No |
> | bastionhosts | No | No |
> | bgpservicecommunities | No | No |
> | checkfrontdoornameavailability | No | No |
> | checktrafficmanagernameavailability | No | No |
> | připojení | Ano | Ano |
> | ddoscustompolicies | Ano | Ano |
> | ddosprotectionplans | No | No |
> | dnsoperationresults | No | No |
> | dnsoperationstatuses | No | No |
> | dnszones | Ano | Ano |
> | dnszones/a | No | No |
> | dnszones/AAAA | No | No |
> | dnszones/vše | No | No |
> | dnszones/CAA | No | No |
> | dnszones/CNAME | No | No |
> | dnszones/MX | No | No |
> | dnszones/NS | No | No |
> | dnszones/PTR | No | No |
> | dnszones/sady záznamů | No | No |
> | dnszones/SOA | No | No |
> | dnszones/SRV | No | No |
> | dnszones/txt | No | No |
> | expressroutecircuits | No | No |
> | expressroutegateways | No | No |
> | expressrouteserviceproviders | No | No |
> | firewallpolicies | Ano | Ano |
> | frontdooroperationresults | No | No |
> | frontdoors | No | No |
> | frontdoors / frontendendpoints | No | No |
> | frontdoorwebapplicationfirewallmanagedrulesets | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | getdnsresourcereference | No | No |
> | internalnotify | No | No |
> | ipallocations | Ano | Ano |
> | ipgroups | Ano | Ano |
> | loadbalancers | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | localnetworkgateways | Ano | Ano |
> | polohy | No | No |
> | umístění/autoapprovedprivatelinkservices | No | No |
> | umístění/availabledelegations | No | No |
> | umístění/availableprivateendpointtypes | No | No |
> | umístění/availableservicealiases | No | No |
> | umístění/baremetaltenants | No | No |
> | umístění/batchnotifyprivateendpointsforresourcemove | No | No |
> | umístění/batchvalidateprivateendpointsforresourcemove | No | No |
> | umístění/checkacceleratednetworkingsupport | No | No |
> | umístění/checkdnsnameavailability | No | No |
> | umístění/checkprivatelinkservicevisibility | No | No |
> | umístění/commitinternalazurenetworkmanagerconfiguration | No | No |
> | umístění/effectiveresourceownership | No | No |
> | umístění/nfvoperationresults | No | No |
> | umístění/nfvoperations | No | No |
> | umístění/operationresults | No | No |
> | umístění/operace | No | No |
> | umístění/servicetags | No | No |
> | umístění/setresourceownership | No | No |
> | umístění/supportedvirtualmachinesizes | No | No |
> | umístění/použití | No | No |
> | umístění/validateresourceownership | No | No |
> | umístění/virtualnetworkavailableendpointservices | No | No |
> | natgateways | No | No |
> | networkexperimentprofiles | No | No |
> | networkintentpolicies | Ano | Ano |
> | networkinterfaces | Ano | Ano |
> | networkprofiles | No | No |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | Ano | No |
> | networkwatchers / connectionmonitors | Ano | No |
> | networkwatchers / flowlogs | Ano | No |
> | networkwatchers / pingmeshes | Ano | No |
> | Operace | No | No |
> | p2svpngateways | No | No |
> | privatednsoperationresults | No | No |
> | privatednsoperationstatuses | No | No |
> | privatednszones | Ano | Ano |
> | privatednszones/a | No | No |
> | privatednszones/AAAA | No | No |
> | privatednszones/vše | No | No |
> | privatednszones/CNAME | No | No |
> | privatednszones/MX | No | No |
> | privatednszones/PTR | No | No |
> | privatednszones/SOA | No | No |
> | privatednszones/SRV | No | No |
> | privatednszones/txt | No | No |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privatednszonesinternal | No | No |
> | privateendpointredirectmaps | No | No |
> | privateendpoints | Ano | Ano |
> | privatelinkservices | No | No |
> | publicipaddresses | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ano | Ano |
> | routefilters | No | No |
> | routetables | Ano | Ano |
> | securitypartnerproviders | Ano | Ano |
> | serviceendpointpolicies | Ano | Ano |
> | trafficmanagergeographichierarchies | No | No |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/Heat mapy | No | No |
> | trafficmanagerusermetricskeys | No | No |
> | virtualhubs | No | No |
> | virtualnetworkgateways | Ano | Ano |
> | virtualnetworks | Ano | Ano |
> | virtualnetworktaps | No | No |
> | virtualrouters | Ano | Ano |
> | virtualwans | No | No |
> | vpngateways (virtuální síť WAN) | No | No |
> | vpnserverconfigurations | No | No |
> | vpnsites (virtuální síť WAN) | No | No |

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | obsažené | Ano | Ano |
> | obory názvů/notificationhubs | Ano | Ano |
> | operationresults | No | No |
> | Operace | No | No |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hypervsites | No | No |
> | importsites | No | No |
> | Operace | No | No |
> | serversites | No | No |
> | vmwaresites | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | No | No |
> | deletedworkspaces | No | No |
> | linktargets | No | No |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | storageinsightconfigs | No | No |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/zdroje dat | No | No |
> | pracovní prostory/linkedservices | No | No |
> | pracovní prostory/linkedstorageaccounts | No | No |
> | pracovní prostory/metadata | No | No |
> | pracovní prostory a dotazy | No | No |
> | pracovní prostory/scopedprivatelinkproxies | No | No |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Pracovní prostory, které mají propojený účet Automation, nejde přesunout. Než začnete s přesunem, nezapomeňte zrušit propojení jakýchkoli účtů Automation.

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managementassociations | No | No |
> | managementconfigurations | Ano | Ano |
> | Operace | No | No |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | No | No |
> | legacypeerings | No | No |
> | Operace | No | No |
> | peerasns | No | No |
> | peeringlocations | No | No |
> | partnerské vztahy | No | No |
> | peeringservicecountries | No | No |
> | peeringservicelocations | No | No |
> | peeringserviceproviders | No | No |
> | peeringservices | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | No | No |
> | Operace | No | No |
> | policyevents | No | No |
> | policystates | No | No |
> | policytrackedresources | No | No |
> | nápravy | No | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Konzoly nástroje | No | No |
> | řídicí panely | Ano | Ano |
> | polohy | No | No |
> | umístění/konzoly | No | No |
> | umístění/UserSettings | No | No |
> | Operace | No | No |
> | usersettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | schopností | Ano | Ano |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | checknameavailability | No | No |
> | Operace | No | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableaccounts | No | No |
> | providerregistrations | No | No |
> | providerregistrations / resourcetyperegistrations | No | No |
> | uvádění | No | No |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | pracovní prostory | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | No | No |
> | polohy | No | No |
> | umístění/allocatedstamp | No | No |
> | umístění/allocatestamp | No | No |
> | umístění/backupaadproperties | No | No |
> | umístění/backupcrossregionrestore | No | No |
> | umístění/backupcrrjob | No | No |
> | umístění/backupcrrjobs | No | No |
> | umístění/backupcrroperationresults | No | No |
> | umístění/backupcrroperationsstatus | No | No |
> | umístění/backupprevalidateprotection | No | No |
> | umístění/backupstatus | No | No |
> | umístění/backupvalidatefeatures | No | No |
> | umístění/checknameavailability | No | No |
> | Operace | No | No |
> | replicationeligibilityresults | No | No |
> | trezory | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationsstatus | No | No |
> | openshiftclusters | No | No |
> | Operace | No | No |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/hybridconnections | No | No |
> | obory názvů/hybridconnections/autorizačních pravidel | No | No |
> | obory názvů/privateendpointconnections | No | No |
> | obory názvů/wcfrelays | No | No |
> | obory názvů/wcfrelays/autorizačních pravidel | No | No |
> | Operace | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |
> | odešle | Ano | Ano |
> | resourcechangedetails | No | No |
> | resourcechanges | No | No |
> | resources | No | No |
> | resourceshistory | No | No |
> | subscriptionsstatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | No | No |
> | childavailabilitystatuses | No | No |
> | childresources | No | No |
> | emergingissues | No | No |
> | stránka events | No | No |
> | zprostředkovatele identity | No | No |
> | připomenutí | No | No |
> | Operace | No | No |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | No | No |
> | checkpolicycompliance | No | No |
> | checkresourcename | No | No |
> | nasazení | No | No |
> | nasazení/operace | No | No |
> | deploymentscripts | No | No |
> | deploymentscripts/protokoly | No | No |
> | odkazy | No | No |
> | polohy | No | No |
> | umístění/deploymentscriptoperationresults | No | No |
> | notifyresourcejobs | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | dodavateli | No | No |
> | ResourceGroups | No | No |
> | resources | No | No |
> | odběru | No | No |
> | předplatná/umístění | No | No |
> | předplatná/operationresults | No | No |
> | předplatná/poskytovatelé | No | No |
> | předplatná/ResourceGroups | No | No |
> | předplatná/ResourceGroups/prostředky | No | No |
> | předplatná/prostředky | No | No |
> | předplatná/TagNames | No | No |
> | předplatná/TagNames/tagvalues | No | No |
> | tags | No | No |
> | templatespecs | No | No |
> | templatespecs/verze | No | No |
> | tenantů | No | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | No |
> | checkmoderneligibility | No | No |
> | checknameavailability | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | saasresources | No | No |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checkservicenameavailability | No | No |
> | Operace | No | No |
> | resourcehealthmetadata | No | No |
> | searchservices | Ano | Ano |

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | No | No |
> | advancedthreatprotectionsettings | No | No |
> | výstrahy | No | No |
> | allowedconnections | No | No |
> | applicationwhitelistings | No | No |
> | assessmentmetadata | No | No |
> | hodnocení | No | No |
> | autodismissalertsrules | No | No |
> | automatizace | Ano | Ano |
> | autoprovisioningsettings | No | No |
> | complianceresults | No | No |
> | předpisů | No | No |
> | datacollectionagents | No | No |
> | devicesecuritygroups | No | No |
> | discoveredsecuritysolutions | No | No |
> | externalsecuritysolutions | No | No |
> | informationprotectionpolicies | No | No |
> | iotsecuritysolutions | Ano | Ano |
> | iotsecuritysolutions / analyticsmodels | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | No |
> | jitnetworkaccesspolicies | No | No |
> | polohy | No | No |
> | umístění/výstrahy | No | No |
> | umístění/allowedconnections | No | No |
> | umístění/applicationwhitelistings | No | No |
> | umístění/discoveredsecuritysolutions | No | No |
> | umístění/externalsecuritysolutions | No | No |
> | umístění/jitnetworkaccesspolicies | No | No |
> | umístění/securitysolutions | No | No |
> | umístění/securitysolutionsreferencedata | No | No |
> | umístění/úkoly | No | No |
> | umístění/topologie | No | No |
> | Operace | No | No |
> | Zásady | No | No |
> | ceny | No | No |
> | regulatorycompliancestandards | No | No |
> | regulatorycompliancestandards / regulatorycompliancecontrols | No | No |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | No | No |
> | securitycontacts | No | No |
> | securitysolutions | No | No |
> | securitysolutionsreferencedata | No | No |
> | securitystatuses | No | No |
> | securitystatusessummaries | No | No |
> | servervulnerabilityassessments | No | No |
> | nastavení | No | No |
> | podhodnocení | No | No |
> | úlohy | No | No |
> | topologie | No | No |
> | workspacesettings | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | agregace | No | No |
> | alertrules | No | No |
> | alertruletemplates | No | No |
> | automationrules | No | No |
> | záložky | No | No |
> | věcech | No | No |
> | dataconnects | No | No |
> | dataconnectorscheckrequirements | No | No |
> | podnikům | No | No |
> | entityqueries | No | No |
> | Incidenty | No | No |
> | officeconsents | No | No |
> | Operace | No | No |
> | nastavení | No | No |
> | threatintelligence | No | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | consoleservices | No | No |
> | polohy | No | No |
> | umístění/consoleservices | No | No |
> | Operace | No | No |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | brány | No | No |
> | sortiment | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | checknamespaceavailability | No | No |
> | polohy | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/disasterrecoveryconfigs | No | No |
> | obory názvů/disasterrecoveryconfigs/checknameavailability | No | No |
> | obory názvů/eventgridfilters | No | No |
> | obory názvů/networkrulesets | No | No |
> | obory názvů/fronty | No | No |
> | obory názvů/fronty/autorizačních pravidel | No | No |
> | obory názvů/témata | No | No |
> | obory názvů/témata/autorizačních pravidel | No | No |
> | obory názvů/témata/předplatná | No | No |
> | obory názvů/témata/předplatná/pravidla | No | No |
> | Operace | No | No |
> | premiummessagingregions | No | No |
> | skladové | No | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | No | No |
> | existující | Ano | Ano |
> | clustery/aplikace | No | No |
> | containergroups | No | No |
> | containergroupsets | No | No |
> | edgeclusters | No | No |
> | polohy | No | No |
> | umístění/clusterversions | No | No |
> | umístění/prostředí | No | No |
> | umístění/operationresults | No | No |
> | umístění/operace | No | No |
> | managedclusters | No | No |
> | sítí | No | No |
> | Operace | No | No |
> | secretstores | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | containergroups | No | No |
> | brány | Ano | Ano |
> | polohy | No | No |
> | umístění/applicationoperations | No | No |
> | umístění/gatewayoperations | No | No |
> | umístění/networkoperations | No | No |
> | umístění/secretoperations | No | No |
> | umístění/volumeoperations | No | No |
> | sítí | Ano | Ano |
> | Operace | No | No |
> | záleží | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | uvádění | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/operationstatuses | No | No |
> | umístění/použití | No | No |
> | Operace | No | No |
> | SignalR | Ano | Ano |
> | signál/eventgridfilters | No | No |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | No | No |
> | Operace | No | No |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | No | No |
> | aplikace | No | No |
> | jitrequests | No | No |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | instancepools | No | No |
> | polohy | Ano | Ano |
> | umístění/administratorazureasyncoperation | No | No |
> | umístění/administratoroperationresults | No | No |
> | umístění/auditingsettingsazureasyncoperation | No | No |
> | umístění/auditingsettingsoperationresults | No | No |
> | umístění/možnosti | No | No |
> | umístění/databaseazureasyncoperation | No | No |
> | umístění/databaseoperationresults | No | No |
> | umístění/databaserestoreazureasyncoperation | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/deletevirtualnetworkorsubnetsazureasyncoperation | No | No |
> | umístění/deletevirtualnetworkorsubnetsoperationresults | No | No |
> | umístění/dnsaliasasyncoperation | No | No |
> | umístění/dnsaliasoperationresults | No | No |
> | umístění/elasticpoolazureasyncoperation | No | No |
> | umístění/elasticpooloperationresults | No | No |
> | umístění/encryptionprotectorazureasyncoperation | No | No |
> | umístění/encryptionprotectoroperationresults | No | No |
> | umístění/extendedauditingsettingsazureasyncoperation | No | No |
> | umístění/extendedauditingsettingsoperationresults | No | No |
> | umístění/failovergroupazureasyncoperation | No | No |
> | umístění/failovergroupoperationresults | No | No |
> | umístění/firewallrulesazureasyncoperation | No | No |
> | umístění/firewallrulesoperationresults | No | No |
> | umístění/instancefailovergroupazureasyncoperation | No | No |
> | umístění/instancefailovergroupoperationresults | No | No |
> | umístění/instancefailovergroups | No | No |
> | umístění/instancepoolazureasyncoperation | No | No |
> | umístění/instancepooloperationresults | No | No |
> | umístění/jobagentazureasyncoperation | No | No |
> | umístění/jobagentoperationresults | No | No |
> | umístění/longtermretentionbackupazureasyncoperation | No | No |
> | umístění/longtermretentionbackupoperationresults | No | No |
> | umístění/longtermretentionbackups | No | No |
> | umístění/longtermretentionmanagedinstancebackupazureasyncoperation | No | No |
> | umístění/longtermretentionmanagedinstancebackupoperationresults | No | No |
> | umístění/longtermretentionmanagedinstancebackups | No | No |
> | umístění/longtermretentionmanagedinstances | No | No |
> | umístění/longtermretentionpolicyazureasyncoperation | No | No |
> | umístění/longtermretentionpolicyoperationresults | No | No |
> | umístění/longtermretentionservers | No | No |
> | umístění/manageddatabaseazureasyncoperation | No | No |
> | umístění/manageddatabasecompleterestoreazureasyncoperation | No | No |
> | umístění/manageddatabasecompleterestoreoperationresults | No | No |
> | umístění/manageddatabaseoperationresults | No | No |
> | umístění/manageddatabaserestoreazureasyncoperation | No | No |
> | umístění/manageddatabaserestoreoperationresults | No | No |
> | umístění/managedinstanceazureasyncoperation | No | No |
> | umístění/managedinstanceencryptionprotectorazureasyncoperation | No | No |
> | umístění/managedinstanceencryptionprotectoroperationresults | No | No |
> | umístění/managedinstancekeyazureasyncoperation | No | No |
> | umístění/managedinstancekeyoperationresults | No | No |
> | umístění/managedinstancelongtermretentionpolicyazureasyncoperation | No | No |
> | umístění/managedinstancelongtermretentionpolicyoperationresults | No | No |
> | umístění/managedinstanceoperationresults | No | No |
> | umístění/managedinstancetdecertazureasyncoperation | No | No |
> | umístění/managedinstancetdecertoperationresults | No | No |
> | umístění/managedserversecurityalertpoliciesazureasyncoperation | No | No |
> | umístění/managedserversecurityalertpoliciesoperationresults | No | No |
> | umístění/managedshorttermretentionpolicyazureasyncoperation | No | No |
> | umístění/managedshorttermretentionpolicyoperationresults | No | No |
> | umístění/notifyazureasyncoperation | No | No |
> | umístění/privateendpointconnectionazureasyncoperation | No | No |
> | umístění/privateendpointconnectionoperationresults | No | No |
> | umístění/privateendpointconnectionproxyazureasyncoperation | No | No |
> | umístění/privateendpointconnectionproxyoperationresults | No | No |
> | umístění/securityalertpoliciesazureasyncoperation | No | No |
> | umístění/securityalertpoliciesoperationresults | No | No |
> | umístění/serveradministratorazureasyncoperation | No | No |
> | umístění/serveradministratoroperationresults | No | No |
> | umístění/serverazureasyncoperation | No | No |
> | umístění/serverkeyazureasyncoperation | No | No |
> | umístění/serverkeyoperationresults | No | No |
> | umístění/serveroperationresults | No | No |
> | umístění/shorttermretentionpolicyazureasyncoperation | No | No |
> | umístění/shorttermretentionpolicyoperationresults | No | No |
> | umístění/syncagentoperationresults | No | No |
> | umístění/syncdatabaseids | No | No |
> | umístění/syncgroupoperationresults | No | No |
> | umístění/syncmemberoperationresults | No | No |
> | umístění/tdecertazureasyncoperation | No | No |
> | umístění/tdecertoperationresults | No | No |
> | umístění/použití | No | No |
> | umístění/virtualclusterazureasyncoperation | No | No |
> | umístění/virtualclusteroperationresults | No | No |
> | umístění/virtualnetworkrulesazureasyncoperation | No | No |
> | umístění/virtualnetworkrulesoperationresults | No | No |
> | umístění/vulnerabilityassessmentscanazureasyncoperation | No | No |
> | umístění/vulnerabilityassessmentscanoperationresults | No | No |
> | managedinstances | No | No |
> | managedinstances/Administrators | No | No |
> | managedinstances/databáze | No | No |
> | managedinstances/databáze/backuplongtermretentionpolicies | No | No |
> | managedinstances/databáze/vulnerabilityassessments | No | No |
> | managedinstances / metricdefinitions | No | No |
> | managedinstances/metriky | No | No |
> | managedinstances / recoverabledatabases | No | No |
> | managedinstances / tdecertificates | No | No |
> | managedinstances / vulnerabilityassessments | No | No |
> | Operace | No | No |
> | servery | Ano | Ano |
> | servery/administratoroperationresults | No | No |
> | servery/správci | No | No |
> | servery/poradci | No | No |
> | servery/aggregateddatabasemetrics | No | No |
> | servery/auditingpolicies | No | No |
> | servery/auditingsettings | No | No |
> | servery/automatictuning | No | No |
> | servery/communicationlinks | No | No |
> | servery/connectionpolicies | No | No |
> | servery/databáze | Ano | Ano |
> | servery/databáze/poradci | No | No |
> | servery/databáze/auditingpolicies | No | No |
> | servery/databáze/auditingsettings | No | No |
> | servery/databáze/AuditRecords | No | No |
> | servery/databáze/automatictuning | No | No |
> | servery/databáze/backuplongtermretentionpolicies | No | No |
> | servery/databáze/backupshorttermretentionpolicies | No | No |
> | servery/databáze/connectionpolicies | No | No |
> | servery/databáze/datamaskingpolicies | No | No |
> | servery/databáze/datamaskingpolicies/pravidla | No | No |
> | servery/databáze/rozšíření | No | No |
> | servery/databáze/geobackuppolicies | No | No |
> | servery/databáze/metricdefinitions | No | No |
> | servery/databáze/metriky | No | No |
> | servery/databáze/recommendedsensitivitylabels | No | No |
> | servery/databáze/securityalertpolicies | No | No |
> | servery/databáze/syncgroups | No | No |
> | servery/databáze/syncgroups/syncmembers | No | No |
> | servery/databáze/topqueries | No | No |
> | servery/databáze/topqueries/QUERYTEXT | No | No |
> | servery/databáze/transparentdataencryption | No | No |
> | servery/databáze/vulnerabilityassessment | No | No |
> | servery/databáze/vulnerabilityassessments | No | No |
> | servery/databáze/vulnerabilityassessmentscans | No | No |
> | servery/databáze/vulnerabilityassessmentsettings | No | No |
> | servery/databáze/workloadgroups | No | No |
> | servery/databasesecuritypolicies | No | No |
> | servery/disasterrecoveryconfiguration | No | No |
> | servery/dnsaliases | No | No |
> | servery/elasticpoolestimates | No | No |
> | servery/elasticpools | Ano | Ano |
> | servery/elasticpools/poradci | No | No |
> | servery/elasticpools/metricdefinitions | No | No |
> | servery/elasticpools/metriky | No | No |
> | servery/encryptionprotector | No | No |
> | servery/extendedauditingsettings | No | No |
> | servery/failovergroups | No | No |
> | servery/import | No | No |
> | servery/importexportoperationresults | No | No |
> | servery/jobaccounts | Ano | Ano |
> | servery/jobagents | Ano | Ano |
> | servery/jobagents/úlohy | No | No |
> | servery/jobagents/úlohy/spuštění | No | No |
> | servery/jobagents/úlohy/kroky | No | No |
> | servery/klíče | No | No |
> | servery/operationresults | No | No |
> | servery/recommendedelasticpools | No | No |
> | servery/recoverabledatabases | No | No |
> | servery/restorabledroppeddatabases | No | No |
> | servery/securityalertpolicies | No | No |
> | servery/serviceobjectives | No | No |
> | servery/syncagents | No | No |
> | servery/tdecertificates | No | No |
> | servery/použití | No | No |
> | servery/virtualnetworkrules | No | No |
> | servery/vulnerabilityassessments | No | No |
> | virtualclusters | Ano | Ano |

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure synapse Analytics.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/availabilitygrouplisteneroperationresults | No | No |
> | umístění/operationtypes | No | No |
> | umístění/sqlvirtualmachinegroupoperationresults | No | No |
> | umístění/sqlvirtualmachineoperationresults | No | No |
> | Operace | No | No |
> | sqlvirtualmachinegroups | Ano | Ano |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | No | No |
> | sqlvirtualmachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | polohy | No | No |
> | umístění/asyncoperations | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/použití | No | No |
> | Operace | No | No |
> | storageaccounts | Ano | Ano |
> | storageaccounts/blobservices | No | No |
> | storageaccounts/služby | No | No |
> | storageaccounts/listaccountsas | No | No |
> | storageaccounts/listservicesas | No | No |
> | storageaccounts/queueservices | No | No |
> | storageaccounts/služby | No | No |
> | storageaccounts/služby/metricdefinitions | No | No |
> | storageaccounts/tableservices | No | No |
> | použití | No | No |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mezipaměti | No | No |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/checknameavailability | No | No |
> | umístění/operationresults | No | No |
> | umístění/operace | No | No |
> | umístění/pracovní postupy | No | No |
> | Operace | No | No |
> | storagesyncservices | Ano | Ano |
> | storagesyncservices/registeredserver | No | No |
> | storagesyncservices / syncgroups | No | No |
> | storagesyncservices / syncgroups / cloudendpoints | No | No |
> | storagesyncservices / syncgroups / serverendpoints | No | No |
> | storagesyncservices/pracovní postupy | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | jednatel | No | No |
> | Operace | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | No | No |
> | polohy | No | No |
> | umístění/kvóty | No | No |
> | Operace | No | No |
> | streamingjobs | Ano | Ano |

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | No | No |
> | prostředí/EventSources | No | No |
> | instance | No | No |
> | instance/prostředí | No | No |
> | instance/prostředí/EventSources | No | No |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cancel | No | No |
> | createsubscription | No | No |
> | aby | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | přejmenovat | No | No |
> | subscriptiondefinitions | No | No |
> | subscriptionoperations | No | No |
> | odběru | No | No |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | operationresults | No | No |
> | Operace | No | No |
> | operationsstatus | No | No |
> | services | No | No |
> | služby/problemclassifications | No | No |
> | supporttickets | No | No |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability | No | No |
> | Operace | No | No |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/bigdatapools | Ano | Ano |
> | pracovní prostory/operationresults | No | No |
> | pracovní prostory/operationstatuses | No | No |
> | pracovní prostory/sqlpools | Ano | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | Ano | Ano |
> | prostředí/accesspolicies | No | No |
> | prostředí/EventSources | Ano | Ano |
> | prostředí/referencedatasets | Ano | Ano |
> | Operace | No | No |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Store | Ano | Ano |
> | obchody/accesspolicies | No | No |
> | obchody/služby | No | No |
> | úložiště/služby/tokeny | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | imagetemplates | No | No |
> | imagetemplates / runoutputs | No | No |
> | polohy | No | No |
> | umístění/operace | No | No |
> | Operace | No | No |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | účet/rozšíření | No | No |
> | účet/projekt | No | No |
> | checknameavailability | No | No |
> | Operace | No | No |

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | arczones | No | No |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | resourcepools | No | No |
> | servery vCenter | No | No |
> | virtualmachines | No | No |
> | virtualmachinetemplates | No | No |
> | virtualnetworks | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | No | No |
> | dedicatedcloudservices | No | No |
> | polohy | No | No |
> | umístění/dostupnosti | No | No |
> | umístění/operationresults | No | No |
> | umístění/privateclouds | No | No |
> | umístění/privateclouds/resourcepools | No | No |
> | umístění/privateclouds/virtualmachinetemplates | No | No |
> | umístění/privateclouds/virtualnetworks | No | No |
> | umístění/použití | No | No |
> | Operace | No | No |
> | virtualmachines | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | No | No |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | vnfs | No | No |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | Operace | No | No |
> | plánují | No | No |
> | registeredsubscriptions | No | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availablestacks | No | No |
> | billingmeters | No | No |
> | certifikáty | No | Ano |
> | checknameavailability | No | No |
> | connectiongateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | deletedsites | No | No |
> | deploymentlocations | No | No |
> | geografická umístění | No | No |
> | hostingenvironments | No | No |
> | hostingenvironments / eventgridfilters | No | No |
> | hostingenvironments / multirolepools | No | No |
> | hostingenvironments / workerpools | No | No |
> | ishostingenvironmentnameavailable | No | No |
> | ishostnameavailable | No | No |
> | isusernameavailable | No | No |
> | kubeenvironments | Ano | Ano |
> | listsitesassignedtohostname | No | No |
> | polohy | No | No |
> | umístění/apioperations | No | No |
> | umístění/connectiongatewayinstallations | No | No |
> | umístění/deletedsites | No | No |
> | umístění/deletevirtualnetworkorsubnets | No | No |
> | umístění/extractapidefinitionfromwsdl | No | No |
> | umístění/getnetworkpolicies | No | No |
> | umístění/listwsdlinterfaces | No | No |
> | umístění/Inspirujte | No | No |
> | umístění/operationresults | No | No |
> | umístění/operace | No | No |
> | umístění/moduly runtime | No | No |
> | Operace | No | No |
> | publishingusers | No | No |
> | doporučit | No | No |
> | resourcehealthmetadata | No | No |
> | moduly runtime | No | No |
> | serverových farem | Ano | Ano |
> | serverových farem/eventgridfilters | No | No |
> | místa | Ano | Ano |
> | lokality/eventgridfilters | No | No |
> | lokality/hostnamebindings | No | No |
> | lokality/networkconfig | No | No |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | lokality/sloty/eventgridfilters | No | No |
> | lokality/sloty/hostnamebindings | No | No |
> | lokality/sloty/networkconfig | No | No |
> | sourcecontrols | No | No |
> | staticsites | No | No |
> | oproti | No | No |
> | verifyhostingenvironmentvnet | No | No |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | multipleactivationkeys | No | No |
> | Operace | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deviceservices | No | No |
> | Operace | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | polohy | No | No |
> | umístění/operationstatuses | No | No |
> | Operace | No | No |
> | úlohy | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | No | No |
> | componentssummary | No | No |
> | monitorinstances | No | No |
> | monitorinstancessummary | No | No |
> | Monitor | No | No |
> | notificationsettings | No | No |
> | Operace | No | No |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky
Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
