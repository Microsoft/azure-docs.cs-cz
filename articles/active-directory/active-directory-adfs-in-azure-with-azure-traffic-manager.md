---
title: Vysoká dostupnost mezi geografickými nasazeními služby AD FS v Azure pomocí Azure Traffic Manageru | Dokumentace Microsoftu
description: V tomto dokumentu se dozvíte, jak nasadit služby AD FS v Azure a zajistit vysokou dostupnost.
keywords: AD FS pomoc Azure Traffic Manageru, ADFS pomocí Azure Traffic Manageru, geografické, více datových center, geografická datová centra, více geografických datových center, nasazení AD FS v Azure, nasazení Azure AD FS, Azure ADFS, Azure AD FS, nasazení ADFS, nasazení AD FS, AD FS v Azure, nasazení ADFS v Azure, nasazení AD FS v Azure, ADFS Azure, úvod do AD FS, Azure, AD FS v Azure, IAAS, ADFS, přesunutí AD FS do Azure
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: e984d3d590021e3dd9e46d0f12493889b2acc229
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26604775"
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Vysoká dostupnost mezi geografickými nasazeními služby AD FS v Azure pomocí Azure Traffic Manageru
Článek [Nasazení služby AD FS v Azure](active-directory-aadconnect-azure-adfs.md) poskytuje podrobné pokyny pro nasazení jednoduché infrastruktury služby AD FS pro vaši organizaci v Azure. Tento článek popisuje další kroky při vytváření nasazení služby AD FS v Azure mezi geografickými lokalitami pomocí [Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md). Azure Traffic Manager pomáhá vytvořit geograficky rozsáhlou infrastruktury služby AD FS s vysokou dostupnost a vysokým výkonem pro vaši organizaci tím, že využívá široké spektrum metod směrování, které jsou k dispozici pro splnění různých požadavků infrastruktury.

Infrastruktura služby AD FS s vysokou dostupností mezi geografickými lokalitami umožňuje využití následujících funkcí:

* **Eliminace jediného bodu selhání:** Díky možnostem převzetí služeb při selhání, které Azure Traffic Manager má, můžete zajistit infrastrukturu služby AD FS s vysokou dostupností i v případě, že se jedno z datových center v některé části světa ocitne mimo provoz.
* **Vyšší výkon:** Nasazení navržené v tomto článku můžete využít k poskytování vysoce výkonné infrastruktury služby AD FS, která přispěje k rychlejšímu ověřování uživatelů. 

## <a name="design-principles"></a>Principy návrhu
![Celkový návrh](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Základní principy návrhu budou stejné, jako je uvedeno v části Principy návrhu v článku o nasazení služby AD FS v Azure. Na uvedeném diagramu je znázorněno jednoduché rozšíření základního nasazení do jiné geografické oblasti. Níže jsou uvedeny některé body, které je vhodné zvážit při rozšiřování nasazení do nové geografické oblasti.

* **Virtuální síť:** Měli byste vytvořit novou virtuální síť v geografické oblasti, do které chcete nasadit další infrastrukturu služby AD FS. V diagramu uvedeném výše jsou znázorněny dvě virtuální sítě Geo1 VNET a Geo2 VNET, přičemž každá se nachází v jiné geografické oblasti.
* **Řadiče domény a servery služby AD FS v nové geografické virtuální síti:** Doporučujeme nasazovat řadiče domény v nové geografické oblasti tak, aby servery služby AD FS v nové oblasti nemusely kvůli ověřování kontaktovat řadič domény; tím se zvýší výkon.
* **Účty úložiště:** Účty úložiště jsou přidruženy k oblasti. Vzhledem k tomu, že budete nasazovat počítače v nové geografické oblasti, budete muset vytvořit nové účty úložiště, které se v příslušné oblasti budou používat.  
* **Skupiny zabezpečení sítě:** Jako účty úložiště nelze skupiny zabezpečení sítě vytvořené v určité oblasti použít v jiné geografické oblasti. Je proto třeba pro podsíť INT a DMZ v nové geografické oblasti vytvořit nové skupiny zabezpečení sítě podobné těm v první geografické oblasti.
* **Názvy DNS pro veřejné IP adresy:** Azure Traffic Manager může na koncové body odkazovat POUZE prostřednictvím názvů DNS. Proto je nutné, abyste vytvořili názvy DNS pro veřejné IP adresy externích nástrojů pro vyrovnávání zatížení.
* **Azure Traffic Manager:** Microsoft Azure Traffic Manager vám umožňuje řídit distribuci uživatelského provozu do koncových bodů služby spuštěných v různých datových centrech po celém světě. Azure Traffic Manager pracuje na úrovni služby DNS. Využívá odpovědi služby DNS ke směrování provozu koncových uživatelů do globálně distribuovaných koncových bodů. Klienti se pak připojují přímo k těmto koncovým bodům. S použitím různých možností směrování Výkon, Vážená a Priorita můžete snadno zvolit možnost směrování, která nejlépe vyhovuje potřebám vaší organizace. 
* **Možnosti propojení dvou virtuálních sítí mezi dvěma oblastmi:** Není třeba, aby existovalo propojení přímo mezi oběma virtuálními sítěmi. Vzhledem k tomu, že každá virtuální síť má přístup k řadičům domény a má vlastní server služby AD FS a WAP, mohou pracovat bez jakéhokoli připojení mezi virtuálními sítěmi v různých oblastech. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Kroky při integraci Azure Traffic Manageru
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Nasazení služby AD FS v nové geografické oblasti
Při nasazování stejné topologie v nové geografické oblasti postupujte podle kroků a pokynů v [Nasazení služby AD FS v Azure](active-directory-aadconnect-azure-adfs.md).

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Názvy DNS pro veřejné IP adresy internetových (veřejných) nástrojů pro vyrovnávání zatížení
Jak je uvedeno výše, Azure Traffic Manager může odkazovat na názvy DNS jako koncové body. Proto je důležité vytvořit názvy DNS pro veřejné IP adresy externí nástrojů pro vyrovnávání zatížení. Následující snímek obrazovky ilustruje způsob konfigurace názvu DNS pro veřejnou IP adresu. 

![Název DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Nasazení Azure Traffic Manageru
Podle následujících pokynů můžete vytvořit profil Traffic Manageru. Další informace najdete také v článku [Správa profilu Azure Traffic Manageru](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Vytvoření profilu Traffic Manageru:** Zadejte pro svůj profil Traffic Manageru jedinečný název. Tento název profilu je součástí názvu DNS a slouží jako předpona pro popisek názvu domény Traffic Manageru. Přidáním názvu/předpony k řetězci trafficmanager.net se vytvoří název DNS pro Traffic Manager. Následující snímek obrazovky ilustruje nastavení předpony DNS Traffic Manageru mysts. Výsledný název DNS bude mysts.trafficmanager.net. 
   
    ![Vytvoření profilu Traffic Manageru](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Metoda směrování provozu:** V Traffic Manageru jsou k dispozici tři možnosti směrování:
   
   * Priorita 
   * Výkon
   * Vážená
     
     Možnost **Výkon** se doporučuje pro zajištění infrastruktury služby AD FS s rychlou odezvou. Můžete ovšem zvolit kteroukoli metodu směrování, která nejlépe vyhovuje vašim potřebám nasazení. Vybraná možnost směrování nemá na funkci služby AD FS vliv. Další informace najdete v článku [Metody směrování provozu Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md). Na ukázkovém snímku obrazovky je vybrána metoda **Výkon**.
3. **Konfigurace koncových bodů:** Na stránce Traffic Manageru klikněte na koncové body a vyberte možnost Přidat. Otevře se stránka Přidat koncový bod podobná následujícímu snímku obrazovky.
   
   ![Konfigurace koncových bodů](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   Pro různé vstupy postupujte podle následujících pokynů:
   
   **Typ:** Vyberte koncový bod Azure, protože budeme odkazovat na veřejnou IP adresu Azure.
   
   **Název:** Vytvořte název, který chcete přidružit ke koncovému bodu. Nejedná se o název DNS a nemá žádný vliv na záznamy DNS.
   
   **Typ cílového prostředku:** Jako hodnotu této vlastnosti vyberte možnost Veřejná IP adresa. 
   
   **Cílový prostředek:** Prostřednictvím této možností můžete vybírat z různých názvů DNS, které máte v rámci předplatného k dispozici. Zvolte název DNS odpovídající konfigurovanému koncovému bodu.
   
   Přidejte koncový bod pro každou geografickou oblast, do které chcete směrovat provoz Azure Traffic Manageru.
   Další informace a podrobné pokyny pro přidávání či konfigurování koncových bodů v Traffic Manageru najdete v článku [Přidávání, zakazování, povolování nebo odstraňování koncových bodů](../traffic-manager/traffic-manager-endpoints.md).
4. **Konfigurace testu:** na stránce Traffic Manageru klikněte na možnost Konfigurace. Na stránce konfigurace je třeba změnit nastavení monitorování na test na portu HTTP 80 a nastavit relativní cestu /adfs/probe.
   
    ![Konfigurace testu](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Zajistěte, aby byl stav koncových bodů po dokončení konfigurace ONLINE**. Pokud jsou všechny koncové body ve stavu „sníženo“, Azure Traffic Manager vyvine maximální úsilí při směrování provozu; bude se předpokládat, že diagnostika je chybná a všechny koncové body jsou dostupné.
   > 
   > 
5. **Úprava záznamů DNS pro Azure Traffic Manager:** Vaše služba FS (Federation Service) by měla být záznamem CNAME pro název DNS Azure Traffic Manageru. Vytvořte záznam CNAME ve veřejných záznamech DNS tak, aby se každý, kdo se pokouší o spojení se službou FS, ve skutečnosti spojil s Azure Traffic Managerem.
   
    Chcete-li například, aby služba FS fs.fabidentity.com odkazovala na Traffic Manager, je třeba aktualizovat příslušný záznam o prostředku DNS následujícím způsobem:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Test směrování a přihlášení ke službě AD FS
### <a name="routing-test"></a>Test směrování
Zcela jednoduchým testem směrování je pokus o testování názvu DNS služby FS pomocí příkazu Ping z počítače v každé geografické oblasti. V závislosti na zvolené metodě směrování bude v zobrazení příkazu Ping uveden koncový bod, do kterého jsou pakety příkazu Ping ve skutečnosti odesílány. Pokud jste například vybrali směrování podle výkonu, bude dosažen koncový bod nejblíže oblasti klienta. Níže je uveden snímek dvou provedení příkazu Ping z klientských počítačů ve dvou různých oblastech: jednoho v oblasti Východní Asie a jednoho v Západní USA. 

![Test směrování](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>Test přihlášení ke službě AD FS
Nejjednodušší způsob otestování služby AD FS je pomocí stránky IdpInitiatedSignon.aspx. Abyste to mohli provést, musíte ve vlastnostech služby AD FS povolit IdpInitiatedSignOn. Pomocí níže uvedených pokynů ověřte nastavení služby AD FS.

1. Pomocí PowerShellu spusťte níže uvedenou rutinu na serveru služby AD FS a povolte požadovanou možnost. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. Ze kteréhokoli externího počítače zobrazte tuto stránku: https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Měla by se zobrazit stránka služby AD FS podobná níže uvedenému příkladu:
   
    ![Test služby AD FS – test ověřování](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    Po úspěšném přihlášení se zobrazí zpráva o úspěchu, jak je znázorněno níže:
   
    ![Test služby AD FS – úspěšné ověření](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Související odkazy
* [Základní nasazení služby AD FS v Azure](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Metody směrování provozu Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Další kroky
* [Správa profilu Azure Traffic Manageru](../traffic-manager/traffic-manager-manage-profiles.md)
* [Přidávání, zakazování, povolování nebo odstraňování koncových bodů](../traffic-manager/traffic-manager-endpoints.md) 

