---
title: Doprovodné materiály k omezování služby Azure Key Vault
description: Omezení Key Vault omezuje počet souběžných volání, aby se zabránilo nadměrné využití prostředků.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067686"
---
# <a name="azure-key-vault-throttling-guidance"></a>Doprovodné materiály k omezování služby Azure Key Vault

Omezení je proces, který zahájení, který omezuje počet souběžných volání ve službě Azure, aby se zabránilo nadměrné využití prostředků. Službou Azure Key Vault (AZURE) je určený k řešení k velkému počtu požadavků. Pokud dojde k čtenáře počet požadavků, omezení požadavků vašeho klienta pomáhá udržovat optimální výkon a spolehlivost služby službou AZURE.

Omezení omezení lišit v závislosti na scénáři. Například pokud provádíte značný zápisů, možnost omezení je vyšší než pokud pouze provádíte čtení.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak Key Vault zpracovávat jeho omezení?

Omezení služby v Key Vault existují zabránili zneužití prostředky a zajistit kvality služeb pro všechny klienty Key Vault. Pokud je překročena prahová hodnota služby, limity Key Vault žádné další požadavky z tohoto klienta v časovém intervalu. V takovém případě Key Vault vrátí stavový kód HTTP 429 (příliš mnoho požadavků), a požadavky selžou. Navíc neúspěšné požadavky, které vrátí 429 směrem k mezní hodnoty omezení sledovanými Key Vault. 

Pokud máte platný obchodní případu pro vyšší šířku pásma, kontaktujte nás.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Omezení aplikace v reakci na omezení služby

Následují **osvědčené postupy** omezení aplikace:
- Snižte počet operací na základě požadavku.
- Snižte frekvenci požadavků.
- Vyhněte se okamžitě opakování. 
    - Všechny požadavky nabíhat proti vaší omezení použití.

Pokud implementujete zpracování chyb vaší aplikace, použijte kód chyby HTTP 429 k detekci potřebu omezení na straně klienta. Pokud požadavek selže s kódem chyby HTTP 429 znovu, k stále dochází omezení služby Azure. Nadále používat doporučené na straně klienta omezování metoda, opakování žádosti, dokud neproběhne úspěšně.

### <a name="recommended-client-side-throttling-method"></a>Doporučený způsob omezení na straně klienta

Na kód chyby protokolu HTTP 429 začněte omezení vašeho klienta pomocí exponenciálního omezení rychlosti přístup:

1. Počkejte 1 sekundu, opakujte žádost
2. Pokud stále omezeny počkejte 2 sekundy, opakujte žádost
3. Pokud stále omezeny čekání 4 a víc sekund, opakujte žádost
4. Pokud stále omezeny čekání 8 sekund, opakujte žádost
5. Pokud stále omezeny čekání 16 sekund, opakujte žádost

Nesmí být v tomto okamžiku získávání kódy odpovědí HTTP 429.

## <a name="see-also"></a>Další informace najdete v tématech

Hlubší orientaci omezení na cloudu Microsoftu, najdete v části [omezení vzor](https://docs.microsoft.com/azure/architecture/patterns/throttling).

