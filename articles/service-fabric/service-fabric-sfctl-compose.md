---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl tvoří | Microsoft Docs
description: Popisuje rozhraní příkazového řádku Service Fabric tvoří sfctl příkazy.
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
ms.openlocfilehash: 19afd35248cc0796eddbb50db4f38b813f5d568e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-compose"></a>sfctl compose
Vytvořit, odstranit a správa Docker Compose nasazení.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
|    vytvoření| Nasazení ze souboru vytvářené aplikace Service Fabric.|
|    Seznam  | Získá seznam tvoří nasazení vytvořených v clusteru Service Fabric.|
|   odebrat| Odstranění existující Service Fabric tvoří nasazení z clusteru.|
|   status| Získá informace o Service Fabric tvoří nasazení.|
|upgrade       | Spustí se upgrade nové nasazení v clusteru Service Fabric.|
|    Stav upgradu| Získá informace pro upgrade na nejnovější provést na tento Service Fabric tvoří nasazení.|


## <a name="sfctl-compose-create"></a>Napište sfctl vytvořit
Vytvoří Service Fabric tvoří nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Cesta [vyžaduje]| Cesta k cílovému souboru Docker Compose.|
 |   – Název nasazení [vyžaduje]| Název nasazení.|
|    --šifrované průchodu             | Místo výzvy k zadání hesla registru kontejneru, využít již šifrované přístupové heslo.|
|    --has-pass                   | Vyzve k zadání hesla do registru kontejneru.|
|    časový limit – -t                 | Server časový limit v sekundách.  Výchozí: 60.|
 |   --user                       | Uživatelské jméno pro připojení k registru kontejneru.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                 | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h               | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o             | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu                 | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose               | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-compose-list"></a>sfctl tvoří seznamu
Získá seznam tvoří nasazení vytvořených v clusteru Service Fabric.

Získá stav o nové nasazení, které byly vytvořeny nebo se právě probíhá vytváření v clusteru Service Fabric. Odpověď obsahuje název, stav a další podrobnosti o nové nasazení. Pokud nasazení se nevejdou na stránce, vrátí se jeden stránky s výsledky a také pokračovací token, který můžete použít k získání na další stránku.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --token pokračování| Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdné je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné.      Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL.|
| – maximální počet výsledků    | Maximální počet výsledků, které má být vrácen jako součást stránkové dotazy.      Tento parametr určuje horní mez počtu výsledků vrácených.      Pokud se nebudou vyhovovat ve zprávě podle definované v konfiguraci omezení velikosti maximální počet zpráv, může být výsledky vrácené menší než zadaná maximální počet výsledků. Pokud tento parametr je nulová nebo není zadaný, stránkové dotazy obsahovat libovolný počet výsledků jako možné, že se nevešla návratové zprávy.|
| časový limit – -t     | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění          | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h        | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o      | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu          | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose        | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-compose-remove"></a>sfctl tvoří odebrat
Odstranění existující Service Fabric tvoří nasazení z clusteru.

Odstranění existující Service Fabric tvoří nasazení. 

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje]| Identita nasazení. Toto je obvykle úplný název aplikace bez ' prostředků infrastruktury:' schéma identifikátoru URI.|
| časový limit – -t            | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                 | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h               | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o             | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu                 | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose               | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-compose-status"></a>sfctl tvoří stav
Získá informace o Service Fabric tvoří nasazení.

Vrátí stav vytvořit nasazení, který byl vytvořen nebo právě probíhá vytváření v Service Fabric clusteru a jejíž název odpovídá zadanému jako parametr. Odpověď obsahuje název, stav a další podrobnosti o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje]| Identita nasazení. |
| časový limit – -t            | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                 | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h               | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o             | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu                 | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose               | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-compose-upgrade"></a>sfctl tvoří upgradu
Spustí se upgrade nové nasazení v clusteru Service Fabric.

Ověří zadaný upgradu parametry a spustí upgradu nasazení.

### <a name="arguments"></a>Argumenty
|Argument|Popis|
| --- | --- |
|    – Cesta [vyžaduje]| Cesta k cílovému souboru Docker Compose.|
|    – Název nasazení [vyžaduje]| Název nasazení.|
|    --default-svc-type-health-map| JSON kódovaný slovník, který popisuje zásady stavu používají k vyhodnocení stavu služeb.|
|    --šifrované průchodu             | Místo výzvy k zadání hesla registru kontejneru, využít již šifrované přístupové heslo.|
 |   --selhání akce             | Možné hodnoty patří: "Neplatná',"Vrácení","Ruční".|
|    --Vynutit restartování              | Vynuťte restartování.|
 |   --has-pass                   | Vyzve k zadání hesla do registru kontejneru.|
|    --stavu. Zkontrolujte opakování         | Časový limit opakování kontroly stavu se měří v milisekundách.|
|    --stavu. Zkontrolujte nestabilním        | Stav zkontrolujte stabilní doba v milisekundách.|
|    --health-check-wait          | Doba čekání kontroly stavu se měří v milisekundách.|
|    --repliky set kontrola          | Upgrade repliky nastavit časový limit kontroly, které měří v sekundách.|
|    --svc-type-health-map        | JSON kódovaný seznam objektů, které popisují zásady stavu používají k vyhodnocení stavu typy jinou službu.|
|    časový limit – -t                 | Server časový limit v sekundách.  Výchozí: 60.|
|    --není v pořádku aplikace              | Maximální povolené procento žádostí, není v pořádku, než ohlásí chybu.        Například povolit 10 % aplikací jako chybný, tato hodnota je 10. Procento představuje maximální procento. povolená aplikací, které může být není v pořádku, než clusteru považován za chybu. Pokud je dodržena procento, ale existuje alespoň jedna aplikace není v pořádku, stav budou vyhodnocené jako varování. Se počítá jako podíl počtu není v pořádku aplikací přes celkový počet instancí aplikace v clusteru.|
|    --upgrade-domain-timeout     | Časový limit domény upgradu se měří v milisekundách.|
|    – typ upgradu               | Výchozí: vrácení.|
|    --režimu upgradu               | Možné hodnoty patří: "Neplatná', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorované'.  Výchozí: UnmonitoredAuto.|
|    – upgrade vypršení časového limitu            | Časový limit upgradu se měří v milisekundách.|
|    --user                       | Uživatelské jméno pro připojení k registru kontejneru.|
|    --upozornění jako chyba           | Upozornění jsou zachází se stejnou závažnost jako chyby.|

### <a name="global-arguments"></a>Globální argumenty
 |Argument|Popis|
| --- | --- |
|   – ladění                      | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
|    – Nápověda -h                    | Zobrazte tuto zprávu nápovědy a ukončení.|
|   --výstup -o                  | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv. Výchozí: json.|
|   --dotazu                      | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
|   -verbose                    | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).