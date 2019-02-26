---
title: Přehled značky služeb Brána Firewall služby Azure
description: Tento článek představuje přehled značky služeb Brána Firewall služby Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805616"
---
# <a name="azure-firewall-service-tags"></a>Značky služeb Azure bránu Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služeb Azure bránu Firewall je možné v síti pravidla cílového pole. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služeb

Následující značky služeb jsou k dispozici pro použití v pravidlech brány firewall Azure sítě:

* **AzureCloud** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro Azure, včetně všech [veřejné IP adresy datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jako hodnotu zadáte *AzureCloud*, provoz směřující na veřejné IP adresy Azure se povolí nebo zakáže. Pokud chcete povolit přístup ke cloudu Azure jenom v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup ke cloudu Azure pouze v oblasti USA – východ, můžete jako značku služby zadat *AzureCloud.EastUS*. 
* **AzureTrafficManager** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro IP adresy Azure Traffic Manageru test paměti. Další informace o IP adresách sondy pro Traffic Manager najdete v tématu [Azure Traffic Manager – nejčastější dotazy](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Úložiště** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro službu Azure Storage. Pokud jako hodnotu zadáte *Storage*, provoz směřující do úložiště se povolí nebo zakáže. Pokud chcete povolit přístup k úložišti pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup pouze ke službě Azure Storage v oblasti Východní USA, můžete jako značku služby zadat *Storage.EastUS*. Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage.
* **SQL** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure SQL Database a Azure SQL Data Warehouse. Pokud jako hodnotu zadáte *SQL*, provoz směřující do SQL se povolí nebo zakáže. Pokud chcete povolit přístup k SQL jenom v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup pouze ke službě Azure SQL Database v oblasti Východní USA, můžete jako značku služby zadat *Storage.EastUS*. Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL.
* **AzureCosmosDB** (pouze Resource Manager): Tato značka označuje předpony adres služby databáze Azure Cosmos. Pokud jako hodnotu zadáte *AzureCosmosDB*, provoz směřující do služby Azure Cosmos DB se povolí nebo zakáže. Pokud chcete pouze chcete povolit přístup k AzureCosmosDB v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast, ve formátu AzureCosmosDB. [ Název oblasti].
* **AzureKeyVault** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Key Vaultu. Pokud jako hodnotu zadáte *AzureKeyVault*, provoz směřující do služby Azure Key Vault se povolí nebo zakáže. Pokud chcete pouze chcete povolit přístup k AzureKeyVault v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast, ve formátu AzureKeyVault. [ Název oblasti].
* **Centrum EventHub** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure EventHub. Pokud jako hodnotu zadáte *EventHub*, provoz směřující do služby Event Hubs se povolí nebo zakáže. Pokud chcete pouze povolit přístup k centru EventHub v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat oblast, ve formátu EventHub. [ Název oblasti]. 
* **Služby Service Bus** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Service Bus. Pokud jako hodnotu zadáte *ServiceBus*, provoz směřující do služby Service Bus se povolí nebo zakáže. Pokud chcete pouze povolit přístup pro ServiceBus v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat oblast, ve formátu servicebus není platná. [ Název oblasti].
* **MicrosoftContainerRegistry** (pouze Resource Manager): Tato značka označuje předpony adres služby Microsoft Container Registry. Pokud jako hodnotu zadáte *MicrosoftContainerRegistry*, provoz směřující do služby Microsoft Container Registry se povolí nebo zakáže. Pokud chcete pouze chcete povolit přístup k MicrosoftContainerRegistry v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast, ve formátu MicrosoftContainerRegistry. [ Název oblasti].
* **AzureContainerRegistry** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Container Registry. Pokud jako hodnotu zadáte *AzureContainerRegistry*, provoz směřující do služby Azure Container Registry se povolí nebo zakáže. Pokud chcete pouze chcete povolit přístup k AzureContainerRegistry v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast, ve formátu AzureContainerRegistry. [ Název oblasti]. 
* **Služby App Service** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure AppService. Pokud jako hodnotu zadáte *AppService*, provoz směřující do služby App Service se povolí nebo zakáže. Pokud chcete pouze chcete povolit přístup k App Service v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast, ve formátu služby App Service. [ Název oblasti]. 
* **AppServiceManagement** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure App Service Management. Pokud jako hodnotu zadáte *AppServiceManagement*, provoz směřující do služby pro správu služby App Service se povolí nebo zakáže. 
* **ApiManagement** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure API Management. Pokud jako hodnotu zadáte *ApiManagement*, provoz směřující do služby API Management se povolí nebo zakáže.  
* **AzureConnectors** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure konektory. Pokud jako hodnotu zadáte *AzureConnectors*, provoz směřující do konektorů Azure se povolí nebo zakáže. Pokud chcete pouze chcete povolit přístup k AzureConnectors v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast, ve formátu AzureConnectors. [ Název oblasti].
* **AzureDataLake** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Data Lake. Pokud jako hodnotu zadáte *AzureDataLake*, provoz směřující do služby Azure Data Lake se povolí nebo zakáže.
* **AzureActiveDirectory** (pouze Resource Manager): Tato značka označuje předpony adres AzureActiveDirectory služby. Pokud jako hodnotu zadáte *AzureActiveDirectory*, provoz směřující do služby Azure Active Directory se povolí nebo zakáže.
* **AzureMonitor** (pouze Resource Manager): Tato značka označuje předpony adres AzureMonitor služby. Pokud zadáte *AzureMonitor* pro hodnotu, je povolené nebo zakázané AzureMonitor přenosy.
* **ServiceFabric** (pouze Resource Manager): Tato značka označuje předpony adres služby ServiceFabric. Pokud zadáte *ServiceFabric* pro hodnotu, je povolené nebo zakázané ServiceFabric přenosy.
* **AzureMachineLearning** (pouze Resource Manager): Tato značka označuje předpony adres AzureMachineLearning služby. Pokud zadáte *AzureMachineLearning* pro hodnotu, je povolené nebo zakázané AzureMachineLearning přenosy.

## <a name="next-steps"></a>Další postup

Další informace o pravidlech brány Firewall Azure najdete v tématu [pravidlo brány Firewall na Azure zpracování logiky](rule-processing.md).