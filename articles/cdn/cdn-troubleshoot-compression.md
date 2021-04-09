---
title: Řešení potíží s kompresí souborů v Azure CDN | Microsoft Docs
description: Naučte se řešit problémy s kompresí souborů v Azure Content Delivery Network. Tento článek se věnuje několika možným příčinám.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996147"
---
# <a name="troubleshooting-cdn-file-compression"></a>Poradce při potížích s kompresí souborů CDN
Tento článek vám pomůže při řešení potíží s [kompresí souborů CDN](cdn-improve-performance.md).

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a ve Stack Overflowch fórech](https://azure.microsoft.com/support/forums/). Případně můžete také použít incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="symptom"></a>Příznak
Komprese pro koncový bod je povolená, ale soubory se vrací nekomprimované.

> [!TIP]
> Chcete-li zjistit, zda jsou soubory vraceny zkomprimované, je třeba použít nástroj, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)v prohlížeči.  Ověřte hlavičky odpovědí HTTP vrácené obsahem CDN uloženým v mezipaměti.  Pokud je hlavička s názvem `Content-Encoding` s hodnotou **gzip**, **bzip2** nebo **Deflate**, obsah se komprimuje.
> 
> ![Hlavička Content-Encoding](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, mezi které patří:

* Požadovaný obsah není způsobilý pro kompresi.
* Pro požadovaný typ souboru není komprese povolena.
* Požadavek HTTP neobsahoval hlavičku požadující platný typ komprese.
* Počátek odesílá obsah v bloku dat.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!TIP]
> Stejně jako při nasazování nových koncových bodů se v síti dokončí Změna konfigurace CDN v průběhu sítě.  Změny se obvykle aplikují do 90 minut.  Pokud jste pro koncový bod CDN nastavili kompresi poprvé, měli byste zvážit počkat 1-2 hodin, než se zajistěte, aby se nastavení komprese rozšířila do bodů POP. 
> 
> 

### <a name="verify-the-request"></a>Ověření žádosti
Nejdřív byste měli provést rychlou správnosti kontrolu na žádosti.  Pomocí [vývojářských nástrojů](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) v prohlížeči můžete zobrazit požadavky, které se provedou.

* Ověřte, že se požadavek posílá na adresu URL vašeho koncového bodu, `<endpointname>.azureedge.net` a ne na váš původ.
* Ověřte, že požadavek obsahuje hlavičku **Accept-Encoding** a že hodnota pro tuto hlavičku obsahuje **gzip**, **Deflate** nebo **bzip2**.

> [!NOTE]
> **Azure CDN ze profilů Akamai** podporují pouze kódování **gzip** .
> 
> 

![Hlavičky žádosti CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Ověřit nastavení komprese (standardní profily CDN)
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizon** nebo **Azure CDN Standard od profilu Akamai** . 
> 
> 

V [Azure Portal](https://portal.azure.com) přejděte na koncový bod a klikněte na tlačítko **Konfigurovat** .

* Ověřte, zda je povolena komprese.
* Ověřte, že typ MIME obsahu, který se má komprimovat, je zahrnutý v seznamu komprimovaných formátů.

![Nastavení komprese CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Ověřit nastavení komprese (profily CDN úrovně Premium)
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Azure CDN Premium z profilu Verizon** .
> 
> 

V [Azure Portal](https://portal.azure.com) přejděte na koncový bod a klikněte na tlačítko **Spravovat** .  Otevře se doplňkový portál.  Najeďte myší na **velkou kartu http** a pak najeďte myší na informační rámeček **nastavení mezipaměti** .  Klikněte na **Komprese**. 

* Ověřte, zda je povolena komprese.
* Ověřte, že seznam **typy souborů** obsahuje seznam oddělený čárkami (bez mezer) typů MIME.
* Ověřte, že typ MIME obsahu, který se má komprimovat, je zahrnutý v seznamu komprimovaných formátů.

![Nastavení komprese pro CDN Premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Ověřte, jestli je obsah uložený v mezipaměti (profily CDN Verizon).
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Azure CDN Standard od Verizon** nebo **Azure CDN Premium od profilu Verizon** .
> 
> 

Pomocí vývojářských nástrojů v prohlížeči zkontrolujte hlavičky odpovědí a ujistěte se, že je soubor uložen v mezipaměti v oblasti, kde se požaduje.

* Podívejte se na hlavičku odpovědi **serveru** .  Hlavička by měla mít formátovou **platformu (pop/server ID)**, jak je vidět v následujícím příkladu.
* Podívejte se na hlavičku odpovědi **X-cache** .  Hlavička by měla být **načtena**.  

![Hlavičky odpovědí CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ověřte, že soubor splňuje požadavky na velikost (profily CDN Verizon).
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Azure CDN Standard od Verizon** nebo **Azure CDN Premium od profilu Verizon** .
> 
> 

Aby měl soubor nárok na kompresi, musí splňovat následující požadavky na velikost:

* Větší než 128 bajtů (Content-Length: 128)
* Menší než 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Ověřte požadavek na zdrojovém serveru **přes hlavičku Via** .
Hlavička **Via** protokolu HTTP indikuje webovému serveru, že požadavek předává proxy server.  Webové servery Microsoft IIS ve výchozím nastavení nekomprimuje odpovědi, pokud požadavek obsahuje hlavičku **Via** .  Chcete-li toto chování přepsat, postupujte následovně:

* **IIS 6**: [ve vlastnostech metabáze služby IIS nastavte HCNOCOMPRESSIONFORPROXIES = "false"](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)) .
* **IIS 7 a up**: [v konfiguraci serveru nastavte jak **noCompressionForHttp10** , tak **noCompressionForProxies** na false](https://www.iis.net/configreference/system.webserver/httpcompression) .

