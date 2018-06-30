---
title: Nastavení převzetí služeb při selhání mezi několik koncových bodů Azure CDN s Azure Traffic Manageru | Microsoft Docs
description: Další informace o tom, jak nastavit až Azure Traffic Manager s koncovými body Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: b52cad1f32cc3d16cf70bb81640dcb1d9f8614bf
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132871"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Nastavení převzetí služeb při selhání mezi několik koncových bodů Azure CDN s Azure Traffic Manager

Při konfiguraci Azure Content Delivery Network (CDN), můžete vybrat optimální zprostředkovatele a cenovou úroveň pro vaše potřeby. Azure CDN, jeho globálně distribuované infrastruktury, ve výchozím nastavení vytvoří místní a geografické redundance a globální zlepšit výkon a dostupnost služeb pro vyrovnávání zatížení. Pokud umístění není k dispozici pro práci s obsahem, požadavky jsou automaticky směrovány do jiného umístění a optimální POP (založené na různých faktorech, například žádosti o umístění a server zatížení) se používá k obsluze každý požadavek klienta. 
 
Pokud máte víc profilů CDN, lze dále zvýšit dostupnost a výkon Azure Traffic Manager. Azure Traffic Manager s Azure CDN slouží k načtení vyrovnání mezi několik koncových bodů CDN pro převzetí služeb při selhání, geograficky zatížení vyrovnávání a dalších scénářů. V případě typické převzetí služeb při selhání jsou všechny požadavky nejprve směrované primární profil CDN; Pokud je profil není k dispozici, požadavky jsou předána do sekundární profil CDN dokud primární profilu CDN je zpět do režimu online. Pomocí Azure Traffic Manager tímto způsobem zajistíte, že webová aplikace je vždy k dispozici. 

Tento článek obsahuje pokyny a příklady jak nastavit převzetí služeb při selhání s **Azure CDN Standard od společnosti Verizon** a **Azure CDN Standard od společnosti Akamai** profily.

## <a name="set-up-azure-cdn"></a>Nastavení Azure CDN 
Vytvoření dvou nebo více profilů Azure CDN a koncové body s různé zprostředkovatele.

1. Vytvořit **Azure CDN Standard od společnosti Verizon** a **Azure CDN Standard od společnosti Akamai** profil podle kroků v [vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN víc profilů](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. V každé nové profily, vytvořte aspoň jeden koncový bod pomocí následujících kroků v [vytvořte nový koncový bod CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Nastavte si Azure Traffic Manager
Vytvoření profilu Azure Traffic Manageru a nastavit vyrovnávání zatížení napříč koncové body CDN. 

1. Vytvoření profilu Azure Traffic Manager podle pokynů v [vytvořit profil Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#create-a-traffic-manager-profile-1). 

    Pro **metoda směrování**, vyberte **s prioritou**.

2. Přidat koncové body CDN ve vašem profilu Traffic Manager podle pokynů v [koncové body přidat Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Pro **typ**, vyberte **externí koncové body**. Pro **s prioritou**, zadejte číslo.

    Můžete například vytvořit *cdndemo101akamai.azureedge.net* s prioritou *1* a *cdndemo101verizon.azureedge.net* s prioritou *2*.

   ![Koncové body CDN provoz manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Nastavit vlastní doménu Azure CDN a Azure Traffic Manager
Po nastavení o profilech sítě CDN a Traffic Manager, použijte následující postup přidání mapování DNS a zaregistrovat vlastní domény do koncových bodů CDN. V tomto příkladu je název vlastní domény *cdndemo101.dustydogpetcare.online*.

1. Přejděte na web pro zprostředkovatele domény vaši vlastní doménu, jako je například GoDaddy a vytvořit dva záznamy DNS CNAME. 

    a. Pro první záznam CNAME mapovat vaši vlastní doménu, s subdoméně cdnverify koncový bod CDN. Tato položka je povinný krok zaregistrovat vlastní doménu pro koncový bod CDN, které jste přidali do Traffic Manageru v kroku 2.

      Příklad: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Pro druhý záznam CNAME mapovat vaši vlastní doménu, bez subdoméně cdnverify koncový bod CDN. Tato položka mapuje vlastní domény do Traffic Manageru. 

      Příklad: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Pokud vaše doména je aktuálně za provozu a nelze ji přerušit, proveďte tento krok poslední. Ověřte, zda koncové body CDN a provoz správce domény jsou za provozu než aktualizujete vaše vlastní doména DNS do Traffic Manageru.
    >


2.  Váš profil Azure CDN vyberte první koncový bod CDN (Akamai). Vyberte **přidat vlastní doménu** a vstupní *cdndemo101akamai.azureedge.net*. Ověřte, zda je zelené zaškrtnutí ověření vlastní domény. 

    Používá Azure CDN *cdnverify* subdomény ověřit mapování DNS k dokončení tohoto procesu registrace. Další informace najdete v tématu [vytvořit záznam CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Tento krok umožňuje Azure CDN rozpoznat vlastní doménu, aby mohli odpovídat na jeho požadavky.

3.  Vraťte se na webu pro zprostředkovatele domény vlastní domény a aktualizovat první mapování DNS, kterou jste vytvořili v tak, aby vlastní doména je namapována na druhý koncový bod CDN.
                             
    Příklad: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Váš profil Azure CDN vyberte druhý koncový bod CDN (Verizon) a opakujte krok 2. Vyberte **přidat vlastní doménu**a vstupní *cdndemo101akamai.azureedge.net*.
 
Po dokončení těchto kroků služby více CDN prostřednictvím funkce převzetí služeb při selhání je nastavený Azure Traffic Manager. Budete mít přístup k test adresy URL z vlastní domény. Chcete-li otestovat funkci, zakažte primární koncový bod CDN a ověřte, že požadavek je správně přesouvá na sekundární koncový bod CDN. 

## <a name="next-steps"></a>Další postup
Můžete také nastavit další metody směrování, jako například geografické k vyrovnávání zatížení mezi různými koncovými body CDN. Další informace najdete v tématu [konfigurace metody směrování provozu geografické pomocí Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



