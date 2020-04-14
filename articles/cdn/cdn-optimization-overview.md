---
title: Optimalizace Azure CDN pro typ doručování obsahu
description: Optimalizace Azure CDN pro typ doručování obsahu
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
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 473636dc95d96ea348a42ec0f1090029bf3a7728
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260430"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimalizace Azure CDN pro typ doručování obsahu

Když doručujete obsah velkému globálnímu publiku, je důležité zajistit optimalizované doručování obsahu. [Azure Content Delivery Network (CDN)](cdn-overview.md) můžete optimalizovat prostředí pro doručování na základě typu obsahu, který máte. Obsahem může být web, živý přenos, video nebo velký soubor ke stažení. Při vytváření koncového bodu CDN zadáte scénář v možnosti **Optimalizováno pro.** Vaše volba určuje, která optimalizace se použije na obsah dodaný z koncového bodu CDN.

Volby optimalizace jsou navrženy tak, aby používaly osvědčené postupy ke zlepšení výkonu doručování obsahu a lepšímu snížení zátěže původu. Volby scénáře ovlivňují výkon úpravou konfigurací pro částečné ukládání do mezipaměti, ukládání objektů do mezipaměti a zásady opakování selhání původu. 

Tento článek obsahuje přehled různých funkcí optimalizace a kdy byste je měli použít. Další informace o funkcích a omezeních naleznete v příslušných článcích jednotlivých typů optimalizace.

> [!NOTE]
> Když vytvoříte koncový bod CDN, může **se možnost Optimalizovat pro** volby lišit v závislosti na typu profilu, ve které je koncový bod vytvořen. Azure CDN zprostředkovatelé použít vylepšení různými způsoby, v závislosti na scénáři. 

## <a name="provider-options"></a>Možnosti zprostředkovatele

**Profily Azure CDN Standard od Microsoftu** podporují následující optimalizace:

* [Obecné poskytování webových stránek](#general-web-delivery). Tato optimalizace se také používá pro streamování médií a stahování velkých souborů.

> [!NOTE]
> Dynamické zrychlení webu od Microsoftu je nabízeno prostřednictvím [služby Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

**Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** podporují následující optimalizace:

* [Obecné poskytování webových stránek](#general-web-delivery). Tato optimalizace se také používá pro streamování médií a stahování velkých souborů.

* [Dynamická akcelerace webu](#dynamic-site-acceleration) 


**Azure CDN Standard z akamai** profilů podporují následující optimalizace:

* [Obecné doručování webových stránek](#general-web-delivery) 

* [Obecné vysílání datových proudů médií](#general-media-streaming)

* [Streamování médií videa na vyžádání](#video-on-demand-media-streaming)

* [Velké stahování souborů](#large-file-download)

* [Dynamická akcelerace webu](#dynamic-site-acceleration) 

Společnost Microsoft doporučuje otestovat rozdíly výkonu mezi různými poskytovateli a vybrat optimálního zprostředkovatele pro doručení.

## <a name="select-and-configure-optimization-types"></a>Výběr a konfigurace typů optimalizace

Při vytváření koncového bodu CDN vyberte typ optimalizace, který nejlépe odpovídá scénáři a typu obsahu, který má koncový bod doručit. Výchozí výběr je **obecné doručování webu.** Pro existující Azure CDN Standard z koncových bodů **Akamai** můžete kdykoli aktualizovat možnost optimalizace. Tato změna nepřeruší doručování z Azure CDN. 

1. V **azure cdn standard u profilu Akamai** vyberte koncový bod.

    ![Výběr koncového bodu](./media/cdn-optimization-overview/01_Akamai.png)

2. V části NASTAVENÍ vyberte **Optimalizace**. Potom vyberte typ z rozevíracího seznamu **Optimalizováno pro.**

    ![Optimalizace a výběr typu](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalizace pro konkrétní scénáře

Můžete optimalizovat koncový bod CDN pro jeden z těchto scénářů. 

### <a name="general-web-delivery"></a>Obecné doručování webových stránek

Nejběžnější možností optimalizace je obecné doručování webu. Je určen pro obecnou optimalizaci webového obsahu, jako jsou webové stránky a webové aplikace. Tato optimalizace může být také použita pro stahování souborů a videí.

Typický web obsahuje statický a dynamický obsah. Statický obsah zahrnuje obrázky, knihovny JavaScriptu a šablony stylů, které lze uložit do mezipaměti a doručit různým uživatelům. Dynamický obsah je přizpůsoben pro jednotlivé uživatele, například zpravodajské položky, které jsou přizpůsobeny profilu uživatele. Dynamický obsah, například obsah nákupního košíku, není uložen do mezipaměti, protože je jedinečný pro každého uživatele. Obecné doručování webových stránek může optimalizovat celý web. 

> [!NOTE]
> Pokud používáte **Azure CDN Standard z profilu Akamai,** vyberte tento typ optimalizace, pokud je průměrná velikost souboru menší než 10 MB. V opačném případě, pokud je průměrná velikost souboru větší než 10 MB, vyberte v rozevíracím seznamu **Optimalizovaný pro** soubor **možnost Velké stahování souborů.**

### <a name="general-media-streaming"></a>Obecné vysílání datových proudů médií

Pokud potřebujete použít koncový bod pro živé streamování a streamování videa na vyžádání, vyberte typ obecné optimalizace streamování médií.

Vysílání datových proudů médií je časově náročné, protože pakety, které přicházejí pozdě na klienta, jako je časté ukládání videoobsahu do vyrovnávací paměti, může způsobit zhoršení zobrazení. Optimalizace streamování médií snižuje latenci doručování mediálního obsahu a poskytuje uživatelům plynulé možnosti streamování. 

Tento scénář je běžný pro zákazníky mediálních služeb Azure. Když používáte mediální služby Azure, získáte jeden koncový bod streamování, který se dá použít pro živé i na vyžádání. V tomto scénáři zákazníci nemusí přepnout na jiný koncový bod při změně z živého na streamování na vyžádání. Obecná optimalizace streamování médií podporuje tento typ scénáře.

Pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizonu**a **Azure CDN Premium od verizonu**použijte obecný typ optimalizace webového doručování k doručování obecného mediálního obsahu datových proudů.

Další informace o optimalizaci datových proudů médií naleznete v [tématu Optimalizace datových proudů médií](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streamování médií videa na vyžádání

Optimalizace streamování videa na vyžádání zlepšuje streamování obsahu videa na vyžádání. Pokud používáte koncový bod pro streamování videa na vyžádání, použijte tuto možnost.

Pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizonu**a **Azure CDN Premium od Verizonu** použijte obecný typ optimalizace webového doručování k doručování streamovaného mediálního obsahu videa na vyžádání.

Další informace o optimalizaci datových proudů médií naleznete v [tématu Optimalizace datových proudů médií](cdn-media-streaming-optimization.md).

> [!NOTE]
> Pokud koncový bod CDN primárně slouží obsah videa na vyžádání, použijte tento typ optimalizace. Hlavní rozdíl mezi tímto typem optimalizace a obecným typem optimalizace streamování médií je časový limit opakování připojení. Časový čas je mnohem kratší pro práci se scénáři živého streamování.
>

### <a name="large-file-download"></a>Velké stahování souborů

Pro **profily Azure CDN Standard z Akamai** jsou soubory s velkým souborem optimalizovány pro obsah větší než 10 MB. Pokud je průměrná velikost souboru menší než 10 MB, použijte obecné doručování na webu. Pokud jsou průměrné velikosti souborů konzistentně větší než 10 MB, může být efektivnější vytvořit samostatný koncový bod pro velké soubory. Například aktualizace firmwaru nebo softwaru jsou obvykle velké soubory. Chcete-li dodat soubory větší než 1,8 GB, je vyžadována optimalizace stahování velkých souborů.

Pro **azure cdn standard od Microsoftu**, **Azure CDN Standard od Verizonu**a **Azure CDN Premium od Verizonu,** použijte obecný typ optimalizace webového doručování k doručování obsahu pro stahování velkých souborů. Neexistuje žádné omezení velikosti stahování souborů.

Další informace o optimalizaci velkých souborů naleznete v [tématu Optimalizace velkých souborů](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Akcelerace dynamických webů

 Dynamická akcelerace webu (DSA) je dostupná pro **Azure CDN Standard od Akamai**, **Azure CDN Standard od Verizonu**a **Azure CDN Premium od Verizonu.** Tato optimalizace zahrnuje další poplatek za použití; Další informace naleznete v [tématu Content Delivery Network pricing](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Dynamické zrychlení webu od Microsoftu je nabízeno prostřednictvím [služby Azure Front Door Service,](https://docs.microsoft.com/azure/frontdoor/front-door-overview) což je globální služba [anycast](https://en.wikipedia.org/wiki/Anycast) využívající privátní globální síť společnosti Microsoft k poskytování úloh aplikací.

DSA obsahuje různé techniky, které využívají latenci a výkon dynamického obsahu. Techniky zahrnují optimalizaci trasy a sítě, optimalizaci Protokolu TCP a další. 

Tuto optimalizaci můžete použít k urychlení webové aplikace, která obsahuje mnoho odpovědí, které nelze uložit do mezipaměti. Příkladem jsou výsledky hledání, transakce pokladny nebo data v reálném čase. Můžete pokračovat v používání základních funkcí mezipaměti Azure CDN pro statická data. 

Další informace o dynamické akceleraci webu naleznete [v tématu Dynamická akcelerace webu](cdn-dynamic-site-acceleration.md).



