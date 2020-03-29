---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943808"
---
Manipulace s komprimovaným zvukem je realizována pomocí [GStreameru](https://gstreamer.freedesktop.org). Z licenčních důvodů gstreamer binární soubory nejsou kompilovány a propojeny s sadou Speech SDK. Místo toho musí být vytvořena obálková knihovna obsahující tyto funkce a dodána s aplikacemi pomocí sady SDK.

Chcete-li vytvořit tuto knihovnu obálky, nejprve stáhněte a nainstalujte [sadu GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Potom stáhněte projekt **Xcode** pro [knihovnu obálky](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Otevřete projekt v **Xcode** a sestavte ho pro obecný cíl **zařízení iOS** – *nebude* fungovat jeho sestavení pro konkrétní cíl.

Krok sestavení vygeneruje balíček dynamického frameworku s dynamickou `GStreamerWrapper.framework`knihovnou pro všechny potřebné architektury s názvem .

Tento rámec musí být součástí všech aplikací, které používají komprimované zvukové proudy se sadou SDK služby Rozpoznávání řeči.

Chcete-li to provést, použijte v projektu **Xcode** následující nastavení:

1. `GStreamerWrapper.framework` Zkopírujte právě vytvořené sady a rámec sady SDK pro rozpoznávání řeči služby Cognitive Services, kterou si můžete stáhnout [zde](https://aka.ms/csspeech/iosbinary), do adresáře obsahujícího ukázkový projekt.
1. Upravte cesty k rozhraní mj. *Project Settings*
   1. Na kartě **Obecné** v záhlaví **Vložené binární soubory** přidejte knihovnu Sady SDK jako architekturu: **Přidejte vložené binární soubory** > **Přidat další...** > Přejděte do vybraného adresáře a vyberte obě architektury.
   1. Přejděte na kartu **Build Settings** (Nastavení sestavení) a aktivujte **všechna** nastavení.
1. Do části _Framework Search Paths_ (Cesty pro hledání rozhraní) pod nadpisem **Search Paths** (Cesty pro hledání) přidejte adresář `$(SRCROOT)/..`.
