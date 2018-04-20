---
title: Azure Service Fabric rozhraní příkazového řádku sfctl ot | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl ot. / min.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e7693ea40df2bf12fd6e9be2ef627f30748bcd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-rpm"></a>sfctl rpm
Dotaz a odesílat příkazy na service manager opravit.

## <a name="commands"></a>Příkazy
|Příkaz|Popis|
| --- | --- |
|    platnost schválení| Vynutí schválení úloha dané opravy.|
|    odstraňovat       | Odstraní úloha dokončena opravy.|
|    Seznam         | Získá seznam úloh oprava odpovídající dané filtry.|

## <a name="sfctl-rpm-delete"></a>Odstranit sfctl ot. / min
Odstraní úloha dokončena opravy.

Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu. 

### <a name="arguments"></a>Argumenty
|Argument|Popis|
| --- | --- |
|    – id úlohy [vyžaduje]| ID úloha dokončena opravy k odstranění.|
|    – verze           | Aktuální číslo verze úloha opravy. Pokud nulová, pak požadavek bude úspěšné, pouze pokud tato hodnota odpovídá skutečným aktuální verzi úloha opravy. Pokud nula, bude provedena žádná kontrola verze.|

### <a name="global-arguments"></a>Globální argumenty
|Argument|Popis|
| --- | --- |
|    – ladění             | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
|    – Nápověda -h           | Zobrazte tuto zprávu nápovědy a ukončení.|
|    --výstup -o         | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.
|    --dotazu             | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
|    -verbose           | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|


## <a name="sfctl-rpm-list"></a>sfctl rpm seznamu
Získá seznam úloh oprava odpovídající dané filtry.

Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu. 

### <a name="arguments"></a>Argumenty
|Argument|Popis|
| --- | --- |
|    --prováděcího modulu filtru| Název opravy prováděcího modulu, jehož deklarovaný úlohy by měl být součástí seznamu.|
|    --Filtr stavu   | Seznam výsledků by měl být součástí bitové operace OR následující hodnoty určující, který úkol stavy. -1 - vytvořený - 2 - deklarovaný - 4 – Příprava - 8 - schválené - 16 - provádění - 32 - obnovení - 64 - dokončit.|
|    --Filtr id úkolu | Oprava předpona ID úloh lze porovnat.|

### <a name="global-arguments"></a>Globální argumenty
|Argument|Popis|
| --- | --- |
|    – ladění          | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
|    – Nápověda -h        | Zobrazte tuto zprávu nápovědy a ukončení.|
|    --výstup -o      | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí| JSON.|
|    --dotazu          | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
|    -verbose        | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).