---
title: Aplikace Azure Service Fabric rozhraní příkazového řádku - sfctl | Microsoft Docs
description: Popisuje příkazy sfctl aplikace Service Fabric rozhraní příkazového řádku.
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
ms.openlocfilehash: 3ecc5a03ff1847dc11c5a5047e35566a4e68fec2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763693"
---
# <a name="sfctl-application"></a>sfctl application
Vytvoření, odstranění a spravovali aplikace a typy aplikací.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří aplikace Service Fabric pomocí zadaný popis. |
| odstraňovat | Odstraní stávající aplikace Service Fabric. |
| Nasazení | Získá informace o aplikace nasazené na uzlu Service Fabric. |
| nasazení stavu | Získá informace o stavu aplikace nasazené na uzlu Service Fabric. |
| nasazení seznamu | Získá seznam aplikace nasazené na uzlu Service Fabric. |
| stav | Získá stav aplikace service fabric. |
| informace | Získá informace o aplikace Service Fabric. |
| Seznam | Získá seznam aplikace vytvořené v clusteru Service Fabric, které odpovídají zadané filtry. |
| načítání | Získá načíst informace o aplikaci Service Fabric. |
| Manifest | Získá manifest popisující typ aplikace. |
| Zřizování | Zřizuje nebo zaregistruje typ aplikace Service Fabric s clusterem pomocí balíčku .sfpkg v externím obchodu nebo pomocí balíčku aplikace v úložišti bitové kopie. |
| Sestava stavu | Odešle zprávu o stavu na aplikace Service Fabric. |
| type | Získá seznam typů aplikací v clusteru Service Fabric odpovídající zadanému názvu. |
| seznam typů | Získá seznam typů aplikací v clusteru Service Fabric. |
| Zrušení zajišťování | Odebere nebo zrušení registrace typu aplikace Service Fabric z clusteru. |
| upgrade | Spustí se upgrade aplikace v clusteru Service Fabric. |
| obnovení upgradu | Obnoví upgrade aplikace v clusteru Service Fabric. |
| vrácení upgradu | Spustí se vracení zpět aktuálně probíhající upgradu aplikace v clusteru Service Fabric. |
| Stav upgradu | Získá informace pro upgrade na nejnovější provést na tuto aplikaci. |
| nahrání | Zkopírujte balíček aplikace Service Fabric do úložiště bitové kopie. |

## <a name="sfctl-application-create"></a>Vytvoření aplikace sfctl
Vytvoří aplikace Service Fabric pomocí zadaný popis.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| název – aplikace [vyžaduje] | Název aplikace, včetně ' fabric\:' schéma identifikátoru URI. |
| Typ – aplikace [vyžaduje] | Název typu aplikace najít v manifestu aplikace. |
| verze – aplikace [vyžaduje] | Verze typu aplikace, jak jsou definovány v manifestu aplikace. |
| – maximální počet uzlů | Maximální počet uzlů, kde bude Service Fabric rezervaci kapacity pro tuto aplikaci. Všimněte si, že to neznamená, že služby tato aplikace se umístí na všechny tyto uzly. |
| --metriky | JSON kódovaný seznam popisů aplikace kapacity metriky. Metrika je definován jako název, související se sadou kapacity pro každý uzel, který aplikace existuje v. |
| – minimální počet uzlů | Minimální počet uzlů, kde bude Service Fabric rezervaci kapacity pro tuto aplikaci. Všimněte si, že to neznamená, že služby tato aplikace se umístí na všechny tyto uzly. |
| – Parametry | Seznam JSON kódovaný aplikace parametr přepsání má být použita při vytváření aplikace. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-delete"></a>odstranění aplikace sfctl
Odstraní stávající aplikace Service Fabric.

Odstraní stávající aplikace Service Fabric. Aplikace musí být vytvořeny, než mohl být odstraněn. Odstranění aplikace odstraní všechny služby, které jsou součástí této aplikace. Ve výchozím nastavení Service Fabric se pokusí v řádně ukončit službu repliky a pak odstraňte službu. Ale pokud služby se vyskytují se problémy řádně zavření repliky, operace odstranění může trvat dlouhou dobu nebo zablokuje. Použijte volitelné příznak ForceRemove přeskočit řádné zavření sekvence a vynuceně odstranit aplikaci a všechny jeho služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --remove silou | Odebrání aplikace Service Fabric nebo služby vynuceně bez průchodu přes pořadí řádné vypnutí. Tento parametr slouží k vynuceně odstranit aplikace nebo služby, pro které odstranění je řádně vypršení časového limitu z důvodu problémů v kódu služby, která zabraňuje ukončení repliky. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-deployed"></a>sfctl aplikace nasazená
Získá informace o aplikace nasazené na uzlu Service Fabric.

Získá informace o aplikace nasazené na uzlu Service Fabric.  Tento dotaz vrací informace o aplikaci system, pokud je zadané ID aplikace pro aplikaci systému. Výsledky zahrnovat nasazené aplikace v aktivní, aktivace a stahování stavy. Tento dotaz vyžaduje, aby název uzlu odpovídá uzlu v clusteru. Se dotaz nezdaří, pokud je název zadaný uzlu neukazuje na všechny aktivní služba Fabric uzly v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --zahrnovat stav stavu | Zahrnout stav entity. Pokud tento parametr je false nebo není zadaná, pak vrátil stav je "Neznámý". Při přechodu nastaven na hodnotu true, dotaz paralelně na uzel a službu stavu systému předtím, než jsou sloučeny výsledky. Dotaz v důsledku toho je dražší a může trvat delší dobu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-deployed-health"></a>nasazení aplikace sfctl-stavu
Získá informace o stavu aplikace nasazené na uzlu Service Fabric.

Získá informace o stavu aplikace nasazené na uzlu Service Fabric. Volitelně můžete filtrovat kolekci objektů HealthEvent ohlášeny nasazené aplikace na základě stavu, které pomocí EventsHealthStateFilter. Volitelně můžete filtrovat na základě stavu podřízené DeployedServicePackageHealth pomocí DeployedServicePackagesHealthStateFilter.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --Deployed-Service-Packages-Health-State-Filter | Umožňuje filtrování nasazené služby balíček stavu stavu objektů vrácených ve výsledku dotazu stavu nasazené aplikace podle jejich stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Nasadit pouze služby, které jsou vráceny balíčky, které odpovídají filtru. Všechny nasazené balíčky služeb se používají k vyhodnocení agregovaný stav nasazené aplikace. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 stav balíčky služeb s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --statistiky vyloučení stavu | Určuje, zda má být vrácen stav statistiky jako součást výsledků dotazu. Chcete-li hodnotu false ve výchozím nastavení. Statistiku zobrazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-deployed-list"></a>nasazení aplikace sfctl – seznam
Získá seznam aplikace nasazené na uzlu Service Fabric.

Získá seznam aplikace nasazené na uzlu Service Fabric. Výsledky neobsahují informace o aplikacích nasazený systém, pokud explicitně dotázána ohledně podle ID. Výsledky zahrnovat nasazené aplikace v aktivní, aktivace a stahování stavy. Tento dotaz vyžaduje, aby název uzlu odpovídá uzlu v clusteru. Se dotaz nezdaří, pokud je název zadaný uzlu neukazuje na všechny aktivní služba Fabric uzly v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --zahrnovat stav stavu | Zahrnout stav entity. Pokud má parametr hodnotu false nebo nebyl zadán, je vrácen stav "Neznámý". Při přechodu nastaven na hodnotu true, dotaz paralelně na uzel a službu stavu systému předtím, než jsou sloučeny výsledky. Dotaz v důsledku toho je dražší a může trvat delší dobu. |
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

## <a name="sfctl-application-health"></a>Stav sfctl aplikací
Získá stav aplikace service fabric.

Vrátí stav stavu aplikace service fabric. Odpověď hlásí stav Ok, chyby nebo upozornění. Pokud entita nebyla nalezena v úložišti stavů, se vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – nasazení aplikace – stavu stavu filtru | Umožňuje filtrování stavu objektů nasazené aplikace stavu vrátil ve výsledku dotazu stavu aplikace na základě jejich stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Pouze nasazené aplikace, které odpovídají filtru, bude vrácen. Všechny nasazené aplikace se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 stav nasazených aplikací s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --statistiky vyloučení stavu | Určuje, zda má být vrácen stav statistiky jako součást výsledků dotazu. Chcete-li hodnotu false ve výchozím nastavení. Statistiku zobrazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --služby stavu stavu filtru | Umožňuje filtrování stavu objektů služby stavu vrátil ve výsledku dotazu stavu služeb na základě jejich stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Vrátí se pouze služby, které odpovídají filtru. Všechny služby se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 pak stavu služeb s hodnotou elementu HealthState OK (2) a upozornění (4), bude vrácen.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-info"></a>informace o aplikaci sfctl
Získá informace o aplikace Service Fabric.

Vrací informace o aplikaci, která byla vytvořena nebo právě probíhá vytváření clusteru Service Fabric a jejíž název odpovídá zadanému jako parametr. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --vyloučení parametry aplikace | Příznak, který určuje, zda aplikace parametry budou vyloučeny z výsledek. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-list"></a>seznam aplikací sfctl
Získá seznam aplikace vytvořené v clusteru Service Fabric, které odpovídají zadané filtry.

Získá informace o aplikacích, které byly vytvořeny nebo právě probíhá vytváření v Service Fabric clusteru a odpovídají určenému filtru. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci. Pokud aplikace na stránce nebudou vyhovovat, vrátí se jeden stránky s výsledky a také pokračovací token, který můžete použít k získání na další stránku. Filtry ApplicationTypeName a ApplicationDefinitionKindFilter nelze zadat současně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --aplikace definice typ filtru | Použít pro filtrování ApplicationDefinitionKind, což je mechanismus používaný k definování aplikace Service Fabric.  <br> -Výchozí – výchozí hodnotu, která provádí stejnou funkci jako výběrem možnosti "Všechny". Hodnota je 0.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou ApplicationDefinitionKind. Hodnota je 65535.  <br> -ServiceFabricApplicationDescription - filtr, který odpovídá vstup s hodnotou ApplicationDefinitionKind ServiceFabricApplicationDescription. Hodnota je 1.  <br> -Vytvářené - filtr, který odpovídá vstup s hodnotou ApplicationDefinitionKind vytvářené. Hodnota je 2. |
| – Název typu aplikace | Název typu aplikace použít k filtrování aplikace, které chcete vyhledat. Tato hodnota by neměla obsahovat verze typu aplikace. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --vyloučení parametry aplikace | Příznak, který určuje, zda aplikace parametry budou vyloučeny z výsledek. |
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

## <a name="sfctl-application-load"></a>zatížení sfctl aplikace
Získá načíst informace o aplikaci Service Fabric.

Vrací zatížení informace o aplikaci, která byla vytvořena nebo právě probíhá vytváření clusteru Service Fabric a jejíž název odpovídá zadanému jako parametr. Odpověď obsahuje název, uzly minimální, maximální počet uzlů, počet uzlů, ke kterému je aktuálně zabírá aplikace a aplikace zatížení metriky informace o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-manifest"></a>manifest aplikace sfctl
Získá manifest popisující typ aplikace.

Získá manifest popisující typ aplikace. Odpověď obsahuje manifest aplikace XML jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– Název typu aplikace – [vyžaduje] | Název typu aplikace. |
| --– verze typu aplikace – [vyžaduje] | Verze typu aplikace. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-provision"></a>zřídit aplikace sfctl
Zřizuje nebo zaregistruje typ aplikace Service Fabric s clusterem pomocí balíčku .sfpkg v externím obchodu nebo pomocí balíčku aplikace v úložišti bitové kopie.

Zřídí typu aplikace Service Fabric s clusterem. To je potřeba, než může být vytvořena instance všechny nové aplikace. Zřízení operaci lze provést buď na zadaný relativePathInImageStore, nebo pomocí identifikátoru URI externí .sfpkg balíčku aplikace. Pokud – externí provision nastavena, bude tento příkaz očekávat zřízení úložiště bitové kopie.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --aplikace balíčku stažení uri | Cesta k balíčku aplikace, .sfpkg, odkud balíčku aplikace si můžete stáhnout pomocí protokolů HTTP nebo HTTPS. <br><br> Pro typ zřizování externího úložiště jenom. Balíček aplikace může být uložen v externím obchodu, která poskytuje operaci GET stažení souboru. Jsou podporované protokoly HTTP a HTTPS a cesta musí umožňovat přístup pro čtení. |
| – aplikace typu sestavení cesta | Pro zřízení pouze typu image store. Relativní cestu k balíčku aplikace v úložišti bitové kopie zadaný během operace odesílání předchozí. |
| – Název typu aplikace | Pro typ zřizování externího úložiště jenom. Název typu aplikace představuje název typu aplikace najít v manifestu aplikace. |
| --verze typu aplikace | Pro typ zřizování externího úložiště jenom. Verze typu aplikace představuje verze typu aplikace najít v manifestu aplikace. |
| --externí zřizování | Umístění, ze kde balíček aplikace může být zaregistrován nebo zřízený. Označuje, že zřizování je pro balíček aplikace, která se předtím nahrála do externího úložiště. Balíček aplikace končí *.sfpkg rozšíření. |
| – Ne čekání | Určuje, zda zřizování provedeno asynchronně. <br><br> Pokud nastavíte na hodnotu true, vrátí zřídit operaci, když je systém přijme požadavek, a operaci zřizování, pokračuje bez jakékoli časový limit. Výchozí hodnota je false. U velkých balíčků aplikací doporučujeme nastavení na hodnotu true. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-report-health"></a>Sestava stavu sfctl aplikací
Odešle zprávu o stavu na aplikace Service Fabric.

Sestava stavu zadané aplikace Service Fabric. Sestava musí obsahovat informace o zdroji této sestavy stavu a vlastnosti, na kterém je zaznamenána. Sestava je odeslána do Service Fabric brány aplikace, který předává do úložiště stavu. Sestava může přijal bránou, ale byl odmítnut úložiště zdravotní po doplňující ověření. Úložiště stavu může například odmítnout sestavy z důvodu neplatný parametr, jako je zastaralé pořadové číslo. Pokud chcete zjistit, zda sestava byla použita v health store, získání stavu aplikací a zkontrolujte, že sestava se zobrazí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. <br><br> To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s '\~' znak. Například, pokud je název aplikace se prostředků infrastruktury\:/myapp/app1 ', identita aplikace by se Moje aplikace\~app1' v 6.0 + a ' myapp/app1' v předchozích verzích. |
| --stavu – vlastnost [vyžaduje] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a pevnou výčtu umožňuje flexibilitu ohlašování zařadit do kategorií stavu podmínku, která aktivuje sestavy. Ohlašování s ID zdroje "LocalWatchdog" například můžete monitorovat stav je k dispozici disk na uzlu, takže ho mohou zasílat zprávy o "AvailableDisk" vlastnost v tomto uzlu. Stejné ohlašování můžete monitorovat připojení k uzlu, takže ho mohou zasílat zprávy o vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za samostatné stavu události pro určeného uzlu. Společně s ID zdroje vlastnost jednoznačně identifikuje informace o stavu. |
| --stavu [vyžaduje] | Možné hodnoty patří\: 'Neplatný', 'Ok', 'Upozornění', "Chyba", "Neznámý". |
| – id zdroje [vyžaduje] | Název zdroje, který identifikuje součásti klienta nebo sledovací zařízení nebo systému, která generuje informace o stavu. |
| – Popis | Popis informací o stavu. <br><br> Reprezentuje textové použít k přidání lidského čtení informací o sestavě. Maximální délka řetězce pro popis je 4 096 znaků. Pokud zadaný text je delší, bude automaticky zkrácen. Pokud zkrácen, poslední znaky popis obsahovat značku "[Truncated]" a velikost celkový řetězce je 4 096 znaků. Přítomnost značky naznačuje pro uživatele, že zkrácení došlo k chybě. Upozorňujeme, že pokud zkrácen, popis má méně než 4 096 znaků z původního řetězce. |
| --okamžitou | Příznak, který indikuje, zda mají být sestavy odesílány okamžitě. <br><br> Sestava stavu posílá bránu Service Fabric aplikace, který předává do úložiště stavu. Pokud Immediate je nastaven na hodnotu true, zpráva se odešle okamžitě z brány protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá aplikace brány protokolu HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále selžou, například pokud je uzavřen bráně HTTP nebo zprávy není kontaktovat bránu. Pokud Immediate nastavena na hodnotu false, sestavy se odesílá podle nastavení stavu klienta z brány v protokolu HTTP. Proto se bude zpracovat v dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučená nastavení, protože umožňuje stavu klienta k optimalizaci zpráv, které mají stav úložiště, jakož i zpracování sestavy stavu vykazování stavu. Ve výchozím nastavení nejsou odesílány okamžitě sestavy. |
| --odebrat Pokud platnost | Hodnota, která určuje, zda sestava je po jeho vypršení odebrat z health store. <br><br> Pokud je nastaven na hodnotu true, sestavy se odebere z health store po vypršení platnosti. Pokud je nastaven na hodnotu false, sestava je považovat za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnota false. Když klienti pravidelně zprávy, nastavují by měl RemoveWhenExpired NEPRAVDA (výchozí). Tímto způsobem je zpravodaje, která se vyskytují problémy (například zablokování) a nemůžete hlásit entity je vyhodnocován v chybě, když vyprší platnost sestava stavu. Označí entitu, že je ve stavu chyby příznakem. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselných řetězců. <br><br> Pořadové číslo sestavy se používají ve store stavu ke zjišťování zastaralých sestavy. Pokud není zadáno, je číslo sekvence automaticky generovaný klientem stavu při přidání sestavy. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| Hodnota ttl-- | Doba trvání, pro který je tato sestava stavu platný. Toto pole pro zadání trvání používá ve formátu ISO 8601. <br><br> Když klienti pravidelně zprávy, se musí odesílat zprávy s četností vyšší než hodnota time to live. Pokud klienti zprávy na přechod, nastavují TTL k nekonečné. Když vyprší platnost hodnota time to live, událost stavu, který obsahuje informace o stavu je buď odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocen na chyby, pokud RemoveWhenExpired false. Pokud není zadaný, hodnota time to live výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-type"></a>Typ aplikace sfctl
Získá seznam typů aplikací v clusteru Service Fabric odpovídající zadanému názvu.

Vrací informace o typech aplikací, které jsou zřízené nebo právě probíhá zřizování v clusteru Service Fabric. Tyto výsledky se typy aplikací, jejíž název odpovídá přesně jeden zadaný jako parametr, a které jsou v souladu s parametry daný dotaz. Každá verze vrácena jako jeden typ aplikace jsou vráceny všechny verze aplikace typ odpovídající názvu typu aplikace. Odpověď obsahuje název, verzi, stav a další podrobnosti o typu aplikace. Toto je stránkové dotaz, což znamená, není-li všechny typy aplikací nevejde na stránce se vrátí jednu stránku výsledků a také pokračovací token, který můžete použít k získání na další stránku. Například pokud existují 10 typy aplikací, ale na stránce odpovídá pouze první typy tři aplikací, nebo pokud maximální výsledek je nastaven na 3, 3 je vracen. Pro přístup k zbytek výsledky se načtěte následující stránky s použitím token vrácený pokračování v dalším dotazu. Token prázdný pokračování je vrácena, pokud nejsou žádné další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– Název typu aplikace – [vyžaduje] | Název typu aplikace. |
| --verze typu aplikace | Verze typu aplikace. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --vyloučení parametry aplikace | Příznak, který určuje, zda aplikace parametry budou vyloučeny z výsledek. |
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

## <a name="sfctl-application-type-list"></a>sfctl typu – seznam aplikací
Získá seznam typů aplikací v clusteru Service Fabric.

Vrací informace o typech aplikací, které jsou zřízené nebo právě probíhá zřizování v clusteru Service Fabric. Každá verze nástroje typ aplikace je vrácena jako typ jednu aplikaci. Odpověď obsahuje název, verzi, stav a další podrobnosti o typu aplikace. Toto je stránkové dotaz, což znamená, není-li všechny typy aplikací nevejde na stránce se vrátí jednu stránku výsledků a také pokračovací token, který můžete použít k získání na další stránku. Například pokud existují 10 typy aplikací, ale na stránce odpovídá pouze první typy tři aplikací, nebo pokud maximální výsledek je nastaven na 3, 3 je vracen. Pro přístup k zbytek výsledky se načtěte následující stránky s použitím token vrácený pokračování v dalším dotazu. Token prázdný pokračování je vrácena, pokud nejsou žádné další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace typu definice typ filtru | Použít pro filtrování ApplicationTypeDefinitionKind, což je mechanismus používaný k definování typu aplikace Service Fabric.  <br> -Výchozí – výchozí hodnotu, která provádí stejnou funkci jako výběrem možnosti "Všechny". Hodnota je 0.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou ApplicationTypeDefinitionKind. Hodnota je 65535.  <br> -ServiceFabricApplicationPackage - filtr, který odpovídá vstup s hodnotou ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. Hodnota je 1.  <br> -Vytvářené - filtr, který odpovídá vstup s hodnotou ApplicationTypeDefinitionKind vytvářené. Hodnota je 2. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --vyloučení parametry aplikace | Příznak, který určuje, zda aplikace parametry budou vyloučeny z výsledek. |
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

## <a name="sfctl-application-unprovision"></a>unprovision sfctl aplikace
Odebere nebo zrušení registrace typu aplikace Service Fabric z clusteru.

Odebere nebo zrušení registrace typu aplikace Service Fabric z clusteru. Tuto operaci může provést pouze v případě byly odstraněny všechny instance aplikace typu aplikace. Po zrušení registrace typu aplikace nelze vytvořit žádné nové instance aplikace pro tento typ konkrétní aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– Název typu aplikace – [vyžaduje] | Název typu aplikace. |
| --– verze typu aplikace – [vyžaduje] | Verze typu aplikace, jak jsou definovány v manifestu aplikace. |
| --async-parameter | Příznak, který udává, zda unprovision provedeno asynchronně. Pokud nastavíte na hodnotu true, vrátí unprovision operaci, když systém přijme požadavek, a operaci unprovision pokračuje bez jakékoli časový limit. Výchozí hodnota je false. Doporučujeme však jeho nastavení na hodnotu true pro rozsáhlé aplikace balíčky, které byly zřízeny. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-upgrade"></a>upgrade aplikace sfctl
Spustí se upgrade aplikace v clusteru Service Fabric.

Ověří upgradu parametry zadané aplikace a spustí upgradu aplikace, pokud jsou parametry platné. Všimněte si, že upgrade popis nahradí existující popis aplikace. To znamená, že pokud nejsou zadané parametry, parametrech existující aplikace, budou přepsány seznamu prázdné parametry. To by způsobilo aplikace pomocí výchozí hodnoty parametrů z manifestu aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. <br><br> To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s '\~' znak. Například, pokud je název aplikace se prostředků infrastruktury\:/myapp/app1 ', identita aplikace by se Moje aplikace\~app1' v 6.0 + a ' myapp/app1' v předchozích verzích. |
| --verze aplikace [vyžaduje] | Cílová verze aplikace. |
| --parametrů [požadovaných] | Seznam JSON kódovaný aplikace parametr přepsání použijí při upgradu aplikace. |
| --default-service-health-policy | JSON kódovaný specifikace zásad stavu, ve výchozím nastavení použit k vyhodnocení stavu typu služby. |
| --selhání akce | Akce se provede při upgradu monitorované zaznamená monitorování zásad nebo stavu porušení zásad. |
| --Vynutit restartování | Vynuceně restartování procesů během upgradu, i když verze kódu nebylo změněno. |
| --stavu – kontrola opakování-časový limit | Množství času opakovat hodnocení stavu, pokud je není v pořádku, než akce selhání aplikace nebo clusteru je spustit. Měří v milisekundách.  Výchozí\: PT0H10M0S. |
| --stavu kontrola stabilní trvání | Množství času, aby aplikace nebo clusteru musí zůstat v pořádku před upgradem k další upgradovací doméně. Měří v milisekundách.  Výchozí\: PT0H2M0S. |
| --stavu kontrola čekací doba | Množství času čekání po dokončení upgradu domény před použitím zásad stavu. Měří v milisekundách.  Výchozí\: 0. |
| --max-unhealthy-apps | Maximální povolené procento není v pořádku nasazené aplikace. Reprezentován jako číslo mezi 0 a 100. |
| --režimu | Režim použitý pro sledování stavu během postupného upgradu.  Výchozí\: UnmonitoredAuto. |
| --repliky set kontrola-časový limit | Maximální množství času blokovat zpracování upgradu domény a zabránit ztrátě dostupnosti po neočekávaným problémům. Měří v sekundách. |
| --service-health-policy | JSON kódovaný mapa s zásady stavu typu služby za název typu služby. Mapy je prázdný být výchozí. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --upgrade-domain-timeout | Množství času každé upgradované domény musí dokončit před provedením FailureAction. Měří v milisekundách.  Výchozí\: P10675199DT02H48M05.4775807S. |
| – upgrade vypršení časového limitu | Množství času celkové upgrade musí dokončit před provedením FailureAction. Měří v milisekundách.  Výchozí\: P10675199DT02H48M05.4775807S. |
| --upozornění jako chyba | Vyhodnocení upozornění stavu s stejné závažnost považovat za chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-upgrade-resume"></a>upgrade aplikace sfctl-obnovení
Obnoví upgrade aplikace v clusteru Service Fabric.

Obnoví aplikace Service Fabric sledována ruční upgrade. Service Fabric upgraduje upgradovací domény současně. Pro sledována ruční upgrade po dokončení upgradu domény, Service Fabric se čeká na volat toto rozhraní API před pokračováním na další upgradovací doméně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --upgradu domain-name [vyžaduje] | Název upgradovací doméně, ve kterém chcete-li pokračovat v upgradu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-upgrade-rollback"></a>upgrade aplikace sfctl-vrácení
Spustí se vracení zpět aktuálně probíhající upgradu aplikace v clusteru Service Fabric.

Spustí vrácení zpět aktuální aplikace upgradovat na předchozí verzi. Toto rozhraní API slouží pouze k vrácení aktuální v průběhu upgradu, které dál zavádí novou verzi. Pokud aplikace není právě probíhá upgrade pomocí rozhraní API StartApplicationUpgrade upgradovat na požadovanou verzi, včetně, abyste vrátili zpět na předchozí verze.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-upgrade-status"></a>upgrade aplikace sfctl-status
Získá informace pro upgrade na nejnovější provést na tuto aplikaci.

Vrací informace o stavu nejnovější upgradu aplikace společně se informace, které pomáhají problémy v oblasti stavu ladění aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-application-upload"></a>Nahrání aplikace sfctl
Zkopírujte balíček aplikace Service Fabric do úložiště bitové kopie.

Volitelně můžete zobrazte průběhu odesílání pro každý soubor v balíčku. Nahrát posílá průběh `stderr`.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Cesta [vyžaduje] | Cesta k balíčku místní aplikace. |
| úložiště bitových kopií – řetězec | Cílové image úložiště pro nahrání balíčku aplikace.  Výchozí\: fabric\:úložiště bitových kopií. |
| – Zobrazí průběh | Ukázat průběh nahrávání souboru pro velké balíčky. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |



## <a name="next-steps"></a>Další postup
- [Instalační program](service-fabric-cli.md) Service Fabric rozhraní příkazového řádku.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
