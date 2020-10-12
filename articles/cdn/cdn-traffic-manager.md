---
title: Převzetí služeb při selhání napříč několika koncovými body Azure CDN pomocí Traffic Manager
description: Přečtěte si, jak nastavit převzetí služeb při selhání napříč několika koncovými body Azure Content Delivery Network pomocí Azure Traffic Manager.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b55e418393d6d446ae0d3557f2d1f4cf98d89293
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192507"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Nastavení převzetí služeb při selhání napříč několika koncovými body Azure CDN pomocí Azure Traffic Manager

Při konfiguraci služby Azure Content Delivery Network (CDN) můžete vybrat optimálního poskytovatele a cenovou úroveň podle svých potřeb. Azure CDN s jeho globální distribuovanou infrastrukturou ve výchozím nastavení vytvoří místní a geografickou redundanci a globální vyrovnávání zatížení, aby se zlepšila dostupnost a výkon služby. Pokud není k dispozici umístění pro poskytování obsahu, požadavky jsou automaticky směrovány do jiného umístění a optimální POP (na základě takových faktorů, jako je umístění požadavku a zatížení serveru), slouží k obsluze jednotlivých požadavků klienta. 
 
Pokud máte více profilů CDN, můžete dál vylepšit dostupnost a výkon pomocí Azure Traffic Manager. Azure Traffic Manager s Azure CDN můžete použít k vyrovnávání zatížení mezi několika koncovými body CDN pro převzetí služeb při selhání, geografického vyrovnávání zatížení a dalších scénářů. V typickém scénáři převzetí služeb při selhání jsou všechny požadavky klientů nejprve směrovány do primárního profilu CDN; Pokud profil není dostupný, požadavky se pak předají do sekundárního profilu CDN, dokud se váš primární profil CDN nevrátí do online režimu. Používání Azure Traffic Manager tímto způsobem zajišťuje, že vaše webová aplikace bude vždycky k dispozici. 

V tomto článku najdete pokyny a příklad nastavení převzetí služeb při selhání pomocí **Azure CDN Standard z Verizon** a **Azure CDN Standard z profilů Akamai** .

## <a name="set-up-azure-cdn"></a>Nastavit Azure CDN 
Vytvořte dva nebo více Azure CDN profilů a koncových bodů s různými poskytovateli.

1. Pomocí postupu v části [Vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile)vytvořte z profilu Akamai standard **Azure CDN Verizon** a **Azure CDN Standard** .
 
   ![CDN – Vícenásobné profily](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. V každém z nových profilů vytvořte alespoň jeden koncový bod podle kroků v části [Vytvoření nového koncového bodu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Nastavení Traffic Manager Azure
Vytvořte profil Azure Traffic Manager a nastavte vyrovnávání zatížení napříč koncovými body CDN. 

1. Pomocí postupu v části [Vytvoření profilu Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)vytvořte profil Azure Traffic Manager. 

    V případě **metody směrování**vyberte **Priorita**.

2. Pomocí postupu v části [přidání Traffic Manager koncových bodů](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) přidejte koncové body CDN do profilu Traffic Manager.

    Jako **typ**vyberte **externí koncové body**. Jako **Priorita**zadejte číslo.

    Můžete například vytvořit *cdndemo101akamai.azureedge.NET* s prioritou *1* a *cdndemo101verizon.azureedge.NET* s prioritou *2*.

   ![Koncové body Traffic Manageru CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Nastavení vlastní domény v Azure CDN a Azure Traffic Manager
Po nastavení profilů pro CDN a Traffic Manager postupujte podle těchto kroků a přidejte mapování DNS a zaregistrujte vlastní doménu do koncových bodů CDN. V tomto příkladu je název vlastní domény *cdndemo101. dustydogpetcare. online*.

1. Přejít na web pro poskytovatele domény vlastní domény, například GoDaddy, a vytvořit dvě položky CNAME DNS. 

    a. Pro první záznam CNAME namapujte vlastní doménu s poddoménou cdnverify na koncový bod CDN. Tato položka je povinný krok k registraci vlastní domény do koncového bodu CDN, který jste přidali do Traffic Manager v kroku 2.

      Například: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Pro druhý záznam CNAME namapujte vlastní doménu bez subdomény cdnverify na koncový bod CDN. Tato položka namapuje vlastní doménu na Traffic Manager. 

      Například: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Pokud je vaše doména momentálně v provozu a nedá se přerušit, udělejte tento krok jako poslední. Před aktualizací vlastní domény DNS na Traffic Manager ověřte, jestli jsou koncové body CDN a domény Traffic Manageru aktivní.
    >


2.  V profilu Azure CDN vyberte první koncový bod CDN (Akamai). Vyberte **Přidat vlastní doménu** a vstup *cdndemo101. dustydogpetcare. online*. Ověřte, že se značka zaškrtnutí pro ověření vlastní domény zeleně. 

    Azure CDN používá subdoménu *cdnverify* k ověření mapování DNS k dokončení tohoto procesu registrace. Další informace najdete v tématu [Vytvoření záznamu DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Tento krok umožňuje Azure CDN rozpoznat vlastní doménu, aby mohla reagovat na své žádosti.
    
    > [!NOTE]
    > Pokud chcete povolit TLS na **Azure CDN z profilů Akamai** , musíte přímo CNAME vlastní doménu do svého koncového bodu. cdnverify pro povolení protokolu TLS ještě není podporováno. 
    >

3.  Vraťte se na web pro poskytovatele domény vlastní domény a aktualizujte první mapování DNS, které jste vytvořili v nástroji, aby se vlastní doména namapovala na druhý koncový bod CDN.
                             
    Například: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. V profilu Azure CDN vyberte druhý koncový bod CDN (Verizon) a opakujte krok 2. Vyberte **Přidat vlastní doménu**a zadejte *cdndemo101. dustydogpetcare. online*.
 
Po dokončení tohoto postupu se služba multi-CDN s možnostmi převzetí služeb při selhání nastaví s využitím Azure Traffic Manager. Budete mít přístup k testovacím adresám URL z vaší vlastní domény. Chcete-li otestovat funkci, zakažte primární koncový bod CDN a ověřte, zda je požadavek správně přesunut do sekundárního koncového bodu CDN. 

## <a name="next-steps"></a>Další kroky
Pro vyrovnávání zatížení mezi různými koncovými body CDN můžete také nastavit jiné metody směrování, například geografické. Další informace najdete v tématu [Konfigurace metody směrování geografického provozu pomocí Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



