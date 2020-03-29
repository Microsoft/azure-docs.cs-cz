---
title: Nejčastější dotazy hlasové asistenty
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se hlasových asistentů pomocí vlastních příkazů (náhled) nebo kanálu Přímé řeči.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110343"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Nejčastější dotazy hlasové asistenty

Pokud v tomto dokumentu nemůžete najít odpovědi na své otázky, podívejte se na [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: Co je hlasový asistent?**

**A:** Stejně jako Cortana, hlasový asistent je řešení, které naslouchá mluvené projevy uživatele, analyzuje obsah těchto projevy pro význam, provádí jednu nebo více akcí v reakci na záměr utterance a pak poskytuje odpověď na uživatele, který často obsahuje mluvené komponenty. Je to "voice-in, voice-out" zážitek pro interakci se systémem. autoři hlasového asistenta vytvářejí aplikaci na zařízení pomocí `DialogServiceConnector` sady Speech SDK ke komunikaci s pomocníkem vytvořeným pomocí [vlastních příkazů (Preview)](custom-commands.md) nebo kanálu Přímé řeči [řeči](direct-line-speech.md) v rozhraní Bot Framework. Tito asistenti mohou používat vlastní klíčová slova, vlastní řeč a vlastní hlas, aby poskytli prostředí přizpůsobené vaší značce nebo produktu.

**Otázka: Mám používat vlastní příkazy (náhled) nebo přímou řeč? Jaký je v tom rozdíl?**

**Odpověď:** [Vlastní příkazy (preview)](custom-commands.md) je sada nástrojů s nižší složitostí, které lze snadno vytvořit a hostit asistenta, který je vhodný pro scénáře dokončení úkolů. [Direct Line Řeč](direct-line-speech.md) poskytuje bohatší, sofistikovanější funkce, které mohou umožnit robustní konverzační scénáře. Další informace naleznete ve [srovnání pomocných řešení.](voice-assistants.md#choosing-an-assistant-solution)

**Otázka: Jak začít?**

**A:** Nejlepší způsob, jak začít s vytvořením vlastní příkazy (Preview) aplikace nebo základní Bot Framework bot.

- [Vytvoření aplikace Vlastní příkazy (Náhled)](quickstart-custom-speech-commands-create-new.md)
- [Vytvořte si základního robota Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Připojení robota ke kanálu řeči přímé čáry](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Ladění

**Otázka: Kde je můj kanál tajemství?**

**A:** Pokud jste použili verzi náhledu direct line řeči nebo čtete související dokumentaci, můžete očekávat, že najít tajný klíč na stránce registrace kanálu Direct Line řeči. V1.7 `DialogServiceConfig` factory `FromBotSecret` metoda v řeči SDK také očekává tuto hodnotu.

Nejnovější verze direct line řeči zjednodušuje proces kontaktování robota ze zařízení. Na stránce registrace kanálu rozevírací seznam v horní části přidruží registraci kanálu řeči přímé čáry k prostředku řeči. Po přidružení sada V1.8 Speech SDK obsahuje metodu `BotFrameworkConfig::FromSubscription` výroby, která nakonfiguruje metodu, která nakonfiguruje `DialogServiceConnector` robota, kterého jste přidružili k předplatnému.

Pokud stále migrujete klientskou aplikaci z v1.7 `DialogServiceConfig::FromBotSecret` na v1.8, může pokračovat v práci s neprázdnou, nenulovou hodnotou pro jeho tajný parametr kanálu, například předchozí tajný klíč, který jste použili. Bude jednoduše ignorována při použití odběru řeči přidružené k registraci novějšího kanálu. Vezměte prosím na vědomí, že hodnota _musí_ být non-null a non-prázdné, protože tyto jsou kontrolovány na zařízení před přidružení na straně služby je relevantní.

Podrobnější průvodce naleznete v [sekci výukového programu,](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) který prochází registrací kanálu.

**Otázka: Při připojování se zobrazuje chyba 401 a nic nefunguje. Vím, že můj klíč předplatného řeči je platný. Co se děje?**

**A:** Při správě předplatného na portálu Azure se ujistěte, že používáte prostředek **řeči** (Microsoft.CognitiveServicesSpeechServices, "Speech") a _ne_ prostředek **kognitivních služeb** (Microsoft.CognitiveServicesAllInOne, "Všechny kognitivní služby"). Zkontrolujte také [podporu oblasti hlasové služby pro hlasové asistenty](regions.md#voice-assistants).

![správné předplatné pro řeč na přímé lince](media/voice-assistants/faq-supported-subscription.png "příklad kompatibilního předplatného řeči")

**Otázka: Dostanu uznání text `DialogServiceConnector`zpět z mého , ale vidím '1011' chyba a nic z mého bot. Proč?**

**A:** Tato chyba označuje problém s komunikací mezi asistentem a službou hlasového asistenta.

- U vlastních příkazů (preview) zajistěte, aby byla aplikace vlastní chod příkazů (Preview) publikována.
- U funkce Direct Line Speech se ujistěte, že jste [robota připojili ke kanálu řeči přímé linky](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [přidali](https://aka.ms/botframework/addstreamingprotocolsupport) jste k botovi podporu protokolu streamování (s podporou souvisejícího webového soketu) a zkontrolujte, zda váš robot reaguje na příchozí požadavky z kanálu.

**Otázka: Tento kód stále nefunguje a/nebo se při použití `DialogServiceConnector`aplikace zobrazuje jiná chyba. Co mám dělat?**

**A:** Protokolování založené na souborech poskytuje podstatně více podrobností a může pomoci urychlit žádosti o podporu. Chcete-li tuto funkci povolit, přečtěte si postup [použití protokolování souborů](how-to-use-logging.md).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Poznámky k verzi](releasenotes.md)
