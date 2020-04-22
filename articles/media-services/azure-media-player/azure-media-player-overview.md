---
title: Azure Media Player – přehled
description: Přečtěte si další informace o Azure Media Playeru.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726519"
---
# <a name="azure-media-player-overview"></a>Přehled Azure Media Playeru #

Azure Media Player je webový přehrávač videa vytvořený pro přehrávání mediálního obsahu ze [služby Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) v široké škále prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroje médií (MSE) a rozšíření šifrovaných médií (EME) k poskytování obohaceného adaptivního streamování.  Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá Flash a Silverlight jako záložní technologie. Bez ohledu na použitou technologii přehrávání budou mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraním API.  To umožňuje přehrávat obsah obsluhovaný mediálními službami Azure v celé řadě zařízení a prohlížečů bez dalšího úsilí.

Microsoft Azure Media Services umožňuje obsah, který se bude zobrazovat ve formátech DASH, Smooth Streaming a HLS pro přehrávání obsahu. Azure Media Player bere v úvahu tyto různé formáty a automaticky přehraje nejlepší odkaz na základě možností platformy nebo prohlížeče. Microsoft Azure Media Services také umožňuje dynamické šifrování prostředků pomocí společného šifrování (PlayReady nebo Widevine) nebo Šifrování obálek AES-128 bit. Azure Media Player umožňuje dešifrování PlayReady a AES-128 bit šifrovaný obsah, pokud je správně nakonfigurován.  Postup konfigurace tohoto obsahu naleznete v části [Chráněný obsah.](azure-media-player-protected-content.md)

Chcete-li požádat o nové funkce, poskytnout nápady nebo zpětnou vazbu, odešlete na [adresu UserVoice pro Azure Media Player](https://aka.ms/ampuservoice). Máte-li a konkrétní problémy, dotazy nebo najít ampinfo@microsoft.comnějaké chyby, napište nám řádek na .

[Zaregistrujte se](https://aka.ms/ampsignup) a nikdy nezmeškáte vydání a abyste zůstali v obraze s nejnovějšími informacemi, které azure media player nabízí.

> [!NOTE]
> Upozorňujeme, že Azure Media Player podporuje jenom datové proudy médií ze služby Azure Media Services.

## <a name="license"></a>Licence ##

Azure Media Player je licencován a podléhá podmínkám uvedeným v licenčních podmínkách pro software společnosti Microsoft pro Azure Media Player. Úplné podmínky naleznete [v licenčním souboru.](azure-media-player-license.md) Další informace naleznete v [Prohlášení o zásadách ochrany osobních údajů.](https://www.microsoft.com/en-us/privacystatement/default.aspx)

Autorská práva 2015 Microsoft Corporation.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)