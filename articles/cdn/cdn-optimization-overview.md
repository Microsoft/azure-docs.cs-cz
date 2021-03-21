---
title: Optimalizuje Azure CDN pro typ doručování obsahu.
description: Přečtěte si, jak může Azure Content Delivery Network optimalizovat doručování na základě typu obsahu. Osvědčené postupy optimalizace zlepšují výkon a snižování zátěže zdroje.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 3d207ee09a76509a65a2645515b182f8d92753b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387916"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimalizuje Azure CDN pro typ doručování obsahu.

Při doručování obsahu velkému počtu globálních cílových skupin je důležité zajistit optimalizované doručování obsahu. [Azure Content Delivery Network (CDN)](cdn-overview.md) může optimalizovat prostředí pro doručování na základě typu obsahu, který máte. Obsahem může být web, živý datový proud, video nebo velký soubor ke stažení. Při vytváření koncového bodu CDN zadáte scénář v možnosti **optimalizované pro** . Vaše volba určí, která optimalizace se použije na obsah doručený z koncového bodu CDN.

Volby optimalizace jsou navržené tak, aby využívaly osvědčené postupy pro zlepšení výkonu doručování obsahu a lepšího snižování zátěže zdroje. Volby scénáře mají vliv na výkon úpravou konfigurací pro částečné ukládání do mezipaměti, vytváření bloků dat a původní zásady opakování selhání. 

Tento článek obsahuje přehled různých funkcí optimalizace a jejich použití. Další informace o funkcích a omezeních najdete v příslušných článcích na jednotlivých typech optimalizace.

> [!NOTE]
> Když vytvoříte koncový bod CDN, **optimalizace pro** možnosti se může lišit v závislosti na typu profilu, ve kterém je koncový bod vytvořen. Azure CDN poskytovatelé používají vylepšení různými způsoby v závislosti na scénáři. 

## <a name="provider-options"></a>Možnosti zprostředkovatele

**Azure CDN Standard z profilů Microsoftu** podporuje následující optimalizace:

* [Obecné doručování webu](#general-web-delivery). Tato optimalizace se používá také pro streamování médií a stahování velkých souborů.

> [!NOTE]
> Akcelerace dynamického webu od Microsoftu se nabízí prostřednictvím [služby Azure front-dveří](../frontdoor/front-door-overview.md).

**Azure CDN Standard od Verizon** a **Azure CDN Premium od profilů Verizon** podporuje následující optimalizace:

* [Obecné doručování webu](#general-web-delivery). Tato optimalizace se používá také pro streamování médií a stahování velkých souborů.

* [Akcelerace dynamických webů](#dynamic-site-acceleration) 


**Azure CDN Standard z profilů Akamai** podporují následující optimalizace:

* [Obecné doručování webu](#general-web-delivery) 

* [Obecné streamování médií](#general-media-streaming)

* [Streamování médií videa na vyžádání](#video-on-demand-media-streaming)

* [Stažení velkých souborů](#large-file-download)

* [Akcelerace dynamických webů](#dynamic-site-acceleration) 

Microsoft doporučuje otestovat variace výkonu mezi různými poskytovateli a vybrat optimálního poskytovatele pro vaše doručení.

## <a name="select-and-configure-optimization-types"></a>Výběr a konfigurace typů optimalizace

Při vytváření koncového bodu CDN vyberte typ optimalizace, který nejlépe odpovídá scénáři a typ obsahu, který má koncový bod doručovat. 

Výchozím výběrem je **Obecné poskytování webu** . **Azure CDN Standard** můžete kdykoli aktualizovat jenom z optimalizace koncových bodů Akamai. 

Pro **Azure CDN Standard od microsoftu** **Azure CDN Standard od Verizon** a **Azure CDN Premium od Verizon** to nemůžete.

1. V **Azure CDNovém standardu z profilu Akamai** vyberte koncový bod.

    ![Výběr koncového bodu](./media/cdn-optimization-overview/01_Akamai.png)

2. V části nastavení vyberte **optimalizace**. Pak vyberte typ z rozevíracího seznamu **optimalizované pro** .

    ![Výběr optimalizace a typu](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalizace pro konkrétní scénáře

Pro jeden z těchto scénářů můžete optimalizovat koncový bod CDN. 

### <a name="general-web-delivery"></a>Obecné doručování webu

Obecná možnost optimalizace webového doručování je nejběžnější. Je navržena pro obecné optimalizace webového obsahu, jako jsou webové stránky a webové aplikace. Tato optimalizace se dá použít taky ke stažení souborů a videí.

Typický web obsahuje statický a dynamický obsah. Statický obsah zahrnuje obrázky, knihovny JavaScript a šablony stylů, které mohou být uloženy do mezipaměti a doručeny různým uživatelům. Dynamický obsah je individuální pro jednotlivé uživatele, jako jsou například položky zpráv, které jsou přizpůsobeny profilu uživatele. Dynamický obsah, jako je například obsah nákupní košíku, není uložen v mezipaměti, protože je jedinečný pro každého uživatele. Obecné doručování webu může optimalizovat celý web. 

> [!NOTE]
> Pokud používáte **Azure CDN Standard z profilu Akamai** , vyberte tento typ optimalizace, pokud je průměrná velikost souboru menší než 10 MB. V opačném případě, pokud je průměrná velikost souboru větší než 10 MB, vyberte možnost **stažení velkých souborů** z rozevíracího seznamu **optimalizované pro** .

### <a name="general-media-streaming"></a>Obecné streamování médií

Pokud potřebujete použít koncový bod pro živé streamování a streamování videa na vyžádání, vyberte možnost obecný typ Optimalizace streamování médií.

Datové proudy médií jsou citlivé na čas, protože pakety, které dorazí na klienta, jako je časté ukládání obsahu videa, můžou způsobit omezené možnosti zobrazení. Optimalizace streamování médií snižuje latenci doručování mediálního obsahu a poskytuje uživatelům hladké možnosti streamování. 

Tento scénář je běžně pro zákazníky Azure Media Service. Když použijete Azure Media Services, získáte jeden koncový bod streamování, který se dá použít pro živé streamování i pro streamování na vyžádání. V tomto scénáři se zákazníkům při změně z živého streamování na na vyžádání nemusejí přepnout na jiný koncový bod. Obecná optimalizace datových proudů médií podporuje tento typ scénáře.

Pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizon** a **Azure CDN Premium od Verizon**, použijte k doručování obecného mediálního obsahu pro streamování běžný typ optimalizace webového doručování.

Další informace o optimalizaci streamování médií najdete v tématu [Optimalizace streamování médií](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streamování médií videa na vyžádání

Optimalizace streamování médií na vyžádání vylepšuje obsah streamování videa na vyžádání. Pokud používáte koncový bod pro streamování videa na vyžádání, použijte tuto možnost.

Pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizon** a **Azure CDN Premium ze profilů Verizon** , použijte k doručování multimediálního obsahu streamování videa na vyžádání základní typ optimalizace webového doručování.

Další informace o optimalizaci streamování médií najdete v tématu [Optimalizace streamování médií](cdn-media-streaming-optimization.md).

> [!NOTE]
> Pokud koncový bod CDN primárně slouží k obsahu videa na vyžádání, použijte tento typ optimalizace. Hlavním rozdílem mezi tímto typem optimalizace a obecným typem optimalizace datových proudů médií je vypršení časového limitu pokusů o připojení. Časový limit je mnohem kratší pro práci se scénáři živého streamování.
>

### <a name="large-file-download"></a>Stažení velkých souborů

U **Azure CDN Standard od profilů Akamai** jsou velké stahování souborů optimalizované pro obsah větší než 10 MB. Pokud je průměrná velikost souboru menší než 10 MB, použijte obecné doručování webu. Pokud jsou průměrné velikosti souborů konzistentně větší než 10 MB, může být efektivnější vytvořit samostatný koncový bod pro velké soubory. Například firmware nebo aktualizace softwaru jsou obvykle velkými soubory. Aby bylo možné doručovat soubory větší než 1,8 GB, je nutná optimalizace stahování velkých souborů.

Pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizon** a **Azure CDN Premium ze profilů Verizon** , použijte k doručování velkého obsahu souborů pro stahování velký soubor pomocí obecného typu optimalizace webového doručování. Velikost souboru ke stažení není nijak omezené.

Další informace o optimalizaci velkých souborů najdete v tématu [optimalizace velkých souborů](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Akcelerace dynamických webů

 Akcelerace dynamického webu (DSA) je k dispozici pro **Azure CDN Standard od Akamai**, **Azure CDN Standard od Verizon** a **Azure CDN Premium z profilů Verizon** . Tato optimalizace zahrnuje další poplatek za použití; Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Akcelerace dynamického webu od Microsoftu se nabízí prostřednictvím [služby Azure front-dveří](../frontdoor/front-door-overview.md) , což je globální služba [libovolného vysílání](https://en.wikipedia.org/wiki/Anycast) , která využívá privátní globální síť Microsoftu k doručování úloh vaší aplikace.

DSA obsahuje různé techniky, které využívají latenci a výkon dynamického obsahu. Mezi techniky patří optimalizace směrování a sítě, optimalizace protokolu TCP a další. 

Tuto optimalizaci můžete použít k urychlení webové aplikace, která obsahuje nejrůznější odpovědi, které nejsou možné ukládat do mezipaměti. Příklady jsou výsledky hledání, transakce rezervace nebo data v reálném čase. V případě statických dat můžete nadále používat základní Azure CDN možnosti ukládání do mezipaměti. 

Další informace o akceleraci dynamického webu najdete v tématu [akcelerace dynamického webu](cdn-dynamic-site-acceleration.md).