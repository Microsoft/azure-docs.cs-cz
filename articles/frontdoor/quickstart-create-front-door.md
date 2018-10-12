---
title: Rychlý start Azure – Vytvoření profilu služby Front Door pomocí portálu Azure Portal k zajištění vysoké dostupnosti aplikací
description: Tento článek Rychlý start popisuje, jak vytvořit Front Door pro vaši vysoce dostupnou a výkonnou globální webovou aplikaci.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 39e7626e6b4c545649e39ff2120d1f1fd105d764
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994662"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rychlý start: Vytvoření Front Dooru pro vysoce dostupnou globální webovou aplikaci

Tento rychlý start popisuje, jak vytvořit profil služby Front Door zajišťující vysokou dostupnost a vysoký výkon globální webové aplikace. 

Scénář popsaný v tomto rychlém startu zahrnuje dvě instance webové aplikace spuštěné v různých oblastech Azure. Na základě rovnoměrně [vyvážených back-endů a back-endů se stejnými prioritami](front-door-routing-methods.md) vytvoříme konfiguraci Front Dooru, která umožní směrovat uživatelský provoz na nejbližší sadu webových back-endů, na kterých běží vaše aplikace. Front Door průběžně monitoruje webovou aplikaci a zajišťuje automatické převzetí služeb při selhání dalším dostupným back-endem v případě, že je nejbližší web nedostupný.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start vyžaduje dvě instance webové aplikace nasazené a běžící v různých oblastech Azure (*USA – východ* a *Západní Evropa*). Obě instance webové aplikace běží v režimu Aktivní/Aktivní. To znamená, že každá z nich může kdykoli převzít provoz – na rozdíl od konfigurace Aktivní/Pohotovostní, při které je jedna instance určená pro případ převzetí služeb při selhání.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Web** > **Webová aplikace** > **Vytvořit**.
2. V části **Webová aplikace** zadejte nebo vyberte následující informace a v případě, že není uvedeno jinak, použijte výchozí nastavení:

     | Nastavení         | Hodnota     |
     | ---              | ---  |
     | Název           | Zadejte jedinečný název vaší webové aplikace.  |
     | Skupina prostředků          | Vyberte **Nová** a zadejte *myResourceGroupFD1*. |
     | Plán služby App Service / umístění         | Vyberte **Nový**.  Jako plán služby App Service zadejte *myAppServicePlanEastUS* a pak vyberte **OK**. 
     |      Umístění  |   USA – východ        |
    |||

3. Vyberte **Vytvořit**.
4. Po úspěšném nasazení webové aplikace se vytvoří výchozí web.
5. Zopakujte kroky 1 až 3 a vytvořte druhý web v jiné oblasti Azure s použitím následujícího nastavení:

     | Nastavení         | Hodnota     |
     | ---              | ---  |
     | Název           | Zadejte jedinečný název vaší webové aplikace.  |
     | Skupina prostředků          | Vyberte **Nová** a zadejte *myResourceGroupFD2*. |
     | Plán služby App Service / umístění         | Vyberte **Nový**.  Jako plán služby App Service zadejte *myAppServicePlanWestEurope* a pak vyberte **OK**. 
     |      Umístění  |   Západní Evropa      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Vytvoření Front Dooru pro vaši aplikaci
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Přidání front-endového hostitele pro Front Door
Vytvořte konfiguraci Front Dooru, která bude směrovat uživatelský provoz na základě nejnižší latence mezi dvěma back-endy.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Front Door** > **Vytvořit**.
2. V rozhraní **Vytvořit Front Door** začnete tím, že přidáte základní informace a uvedete předplatné, pro které chcete Front Door nakonfigurovat. Podobně jako u jakéhokoli jiného nově vytvářeného prostředku Azure musíte také zadat skupinu prostředků a oblast skupiny prostředků. A nakonec musíte zadat název pro váš Front Door.
3. Po vyplnění základních informací je potřeba pro konfiguraci definovat **front-endového hostitele**. Výsledkem by měl být platný název domény, například `myappfrontend.azurefd.net`. Tento název hostitele musí být globálně jedinečný – Front Door to ověří. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Přidání back-endu aplikace a back-endových fondů

Dále je potřeba nakonfigurovat back-end(y) aplikace v back-endovém fondu, aby služba Front Door věděla, kde se vaše aplikace nachází. 

1. Kliknutím na ikonu „+“ přidejte back-endový fond a zadejte jeho **název**, třeba `myBackendPool`.
2. Pak klikněte na Přidat back-endy, abyste přidali dříve vytvořené weby.
3. Jako **Typ cílového hostitele** vyberte App Service, vyberte předplatné, ve kterém jste web vytvořili, a pak v poli **Název cílového hostitele** zvolte první web, to znamená *myAppServicePlanEastUS.azurewebsites.net*.
4. Zbývající pole ponechejte v tuto chvíli tak, jak jsou, a klikněte na **Přidat**.
5. Opakováním kroků 2 až 4 přidejte druhý web, to znamená *myAppServicePlanWestEurope.azurewebsites.net*.
6. Pro back-endový fond můžete volitelně změnit nastavení Sondy stavu a Vyrovnávání zatížení, ale výchozí hodnoty by také měly fungovat. Klikněte na **Přidat**.


### <a name="c-add-a-routing-rule"></a>C. Přidání pravidla směrování
Nakonec klikněte na ikonu „+“ pro Pravidla směrování a nakonfigurujte pravidlo směrování. Je to potřeba k namapování vašeho front-endového hostitele na back-endový fond, což v zásadě znamená nakonfigurovat, že když přijde žádost na `myappfrontend.azurefd.net`, má se přesměrovat na back-endový fond `myBackendPool`. Kliknutím na **Přidat** přidáte pravidlo směrování do Front Dooru. Teď už byste měli být připraveni Front Door vytvořit, proto klikněte na **Zkontrolovat a vytvořit**.

>[!WARNING]
> **Musíte** zajistit, aby všichni front-endoví hostitelé ve vašem Front Dooru měli přidružené pravidlo směrování s výchozí cestou (/\*). To znamená, že mezi všemi pravidly směrování musí pro každého front-endového hostitele existovat alespoň jedno pravidlo směrování s definovanou výchozí cestou (/\*). Pokud by tomu tak nebylo, nemusel by být provoz koncových uživatelů správně směrovaný.

## <a name="view-front-door-in-action"></a>Front Door v akci
Po vytvoření Front Dooru bude pár minut trvat, než se konfigurace všude globálně nasadí. Až bude nasazení dokončené, přistupte k front-endovému hostiteli, kterého jste vytvořili – otevřete webový prohlížeč a zadejte adresu URL `myappfrontend.azurefd.net`. Vaše žádost se automaticky přesměruje na nejbližší back-end z back-endů určených v back-endovém fondu. 

### <a name="view-front-door-handle-application-failover"></a>Front Door při převzetí služeb aplikací při selhání
Pokud chcete otestovat okamžité globální převzetí služeb Front Dooru při selhání v akci, můžete přejít na jeden z webů, který jste vytvořili, a zastavit ho. Na základě nastavení Sondy stavu definovaného pro back-endový fond se provoz okamžitě přesměruje na nasazení druhého webu. Chování můžete testovat také tak, že zakážete back-end v konfiguraci back-endového fondu pro váš Front Door. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte skupiny prostředků, webové aplikace a všechny související prostředky.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili Front Door umožňující směrovat uživatelský provoz pro webové aplikace, které vyžadují vysokou dostupnost a maximální výkon. Další informace o směrování provozu najdete v tématu o [metodách směrování](front-door-routing-methods.md) používaných službou Front Door.