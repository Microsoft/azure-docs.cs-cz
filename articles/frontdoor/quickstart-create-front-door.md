---
title: 'Úvodní příručka: Vytvoření profilu předních dveří pro vysokou dostupnost aplikací'
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
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521456"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rychlý start: Vytvoření Front Dooru pro vysoce dostupnou globální webovou aplikaci

Tento rychlý start popisuje, jak vytvořit profil služby Front Door zajišťující vysokou dostupnost a vysoký výkon globální webové aplikace. 

Scénář popsaný v tomto rychlém startu zahrnuje dvě instance webové aplikace spuštěné v různých oblastech Azure. Na základě rovnoměrně [vyvážených back-endů a back-endů se stejnými prioritami](front-door-routing-methods.md) vytvoříme konfiguraci Front Dooru, která umožní směrovat uživatelský provoz na nejbližší sadu webových back-endů, na kterých běží vaše aplikace. Front Door průběžně monitoruje webovou aplikaci a zajišťuje automatické převzetí služeb při selhání dalším dostupným back-endem v případě, že je nejbližší web nedostupný.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start vyžaduje dvě instance webové aplikace nasazené a běžící v různých oblastech Azure (*USA – východ* a *Západní Evropa*). Obě instance webové aplikace běží v režimu Aktivní/Aktivní. To znamená, že každá z nich může kdykoli převzít provoz – na rozdíl od konfigurace Aktivní/Pohotovostní, při které je jedna instance určená pro případ převzetí služeb při selhání.

1. V levém horním rohu obrazovky vyberte Vytvořit**webovou** > **webovou aplikaci** **pro prostředky** > .
2. V části **Webová aplikace** zadejte nebo vyberte následující informace a v případě, že není uvedeno jinak, použijte výchozí nastavení:

     | Nastavení         | Hodnota     |
     | ---              | ---  |
     | Skupina prostředků          | Vyberte **Nová** a zadejte *myResourceGroupFD1*. |
     | Name (Název)           | Zadejte jedinečný název vaší webové aplikace.  |
     | Zásobník modulu runtime          | Výběr runtime zásobníku pro vaši aplikaci |
     |      Region (Oblast)  |   USA – západ        |
     | Plán služby App Service / umístění         | Vyberte **nový**.  Jako plán služby App Service zadejte *myAppServicePlanEastUS* a pak vyberte **OK**.| 
     |Sku a velikost  | Vyberte **změnit velikost** Vybrat **standardní S1 100 celkem ACU, 1,75 GB paměti** |
     
3. Vyberte **zkontrolovat + vytvořit**.
4. Projděte si souhrnné informace pro webovou aplikaci. Vyberte **Vytvořit**.
5. Po přibližně 5 minutách se při úspěšném nasazení webové aplikace vytvoří výchozí web.
6. Zopakujte kroky 1 až 3 a vytvořte druhý web v jiné oblasti Azure s použitím následujícího nastavení:

     | Nastavení         | Hodnota     |
     | ---              | ---  |
     | Skupina prostředků          | Vyberte **Nová** a zadejte *myResourceGroupFD2*. |
     | Name (Název)           | Zadejte jedinečný název vaší webové aplikace.  |
     | Zásobník modulu runtime          | Výběr runtime zásobníku pro vaši aplikaci |
     |      Region (Oblast)  |   Západní Evropa      |
     | Plán služby App Service / umístění         | Vyberte **nový**.  Jako plán služby App Service zadejte *myAppServicePlanWestEurope* a pak vyberte **OK**.|   
     |Sku a velikost  | Vyberte **změnit velikost** Vybrat **standardní S1 100 celkem ACU, 1,75 GB paměti** |
    
## <a name="create-a-front-door-for-your-application"></a>Vytvoření Front Dooru pro vaši aplikaci
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Přidání front-endového hostitele pro Front Door
Vytvořte konfiguraci Front Dooru, která bude směrovat uživatelský provoz na základě nejnižší latence mezi dvěma back-endy.

1. V levém horním rohu obrazovky vyberte **Vytvořit zdroj** > **Síťové** > přední**dveře**.
2. V **části Vytvořit přední dveře**zadejte nebo vyberte následující informace a zadejte výchozí nastavení, kde není zadáno žádné:

     | Nastavení         | Hodnota     |
     | ---              | ---  |
     |Předplatné  | Vyberte předplatné, ve kterém chcete frontdoor vytvořit.|
     | Skupina prostředků          | Vyberte **nový**a zadejte *příkaz myResourceGroupFD0.* |
     | Umístění skupiny prostředků  |   USA – střed        |
     
     > [!NOTE]
     > Není nutné vytvořit novou skupinu prostředků pro nasazení front door do.  Pokud můžete také vybrat existující skupinu prostředků.
     
3. Klepněte na tlačítko **Další: Konfigurace**.
4. Klikněte na ikonu +na kartě Frontend/domains.  Do pole `<Your Initials>frontend`Název **hostitele** zadejte . Tento název hostitele musí být globálně jedinečný, Front Door se postará o ověření.
5. Klikněte na **Přidat**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Přidání back-endu aplikace a back-endových fondů

Dále je třeba nakonfigurovat frontendy/domény a back-endy aplikací v back-endovém fondu pro frontové dveře, abyste věděli, kde se vaše aplikace nachází. 

1. Kliknutím na ikonu +na kartě fondů back-endu **Name** přidáte back-endový fond `myBackendPool`pro Název pro back-endový fond, zadejte .
2. Dále klikněte na **Přidat back-end** a přidejte své weby vytvořené dříve.
3. Vyberte **Typ hostitele Back-end** jako "Služba aplikace", vyberte předplatné, ve kterém jste web vytvořili, a pak zvolte první web z rozevíracího souboru **Název hostitele back-endu.**
4. Zbývající pole ponechejte v tuto chvíli tak, jak jsou, a klikněte na **Přidat**.
5. Vyberte **Typ hostitele Back-end** jako "Služba aplikace", vyberte předplatné, ve kterém jste web vytvořili, a pak zvolte **druhý** web z rozevíracího souboru Název **hostitele back-endu.**
6. Zbývající pole ponechejte v tuto chvíli tak, jak jsou, a klikněte na **Přidat**.
7. Volitelně můžete aktualizovat sondy stavu a vyrovnávání zatížení nastavení pro back-endového fondu, ale výchozí hodnoty by měly také fungovat. V obou případech klepněte na tlačítko **Přidat**.


### <a name="c-add-a-routing-rule"></a>C. Přidání pravidla směrování
1. Nakonec klepnutím na ikonu +na kartě směrovacích pravidel nakonfigurujete pravidlo směrování. Je to potřeba k namapování vašeho front-endového hostitele na back-endový fond, což v zásadě znamená nakonfigurovat, že když přijde žádost na `myappfrontend.azurefd.net`, má se přesměrovat na back-endový fond `myBackendPool`. 
2. Do **pole Název** zadejte 'LocationRule'.
3. Kliknutím na **Přidat** přidáte pravidlo směrování do Front Dooru. 
4. Klikněte na **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení pro vytvoření předních dveří. Klikněte na **Vytvořit.**

>[!WARNING]
> **Musíte** zajistit, aby všichni front-endoví hostitelé ve vašem Front Dooru měli přidružené pravidlo směrování s výchozí cestou (/\*). To znamená, že mezi všemi pravidly směrování musí pro každého front-endového hostitele existovat alespoň jedno pravidlo směrování s definovanou výchozí cestou (/\*). Pokud se vám to nepodaří zajistit, provoz koncových uživatelů se možná nebude směrovat správně.

## <a name="view-front-door-in-action"></a>Front Door v akci
Po vytvoření Front Dooru bude pár minut trvat, než se konfigurace všude globálně nasadí. Až bude nasazení dokončené, přistupte k front-endovému hostiteli, kterého jste vytvořili – otevřete webový prohlížeč a zadejte adresu URL `myappfrontend.azurefd.net`. Vaše žádost se automaticky přesměruje na nejbližší back-end z back-endů určených v back-endovém fondu. 

### <a name="view-front-door-handle-application-failover"></a>Front Door při převzetí služeb aplikací při selhání
Pokud chcete otestovat okamžité globální převzetí služeb Front Dooru při selhání v akci, můžete přejít na jeden z webů, který jste vytvořili, a zastavit ho. Na základě nastavení Sondy stavu definovaného pro back-endový fond se provoz okamžitě přesměruje na nasazení druhého webu. Chování můžete testovat také tak, že zakážete back-end v konfiguraci back-endového fondu pro váš Front Door. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již není potřeba, odstraňte skupiny prostředků **myResourceGroupFD1**, **myResourceGroupFD2**a **myResourceGroupFD0:**

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili Front Door umožňující směrovat uživatelský provoz pro webové aplikace, které vyžadují vysokou dostupnost a maximální výkon. Další informace o směrování provozu najdete v tématu o [metodách směrování](front-door-routing-methods.md) používaných službou Front Door.
