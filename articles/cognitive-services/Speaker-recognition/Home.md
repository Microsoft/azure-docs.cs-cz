---
title: Co je rozhraní API pro rozpoznávání mluvčího?
titleSuffix: Azure Cognitive Services
description: Ověření mluvčího a identifikace mluvčího pomocí rozhraní API pro rozpoznávání mluvčích ve službách Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464916"
---
# <a name="speaker-recognition-api---preview"></a>Rozhraní API pro rozpoznávání mluvčích – náhled

Api rozpoznávání mluvčích jsou cloudová api, která poskytují pokročilé algoritmy AI pro ověření mluvčího a identifikaci reproduktorů. Rozpoznávání mluvčího je rozděleno do dvou kategorií: ověření mluvčího a identifikace mluvčího.

## <a name="speaker-verification"></a>Ověření mluvčího

Hlas má jedinečné vlastnosti, které mohou být spojeny s jednotlivcem.  Aplikace mohou používat hlas jako další faktor pro ověření, ve scénářích, jako jsou call centra a webové služby.

Rozhraní API pro ověřování mluvčích slouží jako inteligentní nástroj, který pomáhá ověřovat uživatele pomocí hlasových i řečových přístupových frází.

### <a name="enrollment"></a>Registrace

Registrace pro ověření mluvčího je závislá na textu, což znamená, že reproduktory musí zvolit konkrétní přístupové heslo, které se použije během fáze registrace i ověření.

Ve fázi zápisu mluvčího je hlas mluvčího zaznamenán s vyslovením určité fráze. Hlasové funkce jsou extrahovány tak, aby tvořily jedinečný hlasový podpis, zatímco vybraná fráze je rozpoznána. Společně by tato data zápisu reproduktoru použít k ověření reproduktoru. Data pro zápis reproduktorů jsou uložena v zabezpečeném systému. Zákazník určuje, jak dlouho má být zachována. Zákazníci mohou vytvářet, aktualizovat a odebírat data zápisu pro jednotlivé reproduktory prostřednictvím volání rozhraní API.  Po odstranění předplatného budou odstraněna také všechna data o registraci mluvčího přidružená k předplatnému.

Zákazníci by se měli ujistit, že od uživatelů obdrželi příslušná oprávnění k ověření mluvčího.

### <a name="verification"></a>Ověření

Ve fázi ověřování by měl zákazník zavolat rozhraní API pro ověření mluvčího s ID přidruženým k osobě, která má být ověřena.  Služba extrahuje hlasové funkce a přístupové heslo ze vstupního záznamu řeči. Poté porovná funkce s odpovídajícími prvky dat o zápisu mluvčího pro řečníka, který se zákazník snaží ověřit, a určí jakoukoli shodu.  Odpověď vrátí "přijmout" nebo "odmítnout" s různými úrovněmi spolehlivosti.  Zákazník pak určí, jak pomocí výsledků pomoci rozhodnout, zda tato osoba je zapsaný řečník.

Prahová úroveň spolehlivosti by měla být stanovena na základě scénáře a dalších používaných ověřovacích faktorů. Doporučujeme experimentovat s úrovní spolehlivosti a zvážit vhodné nastavení pro každou aplikaci. Api nejsou určeny k určení, zda je zvuk od živé osoby nebo imitace nebo záznam zapsaného mluvčího.

Služba nezachová záznam řeči nebo extrahované hlasové funkce, které jsou odeslány do služby během fáze ověřování.

Další podrobnosti o ověření mluvčího najdete na stránce [rozhraní API pro ověření mluvčího](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identifikace mluvčího

Aplikace mohou používat hlas k identifikaci "kdo mluví" dané skupině zapsaných mluvčích. Identifikační api mluvčího lze použít ve scénářích, jako je zvýšení produktivity schůzky, přizpůsobení a přepis telefonního centra.

### <a name="enrollment"></a>Registrace

Registrace identifikace mluvčího je na textu nezávislá, což znamená, že neexistují žádná omezení, co může mluvčí říct. Není vyžadováno žádné přístupové heslo.

Ve fázi zápisu je zaznamenán hlas mluvčího a hlasové funkce jsou extrahovány tak, aby vytvořily jedinečný hlasový podpis. Extrahované hlasové zvuky a funkce jsou uloženy v zabezpečeném systému. Zákazník určuje, jak dlouho bude zachován. Zákazníci mohou vytvářet, aktualizovat a odebírat data registrace těchto mluvčích pro jednotlivé reproduktory prostřednictvím volání rozhraní API. Po odstranění předplatného budou odstraněna také všechna data o registraci mluvčího přidružená k předplatnému.

Zákazníci by se měli ujistit, že od uživatelů obdrželi příslušná oprávnění k identifikaci mluvčího.

### <a name="identification"></a>Identifikace

Ve fázi identifikace služba identifikace mluvčího extrahuje hlasové funkce ze vstupního záznamu řeči. Poté porovná funkce s údaji o zápisu zadaného seznamu reproduktorů. Pokud je nalezena shoda s zaregistrovaným mluvčím, odpověď vrátí ID mluvčího s úrovní spolehlivosti.  V opačném případě odpověď vrátí "odmítnout", pokud žádný mluvčí není odpovídat zaregistrovaným mluvčím.

Prahová úroveň spolehlivosti by měla být nastavena na základě scénáře. Doporučujeme experimentovat s úrovní spolehlivosti a zvážit vhodné nastavení pro každou aplikaci. Api nejsou určeny k určení, zda je zvuk od živé osoby nebo imitace nebo záznam zapsaného mluvčího.

Služba nezachová záznam řeči nebo extrahované hlasové funkce, které jsou odeslány do služby pro fázi identifikace.

Další podrobnosti o identifikaci mluvčího naleznete v části REPRODUKTOR API [– identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
