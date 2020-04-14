---
title: Zvýšení výkonu kompresí souborů v Azure CDN | Dokumenty společnosti Microsoft
description: Zjistěte, jak zvýšit rychlost přenosu souborů a zvýšit výkon načítání stránek komprimací souborů v Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7124dd40d4510674014afe012a8f40dcb5bb6153
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253760"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Vylepšení výkonu prostřednictvím komprimace souborů v Azure CDN
Komprese souborů je jednoduchá a účinná metoda pro zvýšení rychlosti přenosu souborů a zvýšení výkonu načítání stránky snížením velikosti souboru před odesláním ze serveru. Komprese souborů může snížit náklady na šířku pásma a poskytnout uživatelům citlivější prostředí.

Existují dva způsoby, jak povolit kompresi souborů:

- Povolte kompresi na původním serveru. V takovém případě Azure CDN předá komprimované soubory a doručuje je klientům, kteří o ně požadují.
- Povolit kompresi přímo na CDN POP serverech *(komprese za chodu).* V tomto případě CDN komprimuje soubory a slouží je koncovým uživatelům, i když nebyly komprimovány zdrojovým serverem.

> [!IMPORTANT]
> Změny konfigurace Azure CDN může nějakou dobu trvat, než se rozšíří v síti: 
> - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
> 
> Pokud pro koncový bod CDN nastavujete kompresi poprvé, zvažte čekání 1 až 2 hodiny před odstraňováním potíží, abyste zajistili, že se nastavení komprese rozšíří do bodů POP.

## <a name="enabling-compression"></a>Povolení komprese
Standardní a prémiové úrovně CDN poskytují stejné funkce komprese, ale uživatelské rozhraní se liší. Další informace o rozdílech mezi standardními a prémiovými úrovněmi CDN najdete [v tématu Přehled Azure CDN](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standardní profily CDN 
> [!NOTE]
> Tato část se vztahuje na **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizonu**a Azure CDN Standard z profilů **Akamai.**
> 
> 

1. Na stránce profilu CDN vyberte koncový bod CDN, který chcete spravovat.

    ![Koncové body profilu CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Otevře se stránka koncového bodu CDN.
2. Vyberte **Kompresi**.

    ![Výběr komprese CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    Otevře se stránka komprese.
3. Výběrem **možnosti Zapnuto** zapnete kompresi.

    ![Volby komprese souborů CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Použijte výchozí typy MIME nebo seznam upravte přidáním nebo odebráním typů MIME.

   > [!TIP]
   > I když je to možné, nedoporučuje se použít kompresi na komprimované formáty. Například ZIP, MP3, MP4 nebo JPG.
   > 

   > [!NOTE]
   > Změna výchozího seznamu typů MIME není aktuálně podporována ve standardu Azure CDN od Microsoftu.
   > 

5. Po provedení změn vyberte **Uložit**.

### <a name="premium-cdn-profiles"></a>Prémiové profily CDN
> [!NOTE]
> Tato část se vztahuje jenom pro **Azure CDN Premium od Verizon** profily.
> 

1. Na stránce profilu CDN vyberte **Spravovat**.

    ![Výběr správy CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Velké HTTP** a najeďte na informační rámeček **Nastavení mezipaměti.** Vyberte **Kompresi**.

    ![Výběr komprese CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Zobrazí se možnosti komprese.

    ![Volby komprese souborů CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Povolte kompresi výběrem **možnosti Povoleno komprese**. Do pole **Typy souborů** zadejte typy MIME, které chcete komprimovat jako seznam oddělený čárkami (bez mezer).

   > [!TIP]
   > I když je to možné, nedoporučuje se použít kompresi na komprimované formáty. Například ZIP, MP3, MP4 nebo JPG.
   > 

4. Po provedení změn vyberte **Aktualizovat**.

## <a name="compression-rules"></a>Pravidla komprese

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard od Microsoftu profily

Pro **profily Azure CDN Standard od Microsoftu** jsou komprimovány pouze způsobilé soubory. Aby byl soubor způsobilý pro kompresi, musí:
- Být typu MIME, který byl [nakonfigurován pro kompresi](#enabling-compression).
- Být větší než 1 KB
- Být menší než 8 MB

Tyto profily podporují následující kódování komprese:
- gzip (GNU zip)
- brotli 

Pokud požadavek podporuje více než jeden typ komprese, má přednost komprese brotli.

Když požadavek na prostředek určuje kompresi gzip a výsledkem požadavku dojde k chybě mezipaměti, Azure CDN provede kompresi gzip datového zdroje přímo na serveru POP. Poté je komprimovaný soubor obsluhován z mezipaměti.

### <a name="azure-cdn-from-verizon-profiles"></a>Profily Azure CDN od Verizonu

Pro **Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** jsou komprimované pouze způsobilé soubory. Aby byl soubor způsobilý pro kompresi, musí:
- Být větší než 128 bajtů
- Být menší než 3 MB

Tyto profily podporují následující kódování komprese:
- gzip (GNU zip)
- Deflaci
- bzip2
- brotli 

Pokud požadavek podporuje více než jeden typ komprese, mají tyto typy komprese přednost před kompresí brotli.

Když požadavek na prostředek určuje kompresi brotli (je hlavička HTTP ) a výsledkem požadavku je `Accept-Encoding: br`chybět v mezipaměti, Azure CDN provede brotlisku datového zdroje přímo na serveru POP. Poté je komprimovaný soubor obsluhován z mezipaměti.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard z profilů Akamai

Pro **Azure CDN Standard z akamai** profily všechny soubory jsou způsobilé pro kompresi. Soubor však musí být typu MIME, který byl [nakonfigurován pro kompresi](#enabling-compression).

Tyto profily podporují pouze kódování komprese gzip. Když koncový bod profilu požaduje soubor zakódovaný gzip, je vždy požadován od původu, bez ohledu na požadavek klienta. 

## <a name="compression-behavior-tables"></a>Tabulky chování komprese
Následující tabulky popisují chování komprese Azure CDN pro každý scénář:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprese je zakázána nebo soubor není způsobilý pro kompresi.
| Formát požadovaný klientem (přes hlavičku Accept-Encoding) | Formát souboru uloženého v mezipaměti | Odpověď CDN klientovi | &nbsp; &nbsp; &nbsp; Poznámky&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimované |Komprimované |Komprimované | |
| Komprimované |Nekomprimované |Nekomprimované | |
| Komprimované |Není uloženo v mezipaměti |Komprimované nebo nekomprimované |Původní odpověď určuje, zda CDN provádí kompresi. |
| Nekomprimované |Komprimované |Nekomprimované | |
| Nekomprimované |Nekomprimované |Nekomprimované | |
| Nekomprimované |Není uloženo v mezipaměti |Nekomprimované | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprese je povolena a soubor je způsobilý pro kompresi.
| Formát požadovaný klientem (přes hlavičku Accept-Encoding) | Formát souboru uloženého v mezipaměti | Odpověď CDN klientovi | Poznámky |
| --- | --- | --- | --- |
| Komprimované |Komprimované |Komprimované |CDN překóduje mezi podporovanými formáty. |
| Komprimované |Nekomprimované |Komprimované |CDN provádí kompresi. |
| Komprimované |Není uloženo v mezipaměti |Komprimované |CDN provede kompresi, pokud počátek vrátí nekomprimovaný soubor. <br/>**Azure CDN od společnosti Verizon** předá nekomprimovaný soubor na první požadavek a pak komprimuje a ukládá soubor do mezipaměti pro následné požadavky. <br/>Soubory s `Cache-Control: no-cache` hlavičkou nejsou nikdy komprimovány. |
| Nekomprimované |Komprimované |Nekomprimované |CDN provádí dekompresi. |
| Nekomprimované |Nekomprimované |Nekomprimované | |
| Nekomprimované |Není uloženo v mezipaměti |Nekomprimované | |

## <a name="media-services-cdn-compression"></a>Komprese CDN mediálních služeb
U koncových bodů povolených pro streamování CDN služby Media Services je komprese ve výchozím nastavení povolena pro následující typy MIME: 
- aplikace/vnd.ms-sstr+xml 
- aplikace/pomlčka+xml
- aplikace/vnd.apple.mpegurl
- aplikace/f4m+xml 

## <a name="see-also"></a>Viz také
* [Poradce při potížích s kompresí souborů CDN](cdn-troubleshoot-compression.md)    

