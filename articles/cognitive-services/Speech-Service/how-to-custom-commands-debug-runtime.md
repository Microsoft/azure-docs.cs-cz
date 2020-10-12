---
title: Ladění chyb při spuštění vlastní aplikace příkazů
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak ladit běhové chyby ve vlastní aplikaci příkazů.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 1c9b0b48c7862990cfa2c8ba38bde0851058a228
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86023019"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Ladění chyb při spuštění vlastní aplikace příkazů

Tento článek popisuje, jak ladit, když se zobrazí chyby při spuštění aplikace Custom Commands. 

## <a name="connection-failed"></a>Připojení selhalo.

Pokud vaše aplikace spouštět vlastní příkazy z [klientské aplikace (se sadou Speech SDK)](./how-to-custom-commands-setup-speech-sdk.md) nebo [klienta Windows Voice Assistant](./how-to-custom-commands-developer-flow-test.md), může docházet k chybám připojení, jak je uvedeno níže:

| Kód chyby | Podrobnosti |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: upgrade WebSocket se nezdařil s chybou ověřování. |
| [1002](#error-1002)] | Server vrátil stavový kód "404", když byl očekáván stavový kód "101". |

### <a name="error-401"></a>Chyba 401
- Oblast zadaná v klientské aplikaci se neshoduje s oblastí vlastní aplikace příkazu.

- Klíč prostředku řeči je neplatný.
    
    Ujistěte se, že je klíč prostředku pro rozpoznávání řeči správný.

### <a name="error-1002"></a>Chyba 1002 
- Vaše aplikace vlastního příkazu není publikovaná.
    
    Publikujte aplikaci na portálu.

- Vaše vlastní příkaz applicationId není platný.

    Ujistěte se, že je ID aplikace vlastního příkazu správné.
 aplikace vlastního příkazu mimo prostředek řeči

    Ujistěte se, že je v prostředku rozpoznávání řeči vytvořená aplikace vlastního příkazu.

Další informace o řešení potíží s připojením najdete v referenčních [potížích klienta Windows Voice Assistant](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting) .


## <a name="dialog-is-canceled"></a>Dialogové okno se zrušilo.

Při spuštění vlastní aplikace příkazů se dialog zruší, když dojde k nějakým chybám.

- Pokud testujete aplikaci na portálu, může přímo zobrazit popis zrušení a spustit chybu earcon. 

- Pokud aplikaci spouštíte s [klientem Windows hlas Assistant](./how-to-custom-commands-developer-flow-test.md), dojde k chybě earcon. **Událost: CancelledDialog** můžete najít v **protokolech aktivit**.

- Pokud používáte klientskou aplikaci příklad klientské [aplikace (se sadou Speech SDK)](./how-to-custom-commands-setup-speech-sdk.md), dojde k chybě earcon. V rámci **stavu**můžete najít **událost: CancelledDialog** .

- Pokud vytváříte vlastní klientskou aplikaci, můžete vždy navrhnout požadované logiky pro zpracování událostí CancelledDialog.

Událost CancelledDialog se skládá z kódu a popisu zrušení, jak je uvedeno níže:

| Kód zrušení | Popis zrušení |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Po maximálním počtu zapínání se neudělal žádný pokrok. |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Překročila se kvóta využití nástroje pro rozpoznávání |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Nepovedlo se připojit k nástroji pro rozpoznávání. |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | K této aplikaci nelze přidružit z aktuálního předplatného. |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | Vstup překračuje maximální podporovanou délku pro nástroj pro rozpoznávání. |
| [RecognizerNotFound](#recognizer-not-found) | Nástroj pro rozpoznávání nebyl nalezen |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Neplatný dotaz pro nástroj pro rozpoznávání |
| [RecognizerError](#recognizer-return-an-error) | Funkce rozpoznávání vrátí chybu. |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Po maximálním počtu zapínání se neudělal žádný pokrok.
Dialog se zruší, když se požadovaná patice po určitém počtu kláves úspěšně neaktualizovala. Maximální číslo buildu je 3.

### <a name="recognizer-usage-quota-exceeded"></a>Překročila se kvóta využití nástroje pro rozpoznávání
Language Understanding (LUIS) má omezení využití prostředků. Obvykle se může jednat o chybu při překročení kvóty využití pro rozpoznávání na základě těchto skutečností: 
- Vytváření LUIS překračuje limit.

    Přidejte předpověď prostředku do vlastní aplikace příkazy: 
    1. Přejít na **Nastavení**, prostředek Luis
    1. Vyberte prostředek předpovědi z **prostředku předpovědi**, nebo klikněte na **vytvořit nový prostředek** . 

- Váš prostředek předpovědi LUIS překračuje limit.

    Pokud se nacházíte na prostředku předpovědi F0, má limit 10 tisíc/měsíc, 5 dotazů za sekundu.

Další podrobnosti o omezeních prostředků LUIS najdete v tématu [Language Understanding využití prostředků a omezení](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits#resource-usage-and-limits) .

### <a name="connection-to-the-recognizer-failed"></a>Nepovedlo se připojit k nástroji pro rozpoznávání.
Obvykle to znamená, že došlo k přechodnému selhání připojení k Language Understanding (LUIS) pro rozpoznávání. Zkuste to znovu a problém by se měl vyřešit.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>K této aplikaci nelze přidružit z aktuálního předplatného.
Vaše předplatné nemá autorizaci pro přístup k aplikaci LUIS. 

### <a name="input-exceeds-the-maximum-supported-length"></a>Vstup překračuje maximální podporovanou délku.
Vaše zadání překročilo 500 znaků. Pro vstupní utterance povolujeme jenom maximálně 500 znaků.

### <a name="invalid-query-for-the-recognizer"></a>Neplatný dotaz pro nástroj pro rozpoznávání
Vaše zadání překročilo 500 znaků. Pro vstupní utterance povolujeme jenom maximálně 500 znaků.

### <a name="recognizer-return-an-error"></a>Nástroj pro rozpoznávání vrátil chybu.
Nástroj pro rozpoznávání LUIS při pokusu o rozpoznání vstupu vrátil chybu.

### <a name="recognizer-not-found"></a>Nástroj pro rozpoznávání nebyl nalezen
Nejde najít typ rozpoznávání určený v modelu dialogu vlastních příkazů. V současné době podporujeme [Nástroj pro rozpoznávání Language Understanding (Luis)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Další běžné chyby
### <a name="unexpected-response"></a>Neočekávaná odpověď
Neočekávané odpovědi mohou způsobovat více věcí. Několik kontrol začínajících na:
- V ukázkových větách ano/ne

    Protože v tuto chvíli nepodporujeme žádné záměry s výjimkou případů, kdy se používá funkce with Confirm. Nezjistily se všechny záměry ano/ne definované v ukázkových větách.

- Podobné záměry a příklady vět mezi příkazy

    Přesnost rozpoznávání LUIS může být ovlivněna v případě, že dva příkazy sdílí podobné záměry a příklady vět. Můžete zkusit udělat funkce příkazů a ukázkové věty co nejblíže.

    Osvědčený postup zlepšení přesnosti rozpoznávání najdete v [Luis osvědčených postupech](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

- Dialogové okno se zrušilo.
    
    Podívejte se na důvody zrušení v části výše.

### <a name="error-while-rendering-the-template"></a>Chyba při vykreslování šablony
V reakci na řeč se používá nedefinovaný parametr. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Odkaz na objekt není nastavený na instanci objektu.
Máte prázdný parametr v datové části JSON, který je definovaný v poli **Odeslat aktivitu do akce klienta** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit ukázky na GitHubu](https://aka.ms/speech/cc-samples)