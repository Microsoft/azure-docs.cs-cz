---
title: Přesunutí podpory operace podle typu prostředku
description: Zobrazí seznam typů prostředků Azure, které se dá přesunout do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 2250283136608161956716abadb63b9f706bf581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460411"
---
# <a name="move-operation-support-for-resources"></a>Podpora operace přesunutí pro prostředky
Tento článek uvádí, zda typ prostředku Azure podporuje operaci přesunutí. Poskytuje také informace o zvláštních podmínkách, které je třeba vzít v úvahu při přesunu zdroje.

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppKonfigurace](#microsoftappconfiguration)
> - [Platforma Microsoft.AppPlatforma](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Adresář Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Fakturace](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Služby Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Soubor Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Spotřeba](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerátor](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Ochrana společnosti Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Služby Microsoft.LabServices](#microsoftlabservices)
> - [Služby Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Služby Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Služby Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Úložiště Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Služby Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Hledat](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Služba Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudJednoduché](#microsoftvmwarecloudsimple)
> - [Soubor Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | domainservices | Ne | Ne |

## <a name="microsoftaadiam"></a>Microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Nájemníky | Ne | Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Konfigurace | Ne | Ne |
> | Doporučení | Ne | Ne |
> | potlačení | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pravidla akce | Ano | Ano |
> | výstrahy | Ne | Ne |
> | souhrn výstrah | Ne | Ne |
> | pravidla smartdetectoralert | Ano | Ano |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | služba | Ano | Ano |

> [!IMPORTANT]
> Službu správy rozhraní API, která je nastavena na skladovou položku spotřeby, nelze přesunout.

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kreslicích úložišť | Ano | Ano |

## <a name="microsoftappplatform"></a>Platforma Microsoft.AppPlatforma

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Jaro | Ano | Ano |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apiapps | Ne | Ne |
> | appidentities | Ne | Ne |
> | Brány | Ne | Ne |

> [!IMPORTANT]
> Viz [Pokyny k přesunutí služby App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checkaccess | Ne | Ne |
> | odepřít přiřazení | Ne | Ne |
> | findorphanroleassignments | Ne | Ne |
> | Zámky | Ne | Ne |
> | Oprávnění | Ne | Ne |
> | přiřazení zásad | Ne | Ne |
> | definice politik | Ne | Ne |
> | definice sad politik | Ne | Ne |
> | přiřazení rolí | Ne | Ne |
> | roleassignmentsusagemetrics | Ne | Ne |
> | definice rolí | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ano | Ano |
> | automationaccounts / konfigurace | Ano | Ano |
> | automationaccounts / runbooky | Ano | Ano |

> [!IMPORTANT]
> Sady Runbook musí existovat ve stejné skupině prostředků jako účet automatizace.

## <a name="microsoftazureactivedirectory"></a>Adresář Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | b2cadresáře | Ano | Ano |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Ne | Ne |
> | postgresinstances | Ne | Ne |
> | instance SQL | Ne | Ne |
> | sqlserverregistrace | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Registrace | Ano | Ano |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dávkové účty | Ano | Ano |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Clustery | Ne | Ne |
> | souborové servery | Ne | Ne |
> | Úlohy | Ne | Ne |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftbilling"></a>Microsoft.Fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | fakturační období | Ne | Ne |
> | oprávnění fakturace | Ne | Ne |
> | přiřazení billingrole | Ne | Ne |
> | definice vyúčtování | Ne | Ne |
> | createbillingroleassignment | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Služby Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | biztalk | Ne | Ne |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blockchainčlenové | Ne | Ne |
> | Pozorovatelů | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | přiřazení podrobných plánů | Ne | Ne |
> | Plány | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | služby botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | redis | Ano | Ano |

> [!IMPORTANT]
> Pokud je instance Azure Cache for Redis nakonfigurovaná s virtuální sítí, nelze ji přesunout do jiného předplatného. Viz [Omezení přesouvání sítí](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Soubor Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Ano | Ano |
> | Profily | Ano | Ano |
> | profily / koncové body | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | objednávky certifikátů | Ano | Ano |

> [!IMPORTANT]
> Viz [Pokyny k přesunutí služby App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | názvy domén | Ano | Ne |
> | virtuální stroje | Ano | Ne |

> [!IMPORTANT]
> Viz [Pokyny pro přesun klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení lze přesunout mezi předplatnými s operací specifickou pro daný scénář.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Ne | Ne |
> | reservedips | Ne | Ne |
> | virtuální sítě | Ne | Ne |

> [!IMPORTANT]
> Viz [Pokyny pro přesun klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení lze přesunout mezi předplatnými s operací specifickou pro daný scénář.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ne |

> [!IMPORTANT]
> Viz [Pokyny pro přesun klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení lze přesunout mezi předplatnými s operací specifickou pro daný scénář.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | skupiny dostupnosti | Ano | Ano |
> | diskovací sady | Ne | Ne |
> | Disky | Ano | Ano |
> | Galerie | Ne | Ne |
> | galerie / obrázky | Ne | Ne |
> | galerie / obrázky / verze | Ne | Ne |
> | skupiny hostitelů | Ne | Ne |
> | hostskupiny / hostitelé | Ne | Ne |
> | images | Ano | Ano |
> | proximityplacementgroups | Ano | Ano |
> | obnovení kolekce bodů | Ne | Ne |
> | sharedvmimages | Ne | Ne |
> | sharedvmimages / verze | Ne | Ne |
> | snímky | Ano | Ano |
> | sshpublickeys | Ne | Ne |
> | virtuální stroje | Ano | Ano |
> | virtualmachines / rozšíření | Ano | Ano |
> | sady velikosti virtualmachinescalesets | Ano | Ano |

> [!IMPORTANT]
> Viz [Virtuální počítače přesunout pokyny](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | agregované náklady | Ne | Ne |
> | Zůstatky | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | Poplatky | Ne | Ne |
> | značky nákladů | Ne | Ne |
> | Kredity | Ne | Ne |
> | stránka events | Ne | Ne |
> | Prognózy | Ne | Ne |
> | Hodně | Ne | Ne |
> | Tržiště | Ne | Ne |
> | výsledky operací | Ne | Ne |
> | stav operace | Ne | Ne |
> | ceníky | Ne | Ne |
> | Produkty | Ne | Ne |
> | rezervační údaje | Ne | Ne |
> | doporučení rezervací | Ne | Ne |
> | shrneme-li výhrady | Ne | Ne |
> | rezervacetransakce | Ne | Ne |
> | tags | Ne | Ne |
> | Nájemníky | Ne | Ne |
> | Podmínky | Ne | Ne |
> | podrobnosti o použití | Ne | Ne |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kontejnerové skupiny | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kontejnerové skupiny | Ne | Ne |
> | odkazy asociace služeb | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Rejstříků | Ano | Ano |
> | registry / buildtasks | Ano | Ano |
> | registry / replikace | Ano | Ano |
> | registry / taskruns | Ano | Ano |
> | registrů / úkolů | Ano | Ano |
> | registry / webhooky | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containerservices | Ne | Ne |
> | spravované clustery | Ne | Ne |
> | openshiftmanagedclustery | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerátor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | výstrahy | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | konektory | Ano | Ano |
> | Dimenze | Ne | Ne |
> | Vývoz | Ne | Ne |
> | externí předplatné | Ne | Ne |
> | forecast | Ne | Ne |
> | query | Ne | Ne |
> | reportconfigs | Ne | Ne |
> | reports | Ne | Ne |
> | pravidla showback | Ne | Ne |
> | zobrazení | Ne | Ne |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Rozbočovače | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Sdružení | Ne | Ne |
> | resourceproviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Úlohy | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Ne | Ne |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Katalogy | Ano | Ano |
> | katalogy dat | Ne | Ne |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | správci připojení | Ne | Ne |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Balíčky | Ne | Ne |
> | Plány | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datafactory | Ano | Ano |
> | Továrny | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ne | Ne |
> | služby / projekty | Ne | Ne |
> | Sloty | Ne | Ne |

## <a name="microsoftdataprotection"></a>Ochrana společnosti Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | trezory záloh | Ne | Ne |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Servery | Ano | Ano |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Servery | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | serverové skupiny | Ne | Ne |
> | Servery | Ano | Ano |
> | serveryv2 | Ano | Ano |
> | singleservers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zdroje artefaktů | Ano | Ano |
> | zavedení | Ano | Ano |
> | service topologie | Ano | Ano |
> | service topologie / služby | Ano | Ano |
> | service topologie / služby / servisní jednotky | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | elastické bazény | Ne | Ne |
> | elasticpooly / iothubtenants | Ne | Ne |
> | iothubs | Ano | Ano |
> | zřizování služeb | Ano | Ano |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Potrubí | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | centra laboratoře | Ne | Ne |
> | Labs | Ano | Ne |
> | laboratoře / prostředí | Ano | Ano |
> | laboratoře / servisní běžci | Ano | Ano |
> | laboratoře / virtualmachines | Ano | Ne |
> | Plány | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databázové účty | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Domény | Ano | Ano |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Domény | Ano | Ano |
> | eventSubscriptions | Ne – nelze přesunout samostatně, ale automaticky se přesunout s odebíránom zdrojem. | Ne – nelze přesunout samostatně, ale automaticky se přesunout s odebíránom zdrojem. |
> | odběru událostí | Ne – nelze přesunout samostatně, ale automaticky se přesunout s odebíránom zdrojem. | Ne – nelze přesunout samostatně, ale automaticky se přesunout s odebíránom zdrojem. |
> | rozšířenítémata | Ne | Ne |
> | obory názvů partnerů | Ano | Ano |
> | partnerská témata | Ano | Ano |
> | systemtopics | Ano | Ano |
> | Témata | Ano | Ano |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Clustery | Ano | Ano |
> | Obory názvů | Ano | Ano |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Obory názvů | Ano | Ano |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Ne | Ne |
> | Software | Ne | Ne |
> | softwareupdateprofile | Ne | Ne |
> | aktualizace softwaru | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hanainstances | Ne | Ne |
> | sapmonitory | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Clustery | Ano | Ano |

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nelze však přesunout mezi předplatnými síťové prostředky propojené s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nelze přesunout do nové skupiny prostředků síťové karty, která je připojena k virtuálnímu počítači pro cluster.
>
> Při přesunu clusteru HDInsight do nového předplatného nejprve přesuňte další prostředky (například účet úložiště). Potom přesuňte cluster HDInsight sám.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Stroje | Ano | Ano |
> | stroje / rozšíření | Ne | Ne |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | správci dat | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Úlohy | Ano | Ano |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | skupiny akcí | Ano | Ano |
> | výstrahy protokolů aktivit | Ne | Ne |
> | pravidla výstrah | Ano | Ano |
> | nastavení automatického škálování | Ano | Ano |
> | Podle směrného plánu | Ne | Ne |
> | vypočítat směrný plán | Ne | Ne |
> | Součásti | Ano | Ano |
> | diagnostická nastavení | Ne | Ne |
> | diagnosticsettingscategories | Ne | Ne |
> | typy událostí | Ne | Ne |
> | extendeddiagnosticsettings | Ne | Ne |
> | logdefinice | Ne | Ne |
> | Protokoly | Ne | Ne |
> | upozornění na metriky | Ne | Ne |
> | metrické směrné základny | Ne | Ne |
> | metrické definice | Ne | Ne |
> | obory_názvů_ | Ne | Ne |
> | metriky | Ne | Ne |
> | mojeseštošity | Ne | Ne |
> | privatelinkscopes | Ano | Ano |
> | pravidla scheduledqueryrules | Ano | Ano |
> | topologie | Ne | Ne |
> | transactions | Ne | Ne |
> | stavy vminsightsonboarding | Ne | Ne |
> | webové testy | Ano | Ano |
> | sešity | Ano | Ano |
> | šablony sešitů | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přechod na nové předplatné nepřekročí [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | iovzony | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | checknameavailability checknameavailability checknameavailability checkname | Ano | Ano |
> | Grafu | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Klenby | Ano | Ano |

> [!IMPORTANT]
> Trezory klíčů používané pro šifrování disku nelze přesunout do skupiny prostředků ve stejném předplatném nebo napříč předplatnými.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Clustery | Ano | Ano |

## <a name="microsoftlabservices"></a>Služby Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | laboratorní účty | Ne | Ne |

## <a name="microsoftlocationbasedservices"></a>Služby Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftlocationservices"></a>Služby Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingová prostředí | Ne | Ne |
> | integraceúčty | Ano | Ano |
> | integrationserviceenvironments | Ano | Ne |
> | integrationserviceenvironments / managedapis | Ano | Ne |
> | izolovaná prostředí | Ne | Ne |
> | Pracovní postupy | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | plány závazků | Ano | Ano |
> | Webservices | Ano | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operativně-klastry | Ne | Ne |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | účty / pracovní prostory | Ne | Ne |
> | účty / pracovní prostory / projekty | Ne | Ne |
> | týmové účty | Ne | Ne |
> | týmové účty / pracovní prostory | Ne | Ne |
> | týmové účty / pracovní prostory / projekty | Ne | Ne |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingové účty | Ne | Ne |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |
> | pracovní prostory / počítá | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Identit | Ne | Ne |
> | userassignedidentities | Ne | Ne |

## <a name="microsoftmanagedservices"></a>Služby Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | registrační úkoly | Ne | Ne |
> | definice registrací | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mediální služby | Ano | Ano |
> | mediaservices / liveevents | Ano | Ano |
> | mediaservices / streamingendpoints | Ano | Ano |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | clustery aplikace | Ne | Ne |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hodnoceníprojektů | Ano | Ano |
> | migrateprojects | Ano | Ano |
> | Projekty | Ne | Ne |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Ne | Ne |
> | netappaccounts / backuppolicies | Ne | Ne |
> | netappaccounts / kapacitní pooly | Ne | Ne |
> | netappaccounts / kapacitní pooly / svazky | Ne | Ne |
> | netappaccounts / capacitypools / volumes / mounttargets netappaccounts / capacitypools / volumes / mounttargets netappaccounts / capacitypools / volumes / mounttargets netapp | Ne | Ne |
> | netappaccounts / capacitypools / svazky / snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikační brány | Ne | Ne |
> | applicationgatewaywebapplicationfirewallpolicies | Ne | Ne |
> | applicationsecuritygroups | Ano | Ano |
> | azurefirewalls | Ano | Ano |
> | bastionhosts | Ne | Ne |
> | Připojení | Ano | Ano |
> | ddoscustompolicies | Ano | Ano |
> | ddosprotectionplans | Ne | Ne |
> | dnszóny | Ano | Ano |
> | expressroutecircuits | Ne | Ne |
> | expresníroutebrány | Ne | Ne |
> | zásady brány firewall | Ano | Ano |
> | přední dveře | Ne | Ne |
> | frontdoorwebapplicationfirewallpolicies | Ne | Ne |
> | skupiny ip | Ano | Ano |
> | vynakládače zatížení | Ano - základní skladová položka<br>Ne - standardní skladová položka | Ano - základní skladová položka<br>Ne - standardní skladová položka |
> | brány místní sítě | Ano | Ano |
> | natgateways | Ano | Ano |
> | profily síťových experimentů | Ano | Ano |
> | zásady záměrů sítě | Ano | Ano |
> | síťová rozhraní | Ano | Ano |
> | síťové profily | Ne | Ne |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | Ano | Ne |
> | networkwatchers / connectionmonitors | Ano | Ne |
> | networkwatchers / flowlogs | Ano | Ne |
> | networkwatchers / pingmeshes | Ano | Ne |
> | p2svpnbrány | Ne | Ne |
> | privátnídnszones | Ano | Ano |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privateendpointredirectmaps | Ne | Ne |
> | soukromé koncové body | Ano | Ano |
> | privátní linkové služby | Ne | Ne |
> | publicipaddresses | Ano - základní skladová položka<br>Ne - standardní skladová položka | Ano - základní skladová položka<br>Ne - standardní skladová položka |
> | publicipprefixes | Ano | Ano |
> | routefilters | Ne | Ne |
> | směrovately | Ano | Ano |
> | zásady servisního bodu | Ano | Ano |
> | trafficmanagerprofiles | Ano | Ano |
> | virtualhubs | Ne | Ne |
> | virtuální sítě brány | Ano | Ano |
> | virtuální sítě | Ano | Ano |
> | virtualnetworktaps | Ne | Ne |
> | virtualroutery | Ano | Ano |
> | virtualwans | Ne | Ne |
> | vpngateways (Virtuální WAN) | Ne | Ne |
> | konfigurace vpnserver | Ne | Ne |
> | vpnsites (Virtuální WAN) | Ne | Ne |
> | zásady firewall pro webové aplikace | Ano | Ano |

> [!IMPORTANT]
> Viz [Pokyny k přesunutí v síti](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Obory názvů | Ano | Ano |
> | jmenné prostory / notificationhubs | Ano | Ano |

## <a name="microsoftobjectstore"></a>Úložiště Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Ne | Ne |
> | pracovní prostory | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přechod na nové předplatné nepřekročí [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managementassociations | Ne | Ne |
> | konfigurace správy | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | peeringy | Ano | Ano |
> | služby partnerského vztahu | Ne | Ne |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | události zásad | Ne | Ne |
> | politické státy | Ne | Ne |
> | policytrackedresources policytrackedresources policytrackedresources policytrack | Ne | Ne |
> | náprava | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | řídicí panely | Ano | Ano |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kořenové zdroje | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kolekce pracovních prostorů | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Kapacity | Ano | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zavedení | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Služby Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zálohy chráněné položky | Ne | Ne |
> | výsledky způsobilosti replikace | Ne | Ne |
> | Klenby | Ano | Ano |

> [!IMPORTANT]
> Viz [Pokyny k přesunutí služby pro obnovení](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Obory názvů | Ano | Ano |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Dotazy | Ano | Ano |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dostupnoststavů | Ne | Ne |
> | stavy childavailabilitystatuses | Ne | Ne |
> | podřízené zdroje | Ne | Ne |
> | stránka events | Ne | Ne |
> | Oznámení | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | skripty nasazení | Ne | Ne |
> | Odkazy | Ne | Ne |
> | tags | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ne |

## <a name="microsoftsearch"></a>Microsoft.Hledat

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | vyhledávací služby | Ano | Ano |

> [!IMPORTANT]
> V jedné operaci nelze přesunout několik prostředků hledání v různých oblastech. Místo toho je přesuňte v samostatných operacích.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | adaptivnísíťkalety | Ne | Ne |
> | advancedthreatprotectionsettings | Ne | Ne |
> | hodnocenímetadat | Ne | Ne |
> | hodnocení | Ne | Ne |
> | automatizace | Ano | Ano |
> | výsledky dodržování předpisů | Ne | Ne |
> | dodržování předpisů | Ne | Ne |
> | datainskumačina | Ne | Ne |
> | skupiny zabezpečení zařízení | Ne | Ne |
> | politiky ochrany informací | Ne | Ne |
> | iotsecuritysolutions | Ano | Ano |
> | hodnocení nezranitelnosti serveru | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Agregace | Ne | Ne |
> | pravidla výstrah | Ne | Ne |
> | šablony výstrah | Ne | Ne |
> | Záložky | Ne | Ne |
> | Případech | Ne | Ne |
> | datové konektory | Ne | Ne |
> | dataconnectorscheckrequirements | Ne | Ne |
> | Entity | Ne | Ne |
> | entitydotazy | Ne | Ne |
> | Incidenty | Ne | Ne |
> | kancelářské souhlasy | Ne | Ne |
> | settings | Ne | Ne |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Brány | Ne | Ne |
> | Uzly | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Obory názvů | Ano | Ano |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |
> | Clustery | Ano | Ano |
> | clustery / aplikace | Ne | Ne |
> | kontejnerové skupiny | Ne | Ne |
> | sady skupin kontejnerů | Ne | Ne |
> | okrajové clustery | Ne | Ne |
> | spravované clustery | Ne | Ne |
> | Sítí | Ne | Ne |
> | tajné obchody | Ne | Ne |
> | volumes | Ne | Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | Brány | Ano | Ano |
> | Sítí | Ano | Ano |
> | Tajemství | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zavedení | Ne | Ne |

## <a name="microsoftsignalrservice"></a>Služba Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | signalizátor | Ano | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybridusebenefitsbenefits | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | definice aplikací | Ne | Ne |
> | aplikace | Ne | Ne |
> | jitrequests | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instancí | Ne | Ne |
> | spravované instance | Ne | Ne |
> | spravované instance / databáze | Ne | Ne |
> | Servery | Ano | Ano |
> | servery / databáze | Ano | Ano |
> | servery / elastické bazény | Ano | Ano |
> | servery / jobaccounts | Ano | Ano |
> | servery / pracovní chod | Ano | Ano |
> | virtuální clustery | Ano | Ano |

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Při přesunutí serveru SQL jsou přesunuty také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ano | Ano |
> | sqlvirtualmachines | Ano | Ano |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dwvm | Ne | Ne |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ano |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Manažeři | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | streamováníúlohy | Ano | Ano |

> [!IMPORTANT]
> Úlohy Stream Analytics nelze přesunout, když běží ve stavu spuštění.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Prostředí | Ne | Ne |
> | prostředí / eventsources | Ne | Ne |
> | Instance | Ne | Ne |
> | instance / prostředí | Ne | Ne |
> | instance / prostředí / eventsources | Ne | Ne |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Createsubscription | Ne | Ne |

## <a name="microsoftsupport"></a>Microsoft.support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | lístky podpory | Ne | Ne |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | registrace zprostředkovatelů | Ne | Ne |
> | resources | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Prostředí | Ano | Ano |
> | prostředí / eventsources | Ano | Ano |
> | prostředí / referencedatové soubory | Ano | Ano |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Obchody | Ano | Ano |

## <a name="microsoftvisualstudio"></a>Microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | account | Ne | Ne |
> | účet / prodloužení | Ano | Ano |
> | účet / projekt | Ano | Ano |

> [!IMPORTANT]
> Pokud chcete změnit předplatné Azure DevOps, [přečtěte si, že změníte předplatné Azure používané k fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudJednoduché

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Ne | Ne |
> | dedicatedcloudservices dedicatedcloudservices dedicatedcloudservices dedicatedcloud | Ne | Ne |
> | virtuální stroje | Ne | Ne |

## <a name="microsoftvsonline"></a>Soubor Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | Plány | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | certifikáty | Ne | Ano |
> | connectiongateways | Ano | Ano |
> | Připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | hostingová prostředí | Ne | Ne |
> | serverové farmy | Ano | Ano |
> | Stránky | Ano | Ano |
> | stránky / premieraddons | Ano | Ano |
> | stránky / sloty | Ano | Ano |
> | statické weby | Ne | Ne |

> [!IMPORTANT]
> Viz [Pokyny k přesunutí služby App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | služby zařízení | Ne | Ne |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Součásti | Ne | Ne |
> | monitoruje instance | Ne | Ne |
> | Monitory | Ne | Ne |
> | nastavení oznámení | Ne | Ne |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran v současné době nepodporují operaci přesunutí.

## <a name="next-steps"></a>Další kroky
Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [soubor move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
