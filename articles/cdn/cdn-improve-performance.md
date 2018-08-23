---
title: Vylepšení výkonu prostřednictvím komprimace souborů v Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak zlepšit rychlost přenosu souborů a zvýšit načtení stránky výkonu prostřednictvím komprimace souborů v Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: magattus
ms.openlocfilehash: c3a20bd4fa1cccdca7cba0de52620f09fe01abc5
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054630"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Vylepšení výkonu prostřednictvím komprimace souborů v Azure CDN
Komprese souborů je jednoduchou a efektivní způsob zlepšit rychlost přenosu souborů a zvýšení výkonu načítání stránek díky snížení velikosti souboru před odesláním ze serveru. Komprese můžete snížit náklady na šířku pásma a poskytnout pohotovější prostředí pro vaše uživatele.

Existují dva způsoby, jak povolit kompresi souborů:

- Povolte kompresi na původním serveru. V tomto případě Azure CDN předá komprimované soubory a poskytuje je na klienty, kteří se o ně požádat.
- Povolit kompresi přímo na serverech CDN POP (*komprese průběžně*). V takovém případě CDN komprimuje soubory a slouží pro koncového uživatele, i v případě, že nebyly zkomprimovány server původu.

> [!IMPORTANT]
> Změny konfigurace Azure CDN může trvat nějakou dobu šířit přes síť: 
- Šíření profilů **Azure CDN Standard od Microsoftu** trvá většinou 10 minut. 
- V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
- V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
>
> Pokud nastavujete komprese poprvé pro koncový bod CDN, zvažte, zda nevyčkat 1 až 2 hodin, než je řešit Ujistěte se, že jste nastavení komprese rozšíří do bodů POP.
> 
> 

## <a name="enabling-compression"></a>Povolení komprese
CDN úrovně standard a premium poskytují stejné funkce kompresi, ale uživatelské rozhraní se liší. Další informace o rozdílech mezi CDN úrovně standard a premium najdete v tématu [přehled sítě CDN Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standardní profily CDN 
> [!NOTE]
> Tato část se týká **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Verizonu**, a **Azure CDN Standard od Akamai** profily.
> 
> 

1. Na stránce profilu CDN vyberte koncový bod CDN, které chcete spravovat.
   
    ![Koncové body profilu CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    Otevře se stránka koncového bodu CDN.
2. Vyberte **komprese**.

    ![Výběr CDN komprese](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    Otevře se stránka komprese.
3. Vyberte **na** zapnutí komprese.
   
    ![Možnosti komprese souborů CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Použít výchozí typ MIME, nebo upravit seznam přidáním nebo odebráním typy MIME.
   
   > [!TIP]
   > I když je možné, není doporučeno použít kompresi komprimovaných formátů. Například ZIP, MP3, MP4 nebo JPG.
   > 
   
   > [!NOTE]
   > Změna výchozí seznam typů MIME se aktuálně nepodporuje v Azure CDN Standard od společnosti Microsoft.
   > 
 
5. Po provedení změny, vyberte **Uložit**.

### <a name="premium-cdn-profiles"></a>Profily CDN úrovně Premium
> [!NOTE]
> Tato část se týká pouze **Azure CDN Premium od Verizonu** profily.
> 

1. Na stránce profilu CDN vyberte **spravovat**.
   
    ![Spravovat CDN vyberte](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **HTTP velké** kartu a pak najeďte myší **nastavení mezipaměti** Kontextová nabídka. Vyberte **komprese**.

    ![Výběr CDN komprese](./media/cdn-file-compression/cdn-compress-select.png)
   
    Zobrazí se možnosti komprese.
   
    ![Možnosti komprese souborů CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Povolit kompresi tak, že vyberete **povolena komprese**. Zadejte typy MIME chcete komprimovat jako seznam oddělený čárkami (bez mezer) **typy souborů** pole.
   
   > [!TIP]
   > I když je možné, není doporučeno použít kompresi komprimovaných formátů. Například ZIP, MP3, MP4 nebo JPG.
   > 
    
4. Po provedení změny, vyberte **aktualizace**.

## <a name="compression-rules"></a>Komprese pravidla

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard od společnosti Microsoft profily

Pro **Azure CDN Standard od společnosti Microsoft** profily, všechny soubory jsou způsobilé pro kompresi. Však musí být soubor typu MIME, která byla [nakonfigurované pro kompresi](#enabling-compression).

Tyto profily podporují následující kódování komprese:
- GZIP (GNU zip)
- brotli 
 
Pokud požadavek podporuje více než jeden typ komprese, tyto typy komprese přednost před brotli komprese.

Když žádost pro určitý prostředek určuje kompresi gzip a výsledky požadavku v neúspěšnému přístupu do mezipaměti, Azure CDN provádí kompresi gzip majetku přímo na serveru POP. Následně komprimovaný soubor se načítají z mezipaměti.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN od Verizonu profily

Pro **Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** profilů, pouze vhodné soubory jsou komprimované. Způsobilé pro kompresi, musíte soubor:
- Být větší než 128 bajtů
- Být menší než 1 MB
 
Tyto profily podporují následující kódování komprese:
- GZIP (GNU zip)
- DEFLATE
- bzip2
- brotli 
 
Pokud požadavek podporuje více než jeden typ komprese, tyto typy komprese přednost před brotli komprese.

Když žádost pro určitý prostředek určuje brotli komprese (hlavička HTTP je `Accept-Encoding: br`) a výsledkem žádosti Neúspěšné přístupy do mezipaměti, Azure CDN provádí kompresi brotli majetku přímo na serveru POP. Následně komprimovaný soubor se načítají z mezipaměti.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard od Akamai profily

Pro **Azure CDN Standard od Akamai** profily, všechny soubory jsou způsobilé pro kompresi. Však musí být soubor typu MIME, která byla [nakonfigurované pro kompresi](#enabling-compression).

Tyto profily podporovat gzip komprese kódování pouze. Koncový bod profilu požádá o soubor kódováním gzip, je vždy požadovaný z původního zdroje, bez ohledu na to požadavek klienta. 

## <a name="compression-behavior-tables"></a>Komprese chování tabulky
Následující tabulky popisují komprese chování Azure CDN pro každý scénář:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprese je zakázán nebo jej nelze komprese souboru
| Klient vyžádal formátu (prostřednictvím hlavičky Accept-Encoding) | Formát souboru do mezipaměti | CDN odpověď do klienta | Poznámky k&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimované |Komprimované |Komprimované | |
| Komprimované |Nekomprimovaný |Nekomprimovaný | |
| Komprimované |Není v mezipaměti |Komprimované nebo nekomprimovaný |Původní odpovědi určuje, zda CDN provádí kompresi. |
| Nekomprimovaný |Komprimované |Nekomprimovaný | |
| Nekomprimovaný |Nekomprimovaný |Nekomprimovaný | |
| Nekomprimovaný |Není v mezipaměti |Nekomprimovaný | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Je povolena komprese a podmínky pro kompresi souborů
| Klient vyžádal formátu (prostřednictvím hlavičky Accept-Encoding) | Formát souboru do mezipaměti | CDN odpověď do klienta | Poznámky |
| --- | --- | --- | --- |
| Komprimované |Komprimované |Komprimované |CDN videoúlohy mezi podporovaných formátů. |
| Komprimované |Nekomprimovaný |Komprimované |CDN provádí kompresi. |
| Komprimované |Není v mezipaměti |Komprimované |Pokud zdroj vrátí soubor nekomprimované, kterým CDN řeší kompresi. <br/>**Azure CDN od Verizonu** předá nekomprimovaného souboru na první požadavek a pak zkomprimuje a mezipaměti pro následné požadavky. <br/>Soubory s `Cache-Control: no-cache` nikdy jsou komprimované záhlaví. |
| Nekomprimovaný |Komprimované |Nekomprimovaný |Kterým CDN řeší dekompresi. |
| Nekomprimovaný |Nekomprimovaný |Nekomprimovaný | |
| Nekomprimovaný |Není v mezipaměti |Nekomprimovaný | |

## <a name="media-services-cdn-compression"></a>CDN komprese služby Media Services
Pro koncové body povolené pro Media Services CDN datových proudů komprese povolena ve výchozím nastavení pro následující typy MIME: 
- application/vnd.ms-sstr+xml 
- aplikace/dash + xml
- application/vnd.Apple.mpegurl
- aplikace/f4m + xml 

## <a name="see-also"></a>Další informace najdete v tématech
* [Řešení potíží s kompresí souborů v síti CDN](cdn-troubleshoot-compression.md)    

