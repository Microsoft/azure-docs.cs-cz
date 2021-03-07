---
title: Přehled zvukového zařízení Azure Percept
description: Další informace o službě Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 85eb4ed0832c9384fcd05154833bc21f83f4adf2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426344"
---
# <a name="introduction-to-azure-percept-audio"></a>Seznámení se službou Azure Percept audio

Azure Percept audio je příslušenství zařízení, které přidává funkce řeči AI do Azure Percept DK. Obsahuje předem konfigurovaný zvukový procesor a lineární pole se čtyřmi mikrofony, které vám umožní použít hlasové příkazy, klíčové slovo hledání a mnohem velká pole pro místní zařízení, která se budou monitorovat pomocí Azure Cognitive Services. Azure Percept audio umožňuje výrobcům zařízení rozhodovat Azure Percept DK nad rámec špičkových možností na nové inteligentní zařízení aktivované hlasem. Je integrovaný předem s Azure Percept DK, Azure Percept Studio a dalšími službami Azure Edge Management. K nákupu můžete použít [online obchod Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Zvukové zařízení Azure Percept.":::

## <a name="azure-percept-audio-components"></a>Zvukové komponenty Azure Percept

Azure Percept audio obsahuje následující hlavní součásti:

- Prostředí Azure Percept audio (SoM) připravené pro produkční prostředí a zpracování zvuku pomocí čtyř mikrofonů, které provádí kodek XMOS
- Vývojář (na) vývěsce (zahrnuje 2x tlačítka, 3x diody LED, Micro USB a 3,5 mm audio konektor)
- Požadované kabely: FPC kabel, USB Micro Type-B to USB-A
- Uvítací karta
- Mechanická montážní deska s integrovaným připojením řady 80/20 1010

## <a name="compute-capabilities"></a>Výpočetní funkce 

Zvuk Azure Percept předá zvukový vstup prostřednictvím zásobníku řeči, který běží na procesoru nosiče v rámci služby Azure Percept DK v hybridním cloudovém způsobu. Proto se pro službu Azure Percept audio vyžaduje nosná deska s operačním systémem, který podporuje zásobník řeči, aby bylo možné provést. 

Zpracování je provedeno následujícím způsobem: 

- Azure Percept audio: zachycuje a převádí zvuk a odesílá ho do konektoru DK a zvukového konektoru.

- Azure Percept DK: zásobník řeči provádí zrušení a ozvěnu a zpracovává příchozí zvuk k optimalizaci pro rozpoznávání řeči. Pak provede klíčové slovo hledání.

- Cloud: zpracovává příkazy a fráze přirozeného jazyka, ověřování klíčových slov a přeškolení. 

- Offline: Pokud je zařízení offline, detekuje klíčové slovo a zaznamená telemetrii stavu připojení k Internetu. Zvýšená hodnota nepravdivého přijetí pro klíčové slovo hledání může být pozorována, protože ověření klíčového slova v cloudu nelze provést. 

## <a name="getting-started"></a>Začínáme

- [Sestavte si Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Dokončete prostředí pro instalaci Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Připojení zvukového zařízení Azure Percept k DevKit](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Sestavení prototypu bez kódu

Pomocí šablon hlasového asistenta Azure Percept pro pohostinství, zdravotní péče, inventář a automobil můžete vytvořit [řešení pro rozpoznávání řeči bez kódu](./tutorial-no-code-speech.md) ve [službě Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) .

### <a name="manage-your-no-code-speech-solution"></a>Správa řešení pro rozpoznávání řeči bez kódu

- [Konfigurace hlasového asistenta ve službě IoT Hub](./how-to-manage-voice-assistant.md)
- [Konfigurace hlasového asistenta v Azure Percept studiu](./how-to-configure-voice-assistant.md)
- [Řešení potíží se zvukem v Azure Percept](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Další technické informace

- [Datový list Azure Percept audio](./azure-percept-audio-datasheet.md)
- [Chování tlačítka a indikátoru LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Další kroky

Seřazení zvukového zařízení Azure Percept na [online obchodě Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
