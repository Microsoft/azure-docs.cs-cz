---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl repliky | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl repliky.
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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: ba67a2a20d3f3e8e9fbccb2674cea500bfbde3fb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-replica"></a>sfctl replica
Spravujte repliky, které patří do oddílů služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
|    Nasazení  | Získá podrobnosti repliky nasazené na uzlu Service Fabric.|
|    nasazení seznamu| Získá seznam replik, které jsou nasazeny na uzlu Service Fabric.|
|    stav    | Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby.|
|    Informace o      | Získá informace o repliku oddílu Service Fabric.|
|    Seznam      | Získá informace o replikách oddílu služby Service Fabric.|
|    odebrat    | Odebere repliku služby spuštěné v uzlu.|
|    Sestava stavu| Odešle zprávu o stavu v replice Service Fabric.|
|    Restartování   | Restartuje službu repliku trvalou služby spuštěné na uzlu.|


## <a name="sfctl-replica-deployed"></a>repliky sfctl nasazení
Získá podrobnosti repliky nasazené na uzlu Service Fabric.

Získá podrobnosti repliky nasazené na uzlu Service Fabric. Informace zahrnují typ služby, název služby, aktuální operace služby, aktuální operace služby spuštění datum a čas, ID oddílu, ID repliky nebo instanci, hlášené zatížení a další informace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje]| Název uzlu.|
| – id oddílu [vyžaduje]| Identita oddílu.|
| – id repliky [vyžaduje]| Identifikátor repliky.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-replica-health"></a>sfctl repliky stavu
Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby.

Získá stav repliky Service Fabric. EventsHealthStateFilter použijte k filtrování kolekce události stavu ohlášeny repliky podle stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje]| Identita oddílu.|
| – id repliky [vyžaduje]| Identifikátor repliky.|
| --události stavu stavu filtru| Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny. -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula. -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1. -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2. -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4. -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8. -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535.|
| časový limit – -t             | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                  | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h                | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o              | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu                  | Řetězec dotazu JMESPath. Další informace najdete v tématu http://jmespath.org/.|
| -verbose                | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-replica-info"></a>informace o sfctl repliky
Získá informace o repliku oddílu Service Fabric.

Odpověď obsahuje id, role, stav, stav, název uzlu, provozu a další podrobnosti o repliky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje]| Identita oddílu.|
| – id repliky [vyžaduje]| Identifikátor repliky.|
| --token pokračování  | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdné je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. Další informace najdete v tématu http://jmespath.org/.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-replica-list"></a>seznam replik sfctl
Získá informace o replikách oddílu služby Service Fabric.

Koncový bod GetReplicas vrátí informace o replik zadaný oddíl.
Respons zahrnují ID, role, stav, stav, název uzlu, provozu a další podrobnosti o repliky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje]| Identita oddílu.|
| --token pokračování  | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdné je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-replica-remove"></a>odebrat sfctl repliky
Odebere repliku služby spuštěné v uzlu.

Toto rozhraní API simuluje selhání Service Fabric repliky odstranit repliku z clusteru Service Fabric. Odebrání zavře repliky, přejde repliky na roli, None a potom odebere všechny informace o stavu repliky z clusteru. Toto rozhraní API testuje cesty k replice stavu odebrání a simuluje trvalé cesta sestavy odolnost prostřednictvím rozhraní API klienta. Upozornění: zde nejsou žádné kontroly zabezpečení se provedou, když se používá toto rozhraní API. Nesprávné použití toto rozhraní API může způsobit ztrátu dat pro stavové služby. Kromě toho příznak forceRemove ovlivňuje všechny repliky hostované v rámci jednoho procesu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje]| Název uzlu.|
| – id oddílu [vyžaduje]| Identita oddílu.|
| – id repliky [vyžaduje]| Identifikátor repliky.|
| --remove silou        | Odebrání aplikace Service Fabric nebo služby vynuceně bez průchodu přes pořadí řádné vypnutí. Tento parametr slouží k vynuceně odstranit aplikace nebo služby, pro které odstranění je řádně vypršení časového limitu z důvodu problémů v kódu služby, která zabraňuje ukončení repliky.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-replica-restart"></a>sfctl repliky restartování
Restartuje službu repliku trvalou služby spuštěné na uzlu.

Restartuje službu repliku trvalou služby spuštěné na uzlu. Upozornění: zde nejsou žádné kontroly zabezpečení se provedou, když se používá toto rozhraní API. Nesprávné použití toto rozhraní API může způsobit ztrátu dostupnosti pro stavová služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje]| Název uzlu.|
| – id oddílu [vyžaduje]| Identita oddílu.|
| – id repliky [vyžaduje]| Identifikátor repliky.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="next-steps"></a>Další postup
- [Instalační program](service-fabric-cli.md) Service Fabric rozhraní příkazového řádku.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
