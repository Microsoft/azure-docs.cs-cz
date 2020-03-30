---
title: Navrhování funkcí Azure pro identický vstup
description: Vytváření funkcí Azure, které mají být idempotentní
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226863"
---
# <a name="designing-azure-functions-for-identical-input"></a>Navrhování funkcí Azure pro identický vstup

Realita architektury založené na událostech a na základě zpráv určuje potřebu přijímat identické požadavky při zachování integrity dat a stability systému.

Pro ilustraci zvažte tlačítko volání výtahu. Když stisknete tlačítko, rozsvítí se a výtah je odeslán na podlahu. O pár okamžiků později se k vám v hale připojí někdo jiný. Tato osoba se na vás usmívá a podruhé stiskne osvětlené tlačítko. Usmíváte se a smějete se pro sebe, když vám připomenete, že příkaz zavolat výtah je idempotentní.

Stisknutí tlačítka volání výtahu po druhé, třetí nebo počtvrté nemá žádný vliv na konečný výsledek. Když stisknete tlačítko, bez ohledu na počet, kdy je výtah odeslán na podlahu. Idempotentní systémy, jako je výtah, mají za následek stejný výsledek bez ohledu na to, kolikrát jsou vydány identické příkazy.

Pokud jde o vytváření aplikací, zvažte následující scénáře:

- Co se stane, pokud se aplikace pro řízení inventáře pokusí odstranit stejný produkt vícenež jednou?
- Jak se chová vaše aplikace lidských zdrojů, pokud existuje více než jeden požadavek na vytvoření záznamu zaměstnance pro stejnou osobu?
- Kam jdou peníze, pokud vaše bankovní aplikace dostane 100 žádostí o stejný výběr?

Existuje mnoho kontextů, kde požadavky na funkci mohou přijímat identické příkazy. Některé situace zahrnují:

- Opakování zásad odesílání stejného požadavku mnohokrát
- Příkazy uložené v mezipaměti přehrané do aplikace
- Chyby aplikace při odesílání více identických požadavků

K ochraně integrity dat a stavu systému obsahuje idempotentní aplikace logiku, která může obsahovat následující chování:

- Ověření existence dat před pokusem o provedení odstranění
- Kontrola, zda data již existují před pokusem o provedení akce vytvoření
- Sladění logiky, která vytváří konečnou konzistenci dat
- Ovládací prvky souběžnosti
- Detekce duplikace
- Ověření čerstvosti dat
- Logika guard pro ověření vstupních dat

Nakonec idempotence je dosaženo tím, že zajistí, že daná akce je možná a je provedena pouze jednou.
