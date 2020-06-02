---
title: Služba Azure rozpoznávání mluvčího
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services rozpoznávání mluvčího poskytuje algoritmy pro ověřování a identifikaci mluvčích podle jejich jedinečných vlastností hlasu. Rozpoznávání mluvčího slouží k zodpovězení otázky "kdo se mluví?".
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261783"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Co je služba Azure rozpoznávání mluvčího?

Služba rozpoznávání mluvčího poskytuje algoritmy pro ověřování a identifikaci mluvčích podle jejich jedinečných vlastností hlasu. Rozpoznávání mluvčího slouží k zodpovězení otázky "kdo se mluví?". Zadáváte data o školeních zvuku pro jeden mluvčí, který vytváří profil zápisu na základě jedinečných vlastností hlasu mluvčího. Potom můžete zkontrolovat ukázky zvukového hlasu proti tomuto profilu, abyste ověřili, že je mluvčí stejná osoba (ověření mluvčího), nebo ukázky zvukového hlasu pro *skupinu* zaregistrovaných profilů mluvčího, abyste viděli, jestli se shoduje s jakýmkoli profilem ve skupině (identifikace mluvčího). Naproti tomu [mluvčí Diarization](batch-transcription.md#speaker-separation-diarization) seskupuje segmenty zvuků pomocí mluvčího v operaci Batch.

## <a name="speaker-verification"></a>Ověření mluvčího

Ověření mluvčího zjednodušuje proces ověření zapsané identity mluvčího buď pomocí přístupových hesel, nebo hlasového vstupu ve volném formátu. Dá se použít k ověřování jednotlivců pro zajištění bezpečných a bezproblémových zapojení zákazníků v rámci široké škály řešení, od ověření identity zákazníků ve voláních centra k přístupu k zařízení bez kontaktování.

### <a name="how-does-speaker-verification-work"></a>Jak Ověření mluvčího funguje?

![Jak funguje ověřování mluvčího](media/speaker-recognition/speaker-rec.png)

Ověřování mluvčího může být závislé na textu nebo nezávislé na textu. Ověřování **závislé na textu** znamená, že reproduktory musí zvolit stejné heslo, které se má použít při registraci i ověřovací fázi. Ověřování **nezávislé na textu** znamená, že reproduktory můžou hovořit v každodenním jazyce v rámci frází pro zápis a ověření.

Pro ověřování **závislé na textu** je hlas mluvčího zaregistrovaný pomocí přístupového hesla ze sady předdefinovaných frází. Funkce hlasu jsou extrahovány ze záznamu zvuku, aby bylo možné vytvořit jedinečný podpis hlasu, zatímco zvolené heslo je také rozpoznáno. Společně se signatura hlasu a přístupové heslo slouží k ověření mluvčího. 

Ověřování **nezávislé na textu** nemá žádné omezení na to, co mluvčí říká během registrace, nebo v ukázce zvukového testu, protože extrahuje jenom funkce hlasu, které se zaměří na podobnost. 

Rozhraní API nejsou určena k určení, zda je zvuk od živé osoby nebo napodobeniny/záznamu zaregistrovaného mluvčího. 

## <a name="speaker-identification"></a>Identifikace mluvčího

Identifikace mluvčího slouží k určení identity neznámého mluvčího v rámci skupiny zaregistrovaných mluvčích. Identifikace mluvčího vám umožní přidružit řeč k jednotlivým reproduktorům a odemknout hodnotu ze scénářů s více mluvčími, například:

* Podpora řešení pro produktivitu vzdálené schůzky 
* Sestavování přizpůsobení zařízení pro více uživatelů

### <a name="how-does-speaker-identification-work"></a>Jak Identifikace mluvčího funguje?

Registrace pro identifikaci mluvčího je **nezávislá na textu**, což znamená, že neexistují žádná omezení toho, co mluvčí ve zvukovém zařízení říká. Podobně jako u Ověření mluvčího ve fázi registrace je zaznamenán hlas mluvčího a jsou extrahovány funkce hlasu, aby bylo možné vytvořit jedinečný podpis hlasu. Ve fázi identifikace se vstupní hlasový vzorek porovnává se zadaným seznamem zaregistrovaných hlasů (až 50 v každé žádosti).

## <a name="data-security-and-privacy"></a>Zabezpečení dat a ochrana osobních údajů

Data zápisu mluvčího se ukládají do zabezpečeného systému, včetně zvukového zvuku pro zápis a funkce hlasového podpisu. Zvuk řeči pro zápis se používá pouze v případě, že je tento algoritmus upgradován a funkce je třeba extrahovat znovu. Služba neuchovává záznam řeči ani extrahované funkce hlasu, které se do služby odesílají během fáze rozpoznávání. 

Určujete, jak dlouho se mají uchovávat data. Můžete vytvářet, aktualizovat a odstraňovat registrační data pro jednotlivé reproduktory prostřednictvím volání rozhraní API. Po odstranění odběru se odstraní také všechna data registrace mluvčího, která jsou přidružená k tomuto předplatnému. 

Stejně jako u všech Cognitive Servicesch prostředků musí vývojáři, kteří používají službu rozpoznávání mluvčího, znát zásady společnosti Microsoft týkající se zákaznických dat. Měli byste se ujistit, že jste obdrželi příslušná oprávnění od uživatelů pro rozpoznávání mluvčího. Další informace najdete na [stránce Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   na webu Microsoft Trust Center. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> * Prohlédněte si [kurz videa](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) pro ověřování mluvčí nezávislé na textu.
