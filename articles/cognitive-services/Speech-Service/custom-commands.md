---
title: Vlastní příkazy (náhled) – služba řeči
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro vlastní příkazy (Náhled), řešení pro vytváření hlasových aplikací.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367835"
---
# <a name="what-are-custom-commands-preview"></a>Co jsou vlastní příkazy (náhled)?

Hlasové aplikace, jako jsou [hlasoví asistenti, poslouchají](voice-assistants.md) uživatele a reagují na ně a často se vyjadřují zpět. Používají [převod řeči na text](speech-to-text.md) k přepisu řeči uživatele a poté akce na pochopení textu v přirozeném jazyce. Tato akce často zahrnuje mluvený výstup z pomocníka generovaného [převodem textu na řeč](text-to-speech.md). Zařízení se připojují k asistentům `DialogServiceConnector` pomocí objektu sady Speech SDK.

**Vlastní příkazy (Preview)** je zjednodušené řešení pro vytváření hlasových aplikací. Poskytuje jednotné vytváření zkušeností, automatické hostování modelu, a relativně nižší složitost oproti jiným možnostem, jako [je direct line řeči](direct-line-speech.md). Toto zjednodušení však přichází se snížením flexibility. Takže vlastní příkazy (Preview) je nejvhodnější pro dokončení úkolu nebo příkaz-a-control scénáře. Je obzvláště dobře sladěný pro internet věcí (IoT) a bezhlavá zařízení.

Pro komplexní konverzační interakci a integraci s dalšími řešeními, jako je [řešení virtuálního asistenta a podniková šablona,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) doporučujeme použít přímou řeč.

Dobří kandidáti pro vlastní příkazy (Náhled) mají pevnou slovní zásobu s dobře definovanými sadami proměnných. Ideální jsou například úkoly domácí automatizace, jako je ovládání termostatu.

   ![Příklady scénářů dokončení úkolu](media/voice-assistants/task-completion-examples.png "příklady dokončení úkolu")

## <a name="getting-started-with-custom-commands-preview"></a>Začínáme s vlastními příkazy (náhled)

Prvním krokem pro použití vlastních příkazů (preview) k vytvoření hlasové aplikace je [získání klíče předplatného řeči](get-started.md) a přístup k nástroji Tvůrce vlastních příkazů (Preview) v [aplikaci Speech Studio](https://speech.microsoft.com). Odtud můžete vytvořit novou aplikaci Vlastní příkazy (Náhled) a publikovat ji, po které aplikace na zařízení může komunikovat s ním pomocí sady Speech SDK.

   ![Tok vytváření pro vlastní příkazy (náhled)](media/voice-assistants/custom-commands-flow.png "Tok vytváření vlastních příkazů (náhledu)")

Nabízíme rychlé starty navržené tak, abyste mohli spustit kód za méně než 10 minut.

* [Vytvoření aplikace Vlastní příkazy (Náhled)](quickstart-custom-speech-commands-create-new.md)
* [Vytvoření aplikace vlastních příkazů (Náhled) s parametry](quickstart-custom-speech-commands-create-parameters.md)
* [Připojení k aplikaci Vlastní příkazy (Náhled) pomocí sady Speech SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Jakmile budete hotovi s quickstarts, prozkoumejte naše návody.

- [Přidání ověření do parametrů vlastního příkazu](./how-to-custom-speech-commands-validations.md)
- [Plnit příkazy na straně klienta pomocí sady Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Přidání potvrzení k vlastnímu příkazu](./how-to-custom-speech-commands-confirmations.md)
- [Přidání korekce v jednom kroku k vlastnímu příkazu](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Přejděte do aplikace Speech Studio a vyzkoušejte vlastní příkazy.](https://speech.microsoft.com)
* [Získání sady SDK pro rozpoznávání řeči](speech-sdk.md)
