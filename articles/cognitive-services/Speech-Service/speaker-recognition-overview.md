---
title: Přehled rozpoznávání mluvčího – služba Speech
titleSuffix: Azure Cognitive Services
description: Rozpoznávání mluvčího poskytuje algoritmy pro ověřování a identifikaci mluvčích podle jejich jedinečných hlasových charakteristik pomocí hlasových biometry. Rozpoznávání mluvčího slouží k zodpovězení otázky "kdo se mluví?". Tento článek představuje přehled výhod a možností služby rozpoznávání mluvčího.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: rozpoznávání mluvčího, Voice biometry
ms.openlocfilehash: d07a9960c8a586fa137b4b717afbf91740c265d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015320"
---
# <a name="what-is-speaker-recognition-preview"></a>Co je rozpoznávání mluvčího (Preview)?

Služba rozpoznávání mluvčího poskytuje algoritmy pro ověřování a identifikaci mluvčích podle jejich jedinečných hlasových charakteristik pomocí služby Voice biometry. Rozpoznávání mluvčího slouží k zodpovězení otázky "kdo se mluví?". Zadáváte data o školeních zvuku pro jeden mluvčí, který vytváří profil zápisu na základě jedinečných vlastností hlasu mluvčího. Potom můžete zkontrolovat ukázky zvukového hlasu proti tomuto profilu, abyste ověřili, že je mluvčí stejná osoba (ověření mluvčího), nebo ukázky zvukového hlasu pro *skupinu* zaregistrovaných profilů mluvčího, abyste viděli, jestli se shoduje s jakýmkoli profilem ve skupině (identifikace mluvčího). Naproti tomu [mluvčí Diarization](batch-transcription.md#speaker-separation-diarization) seskupuje segmenty zvuků pomocí mluvčího v operaci Batch.

## <a name="speaker-verification"></a>Ověření mluvčího

Ověření mluvčího zjednodušuje proces ověření zapsané identity mluvčího buď pomocí přístupových hesel, nebo hlasového vstupu ve volném formátu. Dá se použít k ověřování jednotlivců pro zajištění bezpečných a bezproblémových zapojení zákazníků v rámci široké škály řešení, od ověření identity zákazníků ve voláních centra k přístupu k zařízení bez kontaktování.

### <a name="how-does-speaker-verification-work"></a>Jak Ověření mluvčího funguje?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Vývojový diagram Ověření mluvčího.":::

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

## <a name="common-questions-and-solutions"></a>Běžné dotazy a řešení

| Otázka | Řešení |
|---------|----------|
| K jakým scénářům se dá rozpoznávání mluvčího použít? | Ověření zákazníka centra volání, ohlášení hlasu pacientům, přepis schůzky, přizpůsobení zařízení pro více uživatelů|
| Jaký je rozdíl mezi identifikací a ověřováním? | Identifikace je proces zjišťování, který člen ze skupiny mluvčích mluví. Ověření je potvrzení, že mluvčí odpovídá známému nebo **zaregistrovanému** hlasu.|
| Jaký je rozdíl mezi ověřováním závislým na textu a nezávislým na textu? | Ověřování závislé na textu vyžaduje konkrétní předávací frázi pro zápis i rozpoznávání. Ověřování nezávislé na textu vyžaduje pro registraci delší ukázku hlasu, ale může se jednat o cokoli, co je třeba při rozpoznávání.|
| Jaké jazyky se podporují? | Angličtina, francouzština, španělština, italština, němčina, italština, japonština a portugalština |
| Jaké oblasti Azure jsou podporované? | Rozpoznávání mluvčího je služba ve verzi Preview, která je aktuálně dostupná jenom v Západní USA oblasti.|
| Jaké jsou podporované zvukové formáty? | Mono 16 bitů, 16kHz WAV s kódováním PCM |
| Odpovědi **přijmout** a **odmítnout** nejsou přesné, jak tuto prahovou hodnotu vyladit? | Vzhledem k tomu, že se optimální prahová hodnota značně liší u scénářů, rozhraní API rozhoduje, jestli se má "přijmout" nebo "zamítnout" jednoduše na základě výchozí prahové hodnoty 0,5. Pokročilým uživatelům se doporučuje přepsat výchozí rozhodnutí a vyladit výsledek na základě vašeho vlastního scénáře. |
| Můžete jeden mluvčí zaregistrovat několikrát? | Ano, u ověřování závislého na textu můžete zaregistrovat mluvčí až 50 časů. V případě ověření nezávislého na textu nebo identifikace mluvčího se můžete zaregistrovat až po 300 sekund zvuk. |
| Jaká data jsou uložená v Azure? | Zvuk registrace je uložený ve službě, dokud se hlasový profil [neodstraní](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments). Zvukové vzorky rozpoznávání nejsou uchovávány nebo uloženy. |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> * Seznamte se s rozpoznávání mluvčího základy běžných vzorů návrhu, které můžete použít ve svých aplikacích, dokončete v tomto [článku](./get-started-speaker-recognition.md) .
> * Prohlédněte si [kurz videa](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) pro ověřování mluvčí nezávislé na textu.