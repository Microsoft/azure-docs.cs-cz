---
title: Nejčastější dotazy k přímé řádku řeči
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na oblíbené otázky o hlasové první virtuální Asistenti pomocí kanálu s přímým přístupem řádku řeči.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: bd7e1f87fea03d0aac7fd9f746b777e3b15e917e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606640"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>První hlasové virtuálních asistentů ve verzi Preview: Nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, projděte si [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: Co je přímé řádku řeči?**

**Odpověď:** `DialogServiceConnector` z sadou SDK pro řeč poskytuje obousměrné, asynchronní komunikaci s roboty, které jsou připojeny k přímé řeči řádek kanál na rozhraní Bot Framework. Tento kanál obsahuje koordinovat přístup k rozpoznávání řeči na text a převod textu na řeč z hlasové služby Azure, které umožňují robotům budou plně plaformu, hlasové si konverzační prostředí. Díky podpoře pro probuzení slova a funkce ověřování aplikace Word toto řešení umožňuje vytvářet vysoce přizpůsobitelné hlasové první virtuální pomocníci pro vaší značce nebo produktu.

**Otázka: Jak mám začít?**

**Odpověď:** Nejlepším způsobem začneme vytvořením virtuálních asistentů hlasu na prvním je začít s [vytvoření základního robota Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Dalším krokem je propojení robotům, aby [kanál s přímým přístupem řádku řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Ladění

**Otázka: Při připojování se zobrazí chyba 401 a nic funguje. Vím, že můj klíč předplatného řeči je platný. Co se děje?**

**Odpověď:** Ve verzi preview má přímý řeči řádku zvláštní omezení, která předplatná můžete použít. Zkontrolujte, že používáte **řeči** prostředků (Microsoft.CognitiveServicesSpeechServices, "Speech") a *není* **služeb Cognitive Services** prostředků ( Microsoft.CognitiveServicesAllInOne, "Všechny služby Cognitive Services"). Pouze [podmnožině oblastí s hlasové služby](regions.md#voice-first-virtual-assistants) jsou aktuálně podporovány pro přímé řádku řeči.

![Opravte předplatné pro přímou linku řeči](media/voice-first-virtual-assistants/faq-supported-subscription.png "příklad kompatibilní předplatného řeči")

**Otázka: Můžu vrátit rozpoznávání textu z přímé řeči řádku, ale zobrazí chyba "1011", ale nic z mé robota. Proč?**

**Odpověď:** Tato chyba označuje s komunikací mezi robotů a přímé řádku řeči. Ujistěte se, že jste [připojené přímé řeči řádek kanál](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [přidali jsme podporu protokolu streamování](https://aka.ms/botframework/addstreamingprotocolsupport) bot (s podporou související webové sokety) a potom zkontrolujte, jestli váš robot reaguje na příchozí požadavky z tohoto kanálu.

**Otázka: Tento kód stále nefunguje a/nebo při použití DialogServiceConnector dochází k jiné chybě. Co bych měl/a dělat?**

**Odpověď:** Protokolování souborů poskytuje podstatně více podrobností a pomáhají zrychlit žádosti o podporu. Chcete-li tuto funkci povolit, přečtěte si téma [použití protokolování do souboru](how-to-use-logging.md).

## <a name="next-steps"></a>Další postup

* [Odstraňování potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
