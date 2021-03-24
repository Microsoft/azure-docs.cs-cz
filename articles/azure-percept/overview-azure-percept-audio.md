---
title: Přehled zvukového zařízení Azure Percept
description: Další informace o službě Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 7c80159607947dbe7ed17181beac610fa0c202c5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956090"
---
# <a name="introduction-to-azure-percept-audio"></a>Seznámení se službou Azure Percept audio

Azure Percept audio je příslušenství zařízení, které přidává funkce řeči AI do [Azure PERCEPT DK](./overview-azure-percept-dk.md). Obsahuje předem konfigurovaný zvukový procesor a lineární pole se čtyřmi mikrofony, které vám umožní používat hlasové příkazy, klíčové slovo hledání a mnohem bezkontaktní pole s podporou Azure Cognitive Services. Je integrovaný předem s Azure Percept DK, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)a dalšími službami Azure Edge Management. Azure Percept audio je možné zakoupit v [online obchodě Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Koupit](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Zvukové komponenty Azure Percept

Azure Percept audio obsahuje následující hlavní součásti:

- Prostředí Azure Percept Audio Device (SoM) připravené pro produkční a zvukové zpracování pomocí čtyř mikrofonů prostřednictvím kodeku XMOS
- Vývojář () panel: 2x tlačítka, 3x diody, Micro USB a 3,5 mm zvukový konektor
- Požadované kabely: FPC kabel, USB Micro Type-B to USB-A
- Uvítací karta
- Mechanická montážní deska s integrovaným připojením řady 80/20 1010

## <a name="compute-capabilities"></a>Výpočetní funkce 

Zvuk Azure Percept předá zvukový vstup prostřednictvím zásobníku řeči, který běží na procesoru nosné desky Azure Percept DK v hybridním hraničním cloudu. Proto se pro službu Azure Percept audio vyžaduje nosná deska s operačním systémem, který podporuje zásobník řeči, aby bylo možné provést. 

Zpracování zvuku se provádí takto: 

- Azure Percept audio: zachycuje a převádí zvuk a odesílá ho do konektoru DK a zvukového konektoru.

- Azure Percept DK: zásobník řeči provádí zrušení a ozvěnu a zpracovává příchozí zvuk k optimalizaci pro rozpoznávání řeči. Po zpracování se provede klíčové slovo hledání.

- Cloud: zpracovává příkazy a fráze přirozeného jazyka, ověřování klíčových slov a přeškolení. 

- Offline: Pokud je zařízení offline, detekuje klíčové slovo a zaznamená telemetrii stavu připojení k Internetu. Zvýšená hodnota nepravdivého přijetí pro klíčové slovo hledání může být pozorována, protože ověření klíčového slova v cloudu nelze provést. 

## <a name="getting-started"></a>Začínáme

- [Sestavte si Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Připojení zvukového zařízení Azure Percept k DevKit](./quickstart-percept-audio-setup.md)
- [Dokončete prostředí pro instalaci Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Sestavení prototypu bez kódu

Pomocí šablon hlasového asistenta Azure Percept pro pohostinství, zdravotní péče, inventář a automobil můžete vytvořit [řešení pro rozpoznávání řeči bez kódu](./tutorial-no-code-speech.md) ve [službě Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) .

### <a name="manage-your-no-code-speech-solution"></a>Správa řešení pro rozpoznávání řeči bez kódu

- [Konfigurace hlasového asistenta v Azure Percept studiu](./how-to-manage-voice-assistant.md)
- [Konfigurace hlasového asistenta ve službě IoT Hub](./how-to-configure-voice-assistant.md)
- [Řešení potíží se zvukem v Azure Percept](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Další technické informace

- [Datový list Azure Percept audio](./azure-percept-audio-datasheet.md)
- [Chování tlačítka a indikátoru LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Koupit si zvukové zařízení Azure Percept z Microsoft Online Storu](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
