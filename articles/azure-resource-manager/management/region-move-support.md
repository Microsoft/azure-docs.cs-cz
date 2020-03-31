---
title: Podpora přesouvání prostředků Azure napříč oblastmi
description: Zobrazí seznam typů prostředků Azure, které se dá přesouvat napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760704"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Podpora přesouvání prostředků Azure napříč oblastmi

Tento článek potvrzuje, jestli je typ prostředku Azure podporovaný pro přesun do jiné oblasti Azure. 

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppKonfigurace](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Adresář Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
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
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Služby Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Hledat](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Služba Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudJednoduché](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- | 
> | domainservices | Ne | 
> | domainservices / replicasets | Ne | 

## <a name="microsoftaadiam"></a>Microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Nájemníky | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | pravidla akce | Ne | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Servery | Ne |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | služba |  Ano | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | kreslicích úložišť | Ne | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | apiapps | Ne | 
> | appidentities | Ne | 
> | Brány | Ne | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | přiřazení zásad | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | automationaccounts | Ne | 
> | automationaccounts / konfigurace | Ne | 
> | automationaccounts / runbooky | Ne | 



## <a name="microsoftazureactivedirectory"></a>Adresář Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | b2cadresáře | Ne | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | sqlserverregistrace | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Registrace | Ne | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | dávkové účty | Ne |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Clustery | Ne | 
> | souborové servery | Ne | 
> | Úlohy | Ne | 
> | pracovní prostory | Ne | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | mapapis | Ne | 

## <a name="microsoftbiztalkservices"></a>Služby Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | biztalk | Ne | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | blockchainčlenové | Ne |
> | Pozorovatelů | Ne | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | přiřazení podrobných plánů | Ne | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | služby botservices | Ne | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | redis | Ne | 


## <a name="microsoftcdn"></a>Soubor Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Ne |
> | Profily | Ne | 
> | profily / koncové body | Ne | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | objednávky certifikátů | Ne | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | názvy domén | Ne |  
> | virtuální stroje | Ne | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | networksecuritygroups | Ne |
> | reservedips | Ne | 
> | virtuální sítě | Ne | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Ano |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | skupiny dostupnosti | Ne | 
> | diskovací sady | Ne | 
> | Disky | Ne | 
> | Galerie | Ne | 
> | galerie / obrázky | Ne | 
> | galerie / obrázky / verze | Ne | 
> | skupiny hostitelů | Ne | 
> | hostskupiny / hostitelé | Ne | 
> | images | Ne | 
> | proximityplacementgroups | Ne | 
> | obnovení kolekce bodů | Ne | 
> | sharedvmimages | Ne | 
> | sharedvmimages / verze | Ne | 
> | snímky | Ne | 
> | virtuální stroje | Ano | 
> | virtualmachines / rozšíření | Ne | 
> | sady velikosti virtualmachinescalesets | Ne | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | kontejnerové skupiny | Ne | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | kontejnerové skupiny | Ne | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Rejstříků | Ne |  
> | registry / buildtasks | Ne |  
> | registry / replikace | Ne | 
> | registrů / úkolů | Ne |  
> | registry / webhooky | Ne | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | containerservices | Ne | 
> | spravované clustery | Ne | 
> | openshiftmanagedclustery | Ne | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerátor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | konektory | Ne |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Rozbočovače | Ne |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | resourceproviders | Ne | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Úlohy | Ne | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | databoxedgedevices | Ne | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | Ne | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Katalogy | Ne | 
> | katalogy dat | Ne | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | správci připojení | Ne | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Balíčky | Ne | 
> | Plány | Ne | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | datafactory | Ne | 
> | Továrny | Ne |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | datalakeaccounts | Ne | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | services | Ne | 
> | služby / projekty | Ne | 
> | Sloty | Ne | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Servery | Ne |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Servery | Ne |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | serverové skupiny | Ne | 
> | Servery | Ne |  
> | serveryv2 | Ne | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | zdroje artefaktů | Ne | 
> | zavedení | Ne |  
> | service topologie | Ne | 
> | service topologie / služby | Ne |  
> | service topologie / služby / servisní jednotky | Ne | 
> | kroky | Ne | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | elastické bazény | Ne | 
> | elasticpooly / iothubtenants | Ne | 
> | iothubs | Ano | 
> | zřizování služeb | Ne | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | kontrolery | Ne | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | centra laboratoře | Ne | 
> | Labs | Ne | 
> | laboratoře / prostředí | Ne |  
> | laboratoře / servisní běžci | Ne | 
> | laboratoře / virtualmachines | Ne |  
> | Plány | Ne |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | databázové účty | Ne | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Domény | Ne | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | services | Ne |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Domény | Ne |  
> | Témata | Ne | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Clustery | Ne |  
> | Obory názvů | Ne | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | hanainstances | Ne | 
> | sapmonitory | Ne |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Clustery | Ne | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | services | Ne |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Stroje | Ne | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | správci dat |  Ne | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Úlohy |  Ne | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | skupiny akcí |  Ne | 
> | výstrahy protokolů aktivit | Ne | 
> | pravidla výstrah |  Ne | 
> | nastavení automatického škálování |  Ne | 
> | Součásti |  Ne |  
> | guestdiagnosticsettings | Ne | 
> | upozornění na metriky | Ne | 
> | skupiny oznámení | Ne | 
> | pravidla oznámení | Ne | 
> | pravidla scheduledqueryrules |  Ne | 
> | webové testy |  Ne | 
> | sešity |  Ne |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | iovzony |  Ne |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | checknameavailability checknameavailability checknameavailability checkname |  Ne |  
> | Grafu |  Ne | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | hsmpools | Ne | 
> | Klenby |  Ne | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Clustery |  Ne |  

## <a name="microsoftlabservices"></a>Služby Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | laboratorní účty | Ne | 

## <a name="microsoftlocationbasedservices"></a>Služby Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftlocationservices"></a>Služby Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | hostingová prostředí | Ne | 
> | integraceúčty |  Ne |  
> | integrationserviceenvironments | Ne | 
> | izolovaná prostředí | Ne | 
> | Pracovní postupy |  Ne |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | plány závazků |  Ne | 
> | Webservices |  Ne | 
> | pracovní prostory |  Ne | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | operativně-klastry |  Ne | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | účty / pracovní prostory | Ne | 
> | účty / pracovní prostory / projekty | Ne | 
> | týmové účty | Ne | 
> | týmové účty / pracovní prostory | Ne | 
> | týmové účty / pracovní prostory / projekty | Ne | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | hostingové účty | Ne | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | Ne | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | userassignedidentities | Ne | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts |  Ne |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | classicdevservices | Ne | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | mediální služby |  Ne | 
> | mediaservices / liveevents |  Ne | 
> | mediaservices / streamingendpoints |  Ne | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | clustery aplikace | Ne | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | hodnoceníprojektů | Ne | 
> | migrateprojects | Ne | 
> | Projekty | Ne | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | netappaccounts | Ne | 
> | netappaccounts / kapacitní pooly | Ne | 
> | netappaccounts / kapacitní pooly / svazky | Ne | 
> | netappaccounts / capacitypools / volumes / mounttargets netappaccounts / capacitypools / volumes / mounttargets netappaccounts / capacitypools / volumes / mounttargets netapp | Ne | 
> | netappaccounts / capacitypools / svazky / snímky | Ne | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | aplikační brány | Ne | 
> | applicationgatewaywebapplicationfirewallpolicies | Ne | 
> | applicationsecuritygroups |  Ne |  
> | azurefirewalls |  Ne |  
> | bastionhosts | Ne | 
> | Připojení |  Ne | 
> | ddoscustompolicies |  Ne | 
> | ddosprotectionplans | Ne | 
> | dnszóny |  Ne | 
> | expressroutecircuits | Ne | 
> | expressroutecrossconnections | Ne | 
> | expresníroutebrány | Ne | 
> | expresnítrasové přístavy | Ne | 
> | přední dveře | Ne | 
> | frontdoorwebapplicationfirewallpolicies | Ne | 
> | vynakládače zatížení | Ano - základní skladová položka<br>Ne - standardní skladová položka | Ano - základní skladová položka<br> -Ano, standardní Skladové položky |
> | brány místní sítě |  Ne | 
> | natgateways |  Ne | 
> | zásady záměrů sítě |  Ne | 
> | síťová rozhraní | Ano | 
> | síťové profily | Ne | 
> | networksecuritygroups | Ano | 
> | networkwatchers |  Ne |  
> | networkwatchers / connectionmonitors |  Ne | 
> | networkwatchers / objektivy |  Ne | 
> | networkwatchers / pingmeshes |  Ne | 
> | p2svpnbrány | Ne | 
> | privátnídnszones |  Ne |  
> | privatednszones / virtualnetworklinks |  Ne |  
> | soukromé koncové body | Ne | 
> | privátní linkové služby | Ne | 
> | publicipaddresses | Ano - základní skladová položka<br>Ne - standardní skladová položka | Ano - základní skladová položka<br>Ne - standardní skladová položka |
> | publicipprefixes | Ne | 
> | routefilters | Ne | 
> | směrovately |  Ne | 
> | zásady servisního bodu |  Ne | 
> | trafficmanagerprofiles |  Ne | 
> | virtualhubs | Ne | 
> | virtuální sítě brány |  Ne |  
> | virtuální sítě |  Ne | 
> | virtualnetworktaps | Ne | 
> | virtualwans | Ne | 
> | vpngateways (Virtuální WAN) | Ne | 
> | vpnsites (Virtuální WAN) | Ne | 
> | zásady firewall pro webové aplikace |  Ne | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Obory názvů |  Ne | 
> | jmenné prostory / notificationhubs |  Ne |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | pracovní prostory |  Ne | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | konfigurace správy |  Ne | 
> | zobrazení |  Ne | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | peeringy | Ne | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | řídicí panely | Ne | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | kořenové zdroje | Ne | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | kolekce pracovních prostorů |  Ne | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Kapacity |  Ne | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftrecoveryservices"></a>Služby Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Klenby | Ne. [Zakázat úschovnu a znovu vytvořit](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) pro obnovení webu  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Obory názvů |  Ne | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Dotazy |  Ne |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | aplikace |  Ne | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | flows |  Ne |  
> | jobcollections |  Ne | 

## <a name="microsoftsearch"></a>Microsoft.Hledat

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | vyhledávací služby |  Ne | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Ne | 
> | playbookkonfigurace | Ne | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Brány | Ne | 
> | Uzly | Ne | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Obory názvů |  Ne | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 
> | Clustery |  Ne | 
> | clustery / aplikace | Ne | 
> | kontejnerové skupiny | Ne | 
> | sady skupin kontejnerů | Ne | 
> | okrajové clustery | Ne | 
> | Sítí | Ne | 
> | tajné obchody | Ne | 
> | volumes | Ne | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | aplikace |  Ne | 
> | kontejnerové skupiny | Ne | 
> | Brány |  Ne | 
> | Sítí |  Ne | 
> | Tajemství |  Ne | 
> | volumes |  Ne |  

## <a name="microsoftsignalrservice"></a>Služba Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | signalizátor |  Ne |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | definice spotřebičů | Ne | 
> | Spotřebiče | Ne | 
> | definice aplikací | Ne | 
> | aplikace | Ne | 
> | jitrequests | Ne | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | instancí | Ne | 
> | spravované instance | Ano | 
> | spravované instance / databáze | Ano | 
> | Servery | Ano | 
> | servery / databáze | Ano | 
> | servery / elastické bazény | Ano | 
> | virtuální clustery | Ano | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Ne |  
> | sqlvirtualmachines |  Ne |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | dwvm | Ne | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Ano | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Mezipaměti | Ne | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | storagesyncservices |  Ne | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | Ne | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | Ne | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Manažeři | Ne | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | streamováníúlohy |  Ne |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Prostředí | Ne | 
> | prostředí / eventsources | Ne | 
> | Instance | Ne | 
> | instance / prostředí | Ne | 
> | instance / prostředí / eventsources | Ne | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | registrace zprostředkovatelů | Ne | 
> | resources | Ne | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Prostředí |  Ne | 
> | prostředí / eventsources |  Ne |  
> | prostředí / referencedatové soubory |  Ne | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | Obchody | Ne | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | imagetemplates | Ne | 

## <a name="microsoftvisualstudio"></a>Microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | account |  Ne | 
> | účet / prodloužení |  Ne | 
> | účet / projekt |  Ne | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudJednoduché

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Ne | 
> | dedicatedcloudservices dedicatedcloudservices dedicatedcloudservices dedicatedcloud | Ne | 
> | virtuální stroje | Ne | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | certifikáty | Ne | 
> | connectiongateways |  Ne |  
> | Připojení |  Ne |  
> | customapis |  Ne | 
> | hostingová prostředí | Ne | 
> | serverové farmy |  Ne |  
> | Stránky |  Ne | 
> | stránky / premieraddons |  Ne |  
> | stránky / sloty |  Ne |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | služby zařízení | Ne | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesun oblasti | 
> | ------------- | ----------- |
> | aplikační skupiny | Ne | 
> | hostitelských poolů | Ne | 
> | pracovní prostory | Ne | 

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran v současné době nepodporují operaci přesunutí.
