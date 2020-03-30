---
title: Vytvoření ase v1
description: Popis toku vytvoření pro prostředí služby aplikace v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 752334e3d594b1f95786aecaca134b74c4e264d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688695"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Jak vytvořit prostředí služby App Service v1 

> [!NOTE]
> Tento článek je o prostředí služby App Service v1. K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).
> 

### <a name="overview"></a>Přehled
Prostředí služby App Service Environment (ASE) je možnost služby Premium služby Azure App Service, která poskytuje rozšířené možnosti konfigurace, která není k dispozici ve víceklientských razítkách. Funkce služby ASE v podstatě nasazuje službu Azure App Service do virtuální sítě zákazníka. Chcete-li získat větší přehled o možnostech nabízených prostředí služby App Service, přečtěte si dokumentaci [What is a App Service Environment.][WhatisASE]

### <a name="before-you-create-your-ase"></a>Před vytvořením ase
Je důležité být si vědom toho, co nemůžete změnit. Tyto aspekty, které nelze změnit o vaší ase po jeho vytvoření jsou:

* Umístění
* Předplatné
* Skupina prostředků
* Použitá virtuální síť
* Použitá podsíť 
* Velikost podsítě

Při výběru virtuální sítě a určení podsítě se ujistěte, že je dostatečně velký, aby vyhovoval y budoucí růst. 

### <a name="creating-an-app-service-environment-v1"></a>Vytvoření prostředí služby App Service v1
Pokud chcete vytvořit prostředí služby App Service v1, můžete prohledat prostředí Azure Marketplace for ***App Service Environment v1***nebo projít **vytvoření prostředí** -> **webových a mobilních** -> **aplikací**. Vytvoření ASEv1:

1. Zadejte název své ase. Název, který zadáte pro službu ASE, bude použit pro aplikace vytvořené v systému ASE. Pokud je název služby ASE appsvcenvdemo, název subdomény bude: *appsvcenvdemo.p.azurewebsites.net*. Pokud jste tak vytvořili aplikaci s názvem *mytestapp*, bylo by adresovatelné na *mytestapp.appsvcenvdemo.p.azurewebsites.net*. V názvu aplikace ASE nelze použít prázdné znaky. Pokud v názvu použijete velká písmena, bude název domény celkově malá verze tohoto názvu. Pokud používáte ILB, název služby ASE se nepoužívá ve vaší subdoméně, ale je místo toho explicitně uvedeno během vytváření služby ASE.
   
    ![][1]
2. Vyberte své předplatné. Předplatné, které používáte pro službu ASE, se bude vztahovat také na všechny aplikace, které v této službě ASE vytvoříte. Nelze umístit službu ASE do virtuální sítě, která je v jiném předplatném.
3. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků používaná pro vaši sestavu se musí stejnou, která se používá pro virtuální síť. Pokud vyberete již existující virtuální síť, výběr skupiny prostředků pro vaši ase se aktualizuje tak, aby odrážela výběr vaší virtuální sítě.
   
    ![][2]
4. Proveďte virtuální síť a umístění výběry. Můžete vytvořit novou virtuální síť nebo vybrat již existující virtuální síť. Pokud vyberete novou virtuální síť, můžete zadat název a umístění. Nová virtuální síť bude mít rozsah adres 192.168.250.0/23 a podsíť s názvem **default,** která je definována jako 192.168.250.0/24. Můžete také jednoduše vybrat již existující virtuální síť Classic nebo Resource Manager. Výběr typu VIP určuje, zda je vaše ase přímo přístupná z internetu (externí) nebo zda používá interní nástroj pro vyrovnávání zatížení (ILB). Další informace o nich najdete [v tématu Použití interního vytápěče zatížení v prostředí služby App Service][ILBASE]. Pokud vyberete typ VIP externí pak můžete vybrat, kolik externích IP adres systém je vytvořen s pro účely IPSSL. Pokud vyberete interní, budete muset zadat subdoménu, kterou bude vaše služba ASE používat. Ases lze nasadit do virtuálních sítí, které používají *rozsahy* veřejných adres *nebo* adresní prostory RFC1918 (tj. soukromé adresy). Chcete-li používat virtuální síť s rozsahem veřejných adres, budete muset vytvořit virtuální síť předem. Když vyberete již existující virtuální síť, budete muset vytvořit novou podsíť během vytváření ase. **Na portálu nelze použít předem vytvořenou podsíť. Pokud službu ASE vytvoříte pomocí šablony správce prostředků, můžete vytvořit službu ASE s již existující podsítí.** Chcete-li vytvořit službu ASE ze šablony, použijte informace zde, [vytvoření prostředí služby App Service ze šablony][ILBAseTemplate] a zde vytvoření prostředí [služby ILB App Service ze šablony][ASEfromTemplate].

### <a name="details"></a>Podrobnosti
ASE je vytvořen s 2 front-endy a 2 pracovníky. Front-endy fungují jako koncové body HTTP/HTTPS a odesílají provoz pracovníkům, což jsou role, které hostují vaše aplikace. Můžete upravit množství po vytvoření ase a dokonce můžete nastavit pravidla automatického škálování na těchto fondech prostředků. Další podrobnosti o ručníškálování, správu a monitorování prostředí služby App Service naleznete zde: [Jak nakonfigurovat prostředí služby App Service][ASEConfig] 

Pouze jeden Způsob ovou ase může existovat v podsíti používané ase. Podsíť nelze použít pro nic jiného než aSE

### <a name="after-app-service-environment-v1-creation"></a>Po vytvoření prostředí služby App Service v1
Po vytvoření ase můžete upravit:

* Množství front-endů (minimálně: 2)
* Počet pracovníků (minimálně: 2)
* Množství IP adres dostupných pro protokol IP SSL
* Vypočítat velikosti prostředků používané front-endy nebo pracovníky (minimální velikost front-endu je P2)

Další podrobnosti o ručníškálování, správu a monitorování prostředí služby App Service zde: [Jak nakonfigurovat prostředí služby App Service][ASEConfig] 

Informace o automatickém škálování zde například: [Konfigurace automatického škálování pro prostředí služby App Service][ASEAutoscale]

Existují další závislosti, které nejsou k dispozici pro vlastní nastavení, jako je například databáze a úložiště. Ty jsou zpracovány Azure a jsou dodávány se systémem. Systémové úložiště podporuje až 500 GB pro celé prostředí služby App Service a databáze je upravena Azure podle potřeby podle škálování systému.

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím Služby aplikací v1, [přečtěte si informace o úvodu do prostředí služby App Service v1.][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
