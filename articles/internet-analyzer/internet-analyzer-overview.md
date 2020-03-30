---
title: Internetový analyzátor Azure | Dokumenty společnosti Microsoft
description: Další informace o Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73501943"
---
# <a name="what-is-internet-analyzer-preview"></a>Co je internetový analyzátor? (Preview)

Internet Analyzer je platforma pro měření na straně klienta, která testuje, jak změny síťové infrastruktury ovlivňují výkon vašich zákazníků. Ať už provádíte migraci z místního prostředí do Azure, nebo vyhodnocujete novou službu Azure, Internet Analyzer vám umožní využívat data vašich uživatelů a bohaté analýzy Microsoftu k tomu, abyste ještě před migrací lépe porozuměli vaší síťové architektuře v Azure a mohli ji optimalizovat.

Internet Analyzer používá malý JavaScript klient vložený do webové aplikace pro měření latence od koncových uživatelů k vybrané sadě síťových cílů, voláme _koncové body_. Internetový analyzátor umožňuje nastavit několik souběžných testů, což vám umožní vyhodnotit různé scénáře podle toho, jak se vaše infrastruktura a potřeby zákazníků vyvíjejí. Internetový analyzátor poskytuje vlastní a předem nakonfigurované koncové body, které poskytují pohodlí i flexibilitu při rozhodování o důvěryhodném výkonu pro koncové uživatele. 


> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Rychlé & přizpůsobitelné testy

Internet Analyzer řeší otázky související s výkonem migrace do cloudu, nasazování do nových nebo dalších oblastí Azure nebo testování nových platforem pro doručování aplikací a obsahu v Azure, jako jsou [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) a Microsoft Azure [CDN](https://azure.microsoft.com/services/cdn/). 

Každý test, který vytvoříte v nástroji Internet Analyzer, se skládá ze dvou koncových bodů – koncový bod A a koncový bod B. Výkon koncového bodu B se analyzuje vzhledem ke koncovému bodu A. 

Můžete buď nakonfigurovat vlastní koncový bod, nebo vybrat z různých předkonfigurovaných koncových bodů Azure. Vlastní koncové body by se měly používat k vyhodnocení místních úloh, vašich instancí v jiných zprostředkovatelích cloudu nebo vlastních konfigurací Azure. Testy se mohou skládat ze dvou vlastních koncových bodů; však musí být hostovaný alespoň jeden vlastní koncový bod v Azure. Předkonfigurované koncové body Azure jsou rychlý a snadný způsob, jak vyhodnotit výkon oblíbených síťových platforem Azure, jako jsou Azure Front Door, Azure Traffic Manager a Azure CDN. 

Během náhledu jsou k dispozici následující předkonfigurované koncové body: 

* **Oblast Azure**
    * Brazílie – jih
    * Indie – střed
    * USA – střed
    * Východní Asie
    * USA – východ
    * Japonsko – západ
    * Severní Evropa
    * Jižní Afrika – sever
    * Jihovýchodní Asie 
    * SAE Sever
    * Spojené království – západ  
    * Západní Evropa
    * USA – západ 
    * USA – západ 2
* **Více kombinací oblastí Azure** 
    * Východní USA, Brazílie – jih 
    * Východní USA, Východní Asie 
    * Západní Evropa, Brazílie – jih
    * Západní Evropa, Jihovýchodní Asie
    * Západní Evropa, SAE Sever
    * Západní USA, Východní USA 
    * Západní USA, Západní Evropa
    * Západní USA, SAE Sever
    * Západní Evropa, SAE Sever, Jihovýchodní Asie
    * Západní USA, Západní Evropa, Východní Asie
    * Západní USA, Severní Evropa, Jihovýchodní Asie, SAE – sever, Jižní Afrika – sever 
* **Azure + Azure Front Door** – nasazené na libovolné kombinaci oblasti Azure, které jsou uvedeny výše
* **Azure + Azure CDN od Microsoftu** – nasazené na libovolné kombinaci oblasti Azure uvedené výše
* **Azure + Azure Traffic Manager** – nasazený na libovolné kombinaci oblastí Azure, která je uvedena výše

## <a name="suggested-test-scenarios"></a>Navrhované testovací scénáře 

Aby vám internet analyzer pomohl při rozhodování o nejlepším výkonu pro zákazníky, umožňuje vám nástroj Internet Analyzer vyhodnotit dva koncové body pro konkrétní populaci koncových uživatelů. 

Zatímco Internet Analyzer může odpovědět na velké množství otázek, některé z nejběžnějších jsou: 
* Jaký dopad migrace do cloudu má na výkon migrace do cloudu? 
    * *Navrhovaný test: Vlastní (vaše aktuální místní infrastruktura) vs. Azure (jakýkoli předkonfigurovaný koncový bod)*
* Jaká je hodnota umístění mých dat na hraniční hodnotu vs. v datovém centru? 
    *  *Navrhovaný test: Azure vs. Azure Front Door, Azure vs. Azure CDN od Microsoftu*
* Jaké jsou výhody výkonu Azure Front Door?
    *  *Navrhovaný test: Vlastní/ Azure/ CDN vs. Přední dveře Azure*
* Jaké jsou výhody výkonu Azure CDN od Microsoftu? 
    *  *Navrhovaný test: Vlastní/ Azure/ AFD vs Azure CDN od Microsoftu*
* Jak azure cdn od Microsoftu zásobníku nahoru? 
    *  *Navrhovaný test: Vlastní (jiný koncový bod CDN) vs. Azure CDN od Microsoftu*
* Jaký je nejlepší cloud pro vaši populaci koncových uživatelů v jednotlivých oblastech? 
    *  *Navrhovaný test: Vlastní (jiná cloudová služba) vs. Azure (jakýkoli předkonfigurovaný koncový bod)*

## <a name="how-it-works"></a>Jak to funguje

Chcete-li používat Nástroj pro analýzu Internetu, nastavte na portálu Microsoft Azure prostředek nástroje Internet Analyzer a nainstalujte do aplikace malého klienta JavaScriptu. Klient měří latenci od koncových uživatelů k vybraným koncovým bodům stažením bitové kopie o jednom obrazovém bodu přes protokol HTTPS. Po shromáždění měření latence klient odešle naměřená data do internetového analyzátoru.

Když uživatel navštíví webovou aplikaci, klient JavaScriptu vybere dva koncové body, které chcete měřit ve všech nakonfigurovaných testech. Pro každý koncový bod klient provádí _měření za studena_ a _teplé._ Měření _za studena_ vznikne další latence vedle čisté latence sítě mezi uživatelem a koncovým bodem, jako je například rozlišení DNS, handshake připojení TCP a vyjednávání SSL/TLS. _Teplé_ měření následuje těsně po dokončení _měření za studena_ a využívá výhod trvalé správy připojení TCP moderních prohlížečů k získání přesné míry latence od konce do konce. Pokud je podporovánprohlížečem uživatele, rozhraní API pro časování prostředků W3C se používá pro přesné časování měření. V současné době se pro analýzu používají pouze měření teplé latence.

![Architektura](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Scorecard 

Po spuštění testu se telemetrická data zobrazí ve vašem prostředku Nástroje pro analýzu internetu na kartě Přehled výkonnostních metrik. Tato data jsou vždy agregována. Pomocí následujících filtrů můžete změnit zobrazení dat, která vidíte: 

* **Test:** Vyberte test, pro který chcete zobrazit výsledky. Testovací data se objeví, jakmile je dostatek dat k dokončení analýzy - ve většině případů do 24 hodin. 
* **Časové období & koncové datum:** Internetový analyzátor generuje denně tři přehledy výkonnostních metrik – každý přehled výkonnostních metrik odráží jiné časové období agregace – 24 hodin před (den), sedm dní před (týden) a 30 dní před (měsíc). Pomocí filtru Koncové datum vyberte časové období, které chcete zobrazit. 
* **Země:** Tento filtr slouží k zobrazení dat specifických pro koncové uživatele s bydlištěm v zemi. Globální filtr zobrazuje data ve všech zeměpisných oblastech.  

Další informace o přehledech výkonnostních metrik naleznete na stránce [Interpretace přehledu výkonnostních metrik.](internet-analyzer-scorecard.md) 


## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit první prostředek nástroje Internet Analyzer](internet-analyzer-create-test-portal.md).
* Přečtěte si [nejčastější dotazy k internetovému analyzátoru](internet-analyzer-faq.md). 
