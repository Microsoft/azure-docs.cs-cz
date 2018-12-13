---
title: Vytvoření služby App Service Environment v1 – Azure
description: Popis vytvoření toku pro app service environment v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1cfe8dae750cb113dd5346bfba261099fd3b6044
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276736"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Vytvoření služby App Service Environment v1 

> [!NOTE]
> Tento článek je o App Service Environment v1. Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

### <a name="overview"></a>Přehled
App Service Environment (ASE) je možnost Premium služby Azure App Service, která poskytuje rozšířené konfigurace schopností, který není k dispozici v razítka pro více tenantů. Funkce služby ASE v podstatě nasadí Azure App Service do virtuální sítě zákazníka. Abyste získali větší představu o funkce, které nabízí služba App Service Environment, přečtěte si [novinky službě App Service Environment] [ WhatisASE] dokumentaci.

### <a name="before-you-create-your-ase"></a>Před vytvořením služby ASE
Je důležité znát věci, které nelze změnit. Tyto aspekty, které o vaší služby ASE nelze změnit po vytvoření jsou tyto:

* Umístění
* Předplatné
* Skupina prostředků
* Použít virtuální síť
* Podsíť používá 
* Velikost podsítě

Při výběru virtuální sítě a určení podsíť, ujistěte se, že je dostatečně velký pro přizpůsobení případnému budoucímu růstu. 

### <a name="creating-an-app-service-environment-v1"></a>Vytvoření App Service Environment v1
K vytvoření App Service Environment v1, můžete na Azure Marketplace vyhledejte ***služby App Service Environment v1***, nebo projít **vytvořit prostředek** -> **Web + mobilní zařízení**  ->  **App Service Environment**. Vytvoření ASEv1:

1. Zadejte název vaší služby ASE. Název, který zadáte pro danou službu ASE se použije pro aplikace vytvořené ve službě ASE. Pokud je název služby ase appsvcenvdemo, název subdomény by: *appsvcenvdemo.p.azurewebsites.net*. Pokud jste tedy vytvořili aplikaci s názvem *mytestapp*, bylo by adresovatelné v *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Prázdné znaky nelze použít název vaší služby ASE. Pokud používáte velká písmena v názvu, název domény bude celkový malá verze s tímto názvem. Pokud používáte ILB, název vaší služby ASE se nepoužívá v vaše subdomény, ale místo toho výslovně stanovena během vytváření služby ASE.
   
    ![][1]
2. Vyberte své předplatné. Předplatné, které používáte pro svoji službu ASE se platí také pro všechny aplikace, které vytvoříte v této službě ASE. Nelze umístit svoji službu ASE ve virtuální síti, která je v jiném předplatném.
3. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků použitá pro vaši službu ASE musí být stejné jako, který se používá pro vaši virtuální síť. Pokud vyberete stávající virtuální síť, výběr skupiny prostředků pro vaši službu ASE se aktualizují, aby to odrážel vaší virtuální sítě.
   
    ![][2]
4. Proveďte požadovaná nastavení virtuální sítě a umístění. Můžete vytvořit novou virtuální síť nebo vybrat už existující virtuální sítě. Pokud vyberete novou virtuální síť, můžete zadat název a umístění. Novou virtuální síť bude mít 192.168.250.0/23 rozsah adres a podsíť s názvem **výchozí** , který je definován jako 192.168.250.0/24. Můžete také jednoduše vybrat již existující Classic nebo virtuální síti správce prostředků. Typ VIP výběr určuje, zda vaše služba ASE je přímo přístupný z Internetu (externí), nebo pokud používá interní zatížení Balancer (ILB). Další informace o nich přečíst [interního nástroje Load Balancer pomocí služby App Service Environment][ILBASE]. Pokud vyberete virtuální IP adresu typu externí můžete vybrat kolik externích IP adres v systému se vytvoří s pro účely IPSSL. Pokud vyberete interní je potřeba zadat subdomény, které vaše služba ASE bude používat. Je možné nasadit služby ase do virtuální sítě, které používají *buď* rozsahů adres veřejné *nebo* definice RFC1918 adresní prostory (tj. privátní adresy). Chcete-li použít virtuální síť s veřejnou adresu rozsahu, je potřeba vytvořit virtuální síť předem. Když vyberete existující virtuální sítě je potřeba vytvořit novou podsíť během vytváření služby ASE. **Nelze použít předem vytvořený podsítě na portálu. Pokud vaše služba ASE pomocí šablony resource Manageru vytvoříte, můžete vytvořit službu ASE s existující podsítí.** Vytvořit službu ASE ze šablony použijte tyto informace tady, [vytváření služby App Service Environment pomocí šablony] [ ILBAseTemplate] a tady [vytvoření ILB App Service Environment pomocí šablony] [ASEfromTemplate].

### <a name="details"></a>Podrobnosti
Služba ASE se vytvoří s 2 front-endy a 2 pracovní procesy. Front-endy fungovat jako koncové body HTTP/HTTPS a odesílat provoz do pracovních procesů, které jsou role, které jsou hostiteli vaše aplikace. Můžete upravit množství po vytvoření služby ASE a dokonce nastavit pravidla automatického škálování na těchto fondů zdrojů. Další podrobnosti o ruční škálování správu a monitorování služby App Service Environment najdete tady: [Postup konfigurace služby App Service Environment][ASEConfig] 

V podsíti služby ASE používá může existovat pouze jeden služby ASE. Podsíť se nedá použít pro ostatní formáty vyjma služby ASE

### <a name="after-app-service-environment-v1-creation"></a>Po vytvoření služby App Service Environment v1
Po vytvoření služby ASE můžete upravit:

* Počet front-endy (minimální: 2)
* Počet pracovních procesů (minimální: 2)
* Počet IP adres pro IP SSL.
* Výpočetní prostředek velikosti používá front-endy nebo pracovní procesy (minimální velikost front-endu je P2)

Existují další podrobnosti o ruční škálování, správu a monitorování služby App Service Environment tady: [Postup konfigurace služby App Service Environment][ASEConfig] 

Informace o automatické škálování je Průvodce tady: [Postup konfigurace automatického škálování služby App Service Environment][ASEAutoscale]

Existují další závislosti, které nejsou k dispozici pro přizpůsobení, jako jsou databáze a úložišť. Tyto jsou zpracovány Azure a součástí systému. Systém úložiště podporuje až 500 GB pro celé služby App Service Environment a databáze je upravit tak, že Azure podle potřeby škálování systému.

## <a name="getting-started"></a>Začínáme
Začít s App Service Environment v1, přečtěte si článek [Úvod do služby App Service Environment v1][WhatisASE]

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
