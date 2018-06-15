---
title: Postup vytvoření aplikační službu prostředí v1
description: Popis toku vytvoření app service prostředí v1
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
ms.openlocfilehash: 2741ea2931ddd7989fc05e1cddbeedb80bf30410
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386618"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Postup vytvoření aplikační službu prostředí v1 

> [!NOTE]
> Tento článek je o v1 App Service Environment. Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

### <a name="overview"></a>Přehled
V aplikaci služby prostředí řízení, je možnost Premium služby Azure App Service, který doručí funkci rozšířené konfigurace, která není k dispozici v víceklientské razítka. Azure App Service funkci App Service Environment v podstatě nasadí do virtuální sítě zákazníka. K získání lépe porozumět funkce nabízené číst prostředí App Service [novinky služby App Service Environment] [ WhatisASE] dokumentaci.

### <a name="before-you-create-your-ase"></a>Před vytvořením vaší App Service Environment
Je důležité si uvědomit věcí, které nelze změnit. Tyto aspekty, které o vaší App Service Environment nelze změnit po vytvoření jsou:

* Umístění
* Předplatné
* Skupina prostředků
* Virtuální síť použít
* Podsítě používané 
* Velikost podsítě

Pokud výběr virtuální sítě a určení podsíť, zkontrolujte, zda je dostatečně velký pro přizpůsobení případnému budoucímu růstu. 

### <a name="creating-an-app-service-environment-v1"></a>Vytvoření aplikace služby prostředí v1
K vytvoření App Service Environment v1, můžete hledat v Azure Marketplace pro ***App Service Environment v1***, nebo projít **vytvořit prostředek** -> **Web + mobilní**  ->  **Služby app Service Environment**. Vytvoření ASEv1:

1. Zadejte název vaší App Service Environment. Název, který jste zadali pro App Service Environment se použije pro aplikace vytvořené v App Service Environment. Pokud je název pomocného mechanismu řízení appsvcenvdemo, bude název subdomény: *appsvcenvdemo.p.azurewebsites.net*. Pokud jste tedy vytvořili aplikaci s názvem *mytestapp*, bylo by adresovatelné v *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Prázdné znaky nelze použít název vaší App Service Environment. Pokud používáte velkých písmen v názvu, název domény bude celkový malá verze s tímto názvem. Pokud používáte ILB, název App Service Environment není používáno ve vašem subdomény, ale je místo výslovně uvedeno během vytváření App Service Environment.
   
    ![][1]
2. Vyberte své předplatné. Odběr, který používáte pro vaše App Service Environment bude platit taky pro všechny aplikace, které vytvoříte v tomto App Service Environment. Nelze umístit vaše App Service Environment ve virtuální síti, který je v jiné předplatné.
3. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků pro vaše App Service Environment musí být stejná, který se používá pro vaši virtuální síť. Pokud vyberete existující virtuální síť, výběr skupiny prostředků pro vaše App Service Environment bude aktualizovat tak, aby odrážela u vaší virtuální sítě.
   
    ![][2]
4. Proveďte požadovaná nastavení virtuální sítě a umístění. Můžete vytvořit novou virtuální síť nebo vybrat existující virtuální síť. Pokud vyberete novou virtuální síť, potom můžete zadat název a umístění. Nové sítě VNet bude mít 192.168.250.0/23 rozsah adres a podsíť s názvem **výchozí** který je definován jako 192.168.250.0/24. Můžete taky jednoduše vybrat existující Classic nebo Resource Manager virtuální sítě. Výběr typu VIP Určuje, zda vaše App Service Environment jsou přímo přístupné z Internetu (externí) nebo, pokud používá interní nástroj pro vyrovnávání zatížení (ILB). Další informace o nich přečíst [interní pro vyrovnávání zatížení pomocí služby App Service Environment][ILBASE]. Pokud vyberete typ externí virtuální IP adresy můžete vybrat kolik externí IP adresy v systému je vytvořena s pro účely IPSSL. Pokud vyberete interní budete muset zadat subdomény, který bude používat vaše App Service Environment. ASEs lze nasadit do virtuálních sítí, které používají *buď* rozsahů veřejných adres *nebo* RFC1918 adresní prostory (tj. privátní adresy). Chcete-li používat virtuální síť s rozsahem veřejnou adresu, musíte vytvořit virtuální síť předem. Když vyberete existující virtuální síť bude muset vytvořit novou podsíť během vytváření App Service Environment. **Podsíť předem vytvořené nelze použít na portálu. Pokud vytvoříte vaší App Service Environment pomocí šablony správce prostředků, můžete vytvořit App Service Environment s existující podsítí.** Vytvořit App Service Environment z šablony použijte informace v tomto [vytváření služby App Service Environment z šablony] [ ILBAseTemplate] a zde [vytváření ILB App Service Environment z šablony][ASEfromTemplate].

### <a name="details"></a>Podrobnosti
App Service Environment je vytvořen s 2 končí Front a 2 pracovní procesy. Front končí fungovat jako koncové body protokolu HTTP nebo HTTPS a odesílá data na pracovních procesů, které jsou role, které umožňuje hostování vašich aplikací. Můžete upravit množství po vytvoření App Service Environment a můžete také nastavit pravidel škálování na těchto fondů zdrojů. Další podrobnosti ohledně ruční škálování, Správa a sledování služby App Service Environment přejděte sem: [postup konfigurace služby App Service Environment][ASEConfig] 

V podsíti používané App Service Environment může existovat jenom na jeden App Service Environment. Podsíť nelze použít pro jakoukoli jinou hodnotu než App Service Environment

### <a name="after-app-service-environment-v1-creation"></a>Po vytvoření služby App Service Environment v1
Po vytvoření App Service Environment můžete upravit:

* Počet Front končí (minimální: 2)
* Počet pracovních procesů (minimální: 2)
* Množství IP adres, které jsou k dispozici pro protokol SSL IP
* Výpočetní velikostí prostředků používá Front končí nebo pracovníci (minimální velikost Front-endu je P2)

Existují další podrobnosti ohledně ruční škálování, správu a monitorování zde prostředí App Service: [postup konfigurace služby App Service Environment][ASEConfig] 

Informace o automatické škálování je Průvodce zde: [postup konfigurace automatického škálování App Service Environment][ASEAutoscale]

Existují další závislosti, které nejsou k dispozici pro vlastní nastavení, jako jsou databáze a úložiště. Jsou zpracovávány v Azure a dodávají s v systému. Úložiště systému podporuje až 500 GB pro celý App Service Environment a databáze se upraví Azure podle potřeb škálování systému.

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment v1, najdete v tématu [Úvod do služby App Service Environment v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
