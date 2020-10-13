---
title: Zvýšení výkonu komprimací souborů v Azure CDN | Microsoft Docs
description: Naučte se zlepšit rychlost přenosu souborů a zvýšit výkon načítání stránek komprimací souborů v Azure CDN.
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
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 3b8ce5b82b7d2022fd7feea1cd9efe8d524ee6a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358283"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Vylepšení výkonu prostřednictvím komprimace souborů v Azure CDN
Komprese souborů představuje jednoduchou a efektivní metodu pro zlepšení rychlosti přenosu souborů a zvýšení výkonu načítání stránek tím, že se velikost souboru před odesláním ze serveru zmenší. Komprese souborů může snížit náklady na šířku pásma a zajistit pro uživatele lépe reagující prostředí.

Existují dva způsoby, jak povolit kompresi souborů:

- Povolte kompresi na zdrojovém serveru. V takovém případě Azure CDN projde komprimovanými soubory a doručuje je klientům, kteří je požadují.
- Povolit kompresi přímo na serverech POP CDN (*Komprese za běhu*). V tomto případě CDN komprimuje soubory a poskytuje je koncovým uživatelům, i když je nekomprimuje zdrojový server.

> [!IMPORTANT]
> Provedení změn konfigurace Azure CDN může trvat delší dobu, než se bude síť šířit: 
> - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
> 
> Pokud pro koncový bod CDN nastavujete kompresi poprvé, zvažte možnost počkat 1-2 hodin, než se pustíte do řešení potíží, abyste zajistili, že se nastavení komprese rozšíří do bodů POP.

## <a name="enabling-compression"></a>Povolení komprese
Úrovně Standard a Premium CDN poskytují stejnou funkci komprese, ale uživatelské rozhraní se liší. Další informace o rozdílech mezi úrovněmi CDN úrovně Standard a Premium najdete v tématu [přehled Azure CDN](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Profily Standard CDN 
> [!NOTE]
> Tato část se vztahuje na **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizon**a **Azure CDN Standard od profilů Akamai** .
> 
> 

1. Na stránce profil CDN vyberte koncový bod CDN, který chcete spravovat.

    ![Koncové body profilu CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Otevře se stránka koncový bod CDN.
2. Vyberte **Komprese**.

    ![Snímek obrazovky zobrazuje koncový bod s vybranou kompresí z nabídky portál.](./media/cdn-file-compression/cdn-compress-select-std.png)

    Otevře se stránka komprese.
3. Výběrem **zapnuto** zapnete kompresi.

    ![Snímek obrazovky ukazuje zapnutí komprese.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Použijte výchozí typy MIME nebo upravte seznam přidáním nebo odebráním typů MIME.

   > [!TIP]
   > I když je to možné, nedoporučujeme používat kompresi do komprimovaných formátů. Například ZIP, MP3, MP4 nebo JPG.
   > 

   > [!NOTE]
   > Změna výchozího seznamu typů MIME v současnosti není v Azure CDN Standard od Microsoftu podporovaná.
   > 

5. Po provedení změn vyberte **Uložit**.

### <a name="premium-cdn-profiles"></a>Profily CDN úrovně Premium
> [!NOTE]
> Tato část se vztahuje jenom na **Azure CDN Premium z profilů Verizon** .
> 

1. Na stránce profil CDN vyberte **Spravovat**.

    ![Správa CDN – výběr](./media/cdn-file-compression/cdn-manage-btn.png)

    Otevře se portál pro správu CDN.
2. Najeďte myší na **velkou kartu http** a pak najeďte myší na informační rámeček **nastavení mezipaměti** . Vyberte **Komprese**.

    ![Výběr komprese CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Zobrazí se možnosti komprese.

    ![Možnosti komprese souborů CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Povolte kompresi výběrem **Komprese povolena**. V poli **typy souborů** zadejte typy MIME, které chcete zkomprimovat jako seznam oddělený čárkami (bez mezer).

   > [!TIP]
   > I když je to možné, nedoporučujeme používat kompresi do komprimovaných formátů. Například ZIP, MP3, MP4 nebo JPG.
   > 

4. Po provedení změn vyberte **aktualizovat**.

## <a name="compression-rules"></a>Pravidla komprese

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard z profilů Microsoftu

Pro **Azure CDN Standard od profilů Microsoftu** se komprimují jenom vhodné soubory. Aby měl soubor nárok na kompresi, musí mít následující:
- Musí být typu MIME, který byl [nakonfigurován pro kompresi](#enabling-compression).
- Být větší než 1 KB
- Být menší než 8 MB

Tyto profily podporují následující kódování komprese:
- gzip (GNU zip)
- brotli 

Pokud požadavek podporuje více než jeden typ komprese, má komprese brotli přednost.

Když požadavek na prostředek určuje kompresi gzip a výsledkem požadavku dojde k neúspěšnému přihlášení do mezipaměti, Azure CDN provádí kompresi assetu pomocí metody gzip přímo na serveru POP. Následně bude komprimovaný soubor obsluhován z mezipaměti.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN z profilů Verizon

Pro **Azure CDN Standard od** profilů Verizon se z Verizon a **Azure CDN Premium** dají komprimovat jenom vhodné soubory. Aby měl soubor nárok na kompresi, musí mít následující:
- Být větší než 128 bajtů
- Být menší než 3 MB

Tyto profily podporují následující kódování komprese:
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

Pokud požadavek podporuje více než jeden typ komprese, mají tyto typy komprese přednost před brotli kompresí.

Když požadavek na určitý prostředek určuje kompresi brotli (hlavička protokolu HTTP je `Accept-Encoding: br` ) a výsledkem požadavku není Neúspěšné přístupy do mezipaměti, Azure CDN provádí brotli kompresi assetu přímo na serveru POP. Následně bude komprimovaný soubor obsluhován z mezipaměti.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard z profilů Akamai

Pro **Azure CDN Standard z profilů Akamai** mají všechny soubory nárok na kompresi. Soubor musí však být typu MIME, který byl [nakonfigurován pro kompresi](#enabling-compression).

Tyto profily podporují pouze kompresní kódování gzip. Když koncový bod profilu požaduje soubor s kódováním gzip, vždy se vyžádá od počátku, bez ohledu na požadavek klienta. 

## <a name="compression-behavior-tables"></a>Tabulky chování komprese
Následující tabulky popisují Azure CDN chování komprese pro každý scénář:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprese je zakázaná nebo soubor nemá nárok na kompresi.
| Formát požadovaný klientem (přes Accept-Encoding záhlaví) | Formát souboru v mezipaměti | Odpověď CDN klientovi | &nbsp; &nbsp; &nbsp; &nbsp; Poznámky &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimované |Komprimované |Komprimované | |
| Komprimované |Nekomprimovaných |Nekomprimovaných | |
| Komprimované |Neuložený v mezipaměti |Komprimovaná nebo nekomprimovaná |Počáteční odpověď určuje, jestli CDN provádí kompresi. |
| Nekomprimovaných |Komprimované |Nekomprimovaných | |
| Nekomprimovaných |Nekomprimovaných |Nekomprimovaných | |
| Nekomprimovaných |Neuložený v mezipaměti |Nekomprimovaných | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprese je povolena a soubor je vhodný pro kompresi
| Formát požadovaný klientem (přes Accept-Encoding záhlaví) | Formát souboru v mezipaměti | Odpověď CDN klientovi | Poznámky |
| --- | --- | --- | --- |
| Komprimované |Komprimované |Komprimované |Překóduje síť CDN mezi podporovanými formáty. |
| Komprimované |Nekomprimovaných |Komprimované |CDN provede kompresi. |
| Komprimované |Neuložený v mezipaměti |Komprimované |CDN provede kompresi, pokud zdroj vrátí nekomprimovaný soubor. <br/>**Azure CDN z Verizon** předá tento nekomprimovaný soubor na první požadavek a pak zkomprimuje a uloží soubor do mezipaměti pro následné požadavky. <br/>Soubory s `Cache-Control: no-cache` hlavičkou nejsou nikdy komprimovány. |
| Nekomprimovaných |Komprimované |Nekomprimovaných |CDN provádí dekompresi. |
| Nekomprimovaných |Nekomprimovaných |Nekomprimovaných | |
| Nekomprimovaných |Neuložený v mezipaměti |Nekomprimovaných | |

## <a name="media-services-cdn-compression"></a>Komprese CDN Media Services
U koncových bodů povolených pro Media Services CDN streaming je komprese ve výchozím nastavení povolená pro následující typy MIME: 
- application/vnd. MS-sstr + XML 
- Application/pomlčka + XML
- Adresa URL aplikace/vnd.apple.mpeg
- Application/f4m + XML 

## <a name="see-also"></a>Viz také
* [Poradce při potížích s kompresí souborů CDN](cdn-troubleshoot-compression.md)    

