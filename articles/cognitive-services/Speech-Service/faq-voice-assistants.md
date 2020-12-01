---
title: Nejčastější dotazy k hlasovým pomocníkům
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
ms.openlocfilehash: b43d446930d1f8c40c12e5d0aeba6eb202ef47ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353727"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Nejčastější dotazy k hlasovým pomocníkům

Pokud nemůžete najít odpovědi na vaše otázky v tomto dokumentu, podívejte se na [Další možnosti podpory](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Obecné

**Otázka: co je hlasový asistent?**

**A:** Podobně jako Cortana, hlasového asistenta je řešení, které naslouchá uživateli mluveného projevy, analyzuje obsah těchto projevy pro účely, provádí jednu nebo více akcí v reakci na záměr utterance a pak poskytuje reakci na uživatele, který často zahrnuje hlasovou komponentu. Je to "Hlasová" Hlasová prostředí pro interakci se systémem. Autoři hlasového asistenta vytvoří aplikaci na zařízení pomocí sady `DialogServiceConnector` v sadě Speech SDK ke komunikaci s pomocníkem vytvořeným pomocí [vlastních příkazů (Preview)](custom-commands.md) nebo [přímého kanálu pro hlasový vstup](direct-line-speech.md) v rozhraní bot. Tito asistenti můžou používat vlastní klíčová slova, vlastní řeč a vlastní hlas k poskytování prostředí přizpůsobeného vaší značkou nebo produktem.

**Otázka: mám použít vlastní příkazy (Preview) nebo Direct line Speech? Jaký je rozdíl?**

Odpověď **:** [vlastní příkazy (Preview)](custom-commands.md) jsou vysoce složitou sadou nástrojů, která umožňuje snadno vytvořit a hostovat pomocníka, který je vhodný pro scénáře dokončování úkolů. [Přímý line Speech](direct-line-speech.md) poskytuje bohatší a sofistikované funkce, které umožňují využít robustní scénáře konverzace. Další informace najdete v [porovnání s řešením pomocníka](voice-assistants.md#choosing-an-assistant-solution) .

**Otázka: Návody začít?**

**A:** Nejlepším způsobem, jak začít vytvářet vlastní příkazy (Preview) nebo základní roboty bot Framework.

- [Vytvoření vlastní aplikace (Preview)](./quickstart-custom-commands-application.md)
- [Vytvoření úrovně Basic bot Framework bot](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Připojení robota k kanálu s přímým přístupem k lince Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Ladění

**Otázka: kde je tajný kód mého kanálu?**

**A:** Pokud jste použili verzi Preview funkce Direct line Speech nebo čtete související dokumentaci, možná budete chtít najít tajný klíč na stránce pro registraci kanálu s přímým přístupem. `DialogServiceConfig` `FromBotSecret` Tato hodnota také očekává výrobní metoda v 1.7 v sadě Speech SDK.

Nejnovější verze funkce Direct line Speech zjednodušuje proces kontaktování robota ze zařízení. Na stránce registrace kanálu rozevírací seznam v horní části přidruží k registraci kanálu rozpoznávání řeči pomocí prostředku řeči. Po přidružení sada Speech SDK v 1.8 zahrnuje `BotFrameworkConfig::FromSubscription` výrobní metodu, která nakonfiguruje tak, `DialogServiceConnector` aby kontaktovala robota, kterou jste přidružili k vašemu předplatnému.

Pokud stále migrujete klientskou aplikaci z verze 1.7 na verzi 1.8, `DialogServiceConfig::FromBotSecret` může i nadále fungovat s neprázdnou hodnotou, která není null, pro parametr tajného klíče kanálu, např. předchozí tajný klíč, který jste použili. Bude jednoduše ignorováno při použití předplatného pro rozpoznávání řeči přidruženého k novější registraci kanálu. Počítejte s tím, že hodnota _nesmí_ být null a neprázdná, protože je na zařízení zaškrtnutá před relevantním přidružení na straně služby.

Podrobnější příručku najdete v [části kurzu](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) , která vás provede registrací kanálu.

**Otázka: zobrazí se chyba 401 při připojování a nic nefunguje. Vím, že klíč předplatného pro rozpoznávání řeči je platný. Co se děje?**

**A:** Když spravujete předplatné na Azure Portal, ujistěte se prosím, že používáte prostředek **řeči** (Microsoft. CognitiveServicesSpeechServices, "Speech"), a _ne_ prostředek **Cognitive Services** (microsoft. CognitiveServicesAllInOne, "All Cognitive Services"). Podívejte se také na [podporu pro hlasové asistenty v oblasti služby Speech](regions.md#voice-assistants).

![správné předplatné pro přímý line Speech](media/voice-assistants/faq-supported-subscription.png "Příklad kompatibilního předplatného pro rozpoznávání řeči")

**Otázka: mohu získat text rozpoznávání zpátky z my `DialogServiceConnector` , ale zobrazí se chyba 1011 a nic od robota. Proč?**

**A:** Tato chyba označuje problém s komunikací mezi vaším asistentem a službou hlasového asistenta.

- Pro vlastní příkazy (Preview) se ujistěte, že je publikovaná vaše aplikace Custom Commands (Preview).
- V případě přímého vstupu na řeč se ujistěte, že jste [připojili robota k kanálu přímého řádku s přímým přístupem](/azure/bot-service/bot-service-channel-connect-directlinespeech), [Přidali jsme podporu protokolu streamování](/azure/bot-service/directline-speech-bot) do robotu (s příslušnou podporou webového soketu) a pak zkontrolujete, že robot reaguje na příchozí požadavky z kanálu.

**Otázka: Tento kód stále nefunguje a/nebo se při použití. zobrazuje jiná chyba `DialogServiceConnector` . Co mám dělat?**

**A:** Protokolování na základě souborů poskytuje podstatně větší podrobnosti a může pomoci zrychlit žádosti o podporu. Pokud chcete tuto funkci povolit, přečtěte si téma [Jak používat protokolování souborů](how-to-use-logging.md).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Zpráva k vydání verze](releasenotes.md)