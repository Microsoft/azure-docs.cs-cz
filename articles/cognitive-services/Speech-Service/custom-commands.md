---
title: Vlastní příkazy (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro vlastní příkazy (Preview), řešení pro vytváření hlasových asistentů.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: dd3e27aefe03ee30e721fcbb915d9ad4b3821618
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806299"
---
# <a name="custom-commands-preview"></a>Vlastní příkazy (Preview)

[Hlasové asistenti](voice-assistants.md) naslouchají uživatelům a probírají akci v reakci, často se domluví zpět. Používají [Převod řeči na text](speech-to-text.md) k přepisovat řeči uživatele a pak provádějí akci s použitím přirozeného jazyka pro porozumění textu. Tato akce často zahrnuje mluvený výstup z pomocníka generovaného [převodem textu na řeč](text-to-speech.md). Zařízení se připojují k asistentům pomocí objektu `DialogServiceConnector` sady Speech SDK.

**Vlastní příkazy (Preview)** jsou zjednodušené řešení pro vytváření hlasového asistenta. Poskytuje jednotné prostředí pro vytváření, automatický hostující model a relativně nižší složitost oproti dalším možnostem vytváření pomocníka, jako je [přímý line Speech](direct-line-speech.md). Toto zjednodušení ale přináší omezení flexibility. Vlastní příkazy (Preview) jsou proto nejvhodnější pro dokončování úkolů nebo pro scénáře příkazů a řízení. Je obzvláště vhodná pro Internet věcí (IoT) a bezobslužná zařízení.

Pro komplexní interakci s konverzací a integraci s dalšími řešeními, jako je [řešení Virtual Assistant a podniková šablona](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , doporučujeme používat přímý hlasový vstup.

Dobré kandidáty pro vlastní příkazy (Preview) mají pevný slovník s dobře definovanými sadami proměnných. Například úlohy pro domovskou automatizaci, jako je řízení termostatu, jsou ideální.

   ![Příklady scénářů dokončování úkolů](media/voice-assistants/task-completion-examples.png "Příklady dokončení úkolu")

## <a name="getting-started-with-custom-commands-preview"></a>Začínáme s vlastními příkazy (Preview)

Prvním krokem pro použití vlastních příkazů (Preview) k vytvoření hlasového asistenta je [získání klíče předplatného pro rozpoznávání řeči](get-started.md) a přístup k vlastnímu tvůrci příkazů (Preview) v [studiu řeči](https://speech.microsoft.com). Odtud můžete vytvořit novou vlastní aplikaci (Preview) a publikovat ji, po které může aplikace na zařízení komunikovat pomocí sady Speech SDK.

   ![Vytváření toku pro vlastní příkazy (Preview)](media/voice-assistants/custom-commands-flow.png "Tok pro vytváření vlastních příkazů (ve verzi Preview)")

Nabízíme rychlé starty, které jsou navržené tak, aby běžely kód za méně než 10 minut.

* [Vytvoření vlastní aplikace (Preview)](quickstart-custom-speech-commands-create-new.md)
* [Vytvoření vlastních příkazů (ve verzi Preview) s parametry](quickstart-custom-speech-commands-create-parameters.md)
* [Připojení k aplikaci Custom Commands (Preview) pomocí sady Speech SDK,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro vytvoření hlasového asistenta s vlastními příkazy (Preview) je k dispozici na GitHubu.

* [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Přizpůsobení

Hlasové asistenti sestavené pomocí služby Speech Service mohou využívat celou škálu možností přizpůsobení, které jsou k dispozici pro [Převod řeči na text](speech-to-text.md), [Převod textu na řeč](text-to-speech.md)a [vlastní výběr klíčového slova](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Získat sadu Speech SDK](speech-sdk.md)
