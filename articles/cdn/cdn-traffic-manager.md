---
title: Převzetí služeb při selhání ve více koncových bodech Azure CDN pomocí Traffic Manageru
description: Přečtěte si, jak nastavit Azure Traffic Manager pomocí koncových bodů Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 8d44e53520481e4ada5c2f16f0c56a4a6a724ec1
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892474"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Nastavení převzetí služeb při selhání ve více koncových bodech Azure CDN pomocí Azure Traffic Manageru

Při konfiguraci sítě pro doručování obsahu Azure (CDN) můžete vybrat optimálního poskytovatele a cenovou úroveň pro vaše potřeby. Azure CDN, s jeho globálně distribuované infrastruktury, ve výchozím nastavení vytváří místní a geografické redundance a globální vyrovnávání zatížení ke zlepšení dostupnosti služeb a výkonu. Pokud umístění není k dispozici pro poskytování obsahu, požadavky jsou automaticky směrovány do jiného umístění a optimální pop (na základě takových faktorů, jako je umístění požadavku a zatížení serveru) se používá k zobrazení každého požadavku klienta. 
 
Pokud máte více profilů CDN, můžete dále zlepšit dostupnost a výkon s Azure Traffic Manager. Azure Traffic Manager s Azure CDN můžete použít k vyrovnávání zatížení mezi více koncovými body CDN pro převzetí služeb při selhání, geografické vyrovnávání zatížení a další scénáře. V typickém scénáři převzetí služeb při selhání jsou všechny požadavky klientů nejprve přesměrovány na primární profil CDN; Pokud profil není k dispozici, požadavky jsou pak předány sekundární profil CDN, dokud primární profil CDN je zpět do režimu online. Pomocí Azure Traffic Manager tímto způsobem zajistíte, že vaše webová aplikace bude vždy k dispozici. 

Tento článek obsahuje pokyny a příklad nastavení převzetí služeb při selhání pomocí **Azure CDN Standard od Verizonu** a Azure CDN Standard z profilů **Akamai.**

## <a name="set-up-azure-cdn"></a>Nastavení Azure CDN 
Vytvořte dva nebo více profilů Azure CDN a koncových bodů s různými poskytovateli.

1. Vytvořte **standard Azure CDN od verizonu** a Azure CDN Standard z profilu **Akamai** podle kroků v [části Vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN více profilů](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. V každém z nových profilů vytvořte alespoň jeden koncový bod podle kroků v [části Vytvoření nového koncového bodu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Nastavení Azure Traffic Manageru
Vytvořte profil Azure Traffic Manager u všech koncových bodů CDN. 

1. Vytvořte profil Azure Traffic Manager u kroků v části [Vytvoření profilu Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    V **metodě Směrování**vyberte **prioritu**.

2. Přidání koncových bodů CDN do profilu Traffic Manageru podle kroků v [části Přidat koncové body Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    V **části Typ**vyberte Externí koncové **body**. Do **su-priority**zadejte číslo.

    Můžete například vytvořit *cdndemo101akamai.azureedge.net* s prioritou *1* a *cdndemo101verizon.azureedge.net* s prioritou *2*.

   ![Koncové body správce provozu CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Nastavení vlastní domény v Azure CDN a Azure Traffic Manageru
Po nastavení profilů CDN a Traffic Manager přidejte mapování DNS a zaregistrujte vlastní doménu do koncových bodů CDN následujícím postupem. V tomto příkladu je vlastní název domény *cdndemo101.dustydogpetcare.online*.

1. Přejděte na web pro poskytovatele domény vaší vlastní domény, jako je GoDaddy, a vytvořte dvě položky DNS CNAME. 

    a. Pro první položku CNAME namapujte vlastní doménu pomocí subdomény cdnverify na koncový bod CDN. Tato položka je povinným krokem k registraci vlastní domény do koncového bodu CDN, který jste přidali do Traffic Manageru v kroku 2.

      Příklad: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Pro druhou položku CNAME namapujte vlastní doménu bez subdomény cdnverify na koncový bod CDN. Tato položka mapuje vlastní doménu na Traffic Manager. 

      Příklad: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Pokud je vaše doména aktuálně aktivní a nelze ji přerušit, proveďte tento krok jako poslední. Před aktualizací vlastní domény DNS do Traffic Manageru ověřte, zda jsou koncové body CDN a domény správce provozu aktivní.
    >


2.  Z profilu Azure CDN vyberte první koncový bod CDN (Akamai). Vyberte **Přidat vlastní doménu** a vstupní *cdndemo101.dustydogpetcare.online*. Ověřte, zda je zaškrtnutí pro ověření vlastní domény zelené. 

    Azure CDN používá *subdoménu cdnverify* k ověření mapování DNS k dokončení tohoto procesu registrace. Další informace naleznete [v tématu Vytvoření záznamu CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Tento krok umožňuje Azure CDN rozpoznat vlastní doménu tak, aby mohla reagovat na jeho požadavky.
    
    > [!NOTE]
    > Chcete-li povolit TLS na Azure CDN z profilů **Akamai,** musíte přímo cname vlastní domény do koncového bodu. cdnverify pro povolení TLS ještě není podporován. 
    >

3.  Vraťte se na web pro poskytovatele domény vlastní domény a aktualizujte první mapování DNS, které jste vytvořili v aplikaci, aby byla vlastní doména mapována na druhý koncový bod CDN.
                             
    Příklad: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Z profilu Azure CDN vyberte druhý koncový bod CDN (Verizon) a opakujte krok 2. Vyberte **Přidat vlastní doménu**a zadejte *cdndemo101.dustydogpetcare.online*.
 
Po dokončení těchto kroků se vaše služba s více než 10 000 s failrem nastaví pomocí Azure Traffic Manageru. Testovací adresy URL budete mít přístup z vlastní domény. Chcete-li otestovat funkci, zakažte primární koncový bod CDN a ověřte, zda je požadavek správně přesunut do sekundárního koncového bodu CDN. 

## <a name="next-steps"></a>Další kroky
Můžete také nastavit další metody směrování, například geografické, k vyrovnání zatížení mezi různými koncovými body CDN. Další informace naleznete [v tématu Konfigurace metody směrování geografického provozu pomocí Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



