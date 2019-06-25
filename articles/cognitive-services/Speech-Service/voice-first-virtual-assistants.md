---
title: Vlastní hlasové první virtuálních asistentů (Preview) – hlasové služby
titleSuffix: Azure Cognitive Services
description: Přehled o funkcích, možnosti a omezení pro vlastní hlasové první virtuální Asistenti pomocí kanálu s přímým přístupem řeči řádku na rozhraní Bot Framework a Cognitive Services řeči Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 1c5712fa8bbdb158992127f8f48d810a0a9b6f79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603476"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>O vlastní hlasové první virtuální Asistenti ve verzi preview

Vlastní virtuálních asistentů pomocí hlasové služby Azure umožní vývojářům vytvářet konverzační rozhraní přirozené, podobnou té lidské pro jejich aplikace a prostředí. Kanál s přímým přístupem řeči řádku rozhraní Bot Framework rozšiřuje tyto možnosti tím, že poskytuje koordinované, iniciovat organizovaně, což vstupní bod do kompatibilní bot umožňující hlasové v hlasových si interakce s nízkou latencí a vysokou spolehlivostí. Tyto roboty můžete použít pro přirozeného jazyka zásahu od Microsoftu Language Understanding (LUIS). Přímé řeči řádku přístupu k zařízení pomocí řeči Software Development Kit (SDK).

   ![Koncepční diagram přímou linku řeči Orchestrace služby flow](media/voice-first-virtual-assistants/overview.png "tok kanál řeči")


Přímé řádku řeči a jeho přidružené funkce pro vlastní hlasové první virtuální Asistenti jsou ideální doplněk k [virtuální pomocníka řešení a šablony organizace](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). I když s přímým přístupem řeči řádek můžete pracovat s robotovi kompatibilní, tyto zdroje poskytují opakovaně použitelné základní konverzační prostředí vysoce kvalitní a běžné podpůrné dovednosti a modely pro rychlé seznámení.


## <a name="core-features"></a>Základní funkce

| Category | Funkce |
|----------|----------|
|[Vlastní probuzení word](speech-devices-sdk-create-kws.md) | Můžete povolit uživatelům začít konverzace s roboty pomocí vlastních klíčových slov, například "Hey Contoso." Provedení této úlohy se modul probuzení vlastní aplikace word v sadě SDK řeči, který se dá nakonfigurovat s vlastní probuzení slovo [, který může vytvořit tady](speech-devices-sdk-create-kws.md). Kanál s přímým přístupem řeči řádku zahrnuje straně služby probuzení slovo ověření, že zvyšují přesnost aktivace slovo probuzení versus zařízení samostatně.
|[Převod řeči na text](speech-to-text.md) | Kanál s přímým přístupem řeči řádku obsahuje v reálném čase přepisu zvuku do textové rozpoznaných pomocí [Speech to text](speech-to-text.md) z hlasové služby Azure. Tento text je k dispozici pro vašeho robota a klientské aplikace, jako je přepisována.
|[Převod textu na řeč](text-to-speech.md) | Textové odpovědí z vašeho robota se má syntetizovat pomocí [převod textu na řeč](text-to-speech.md) z hlasové služby Azure. Tohoto slučování pak budou dostupné do klientské aplikace jako zvukový datový proud. Společnost Microsoft nabízí možnost vytvářet vlastní vysoce kvalitní, vlastní hlasové neuronových sítí převod textu na ŘEČ, umožňující hlasový vstup do vaší značky, další informace [kontaktujte nás](mailto:mstts@microsoft.com).
|[Direct Line řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Jako kanál v rámci rozhraní Bot Framework s přímým přístupem řádku Speech umožňuje hladký a bezproblémový připojení mezi klientskou aplikaci, kompatibilní robotů a možnosti hlasové služby Azure. Další informace o konfiguraci vašeho robota použití kanálu Direct řeči řádku, naleznete v tématu [jeho stránky v dokumentaci k rozhraní Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro vytváření virtuálních asistentů první hlasu je k dispozici na Githubu. Tyto ukázky zahrnují klientská aplikace pro připojení k svého robota v různých oblíbených programovacích jazyků.

* [Ukázky virtuálních asistentů Voice-first (SDK)](https://aka.ms/csspeech/samples)
* [Rychlý start: voice první virtuálních asistentů (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Rychlý start: hlasové první virtuálních asistentů (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Přizpůsobení

Hlasové první virtuální Asistenti vyvíjené hlasové služby Azure můžete použít celou škálu možnosti přizpůsobení, které jsou k dispozici pro [speech to text](speech-to-text.md), [převod textu na řeč](text-to-speech.md), a [custom – klíčové slovo Výběr](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Možnosti vlastního nastavení se liší podle jazyka a národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Další postup

* [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
* [Získání sady SDK pro řeč](speech-sdk.md)
* [Vytvoření a nasazení základního robota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Získejte virtuální pomocníka řešení a šablony organizace](https://github.com/Microsoft/AI)
