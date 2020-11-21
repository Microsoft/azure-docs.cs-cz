---
title: Vlastní příkazy – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro vlastní příkazy, řešení pro vytváření hlasových aplikací.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 40736764cf9cec6f652e0147eb25d83f15c5bee2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024938"
---
# <a name="what-is-custom-commands"></a>Co jsou vlastní příkazy?

Aplikace, jako je [hlasový asistent](voice-assistants.md) , naslouchají uživatelům a probírají akci v reakci, často se domluví zpět. Používají [Převod řeči na text](speech-to-text.md) k přepisovat řeči uživatele a pak provádějí akci s použitím přirozeného jazyka pro porozumění textu. Tato akce často zahrnuje mluvený výstup z pomocníka generovaného [převodem textu na řeč](text-to-speech.md). Zařízení se připojují k asistentům pomocí objektu sady Speech SDK `DialogServiceConnector` .

**Vlastní příkazy** usnadňují vytváření bohatě bohatých aplikací pro hlasové příkazy optimalizovaných pro prostředí s prvními interakcemi hlasu. Poskytuje jednotné prostředí pro vytváření, automatický hostující model a poměrně nižší složitost, což vám pomůže soustředit se na vytváření nejlepšího řešení pro scénáře hlasových příkazů.

Vlastní příkazy jsou nejvhodnější pro dokončování úkolů nebo pro scénáře příkazů a řízení, zejména pro zařízení Internet věcí (IoT), okolí a bezobslužná zařízení. Mezi příklady patří řešení pro pohostinství, maloobchodní prodej a automobilový průmysl, což vám umožní vytvořit pro hosty nejlepší prostředí řízená hlasem, spravovat inventář v úložišti a řídit funkce v klecích při přesunu.

> [!TIP]
> Podívejte se na naše ukázky ukázek na naší úvodní stránce na adrese [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Pokud vás zajímá vytváření složitých konverzačních aplikací, doporučujeme vyzkoušet si rozhraní robota pomocí [řešení Virtual Assistant](/azure/bot-service/bot-builder-enterprise-template-overview). Můžete přidat hlas do libovolného robota bot bot pomocí přímého Lineového rozpoznávání.

Dobré kandidáty pro vlastní příkazy mají pevný slovník s dobře definovanými sadami proměnných. Například úlohy pro domovskou automatizaci, jako je řízení termostatu, jsou ideální.

   ![Příklady scénářů dokončování úkolů](media/voice-assistants/task-completion-examples.png "Příklady dokončení úkolu")

## <a name="getting-started-with-custom-commands"></a>Začínáme s vlastními příkazy

Naším cílem s vlastními příkazy je snížit zátěžové zatížení, abyste se seznámili se všemi různými technologiemi a mohli se soustředit na sestavování aplikací hlasových příkazů. První krok pro použití vlastních příkazů k <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">vytvoření prostředku <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Speech </a>. Pomocí sady Speech SDK můžete vytvořit aplikaci Custom Commands v sadě Speech Studio a publikovat ji. poté může aplikace v zařízení s ní komunikovat.

#### <a name="authoring-flow-for-custom-commands"></a>Vytváření toku pro vlastní příkazy
   ![Vytváření toku pro vlastní příkazy](media/voice-assistants/custom-commands-flow.png "Tok vytváření vlastních příkazů")

Postupujte podle našeho rychlého startu, aby vaše první aplikace vlastních příkazů běžela kód za méně než 10 minut.

* [Vytvoření hlasového asistenta pomocí vlastních příkazů](quickstart-custom-commands-application.md)

Až budete s rychlým startem hotovi, Prozkoumejte naše příručky, kde najdete podrobné kroky pro návrh, vývoj, ladění, nasazení a integraci vlastní aplikace příkazů.

## <a name="building-voice-assistants-with-custom-commands"></a>Vytváření hlasových asistentů pomocí vlastních příkazů
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
* [Ukázky najdete v našem úložišti hlasových asistentů na GitHubu.](https://aka.ms/speech/cc-samples)
* [Pokud si chcete vyzkoušet vlastní příkazy, přejdete do sady Speech Studio.](https://speech.microsoft.com/customcommands)
* [Získat sadu Speech SDK](speech-sdk.md)