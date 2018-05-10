---
title: Optimalizace doručování obsahu Azure pro váš scénář
description: K optimalizaci doručování obsahu pro konkrétní scénáře
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: de748f7fa33b0250b1572dd5ae55cddf15003a0e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>Optimalizace doručování obsahu Azure pro váš scénář

Při doručování obsahu do velkou globální cílovou skupinu, je důležité zajistit optimalizované doručování obsahu. Sítě doručování Azure obsahu (CDN) můžete optimalizovat prostředí doručení založené na typ obsahu, které máte. Obsah může být web, živý datový proud, video nebo velký soubor ke stažení. Když vytvoříte koncový bod CDN, zadáte scénář v **optimalizované pro** možnost. Vaše volba určuje, které optimalizace se použije k obsahu doručit z koncového bodu CDN.

Možnosti optimalizace navržena pro použití chování osvědčené postupy pro zlepšení výkonu doručování obsahu a snižování zátěže lepší původu. Vaše volby scénář ovlivnit výkon změnou konfigurace pro částečné ukládání do mezipaměti, objekt rozdělování a zásady opakování selhání původu. 

Tento článek obsahuje přehled různých funkcí optimalizace a kdy byste měli použít. Další informace o funkcích a omezení naleznete v příslušných článcích na každý typ jednotlivých optimalizace.

> [!NOTE]
> Vaše **optimalizované pro** možnosti může lišit v závislosti na zprostředkovateli vyberete. Vylepšení zprostředkovatele CDN použít různými způsoby v závislosti na scénáři. 

## <a name="provider-options"></a>Možnosti zprostředkovatele

**Azure CDN Standard od společnosti Microsoft** podporuje následující optimalizace:

* [Obecné webové doručení](#general-web-delivery). Tato optimalizace je také použít pro streamování médií a stáhněte velkých souborů.


**Azure CDN Standard od společnosti Verizon**, a **Azure CDN Premium od společnosti Verizon** podporují následující optimalizace:

* [Obecné webové doručení](#general-web-delivery). Tato optimalizace je také použít pro streamování médií a stáhněte velkých souborů.

* [Akcelerace dynamického webu](#dynamic-site-acceleration) 


**Azure CDN Standard od Akamai** podporuje následující optimalizace:

* [Obecné webové doručení](#general-web-delivery) 

* [Obecné streamování médií](#general-media-streaming)

* [Streamování videa na přání médií](#video-on-demand-media-streaming)

* [Stažení velkých souborů](#large-file-download)

* [Akcelerace dynamického webu](#dynamic-site-acceleration) 

Společnost Microsoft doporučuje, že provedete test výkonu rozdíly mezi různé zprostředkovatele můžete vybrat optimální zprostředkovatele pro vaše doručení.

## <a name="select-and-configure-optimization-types"></a>Vyberte a nakonfigurujte optimalizace typy

Pokud chcete vytvořit nový koncový bod, vyberte typ optimalizace, který nejlépe odpovídá scénář a typ obsahu, který chcete, aby koncový bod pro doručování. **Obecné webové doručení** je výchozí výběr. Pro existující **Azure Content Delivery Network společnosti Akamai** koncových bodů, můžete možnost optimalizace kdykoli aktualizovat. Tato změna nemá přerušit doručování z Azure CDN. 

1. V rámci **Azure CDN Standard od společnosti Akamai** profil, vyberte koncový bod.

    ![Výběr koncového bodu ](./media/cdn-optimization-overview/01_Akamai.png)

2. V části nastavení, vyberte **optimalizace**. Pak vyberte typ z **optimalizované pro** rozevíracího seznamu.

    ![Optimalizace a typ výběru](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalizace pro konkrétní scénáře

Koncový bod CDN pro jednu z těchto scénářů, můžete optimalizovat. 

### <a name="general-web-delivery"></a>Obecné doručování webu

Obecné webové doručení je nejběžnější možnosti optimalizace. Je určený pro optimalizaci obecné webového obsahu, například webových stránek a webových aplikací. Tato optimalizace můžete použít také pro soubor, a stáhne video.

Typické webu obsahuje obsah statické a dynamické. Statický obsah zahrnuje obrázků, knihovny jazyka JavaScript a stylů, které lze do mezipaměti a doručit různým uživatelům. Dynamický obsah je přizpůsobené pro jednotlivé uživatele, například položky zpráv, které jsou přizpůsobené profilu uživatele. Dynamický obsah neukládá do mezipaměti, protože je jedinečný pro každého uživatele, třeba nákupní košík obsah. Obecné webové doručení můžete optimalizovat celý web. 

> [!NOTE]
> Pokud používáte **Azure CDN Standard od společnosti Akamai**, můžete chtít použít optimalizace, pokud vaše Průměrná velikost je menší než 10 MB. Pokud vaše Průměrná velikost souboru je větší než 10 MB, vyberte **stahování velkých souborů** z **optimalizované pro** rozevíracího seznamu.

### <a name="general-media-streaming"></a>Streamování obecných médií

Pokud budete muset použít koncový bod pro živé streamování a streamování videa na přání, doporučujeme streamování optimalizace obecné médií.

Datové proudy Media je čas citlivé, protože paketů, které přicházejí pozdní na straně klienta může způsobit sníženou zobrazení prostředí, jako je například časté ukládání do vyrovnávací paměti obsahu videa. Optimalizace streamování médií snižuje latenci doručování obsahu pro média a poskytuje technologie smooth streaming prostředí pro uživatele. 

Tento scénář je běžný u Zákazníci využívající službu Azure media. Když používáte službu Azure media services, zobrazí jeden streamování koncový bod, který lze použít pro datové proudy živě i na vyžádání. V tomto scénáři není nutné přepnout na jiný koncový bod, když se změní ze za provozu se streamování na vyžádání zákazníků. Obecné média streamování optimalizace podporuje tento typ scénáře.

Pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Verizon**, a **Azure CDN Premium od společnosti Verizon**, použijte typ obecné webové doručení optimalizace pro poskytování obecné streamování mediální obsah.

Další informace o optimalizaci streamování médií najdete v tématu [optimalizace streamování médií](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streamování videa na přání médií

Optimalizace streamování na vyžádání Video-on-Demand média zlepšuje streamování obsahu na vyžádání video-on-demand. Pokud používáte koncový bod pro vyžádání video-on-demand streamování, můžete chtít použít tuto možnost.

Pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Verizon**, a **Azure CDN Premium od společnosti Verizon**, použijte typ obecné webové doručení optimalizace pro doručování média obsahu streamování na vyžádání video-on-demand.

Další informace o optimalizaci streamování médií najdete v tématu [optimalizace streamování médií](cdn-media-streaming-optimization.md).

> [!NOTE]
> Pokud koncový bod CDN slouží především na vyžádání video-on-demand obsahu, použijte tento typ optimalizace. Hlavní rozdíl mezi optimalizace a streamování optimalizace obecné médií je časový limit opakování připojení. Časový limit je mnohem kratší pro práci s živé streamování scénáře.

### <a name="large-file-download"></a>Stahování velkých souborů

Pro **Azure CDN Standard od společnosti Akamai**, stahování velkých souborů jsou optimalizované pro obsah větší než 10 MB. Pokud vaše Průměrná velikost souboru je menší než 10 MB, použijte obecné webové doručení. Pokud vaše soubory průměrné velikosti jsou konzistentně větší než 10 MB, může to být efektivnější vytvořit samostatné koncový bod pro velkých souborů. Aktualizace firmwaru a softwaru jsou například obvykle velkých souborů. Doručovat soubory větší než 1,8 GB, optimalizace stahování velkých souborů je potřeba.

Pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Verizon**, a **Azure CDN Premium od společnosti Verizon**, použijte typ obecné webové doručení optimalizace pro doručování velkých souborů stahování obsahu. Neexistuje žádné omezení velikosti stažení souboru.

Další informace o optimalizaci velkých souborů najdete v tématu [velkých souborů optimalizace](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Akcelerace dynamických webů

 Akcelerace dynamických webů je k dispozici pro **Azure CDN Standard od společnosti Akamai**, **Azure CDN Standard od společnosti Verizon**, a **Azure CDN Premium od společnosti Verizon** profily. Tato optimalizace zahrnuje další poplatek se použije. Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

Akcelerace dynamických webů zahrnuje různé techniky využívající latenci a výkonu dynamického obsahu. Mezi dostupné techniky patří trasy a síťové optimalizace, optimalizace TCP a další. 

Tato optimalizace vám pomůže urychlit webovou aplikaci, která zahrnuje mnoho odpovědí, které nejsou lze uložit do mezipaměti. Příklady jsou výsledky hledání, najdete v článku věnovaném transakce nebo dat v reálném čase. Můžete nadále používat možnosti ukládání do mezipaměti jádra CDN statických dat. 

Další informace o dynamických webů akcelerace najdete v tématu [dynamických webů akcelerace](cdn-dynamic-site-acceleration.md).



