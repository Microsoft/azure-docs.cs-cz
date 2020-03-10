---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943808"
---
Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Místo toho je nutné sestavit a dodat obálkovou knihovnu obsahující tyto funkce pomocí sady SDK.

Pro sestavení této knihovny obálek si nejdřív Stáhněte a nainstalujte [sadu GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Pak stáhněte projekt **Xcode** pro [knihovnu obálek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Otevřete projekt v **Xcode** a sestavte ho pro **obecný cíl zařízení iOS** *– nebude tak fungovat,* aby se vytvořil pro konkrétní cíl.

Krok sestavení vygeneruje sadu dynamického rozhraní s dynamickou knihovnou pro všechny nezbytné architektury s názvem `GStreamerWrapper.framework`.

Tato architektura musí být zahrnutá ve všech aplikacích, které používají komprimované zvukové streamy pomocí sady SDK služby pro rozpoznávání řeči.

Použijte následující nastavení v projektu **Xcode** k tomuto účelu:

1. Zkopírujte `GStreamerWrapper.framework`, který jste právě vytvořili, a architektury sady Cognitive Services Speech pro rozpoznávání řeči, kterou si můžete stáhnout z [tohoto tématu](https://aka.ms/csspeech/iosbinary), do adresáře, který obsahuje ukázkový projekt.
1. Upravte cesty k architekturám v *nastavení projektu*.
   1. Na kartě **Obecné** pod hlavičkou **vložená binární soubory** přidejte knihovnu SDK jako rozhraní: **přidejte vložené binární soubory** > **Přidat další...** > přejděte do adresáře, který jste zvolili, a vyberte obě architektury.
   1. Přejděte na kartu **Build Settings** (Nastavení sestavení) a aktivujte **všechna** nastavení.
1. Do části `$(SRCROOT)/..`Framework Search Paths _(Cesty pro hledání rozhraní) pod nadpisem_Search Paths **(Cesty pro hledání) přidejte adresář**.
