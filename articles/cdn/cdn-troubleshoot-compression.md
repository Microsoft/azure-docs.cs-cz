---
title: Poradce při potížích s kompresí souborů v Azure CDN | Dokumenty společnosti Microsoft
description: Řešení problémů s kompresí souborů Azure CDN.
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: aff2dadee365fcdc7e14070714aa1d2cbba901ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476419"
---
# <a name="troubleshooting-cdn-file-compression"></a>Poradce při potížích s kompresí souborů CDN
Tento článek vám pomůže vyřešit problémy s [kompresí souborů CDN](cdn-improve-performance.md).

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete také soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na Získat **podporu**.

## <a name="symptom"></a>Příznak
Komprese pro koncový bod je povolena, ale soubory jsou vráceny nekomprimované.

> [!TIP]
> Chcete-li zkontrolovat, zda jsou soubory vráceny komprimovány, je třeba použít nástroj, jako [je Šumař](https://www.telerik.com/fiddler) nebo [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)prohlížeče .  Zkontrolujte hlavičky odpovědí HTTP vrácené s obsahem CDN uloženým v mezipaměti.  Pokud je záhlaví `Content-Encoding` pojmenované s hodnotou **gzip**, **bzip2**nebo **deflate**, obsah je komprimován.
> 
> ![Záhlaví kódování obsahu](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Příčina
Existuje několik možných příčin, včetně:

* Požadovaný obsah není vhodný pro kompresi.
* Komprese není povolena pro požadovaný typ souboru.
* Požadavek HTTP neobsahoval hlavičku požadující platný typ komprese.
* Origin posílá kusový obsah.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
> [!TIP]
> Stejně jako při nasazování nových koncových bodů trvá šíření konfigurace CDN v síti nějakou dobu.  Obvykle se změny aplikují do 90 minut.  Pokud je to poprvé, co jste nastavili kompresi pro koncový bod CDN, měli byste zvážit čekání 1-2 hodiny, abyste se ujistili, že se nastavení komprese rozšíří na POP. 
> 
> 

### <a name="verify-the-request"></a>Ověření požadavku
Nejprve bychom měli provést rychlou kontrolu příčetnosti žádosti.  K zobrazení předváděcích požadavků můžete použít [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) prohlížeče.

* Ověřte, `<endpointname>.azureedge.net`zda je žádost odesílána na adresu URL koncového bodu , nikoli na váš původ.
* Ověřte, zda požadavek obsahuje hlavičku **Accept-Encoding** a hodnota pro tuto hlavičku obsahuje **gzip**, **deflate**nebo **bzip2**.

> [!NOTE]
> **Azure CDN z akamai** profily podporují pouze **kódování gzip.**
> 
> 

![Hlavičky požadavků CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Ověření nastavení komprese (standardní profily CDN)
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Standard Azure CDN od Microsoftu**, **Azure CDN Standard od Verizonu**nebo Azure CDN Standard z profilu **Akamai.** 
> 
> 

Přejděte na svůj koncový bod na [webu Azure Portal](https://portal.azure.com) a klikněte na tlačítko **Konfigurovat.**

* Ověřte, zda je komprese povolena.
* Ověřte, zda je obsah, který má být komprimován, zahrnut do seznamu komprimovaných formátů.

![Nastavení komprese CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Ověření nastavení komprese (prémiové profily CDN)
> [!NOTE]
> Tento krok platí pouze v případě, že váš profil CDN je **Azure CDN Premium z** profilu Verizon.
> 
> 

Přejděte na svůj koncový bod na [webu Azure Portal](https://portal.azure.com) a klikněte na tlačítko **Spravovat.**  Otevře se doplňkový portál.  Najeďte na kartu **Velké HTTP** a najeďte na informační rámeček **Nastavení mezipaměti.**  Klepněte na **tlačítko Komprese**. 

* Ověřte, zda je komprese povolena.
* Ověřte, zda seznam **Typy souborů** obsahuje seznam odlišených čárkami (bez mezer) typů MIME.
* Ověřte, zda je obsah, který má být komprimován, zahrnut do seznamu komprimovaných formátů.

![Nastavení komprese PREMIUM CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Ověřte, zda je obsah uložen do mezipaměti (profily Verizon CDN)
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Standard Azure CDN od Verizonu** nebo **Azure CDN Premium z** profilu Verizon.
> 
> 

Pomocí vývojářských nástrojů prohlížeče zkontrolujte záhlaví odpovědí a ujistěte se, že je soubor uložen do mezipaměti v oblasti, kde je požadován.

* Zkontrolujte hlavičku odpovědi **serveru.**  Záhlaví by mělo mít formát **platformy (POP/Server ID)**, jak je vidět v následujícím příkladu.
* Zkontrolujte hlavičku odpovědi **X-Cache.**  Záhlaví by mělo číst **HIT**.  

![Hlavičky odpovědí CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ověřte, zda soubor splňuje požadavky na velikost (profily Verizon CDN)
> [!NOTE]
> Tento krok platí jenom v případě, že váš profil CDN je **Standard Azure CDN od Verizonu** nebo **Azure CDN Premium z** profilu Verizon.
> 
> 

Aby byl soubor způsobilý pro kompresi, musí splňovat následující požadavky na velikost:

* Větší než 128 bajtů.
* Menší než 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Zkontrolujte požadavek na původu serveru pro **via** záhlaví
Hlavička **Via** HTTP označuje webovému serveru, že požadavek je předáván proxy serverem.  Webové servery služby Microsoft IIS ve výchozím nastavení nekomprimují odpovědi, pokud požadavek obsahuje hlavičku **Via.**  Chcete-li toto chování přepsat, proveďte následující kroky:

* **IIS 6**: [Nastavení hcnokomprese forproxies="FALSE" ve vlastnostech metabáze služby IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 a nahoru**: [Nastavte **noCompressionForHttp10** a **noCompressionForProxies** na False v konfiguraci serveru](https://www.iis.net/configreference/system.webserver/httpcompression)

