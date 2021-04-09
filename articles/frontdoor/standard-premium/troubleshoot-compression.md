---
title: Řešení potíží s kompresí souborů v frontě Azure na úrovni Standard/Premium
description: Naučte se řešit problémy s kompresí souborů v frontách Azure. Tento článek se věnuje několika možným příčinám.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099270"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Řešení potíží s kompresí souborů na úrovni Standard/Premium pro Azure

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Tento článek vám pomůže vyřešit problémy s kompresí souborů na úrovni Standard/Premium pro Azure.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Příznak

Komprese pro trasu je povolená, ale soubory se vrací nekomprimované.

> [!TIP]
> Chcete-li zjistit, zda jsou soubory vraceny zkomprimované, je třeba použít nástroj, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)v prohlížeči.  Ověřte hlavičky odpovědí HTTP vrácené obsahem CDN uloženým v mezipaměti.  Pokud je hlavička s názvem `Content-Encoding` s hodnotou **gzip**, **bzip2** nebo **Deflate**, obsah se komprimuje.
> 
> ![Hlavička Content-Encoding](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Příčina

Existuje několik možných příčin, mezi které patří:

* Požadovaný obsah není vhodný pro kompresi.
* Pro požadovaný typ souboru není komprese povolena.
* Požadavek HTTP neobsahoval hlavičku požadující platný typ komprese.
* Počátek odesílá obsah v bloku dat.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

> [!TIP]
> Stejně jako při nasazování nových koncových bodů AFD změny konfigurace přes síť nějakou dobu trvat.  Změny se obvykle aplikují do 90 minut.  Pokud jste pro koncový bod CDN nastavili kompresi poprvé, měli byste zvážit počkat 1-2 hodin, než se zajistěte, aby se nastavení komprese rozšířila do bodů POP. 
> 

### <a name="verify-the-request"></a>Ověření žádosti

Nejdřív byste měli na žádost dvakrát ověřit. Pomocí **[vývojářských nástrojů](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** v prohlížeči můžete zobrazit požadavky, které se provedou.

* Ověřte, že se požadavek posílá na adresu URL vašeho koncového bodu, `<endpointname>.z01.azurefd.net` a ne na váš původ.
* Ověřte, že požadavek obsahuje hlavičku **Accept-Encoding** a že hodnota pro tuto hlavičku obsahuje **gzip**, **Deflate** nebo **bzip2**.

![Hlavičky žádosti CDN](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Ověřit nastavení komprese

V [Azure Portal](https://portal.azure.com) přejděte na koncový bod a na panelu trasy vyberte tlačítko **Konfigurovat** . Ověřte, zda je **povolena** komprese.

![Nastavení komprese CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Ověřte požadavek na zdrojovém serveru **přes hlavičku Via** .

Hlavička **Via** protokolu HTTP indikuje webovému serveru, že požadavek předává proxy server.  Webové servery Microsoft IIS ve výchozím nastavení nekomprimuje odpovědi, pokud požadavek obsahuje hlavičku **Via** .  Chcete-li toto chování přepsat, postupujte následovně:

* **IIS 6**: ve vlastnostech metabáze služby IIS nastavte HcNoCompressionForProxies = "false". Informace najdete v tématu [komprese služby IIS 6](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 a up**: v konfiguraci serveru nastavte jak **NoCompressionForHttp10** , tak **noCompressionForProxies** na *false* . Další informace najdete v tématu, [Komprese HTTP](https://www.iis.net/configreference/system.webserver/httpcompression).
