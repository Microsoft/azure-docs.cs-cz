---
title: Převzetí služeb při selhání mezi několika koncovými body pomocí Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Přečtěte si, jak nakonfigurovat převzetí služeb při selhání napříč několika koncovými body Azure Content Delivery Network pomocí Azure Traffic Manager.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b75643d0d526bae4d7b2879dffab3d90dbcbe1eb
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875865"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Převzetí služeb při selhání napříč několika koncovými body pomocí Azure Traffic Manager

Při konfiguraci služby Azure Content Delivery Network (CDN) můžete vybrat optimálního poskytovatele a cenovou úroveň podle svých potřeb. 

Azure CDN s jeho globální distribuovanou infrastrukturou ve výchozím nastavení vytvoří místní a geografickou redundanci a globální vyrovnávání zatížení, aby se zlepšila dostupnost a výkon služby. 

Pokud není umístění k dispozici pro poskytování obsahu, požadavky jsou automaticky směrovány do jiného umístění. Optimální bod přítomnosti (POP) slouží k obsluze jednotlivých požadavků klienta. Automatické směrování je založené na faktorech jako umístění požadavku a zatížení serveru.
 
Pokud máte více profilů CDN, můžete dál vylepšit dostupnost a výkon pomocí Azure Traffic Manager. 

Pro vyrovnávání zatížení mezi několika koncovými body CDN použijte Azure Traffic Manager s Azure CDN:
 
* Převzetí služeb při selhání
* Geografické vyrovnávání zatížení 

V typickém scénáři převzetí služeb při selhání jsou všechny požadavky klienta směrovány do primárního profilu CDN. 

Pokud profil není k dispozici, požadavky se přesměrují do sekundárního profilu.  Požadavky budou obnoveny do vašeho primárního profilu, když se vrátí zpět do online režimu.

Používání Azure Traffic Manager tímto způsobem zajišťuje, že vaše webová aplikace bude vždycky k dispozici. 

Tento článek poskytuje pokyny a příklad, jak nakonfigurovat převzetí služeb při selhání pomocí profilů z: 

* **Azure CDN Standard z Verizon**
* **Azure CDN Standard z Akamai**

Podporuje se taky **Azure CDN od Microsoftu** .

## <a name="create-azure-cdn-profiles"></a>Vytvoření profilů Azure CDN
Vytvořte dva nebo více Azure CDN profilů a koncových bodů s různými poskytovateli.

1. Vytvořte dva profily CDN:
    * **Azure CDN Standard z Verizon**
    * **Azure CDN Standard z Akamai** 

    Vytvořte profily podle kroků v části [Vytvoření nového profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN – Vícenásobné profily](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. V každém z nových profilů vytvořte alespoň jeden koncový bod podle kroků v části [Vytvoření nového koncového bodu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Vytvořit profil Traffic Manageru
Vytvořte profil Azure Traffic Manager a nakonfigurujte vyrovnávání zatížení napříč koncovými body CDN. 

1. Pomocí postupu v části [Vytvoření profilu Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)vytvořte profil Azure Traffic Manager. 

    * **Metoda směrování**, vyberte **Priorita**.

2. Pomocí postupu v části [přidání Traffic Manager koncových bodů](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) přidejte koncové body CDN do profilu Traffic Manager.

    * **Typ**, vyberte **externí koncové body**.
    * **Priorita**, zadejte číslo.

    Můžete například vytvořit **cdndemo101akamai.azureedge.NET** s prioritou **1** a **cdndemo101verizon.azureedge.NET** s prioritou **2**.

   ![Koncové body Traffic Manageru CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurace vlastní domény v Azure CDN a Azure Traffic Manager
Po nakonfigurování profilů CDN a Traffic Manager postupujte podle těchto kroků a přidejte mapování DNS a zaregistrujte vlastní doménu do koncových bodů CDN. V tomto příkladu je název vlastní domény **cdndemo101. dustydogpetcare. online**.

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


2.  V profilu Azure CDN vyberte první koncový bod CDN (Akamai). Vyberte **Přidat vlastní doménu** a vstup **cdndemo101. dustydogpetcare. online**. Ověřte, že se značka zaškrtnutí pro ověření vlastní domény zeleně. 

    Azure CDN používá subdoménu **cdnverify** k ověření mapování DNS k dokončení tohoto procesu registrace. Další informace najdete v tématu [Vytvoření záznamu DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Tento krok umožňuje Azure CDN rozpoznat vlastní doménu, aby mohla reagovat na své žádosti.
    
    > [!NOTE]
    > Pokud chcete povolit TLS na **Azure CDN z profilů Akamai** , musíte přímo CNAME vlastní doménu do svého koncového bodu. cdnverify pro povolení protokolu TLS ještě není podporováno. 
    >

3.  Vraťte se na web pro poskytovatele domény vlastní domény. Aktualizujte první mapování DNS, které jste vytvořili. Namapujte vlastní doménu na druhý koncový bod CDN.
                             
    Například: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. V profilu Azure CDN vyberte druhý koncový bod CDN (Verizon) a opakujte krok 2. Vyberte **Přidat vlastní doménu**a zadejte **cdndemo101. dustydogpetcare. online**.
 
Po dokončení tohoto postupu se služba multi-CDN s možnostmi převzetí služeb při selhání nakonfiguruje s využitím Azure Traffic Manager. 

K testovacím adresám URL můžete přistupovat z vaší vlastní domény. 

Chcete-li otestovat funkci, zakažte primární koncový bod CDN a ověřte, zda je požadavek správně přesunut do sekundárního koncového bodu CDN. 

## <a name="next-steps"></a>Další kroky
Můžete nakonfigurovat další metody směrování, například geografické, a vyrovnávat zatížení mezi různými koncovými body CDN. 

Další informace najdete v tématu [Konfigurace metody směrování geografického provozu pomocí Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



