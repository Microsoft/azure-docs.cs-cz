---
title: Nastavit převzetí služeb při selhání mezi několika koncových bodů Azure CDN pomocí Azure Traffic Manageru | Dokumentace Microsoftu
description: Další informace o tom, jak nastavit si Azure Traffic Manageru s koncovými body Azure CDN.
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
ms.openlocfilehash: 88739cb299e453e32c69f5d0d0189da849b345a7
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573871"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Nastavit převzetí služeb při selhání mezi několika koncových bodů Azure CDN pomocí Azure Traffic Manageru

Při konfiguraci Azure Content Delivery Network (CDN), můžete vybrat optimální zprostředkovatele a cenovou úroveň pro vaše potřeby. Azure CDN, jeho globálně distribuovanou infrastrukturou ve výchozím nastavení vytvoří místní a geografické redundanci a globální zlepšit výkon a dostupnost služeb pro vyrovnávání zatížení. Pokud umístění není k dispozici pro poskytování obsahu, požadavky se automaticky směrují do jiného umístění a optimální POP (založené na faktorech, například zátěž tvořenou umístění a server) se používá pro obsluhu každého požadavku klienta. 
 
Pokud máte víc profilů CDN, můžete ještě vylepšit dostupnost a výkon pomocí Azure Traffic Manageru. Azure Traffic Manageru s Azure CDN slouží k načtení rovnováhu mezi několik koncových bodů CDN pro převzetí služeb při selhání geo zatížení vyrovnávání a další scénáře. Ve scénáři typické převzetí služeb při selhání všechny požadavky klientů nejprve přesměrováni na primární profil CDN; Pokud profil, který není k dispozici, žádosti se potom předávají do sekundární profil CDN, dokud nebude primární profil CDN je zpátky do online režimu. Použití Azure Traffic Manager tímto způsobem zajišťuje, že vaše webová aplikace je vždy k dispozici. 

Tento článek obsahuje pokyny a příklad toho, jak nastavit převzetí služeb při selhání s **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profily.

## <a name="set-up-azure-cdn"></a>Nastavení Azure CDN 
Vytvoření dvou nebo více koncových bodů a profilů CDN s jiným poskytovatelům.

1. Vytvoření **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profilu pomocí následujících kroků v [vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN více profilů](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. V každé nové profily, vytvořte aspoň jeden koncový bod pomocí následujících kroků v [vytvořit nový koncový bod CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Nastavte Azure Traffic Manager
Vytvoření profilu Azure Traffic Manageru a nastavit vyrovnávání zatížení napříč koncových bodů CDN. 

1. Vytvoření profilu Azure Traffic Manager pomocí kroků v [vytvořit profil služby Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#create-a-traffic-manager-profile-1). 

    Pro **metodu směrování**vyberte **Priority**.

2. Přidat koncové body CDN ve vašem profilu Traffic Manageru pomocí následujících kroků v [koncové body přidat Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Pro **typ**vyberte **externí koncové body**. Pro **Priority**, zadejte číslo.

    Například vytvořit *cdndemo101akamai.azureedge.net* s prioritou *1* a *cdndemo101verizon.azureedge.net* s prioritou *2*.

   ![Koncové body CDN traffic Manageru](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Nastavení vlastní domény na Azure CDN a Azure Traffic Manageru
Po nastavení profily CDN a Traffic Manager postupujte podle těchto kroků přidejte mapování DNS a zaregistrujte vlastní domény do koncových bodů CDN. V tomto příkladu je název vlastní domény *cdndemo101.dustydogpetcare.online*.

1. Přejděte na web poskytovatele domény vlastní domény, jako je například GoDaddy a vytvořte dvě položky DNS CNAME. 

    a. Pro první záznam CNAME namapujte vlastní doménu, se subdoménou cdnverify na váš koncový bod CDN. Tato položka je povinný krok registrace vlastní domény do koncového bodu CDN, který jste přidali v kroku 2 do Traffic Manageru.

      Příklad: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Pro druhý záznam CNAME namapujte vlastní doménu, bez subdomény cdnverify na váš koncový bod CDN. Tato položka mapování vlastní domény na Traffic Manager. 

      Příklad: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Pokud se vaše doména je v současné době provozu a nelze ji přerušit, proveďte tento krok poslední. Ověřte, že koncové body CDN a provoz správce domény živé před aktualizací vaší vlastní domény DNS do Traffic Manageru.
    >


2.  Váš profil Azure CDN vyberte první koncový bod CDN (Akamai). Vyberte **přidat vlastní doménu** a vstupní *cdndemo101akamai.azureedge.net*. Ověřte, zda je zelenou značku zaškrtnutí k ověření vlastní domény. 

    Azure CDN pomocí *cdnverify* subdoménu ověřit mapování DNS k dokončení tohoto procesu registrace. Další informace najdete v tématu [vytvořit záznam CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Tento krok povoluje tak, aby můžou reagovat na požadavky na jeho rozpoznání vlastní doménu Azure CDN.

3.  Vraťte se do webové stránky pro poskytovatele vaší vlastní domény a aktualizujte první mapování DNS, kterou jste vytvořili v tak, aby se vlastní doména se mapuje na druhý koncového bodu CDN.
                             
    Příklad: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. V profilu Azure CDN vyberte druhý koncový bod CDN (Verizon) a opakujte krok 2. Vyberte **přidat vlastní doménu**a vstupní *cdndemo101akamai.azureedge.net*.
 
Po dokončení těchto kroků služby s více než CDN s funkcí převzetí služeb při selhání se nastavuje pomocí Azure Traffic Manageru. Budete mít přístup k testu adresy URL z vaší vlastní domény. Chcete-li otestovat funkci, zakažte primární koncový bod CDN a ověřte, že žádost je správně přesouvá sekundárního koncového bodu CDN. 

## <a name="next-steps"></a>Další postup
Můžete také nastavit jiných metod směrování, jako například zeměpisné k vyrovnávání zatížení mezi různé koncové body CDN. Další informace najdete v tématu [nakonfigurovat geografickou metodu směrování provozu pomocí služby Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



