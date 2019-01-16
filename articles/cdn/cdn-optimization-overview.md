---
title: Optimalizovat pro typ doručování obsahu Azure CDN
description: Optimalizovat pro typ doručování obsahu Azure CDN
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: magattus
ms.openlocfilehash: 526f3522bff05618189ad4f8205fbb61afc47fdc
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320292"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimalizovat pro typ doručování obsahu Azure CDN

Při doručování obsahu do rozsáhlému a globálnímu publiku je důležité zajistit optimalizované doručování obsahu. [Azure Content Delivery Network (CDN)](cdn-overview.md) můžete optimalizovat doručování prostředí založené na typ obsahu, které máte. Obsah může být web, živý stream, video nebo s velkými soubory ke stažení. Při vytváření koncového bodu CDN, můžete zadat scénář v **optimalizovaná pro** možnost. Vaše volba určuje, které optimalizace platí pro obsah od koncového bodu CDN.

Možnosti optimalizace jsou navrženy pro použití chování osvědčené postupy pro zlepšení výkonu doručování obsahu a snižování zátěže lepší původu. Vaše volby scénář ovlivnit výkon změnou konfigurace pro částečné ukládání do mezipaměti, objekt bloků a zásady opakování selhání původu. 

Tento článek obsahuje přehled různých funkcí optimalizace a jejich použití. Další informace o funkcích a omezeních najdete v tématu v příslušných článcích pro každý typ jednotlivých optimalizace.

> [!NOTE]
> Při vytváření koncového bodu CDN, **optimalizované pro** možnosti může lišit v závislosti na typu koncový bod se vytvoří v profilu. Azure CDN poskytovatelé vylepšení použít různými způsoby v závislosti na scénáři. 

## <a name="provider-options"></a>Možnosti poskytovatele

**Azure CDN Standard od společnosti Microsoft** profily podporuje následující optimalizace:

* [Obecné doručování webu](#general-web-delivery). Tato optimalizace se používá také pro streamování médií a stahování velkých souborů.


**Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** profily podporují následující optimalizace:

* [Obecné doručování webu](#general-web-delivery). Tato optimalizace se používá také pro streamování médií a stahování velkých souborů.

* [Akcelerace dynamického webu](#dynamic-site-acceleration) 


**Azure CDN Standard od Akamai** profily podporují následující optimalizace:

* [Obecné doručování webu](#general-web-delivery) 

* [Streamování obecných médií](#general-media-streaming)

* [Streamování médií vod (Video-on-demand)](#video-on-demand-media-streaming)

* [Stahování velkých souborů](#large-file-download)

* [Akcelerace dynamického webu](#dynamic-site-acceleration) 

Společnost Microsoft doporučuje, že provedete test výkonu rozdíly mezi různí poskytovatelé vybrat optimální zprostředkovatele pro vaše doručování.

## <a name="select-and-configure-optimization-types"></a>Vyberte a nakonfigurujte typy optimalizace

Při vytváření koncového bodu CDN vyberte typ optimalizace, která nejlépe odpovídá scénář a typ obsahu, který chcete, aby se koncový bod pro doručování. **Obecné doručování webu** je výchozí výběr. Pro existující **Azure CDN Standard od Akamai** koncové body lze aktualizovat pouze, možnost optimalizace kdykoli. Tato změna nebude přerušit doručování z Azure CDN. 

1. V **Azure CDN Standard od Akamai** profil, vyberte koncový bod.

    ![Výběr koncového bodu ](./media/cdn-optimization-overview/01_Akamai.png)

2. V části Nastavení vyberte **optimalizace**. Vyberte typ z **optimalizovaná pro** rozevíracího seznamu.

    ![Optimalizace a typ výběru](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalizace pro konkrétní scénáře

Můžete optimalizovat koncového bodu CDN pro jednu z těchto scénářů. 

### <a name="general-web-delivery"></a>Obecné doručování webu

Obecné doručování webu je nejběžnější možnost optimalizace. Je určená pro optimalizaci obecné webového obsahu, jako jsou třeba webové stránky a webové aplikace. Tato optimalizace je použít také pro soubor a stáhne videa.

Typické webu obsahuje statický a dynamický obsah. Obrázky, knihoven jazyka JavaScript a šablony stylů, které lze do mezipaměti a doručují různým uživatelům obsahuje statický obsah. Dynamický obsah je přizpůsobená pro jednotlivé uživatele, jako jsou příspěvky, které jsou přizpůsobené do profilu uživatele. Dynamický obsah, jako je například obsah nákupního košíku, neukládá do mezipaměti, protože je jedinečný pro každého uživatele. Obecné doručování webu můžete optimalizovat celého webu. 

> [!NOTE]
> Pokud používáte **Azure CDN Standard od Akamai** profil, vyberte tento typ optimalizace, pokud vaše Průměrná velikost souboru je menší než 10 MB. Jinak, pokud vaše Průměrná velikost souboru je větší než 10 MB, vyberte **stahování velkých souborů** z **optimalizovaná pro** rozevíracího seznamu.

### <a name="general-media-streaming"></a>Streamování obecných médií

Pokud je potřeba použít koncový bod pro živé streamování a video na vyžádání streamování, vyberte typ optimalizace streamování obecných médií.

Streamování médií je časově, protože paketů, které dorazí pozdě na straně klienta, jako je například časté ukládání do vyrovnávací paměti obsahu videa, může způsobit zážitek. Optimalizace streamování médií sníží latenci doručování obsahu médií a poskytuje technologie smooth streaming prostředí pro uživatele. 

Tento scénář je běžné, že zákazníci využívající službu Azure media. Při použití služby Azure media services můžete získat jeden koncový bod streamování, který lze použít pro streamování živě i na vyžádání. V tomto scénáři zákazníci nemusí přepínat do jiného koncového bodu při změnách od živého streamování na vyžádání. Optimalizace streamování obecných médií podporuje tento druh scénář.

Pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Verizonu**, a **Azure CDN Premium od Verizonu**, použijte typ optimalizace doručení obecné web do doručovat obsah obecné streamování médií.

Další informace o optimalizace streamování médií, naleznete v tématu [optimalizace streamování médií](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streamování médií vod (Video-on-demand)

Optimalizace streamování médií vod (Video-on-demand) zvyšuje streamování obsahu videa na vyžádání. Tuto možnost použijte, pokud používáte koncový bod streamování videa na vyžádání.

Pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Verizonu**, a **Azure CDN Premium od Verizonu** profilů, použijte Optimalizace obecného doručování webu typ, který doručení streamovaného obsahu médií vod (Video-on-demand).

Další informace o optimalizace streamování médií, naleznete v tématu [optimalizace streamování médií](cdn-media-streaming-optimization.md).

> [!NOTE]
> Pokud koncový bod CDN slouží především obsahu vod (Video-on-demand), použijte tento typ optimalizace. Hlavní rozdíl mezi tento typ optimalizace a obecné streamování typ optimalizace médií je časový limit připojení opakovat. Pro práci s živého streamování scénářů mnohem kratší je časový limit.
>

### <a name="large-file-download"></a>Stahování velkých souborů

Pro **Azure CDN Standard od Akamai** profily, velkých souborů jsou optimalizované soubory ke stažení obsahu, které jsou větší než 10 MB. Pokud vaše Průměrná velikost souboru je menší než 10 MB, použijte obecné doručování webu. Pokud vaše soubory průměrné velikosti jsou konzistentně větší než 10 MB, může být efektivnější vytvořit samostatný koncový bod pro velké soubory. Například aktualizace firmwaru nebo softwaru jsou obvykle velkých souborů. Chcete-li doručovat soubory větší než 1,8 GB, je potřeba optimalizace velkých souborů ke stažení.

Pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Verizonu**, a **Azure CDN Premium od Verizonu** profilů, použijte Optimalizace obecného doručování webu Typ k doručování velkých souborů ke stažení obsahu. Neexistuje žádné omezení velikosti souborů ke stažení.

Další informace o optimalizace velkých souborů najdete v tématu [optimalizace velkých souborů](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Akcelerace dynamických webů

 Akcelerace dynamického webu (DSA) je k dispozici pro **Azure CDN Standard od Akamai**, **Azure CDN Standard od Verizonu**, a **Azure CDN Premium od Verizonu** profily. Tato optimalizace zahrnuje žádné další poplatky se použije. Další informace najdete v tématu [ceny Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

DSA zahrnuje různých technik vytváření využívajících latenci a výkonu dynamického obsahu. Mezi dostupné techniky patří optimalizaci trasy a sítě, optimalizace TCP a další. 

Tato optimalizace můžete použít ke zrychlení webovou aplikaci, která obsahuje mnoho odpovědí, které není možné ukládat do mezipaměti. Příklady jsou výsledky hledání, rezervaci transakce nebo data v reálném čase. Můžete nadále používat základní funkce pro ukládání do mezipaměti Azure CDN pro statická data. 

Další informace o akcelerace dynamického webu, naleznete v tématu [akcelerace dynamického webu](cdn-dynamic-site-acceleration.md).



