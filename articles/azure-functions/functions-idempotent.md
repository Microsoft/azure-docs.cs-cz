---
title: Návrh Azure Functions pro stejný vstup
description: Sestavování Azure Functions idempotentní
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74226863"
---
# <a name="designing-azure-functions-for-identical-input"></a>Návrh Azure Functions pro stejný vstup

Realita architektury založené na událostech a na základě zpráv určuje nutnost přijímat identické požadavky při zachování integrity dat a stability systému.

Pro ilustraci zvažte tlačítko volání výtahu. Po stisknutí tlačítka se indikátory zobrazí nahoru a do vaší základny se pošle výtah. Později se vám někdo jiný připojí v předsálí. Tato osoba se na vás na vás pokusí a podruhé stiskne tlačítko osvětlene. Sami jste se seznámili a chucklei se sami s tím, jak si myslíte, že příkaz pro volání výtahu je idempotentní.

Stisknutí tlačítka pro volání výtahu a druhého, třetího nebo čtvrtého času nemá žádný vliv na konečný výsledek. Když stisknete tlačítko, bez ohledu na počet pokusů se výtah posílá do vaší základny. Idempotentní systémy, jako je například výtah, mají za následek stejný výsledek bez ohledu na to, kolikrát jsou vydávány identické příkazy.

Když přichází k sestavování aplikací, vezměte v úvahu následující scénáře:

- Co se stane, když se aplikace pro řízení zásob pokusí odstranit stejný produkt více než jednou?
- Jak se vaše aplikace lidských zdrojů chová, pokud existuje více než jedna žádost o vytvoření záznamu zaměstnance pro stejnou osobu?
- Kde se peníze přestanou, pokud vaše bankovní aplikace získá 100 požadavků, aby bylo možné provést stejnou odstoupení?

Existuje mnoho kontextů, kde požadavky na funkci mohou obdržet stejné příkazy. Mezi situace patří:

- Počet opakovaných pokusů, které odesílají stejný požadavek
- Příkazy v mezipaměti, které se přehrávají do aplikace
- Chyby aplikací odesílajících více shodných žádostí

V zájmu ochrany integrity dat a stavu systému obsahuje aplikace idempotentní logiku, která může obsahovat následující chování:

- Ověření existence dat před pokusem o provedení odstranění
- Kontroluje se, jestli data už existují, než se pokusíte provést akci vytvoření.
- Sjednocení logiky, která vytváří konečnou konzistenci v datech
- Řízení souběžnosti
- Zjišťování duplicit
- Ověřování aktuálnosti dat
- Chránit logiku pro ověření vstupních dat

Konečné idempotence je dosaženo tím, že zajistíte, že daná akce je možné a provede se pouze jednou.
