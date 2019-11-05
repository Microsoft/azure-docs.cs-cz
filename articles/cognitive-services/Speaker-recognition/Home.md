---
title: Co je rozhraní API pro rozpoznávání mluvčího?
titleSuffix: Azure Cognitive Services
description: Ověření mluvčího a identifikace mluvčího rozhraní API pro rozpoznávání mluvčího v Cognitive Services.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464916"
---
# <a name="speaker-recognition-api---preview"></a>Rozhraní API pro rozpoznávání mluvčího – Preview

Rozhraní API pro rozpoznávání mluvčího jsou cloudová rozhraní API, která poskytují pokročilé algoritmy AI pro ověřování mluvčího a identifikaci mluvčího. Rozpoznávání mluvčího je rozdělené do dvou kategorií: ověřování mluvčího a identifikace mluvčího.

## <a name="speaker-verification"></a>Ověření mluvčího

Voice má jedinečné charakteristiky, které mohou být přidruženy k jednotlivci.  Aplikace mohou používat hlas jako dodatečný faktor pro ověřování ve scénářích, jako jsou například centra volání a webové služby.

Rozhraní API pro Ověření mluvčího slouží jako inteligentní nástroj, který usnadňuje ověřování uživatelů pomocí hlasových a hlasových hesel.

### <a name="enrollment"></a>Registrace

Registrace pro ověřování mluvčího je závislá na textu, což znamená, že reproduktory potřebují zvolit konkrétní přístupové heslo, které se má použít při registraci i ověřovací fázi.

Ve fázi registrace mluvčího se hlas mluvčího říká konkrétní fráze. Funkce hlasu jsou extrahovány za účelem vytvoření jedinečného hlasového podpisu v době, kdy byla zvolená fráze rozpoznána. Společně se k ověření mluvčího použijí tato data o registraci mluvčího. Data zápisu mluvčího se ukládají v zabezpečeném systému. Zákazník řídí, jak dlouho se má zachovat. Zákazníci můžou vytvářet, aktualizovat a odebírat registrační data pro jednotlivé reproduktory prostřednictvím volání rozhraní API.  Po odstranění odběru se odstraní také všechna data registrace mluvčího, která jsou přidružená k tomuto předplatnému.

Zákazníci by měli mít jistotu, že obdrží příslušná oprávnění od uživatelů k ověření mluvčího.

### <a name="verification"></a>Ověření

Ve fázi ověřování by zákazník měl zavolat rozhraní API pro ověření mluvčího s ID přidruženým k osobě, která se má ověřit.  Služba extrahuje hlasové funkce a přístupové heslo ze vstupního záznamu řeči. Pak porovná funkce s odpovídajícími prvky údajů o zápisu mluvčího pro mluvčího, které se zákazník snaží ověřit, a určí jakoukoli shodu.  Odpověď vrátí "přijmout" nebo "zamítnout" s různými úrovněmi spolehlivosti.  Zákazník pak určí, jak se mají výsledky použít k tomu, aby bylo možné určit, jestli je tato osoba zaregistrovaná mluvčí.

Úroveň spolehlivosti prahové hodnoty by měla být nastavena na základě scénáře a dalších používaných faktorů ověřování. Doporučujeme experimentovat s úrovní spolehlivosti a vzít v úvahu vhodné nastavení pro jednotlivé aplikace. Rozhraní API nejsou určena k určení, zda je zvuk od živé osoby nebo napodobeniny nebo záznamu zaregistrovaného mluvčího.

Služba neuchovává záznam řeči ani extrahované funkce hlasu, které se odesílají do služby během ověřovací fáze.

Další podrobnosti o ověření mluvčího najdete na stránce [rozhraní API pro ověření mluvčího](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identifikace mluvčího

Aplikace můžou pomocí hlasu identifikovat uživatele, který je mluví, a určit tak skupinu zaregistrovaných mluvčích. Rozhraní API pro Identifikace mluvčího se dají použít ve scénářích, jako je například produktivita schůzky, přizpůsobení a přepis centra volání.

### <a name="enrollment"></a>Registrace

Registrace identifikace mluvčího je na textu nezávislá, což znamená, že neexistují žádná omezení, co může mluvčí říct. Není vyžadováno žádné heslo.

Ve fázi registrace je zaznamenán hlas mluvčího a funkce hlasu jsou extrahovány za účelem vytvoření jedinečného hlasového podpisu. Extrahovaný zvuk a funkce řeči se ukládají v zabezpečeném systému. Zákazník řídí dobu uchování. Zákazníci můžou data zápisu mluvčího vytvořit, aktualizovat a odebrat pro jednotlivé reproduktory prostřednictvím volání rozhraní API. Po odstranění odběru se odstraní také všechna data registrace mluvčího, která jsou přidružená k tomuto předplatnému.

Zákazníci by měli mít jistotu, že obdrží příslušná oprávnění od uživatelů k identifikaci mluvčího.

### <a name="identification"></a>Identifikace

Ve fázi identifikace extrahuje služba identifikace mluvčí funkce hlasu ze vstupního záznamu řeči. Pak porovná funkce s daty zápisu v zadaném seznamu mluvčích. Pokud se shoda najde u zaregistrovaného mluvčího, vrátí odpověď ID mluvčího s úrovní spolehlivosti.  V opačném případě odpověď vrátí "odmítnout", pokud žádný mluvčí neodpovídá zaregistrovanému mluvčímu.

Úroveň spolehlivosti prahové hodnoty by měla být nastavena na základě scénáře. Doporučujeme experimentovat s úrovní spolehlivosti a vzít v úvahu vhodné nastavení pro jednotlivé aplikace. Rozhraní API nejsou určena k určení, zda je zvuk od živé osoby nebo napodobeniny nebo záznamu zaregistrovaného mluvčího.

Služba neuchovává záznam řeči ani extrahované funkce hlasu, které se odesílají do služby pro identifikační fázi.

Další podrobnosti o identifikaci mluvčího najdete v tématu [Identifikace mluvčího](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)rozhraní API.
