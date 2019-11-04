---
title: Nejčastější dotazy týkající se hlasových asistentů
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejoblíbenější otázky týkající se hlasových asistentů pomocí vlastních příkazů (Preview) nebo přímého kanálu pro hlasový vstup.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 7ad3f932e9a10723d6cc1bae2fc4854c932d4c64
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507074"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Hlasová asistenti: nejčastější dotazy

Pokud nemůžete najít odpovědi na vaše otázky v tomto dokumentu, podívejte se na [Další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: co je hlasový asistent?**

**A:** Podobně jako Cortana, hlasového asistenta je řešení, které naslouchá uživateli mluveného projevy, analyzuje obsah těchto projevy pro účely, provádí jednu nebo více akcí v reakci na záměr utterance a pak poskytuje reakci na uživatele, který často zahrnuje hlasovou komponentu. Je to "Hlasová" Hlasová prostředí pro interakci se systémem. Autoři hlasového asistenta vytvoří aplikaci na zařízení pomocí `DialogServiceConnector` v sadě Speech SDK ke komunikaci s pomocníkem vytvořeným pomocí [vlastních příkazů (Preview)](custom-commands.md) nebo přímého kanálu pro [hlasový vstup](direct-line-speech.md) v rozhraní bot. Tito asistenti můžou používat vlastní klíčová slova, vlastní řeč a vlastní hlas k poskytování prostředí přizpůsobeného vaší značkou nebo produktem.

**Otázka: mám použít vlastní příkazy (Preview) nebo Direct line Speech? Jaký je rozdíl?**

Odpověď **:** [vlastní příkazy (Preview)](custom-commands.md) jsou vysoce složitou sadou nástrojů, která umožňuje snadno vytvořit a hostovat pomocníka, který je vhodný pro scénáře dokončování úkolů. [Přímý line Speech](direct-line-speech.md) poskytuje bohatší a sofistikované funkce, které umožňují využít robustní scénáře konverzace. Další informace najdete v [porovnání s řešením pomocníka](voice-assistants.md#comparing-assistant-solutions) .

**Otázka: Návody začít?**

**A:** Nejlepším způsobem, jak začít vytvářet vlastní příkazy (Preview) nebo základní roboty bot Framework.

* [Vytvoření vlastní aplikace (Preview)](quickstart-custom-speech-commands-create-new.md)
* [Vytvoření úrovně Basic bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Připojení robota k kanálu s přímým přístupem k lince Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Ladění

**Otázka: zobrazí se chyba 401 při připojování a nic nefunguje. Vím, že klíč předplatného pro rozpoznávání řeči je platný. Co se děje?**

**A:** Když spravujete předplatné na Azure Portal, ujistěte se prosím, že používáte prostředek **řeči** (Microsoft. CognitiveServicesSpeechServices, "Speech"), a *ne* prostředek **Cognitive Services** ( Microsoft. CognitiveServicesAllInOne, "All Cognitive Services"). Podívejte se také na [podporu pro hlasové asistenty v oblasti služby Speech](regions.md#voice-assistants).

![správné předplatné pro přímý line Speech](media/voice-assistants/faq-supported-subscription.png "Příklad kompatibilního předplatného pro rozpoznávání řeči")

**Otázka: mohu získat text z rozpoznávání zpátky z mého `DialogServiceConnector`, ale zobrazí se chyba 1011 a nic od my bot. Proč?**

**A:** Tato chyba označuje problém s komunikací mezi vaším asistentem a službou hlasového asistenta.

* Pro vlastní příkazy (Preview) se ujistěte, že je publikovaná vaše aplikace Custom Commands (Preview).
* V případě přímého vstupu na řeč se ujistěte, že jste [připojili robota k přímému line kanálu s přímým přístupem](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [Přidali jsme podporu protokolu streamování](https://aka.ms/botframework/addstreamingprotocolsupport) do robota (s příslušnou podporou webového soketu) a pak zkontrolovali, že váš robot reaguje na příchozí žádosti. z kanálu.

**Otázka: Tento kód stále nefunguje nebo při použití `DialogServiceConnector`došlo k jiné chybě. Co mám dělat?**

**A:** Protokolování na základě souborů poskytuje podstatně větší podrobnosti a může pomoci zrychlit žádosti o podporu. Pokud chcete tuto funkci povolit, přečtěte si téma [Jak používat protokolování souborů](how-to-use-logging.md).

## <a name="next-steps"></a>Další kroky

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
