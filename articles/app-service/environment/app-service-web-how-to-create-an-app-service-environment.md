---
title: Vytvoření namocného mechanismu v1
description: Popis toku vytváření pro službu App Service Environment v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220571"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Vytvoření App Service Environment v1 

> [!NOTE]
> Tento článek se týká App Service Environment v1. Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).
> 

### <a name="overview"></a>Přehled
App Service Environment (pomocným mechanismem) je možnost služby Premium v Azure App Service, která poskytuje rozšířenou možnost konfigurace, která není k dispozici v razítkech s více klienty. Funkce pomocného uživatele v podstatě nasadí Azure App Service do virtuální sítě zákazníka. Pokud chcete získat větší přehled o funkcích, které nabízí App Service prostředí, přečtěte si článek [co je App Service Environment][WhatisASE] dokumentace.

### <a name="before-you-create-your-ase"></a>Před vytvořením pomocného mechanismu
Je důležité vědět, co nemůžete změnit. Tyto aspekty nemůžete po vytvoření pomocného mechanismu změnit:

* Umístění
* Předplatné
* Resource Group
* Použitá virtuální síť
* Použitá podsíť 
* Velikost podsítě

Když vybíráte virtuální síť a zadáváte podsíť, ujistěte se, že je dostatečně velká, aby vyhovovala budoucímu růstu. 

### <a name="creating-an-app-service-environment-v1"></a>Vytvoření App Service Environment v1
Pokud chcete vytvořit App Service Environment V1, můžete vyhledat Azure Marketplace ***App Service Environment v1***nebo si projít **vytvořením prostředku**  ->  **web a mobilní zařízení**  ->  **App Service Environment**. Vytvoření ASEv1:

1. Zadejte název vašeho pomocného programu. Název, který zadáte pro pomocného nástroje, se použije pro aplikace vytvořené v pomocném mechanismu. Pokud je název pomocného mechanismu appsvcenvdemo, název subdomény by byl: *appsvcenvdemo.p.azurewebsites.NET*. Pokud tedy vytvoříte aplikaci s názvem *MyTestApp*, bude se adresovat na *MyTestApp.appsvcenvdemo.p.azurewebsites.NET*. V názvu vašeho pomocného mechanismu se nedá použít prázdné znaky. Použijete-li velká písmena v názvu, bude název domény celková verze tohoto názvu. Pokud použijete interního nástroje, vaše jméno pomocného mechanismu se nepoužije v subdoméně, ale místo toho je explicitně uvedeno během vytváření pomocného mechanismu.
   
    ![Snímek obrazovky, který ukazuje, jak vytvořit App Service Environment (pomocného mechanismu řízení).][1]
2. Vyberte své předplatné. Předplatné, které používáte pro pomocného správce, se použije i pro všechny aplikace, které v tomto pomocném panelu vytvoříte. Do virtuální sítě, která je v jiném předplatném, nemůžete umístit svůj pomocného správce.
3. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků použitá pro váš správce přidaných mechanismů musí být stejná jako ta, která se používá ve vaší virtuální síti. Pokud vyberete existující virtuální síť, bude se výběr skupiny prostředků pro váš ovládací objekt pro pořízení aktualizovat tak, aby odrážel virtuální síť.
   
    ![Snímek obrazovky, který ukazuje, jak vybrat nebo upravit novou skupinu prostředků.][2]
4. Proveďte Virtual Network a výběr umístění. Můžete si vytvořit novou virtuální síť nebo vybrat již existující virtuální síť. Pokud vyberete novou virtuální síť, můžete zadat její název a umístění. Nová virtuální síť bude mít rozsah adres 192.168.250.0/23 a podsíť s názvem **Default** , která je definovaná jako 192.168.250.0/24. Můžete také jednoduše vybrat stávající virtuální síť typu Classic nebo Správce prostředků. Výběr typu VIP určuje, jestli k vašemu přimocnému objektu se dá získat přímý pøístup z Internetu (externí) nebo jestli používá interní Load Balancer (interního nástroje). Další informace o nich najdete v tématu [použití interní Load Balancer s App Service Environment][ILBASE]. Pokud vyberete externí typ VIP, můžete vybrat, kolik externích IP adres se má systém vytvořit, pro účely IP SSL. Vyberete-li možnost interní, je třeba zadat subdoménu, kterou bude používat váš správce přidaných mechanismů. Služby ASE je možné nasadit do virtuálních sítí, které používají *buď* rozsahy veřejných adres, *nebo* RFC1918 adresní prostory (tj. soukromé adresy). Aby bylo možné používat virtuální síť s rozsahem veřejných adres, bude nutné vytvořit virtuální síť předem. Když vyberete existující virtuální síť, budete muset během vytváření pomocného mechanismu vytvořit novou podsíť. **Na portálu nemůžete použít předem vytvořenou podsíť. Pokud vytváříte pomocného správce prostředků pomocí šablony Resource Manageru, můžete vytvořit pomocného programu s již existující podsítí.** Pokud chcete vytvořit pomocného objektu ze šablony, použijte zde tyto informace, vytvořte [App Service Environment ze šablony][ILBAseTemplate] a tady a vytvořte [App Service Environment interního nástroje ze šablony][ASEfromTemplate].

### <a name="details"></a>Podrobnosti
Přístupový objekt pro vytváření se vytvoří se dvěma front-endy a 2 pracovními procesy. Front-endy slouží jako koncové body HTTP/HTTPS a odesílají přenosy do pracovních procesů, které jsou hostiteli vašich aplikací. Po vytvoření pomocného mechanismu můžete upravit množství a můžete dokonce nastavit pravidla automatického škálování na těchto fondech zdrojů. Další informace o ručním škálování, správě a monitorování App Service Environment najdete tady: [jak nakonfigurovat App Service Environment][ASEConfig] 

V podsíti, kterou používá pomocný modul pro pořízení, může existovat jenom jeden přihlášený. Podsíť se nedá použít pro žádnou jinou než pomocného mechanismu.

### <a name="after-app-service-environment-v1-creation"></a>Po vytvoření App Service Environment v1
Po vytvoření pomocného mechanismu můžete upravit:

* Množství front-endy (minimálně: 2)
* Množství pracovních procesů (minimálně: 2)
* Množství IP adres, které jsou k dispozici pro IP SSL
* Velikosti výpočetních prostředků používané předními konci nebo pracovními procesy (minimální velikost front-endu je P2)

V této části najdete další podrobnosti o ručním škálování, správě a monitorování App Servicech prostředí: [jak nakonfigurovat App Service Environment][ASEConfig] 

Informace o automatickém škálování tady je příručka: [jak nakonfigurovat automatické škálování pro App Service Environment][ASEAutoscale]

Existují další závislosti, které nejsou k dispozici pro vlastní nastavení, jako je databáze a úložiště. Tyto služby jsou zpracovávány v Azure a dodávány se systémem. Systémové úložiště podporuje pro celou App Service Environment až 500 GB a databáze se podle potřeby škáluje podle Azure.

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s App Service Environment V1, přečtěte si téma [Úvod do App Service Environment v1][WhatisASE] .

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
