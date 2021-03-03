---
title: Přehled zvukového zařízení Azure Percept
description: Další informace o službě Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662686"
---
# <a name="introduction-to-azure-percept-audio"></a>Seznámení se službou Azure Percept audio

Azure Percept audio je příslušenství zařízení, které přidává funkce řeči AI do Azure Percept DK. Obsahuje předem konfigurovaný akcelerátor řeči AI a lineární pole se čtyřmi mikrofony, které vám umožní použít pro místní naslouchající zařízení vlastní příkazy, klíčové slovo hledání a mnohem velká pole. Azure Percept audio umožňuje výrobcům zařízení rozhodovat možnosti vize Azure Percept DK na nové inteligentní zařízení aktivované hlasem. Je integrovaný předem s Azure Percept DK, Azure Percept Studio a dalšími službami Azure Edge Management. K nákupu můžete použít [online obchod Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Zvukové zařízení Azure Percept.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Zvukové komponenty Azure Percept

Azure Percept audio obsahuje následující hlavní součásti:

- Prostředí Azure Percept Audio Device (SoM) připravené pro produkční prostředí se čtyřmi mikrofony a lineárním polem
- Developer Board (zahrnuje 2x tlačítka, 3x diody, USB Micro a 3,5 mm audio konektor)
- Požadované kabely: pružný kabel, USB Micro Type-B pro USB-A
- Uvítací karta
- Mechanická montážní deska s integrovaným připojením řady 80/20 1010


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Začínáme

- [Sestavte si Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Dokončete prostředí pro instalaci Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Připojení zvukového zařízení Azure Percept k DevKit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Sestavení prototypu bez kódu

Vytvářejte [řešení pro rozpoznávání řeči bez kódu](./tutorial-no-code-speech.md) pomocí šablon hlasového asistenta Azure Percept pro pohostinství, zdravotnictví, inventář a automobilové scénáře.

## <a name="additional-technical-information"></a>Další technické informace

- [Datový list Azure Percept audio](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Další kroky

Seřazení zvukového zařízení Azure Percept na [online obchodě Microsoftu](https://go.microsoft.com/fwlink/p/?LinkId=2155270).