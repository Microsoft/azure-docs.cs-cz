---
title: Řešení potíží s kompresí souborů v Azure CDN | Dokumentace Microsoftu
description: Řešení potíží s kompresí souborů Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b885098ff0efeb4d723cbaaac46fbb57cb40f2ea
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918820"
---
# <a name="troubleshooting-cdn-file-compression"></a>Poradce při potížích s kompresí souborů CDN
V tomto článku vám pomůžou s řešením problémů s [kompresí souborů CDN](cdn-improve-performance.md).

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na tlačítko **získat podporu**.

## <a name="symptom"></a>Příznak
Je povolena komprese pro koncový bod služby, ale soubory se vrací nekomprimované.

> [!TIP]
> Pokud chcete zkontrolovat, jestli jsou soubory vracených komprimovaný, budete muset použít nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) vašeho prohlížeče [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Hlavičky odpovědi kontrola HTTP vrátil v mezipaměti CDN obsahu.  Pokud je záhlaví s názvem `Content-Encoding` s hodnotou **gzip**, **bzip2**, nebo **deflate**, váš obsah je komprimován.
> 
> ![Hlavička kódování obsahu](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, včetně:

* Požadovaný obsah není vhodné pro kompresi.
* Pro požadovaný typ souboru není povolená komprese.
* Požadavek HTTP neobsahuje záhlaví požaduje typ platný komprese.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!TIP]
> Stejně jako u nasazení nové koncové body, změny konfigurace CDN trvat nějakou dobu šířit přes síť.  Změny jsou obvykle použity během 90 minut.  Pokud je to poprvé, které jste nastavili komprese pro koncový bod CDN, měli byste zvážit čekání 1 – 2 hodiny opravdu komprese, které mají nastavení rozšíří do bodů POP. 
> 
> 

### <a name="verify-the-request"></a>Ověřit požadavek
Nejprve jsme měli dělat kontrolu správnosti rychlý požadavek.  Můžete použít v prohlížeči [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) zobrazíte požadavky prováděné.

* Ověření požadavku je odesíláno na adresu URL koncového bodu `<endpointname>.azureedge.net`a ne váš původní název.
* Ověřit, žádost obsahuje **Accept-Encoding** hlavičku a hodnotu této hlavičky obsahuje **gzip**, **deflate**, nebo **bzip2**.

> [!NOTE]
> **Azure CDN od Akamai** profily podporují jenom **gzip** kódování.
> 
> 

![Hlavičky žádosti CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Ověřte nastavení komprese (standardní profily CDN)
> [!NOTE]
> Tento krok platí jenom v případě, že je váš profil CDN **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Verizonu**, nebo **Azure CDN Standard od Akamai** profilu. 
> 
> 

Přejděte na koncový bod v [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **konfigurovat** tlačítko.

* Ověřte, zda je povolena komprese.
* Ověřte, zda typ MIME pro obsah, aby se komprimoval je součástí seznamu komprimovaných formátů.

![Nastavení komprese CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Ověřte nastavení komprese (profily CDN úrovně Premium)
> [!NOTE]
> Tento krok platí jenom v případě, že je váš profil CDN **Azure CDN Premium od Verizonu** profilu.
> 
> 

Přejděte na koncový bod v [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **spravovat** tlačítko.  Doplňkový portál se otevře.  Najeďte myší **HTTP velké** kartu a pak najeďte myší **nastavení mezipaměti** Kontextová nabídka.  Klikněte na tlačítko **komprese**. 

* Ověřte, zda je povolena komprese.
* Ověřte, **typy souborů** seznam obsahuje seznam oddělený čárkami (bez mezer), které typy MIME.
* Ověřte, zda typ MIME pro obsah, aby se komprimoval je součástí seznamu komprimovaných formátů.

![Nastavení komprese premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Ověřte, zda že je obsah uložený v mezipaměti (profily CDN Verizonu)
> [!NOTE]
> Tento krok platí jenom v případě, že je váš profil CDN **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu** profilu.
> 
> 

Používání nástrojů pro vývojáře v prohlížeči, zkontrolujte hlavičky odpovědi k zajištění, že soubor je uložen do mezipaměti v oblasti, ve kterém jsou požadovány.

* Zkontrolujte, **Server** hlavičky odpovědi.  Záhlaví by měl mít formát **platformu (ID serveru/POP)**, jak je znázorněno v následujícím příkladu.
* Zkontrolujte **X-Cache** hlavičky odpovědi.  Údajů byste si přečíst hlavičku **PŘÍSTUPŮ**.  

![Hlavičky odpovědi CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ověřte, že soubor splňuje požadavky na velikost (profily CDN Verizonu)
> [!NOTE]
> Tento krok platí jenom v případě, že je váš profil CDN **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu** profilu.
> 
> 

Způsobilé pro kompresi, soubor musí splňovat následující požadavky na velikost:

* Větší než 128 bajtů.
* Menší než 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrola žádosti o na původním serveru **prostřednictvím** záhlaví
**Prostřednictvím** hlavičky protokolu HTTP označuje na webový server, že žádost je předáván podle proxy serveru.  Webové servery Microsoft IIS ve výchozím nastavení Nekomprimovat odpovědi obsahuje žádost **prostřednictvím** záhlaví.  Chcete-li přepsat toto chování, postupujte takto:

* **IIS 6**: [Nastavte HcNoCompressionForProxies = "FALSE" ve vlastnostech metabáze služby IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **Služba IIS 7 a vyšší**: [Nastavte **noCompressionForHttp10** a **noCompressionForProxies** na hodnotu False v konfiguraci serveru](http://www.iis.net/configreference/system.webserver/httpcompression)

