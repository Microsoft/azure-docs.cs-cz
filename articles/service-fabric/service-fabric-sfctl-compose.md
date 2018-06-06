---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl tvoří | Microsoft Docs
description: Popisuje rozhraní příkazového řádku Service Fabric tvoří sfctl příkazy.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cc3d3e35ce3dd457d981dfe9420be765cf9fc45a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763404"
---
# <a name="sfctl-compose"></a>sfctl compose
Vytvoření, odstranění a spravovat Docker Compose aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří Service Fabric tvoří nasazení. |
| Seznam | Získá seznam tvoří nasazení vytvořených v clusteru Service Fabric. |
| odebrat | Odstranění existující Service Fabric tvoří nasazení z clusteru. |
| status | Získá informace o Service Fabric tvoří nasazení. |
| upgrade | Spustí se upgrade nové nasazení v clusteru Service Fabric. |
| Stav upgradu | Získá informace pro upgrade na nejnovější provést na tento Service Fabric tvoří nasazení. |

## <a name="sfctl-compose-create"></a>Napište sfctl vytvořit
Vytvoří Service Fabric tvoří nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje] | Název nasazení. |
| – Cesta [vyžaduje] | Cesta k cílovému souboru Docker Compose. |
| --šifrované průchodu | Místo výzvy k zadání hesla registru kontejner, použijte již zašifrované heslo. |
| --has-pass | Zobrazí výzvu k zadání hesla pro kontejner registru. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --user | Uživatelské jméno pro připojení k registru kontejneru. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-compose-list"></a>sfctl tvoří seznamu
Získá seznam tvoří nasazení vytvořených v clusteru Service Fabric.

Získá stav o nové nasazení, které byly vytvořeny nebo právě probíhá vytváření v clusteru Service Fabric. Odpověď obsahuje název, stav a další podrobnosti o nové nasazení. Pokud na stránce nebudou vyhovovat seznamu nasazení, vrátí se jednu stránku výsledků a také pokračovací token, který můžete použít k získání na další stránku.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácen jako součást stránkové dotazy. Tento parametr určuje horní mez počtu výsledků vrácených. Vráceny výsledky mohou být menší než zadaná maximální výsledky, pokud nebudou vyhovovat ve zprávě podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nulová nebo není zadaný, stránkové dotaz obsahuje tolik výsledky jako možné, že se nevešla návratové zprávy. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-compose-remove"></a>sfctl tvoří odebrat
Odstranění existující Service Fabric tvoří nasazení z clusteru.

Odstranění existující Service Fabric tvoří nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje] | Identita nasazení. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-compose-status"></a>sfctl tvoří stav
Získá informace o Service Fabric tvoří nasazení.

Vrátí stav nové nasazení, který byl vytvořen nebo právě probíhá vytváření clusteru Service Fabric a jejíž název odpovídá zadanému jako parametr. Odpověď obsahuje název, stav a další podrobnosti o tomto nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje] | Identita nasazení. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-compose-upgrade"></a>sfctl tvoří upgradu
Spustí se upgrade nové nasazení v clusteru Service Fabric.

Ověří zadaný upgradu parametry a spustí upgradu nasazení, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje] | Název nasazení. |
| – Cesta [vyžaduje] | Cesta k cílovému Docker compose souboru. |
| --default-svc-type-health-map | JSON kódovaný slovník, který popisuje zásady stavu používají k vyhodnocení stavu služeb. |
| --šifrované průchodu | Místo výzvy k zadání hesla registru kontejner, použijte již zašifrované heslo. |
| --selhání akce | Možné hodnoty patří\: "Neplatná',"Vrácení","Ruční". |
| --Vynutit restartování | Vynuťte restartování. |
| --has-pass | Zobrazí výzvu k zadání hesla pro kontejner registru. |
| --stavu. Zkontrolujte opakování | Časový limit opakování kontroly stavu se měří v milisekundách. |
| --stavu. Zkontrolujte nestabilním | Stav zkontrolujte stabilní doba v milisekundách. |
| --health-check-wait | Doba čekání kontroly stavu se měří v milisekundách. |
| --repliky set kontrola | Upgrade repliky nastavit časový limit kontroly, které měří v sekundách. |
| --svc-type-health-map | JSON kódovaný seznam objektů, které popisují zásady stavu používají k vyhodnocení stavu typy jinou službu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --není v pořádku aplikace | Maximální povolené procento žádostí, není v pořádku, než ohlásí chybu. <br><br> Například povolit 10 % aplikací jako chybný, tato hodnota je 10. Procento představuje maximální procento. povolená aplikací, které může být není v pořádku, než clusteru považován za chybu. Pokud je dodržena procento, ale existuje alespoň jedna aplikace není v pořádku, stav budou vyhodnocené jako varování. To se vypočítá jako podíl počtu není v pořádku aplikace přes celkový počet instancí aplikace v clusteru. |
| --upgrade-domain-timeout | Časový limit domény upgradu se měří v milisekundách. |
| – typ upgradu | Výchozí\: vrácení. |
| --režimu upgradu | Možné hodnoty patří\: "Neplatná', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorované'.  Výchozí\: UnmonitoredAuto. |
| – upgrade vypršení časového limitu | Časový limit upgradu se měří v milisekundách. |
| --user | Uživatelské jméno pro připojení k registru kontejneru. |
| --upozornění jako chyba | Upozornění jsou zachází se stejnou závažnost jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl tvoří stav upgradu
Získá informace pro upgrade na nejnovější provést na tento Service Fabric tvoří nasazení.

Vrací informace o stavu nasazení upgradu vytvářené společně s informace, které pomáhají problémy v oblasti stavu ladění aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název nasazení [vyžaduje] | Identita nasazení. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |



## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).