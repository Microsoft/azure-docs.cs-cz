---
title: Aplikace Azure Service Fabric CLI- sfctl
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu aplikací.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b4e1066bba1db387c9dc0600bc55522f0b5fe897
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906207"
---
# <a name="sfctl-application"></a>sfctl application
Vytvářejte, odstraňujte a spravujte aplikace a typy aplikací.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří aplikaci Service Fabric pomocí zadaného popisu. |
| delete | Odstraní existující aplikaci Service Fabric. |
| Nasazeny | Získá informace o aplikaci nasazené v uzlu Service Fabric. |
| nasazené zdraví | Získá informace o stavu aplikace nasazené v uzlu Service Fabric. |
| nasazený seznam | Získá seznam aplikací nasazených v uzlu Service Fabric. |
| Zdraví | Získá stav aplikace service fabric. |
| Info | Získá informace o aplikaci Service Fabric. |
| list | Získá seznam aplikací vytvořených v clusteru Service Fabric, které odpovídají zadané filtry. |
| načítání | Získá informace o zatížení aplikace Service Fabric. |
| manifest | Získá manifest popisující typ aplikace. |
| Poskytování | Zřídí nebo zaregistruje typ aplikace Service Fabric s clusterem pomocí balíčku '.sfpkg' v externím úložišti nebo pomocí balíčku aplikace v úložišti bitových obrázků. |
| zpráva-zdraví | Odešle zprávu o stavu aplikace Service Fabric. |
| type | Získá seznam typů aplikací v clusteru Service Fabric odpovídající přesně zadaný název. |
| seznam typů | Získá seznam typů aplikací v clusteru Service Fabric. |
| zrušení poskytnutí | Odebere nebo zruší registraci typu aplikace Service Fabric z clusteru. |
| Upgrade | Spustí upgrade aplikace v clusteru Service Fabric. |
| obnovení upgradu | Pokračuje v inovaci aplikace v clusteru Service Fabric. |
| vrácení upgradu | Spustí vrácení aktuálně probíhá inovace aplikace v clusteru Service Fabric. |
| stav upgradu | Získá podrobnosti o nejnovější upgrade provádí v této aplikaci. |
| nahrání | Zkopírujte balíček aplikace Service Fabric do úložiště obrázků. |

## <a name="sfctl-application-create"></a>sfctl vytvoření aplikace
Vytvoří aplikaci Service Fabric pomocí zadaného popisu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název aplikace [Povinné] | Název aplikace, včetně schématu\:identifikátoru URI struktury prostředků fabric. |
| --typ aplikace [Povinné] | Název typu aplikace nalezený v manifestu aplikace. |
| --verze aplikace [Povinné] | Verze typu aplikace, jak je definována v manifestu aplikace. |
| --max-node-count --max-node-count --max-node-count --max | Maximální počet uzlů, kde Service Fabric vyhradí kapacitu pro tuto aplikaci. Všimněte si, že to neznamená, že služby této aplikace budou umístěny na všech těchto uzlech. |
| --metriky | JSON kódovaný seznam popisů metrik kapacity aplikace. Metrika je definována jako název přidružený k sadě kapacit pro každý uzel, ve kterých aplikace existuje. |
| --min-node-count | Minimální počet uzlů, kde Service Fabric vyhradí kapacitu pro tuto aplikaci. Všimněte si, že to neznamená, že služby této aplikace budou umístěny na všech těchto uzlech. |
| --parametry | JSON kódovaný seznam parametrů aplikace přepíše, které mají být použity při vytváření aplikace. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-delete"></a>sfctl aplikace odstranit
Odstraní existující aplikaci Service Fabric.

Před odstraněním je nutné vytvořit aplikaci. Odstraněním aplikace odstraníte všechny služby, které jsou součástí této aplikace. Ve výchozím nastavení service fabric se pokusí zavřít repliky služby v řádném způsobem a potom odstranit službu. Pokud však služba má problémy s řádném uzavřením repliky, operace odstranění může trvat dlouhou dobu nebo uvíznou. Pomocí volitelného příznaku ForceRemove můžete přeskočit řádné pořadí zavření a důrazně odstranit aplikaci a všechny její služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --force-remove | Odeberte aplikaci nebo službu Service Fabric vynuceně, aniž byste museli projít sekvencí řádného vypnutí. Tento parametr lze použít k vynucené odstranění aplikace nebo služby, pro které je vypršení časového limitu z důvodu problémů v kódu služby, které brání řádnému uzavření replik. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-deployed"></a>SfcTL aplikace nasazena
Získá informace o aplikaci nasazené v uzlu Service Fabric.

Tento dotaz vrátí informace o systémové aplikaci, pokud je id aplikace pro systémovou aplikaci. Výsledky zahrnují nasazené aplikace v aktivních, aktivačních a stahovacích stavech. Tento dotaz vyžaduje, aby název uzlu odpovídal uzlu v clusteru. Dotaz se nezdaří, pokud zadaný název uzlu neukazuje na žádné aktivní uzly Service Fabric v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --include-health-state | Zahrňte stav entity. Pokud je tento parametr false nebo není zadán, je vrácený stav "Neznámý". Pokud je nastavena na hodnotu true, dotaz jde paralelně k uzlu a služby systému zdravotního stavu před sloučením výsledků. V důsledku toho dotaz je dražší a může trvat delší dobu. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-deployed-health"></a>sfctl aplikace nasazena-zdraví
Získá informace o stavu aplikace nasazené v uzlu Service Fabric.

Získá informace o stavu aplikace nasazené v uzlu Service Fabric. Pomocí filtru EventsHealthStateFilter můžete volitelně filtrovat kolekci objektů HealthEvent vykazovaných v nasazené aplikaci na základě stavu. Pomocí nástroje DeployedServicePackagesHealthStateFilter můžete volitelně filtrovat děti DeployedServicePackageHealth na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --deployed-service-packages-state-filter --deployed-service-packages-state-filter --deployed-service-packages-state-filter --deployed-service-packages- | Umožňuje filtrování objektů stavu balíčku nasazených služeb vrácených v důsledku nasazeného dotazu stavu aplikace na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze nasazené balíčky služeb, které odpovídají filtru. Všechny nasazené balíčky služeb se používají k vyhodnocení agregovaného stavu nasazené aplikace. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například pokud je zadaný hodnota 6 pak stav služby balíčky s HealthState hodnotu OK (2) a upozornění (4) jsou vráceny.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --events-health-state-filter | Umožňuje filtrování kolekce HealthEvent objekty vrácené na základě stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak jsou vráceny všechny události s HealthState hodnotu OK (2) a Upozornění (4).  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --vyloučit-zdravotní statistiky | Označuje, zda by měly být vráceny statistiky stavu jako součást výsledku dotazu. False ve výchozím nastavení. Statistiky ukazují počet podřízených entit ve stavu Ok, Upozornění a Chyba. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-deployed-list"></a>sfctl aplikace nasazený seznam
Získá seznam aplikací nasazených v uzlu Service Fabric.

Získá seznam aplikací nasazených v uzlu Service Fabric. Výsledky nezahrnují informace o nasazených systémových aplikacích, pokud není explicitně dotazován id. Výsledky zahrnují nasazené aplikace v aktivních, aktivačních a stahovacích stavech. Tento dotaz vyžaduje, aby název uzlu odpovídal uzlu v clusteru. Dotaz se nezdaří, pokud zadaný název uzlu neukazuje na žádné aktivní uzly Service Fabric v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --include-health-state | Zahrňte stav entity. Pokud je tento parametr false nebo není zadán, je vrácený stav "Neznámý". Pokud je nastavena na hodnotu true, dotaz jde paralelně k uzlu a služby systému zdravotního stavu před sloučením výsledků. V důsledku toho dotaz je dražší a může trvat delší dobu. |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-health"></a>sfctl stav aplikace
Získá stav aplikace service fabric.

Vrátí stav vřesoviště aplikace service fabric. Odpověď hlásí stav Ok, Chyba nebo Upozornění. Pokud entita není nalezena v úložišti stavu, vrátí Error.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --deployed-applications-state-filter --deployed-applications-state-filter --deployed-applications-state-filter --deployed-applications-state- | Umožňuje filtrování objektů stavu nasazených aplikací vrácených v důsledku dotazu stavu aplikace na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Budou vráceny pouze nasazené aplikace, které odpovídají filtru. Všechny nasazené aplikace se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například pokud je zadaný hodnota 6 pak stav nasazených aplikací s HealthState hodnotu OK (2) a upozornění (4) jsou vráceny.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --events-health-state-filter | Umožňuje filtrování kolekce HealthEvent objekty vrácené na základě stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak jsou vráceny všechny události s HealthState hodnotu OK (2) a Upozornění (4).  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --vyloučit-zdravotní statistiky | Označuje, zda by měly být vráceny statistiky stavu jako součást výsledku dotazu. False ve výchozím nastavení. Statistiky ukazují počet podřízených entit ve stavu Ok, Upozornění a Chyba. |
| --služby-stav-filtr | Umožňuje filtrování objektů stavu služeb vrácených v důsledku dotazu na stav služeb na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze služby, které odpovídají filtru. Všechny služby se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například pokud je zadaný hodnota 6 pak stav stavu služeb s HealthState hodnotu OK (2) a upozornění (4) budou vráceny.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-info"></a>sfctl informace o aplikaci
Získá informace o aplikaci Service Fabric.

Vrátí informace o aplikaci, která byla vytvořena nebo právě vytvářena v clusteru Service Fabric a jejíž název odpovídá názvu zadanému jako parametr. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters --exclude | Příznak, který určuje, zda parametry aplikace budou vyloučeny z výsledku. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-list"></a>seznam aplikací sfcTL
Získá seznam aplikací vytvořených v clusteru Service Fabric, které odpovídají zadané filtry.

Získá informace o aplikacích, které byly vytvořeny nebo v procesu vytváření v clusteru Service Fabric a odpovídají zadané filtry. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci. Pokud se aplikace nevejdou na stránku, vrátí se jedna stránka výsledků a token pokračování, který lze použít k získání další stránky. Filtry ApplicationTypeName a ApplicationDefinitionKindFilter nelze zadat současně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --application-definition-kind-filter --application-definition-filter --application-definition-filter --application-definition-filter | Slouží k filtrování na ApplicationDefinitionKind, což je mechanismus používaný k definování aplikace Service Fabric.  <br> - Výchozí - Výchozí hodnota, která plní stejnou funkci jako výběr "Vše". Hodnota je 0.  <br> - Všechny - Filtr, který odpovídá vstupu s libovolnou hodnotou ApplicationDefinitionKind. Hodnota je 65535.  <br> - ServiceFabricApplicationDescription - Filtr, který odpovídá vstupu s value ApplicationDefinitionKind ServiceFabricApplicationDescription. Hodnota je 1.  <br> - Compose - Filtr, který odpovídá vstupu s ApplicationDefinitionKind hodnota Compose. Hodnota je 2. |
| --název typu aplikace | Název typu aplikace používaný k filtrování aplikací, na které se má dotazovat. Tato hodnota by neměla obsahovat verzi typu aplikace. |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters --exclude | Příznak, který určuje, zda parametry aplikace budou vyloučeny z výsledku. |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-load"></a>aplikační zatížení sfctl
Získá informace o zatížení aplikace Service Fabric.

Vrátí informace o zatížení aplikace, která byla vytvořena nebo právě vytvářena v clusteru Service Fabric a jejíž název odpovídá názvu zadanému jako parametr. Odpověď obsahuje název, minimální uzly, maximální uzly, počet uzlů, které aplikace aktuálně zabírá, a informace o metrikách zatížení aplikace o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-manifest"></a>manifest aplikace sfctl
Získá manifest popisující typ aplikace.

Odpověď obsahuje manifest jazyka XML aplikace jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název typu aplikace [Povinné] | Název typu aplikace. |
| --verze typu aplikace [Povinné] | Verze typu aplikace. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-provision"></a>sfctl aplikační ustanovení
Zřídí nebo zaregistruje typ aplikace Service Fabric s clusterem pomocí balíčku '.sfpkg' v externím úložišti nebo pomocí balíčku aplikace v úložišti bitových obrázků.

Zřídí typ aplikace Service Fabric s clusterem. Před vytvořením instance všech nových aplikací je nutné toto ustanovení. Operace zřizování lze provést buď na balíčku aplikace určeném relativníPathInImageStore nebo pomocí URI externí '.sfpkg'. Pokud --external-provision je nastavena, tento příkaz bude očekávat, že úložiště bitových obrázků zřizování.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --aplikace-balíček-download-uri | Cesta k balíčku aplikace '.sfpkg' ze kterého lze balíček aplikace stáhnout pomocí protokolů HTTP nebo HTTPS. <br><br> Pouze pro externí úložiště. Balíček aplikace lze uložit do externího úložiště, které poskytuje get operaci ke stažení souboru. Podporované protokoly jsou HTTP a HTTPS a cesta musí umožňovat přístup ke čtení. |
| --cesta typu aplikace | Pouze pro poskytování druhu úložiště obrázků. Relativní cesta pro balíček aplikace v úložišti obrázků zadané během operace předchozího nahrávání. |
| --název typu aplikace | Pouze pro externí úložiště. Název typu aplikace představuje název typu aplikace nalezený v manifestu aplikace. |
| --verze typu aplikace | Pouze pro externí úložiště. Verze typu aplikace představuje verzi typu aplikace, která se nachází v manifestu aplikace. |
| --externí poskytování | Umístění, odkud lze zaregistrovat nebo zřídit balíček aplikace. Označuje, že ustanovení je pro balíček aplikace, který byl dříve odeslán do externího úložiště. Balíček aplikace končí příponou *.sfpkg. |
| --ne-čekat | Označuje, zda zřizování by mělo dojít asynchronně. <br><br> Pokud je nastavena na hodnotu true, operace zřízení vrátí, když je požadavek přijat systémem a operace zřízení pokračuje bez časového limitu. Výchozí hodnota je False. Pro velké balíčky aplikací doporučujeme nastavit hodnotu na hodnotu true. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-report-health"></a>sfctl aplikace zpráva-zdraví
Odešle zprávu o stavu aplikace Service Fabric.

Hlásí stav zadané aplikace Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava je odeslána do aplikace brány Service Fabric, která předá do úložiště stavu. Sestava může být přijata bránou, ale odmítnuta úložištěm stavu po dalším ověření. Úložiště stavu může například odmítnout sestavu z důvodu neplatného parametru, jako je zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v úložišti stavu, získejte stav aplikace a zkontrolujte, zda se sestava zobrazí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. <br><br> Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " . Například pokud název aplikace je\:'fabric /myapp/app1', identita aplikace\~by 'myapp app1' v 6.0+ a 'myapp/app1' v předchozích verzích. |
| --health-property [Povinné] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a není pevný výčet, aby reportér flexibilitu kategorizovat podmínku stavu, který aktivuje sestavu. Například reportér s SourceId "LocalWatchdog" můžete sledovat stav dostupného disku na uzlu, tak to může hlásit "AvailableDisk" vlastnost na tomto uzlu. Stejný reportér může sledovat připojení uzlu, takže může hlásit vlastnost "Připojení" na stejném uzlu. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro zadaný uzel. Spolu s SourceId vlastnost jednoznačně identifikuje informace o stavu. |
| --stav -state [Povinné] | Možné hodnoty\: zahrnují "Neplatné", Ok, "Upozornění", "Chyba", Neznámý. |
| --source-id [Povinné] | Název zdroje, který identifikuje klient/watchdog/systémová součást, která generovala informace o stavu. |
| --popis | Popis informací o zdravotním stavu. <br><br> Představuje volný text používaný k přidání lidsky čitelných informací o sestavě. Maximální délka řetězce pro popis je 4096 znaků. Pokud je zadaný řetězec delší, bude automaticky zkrácen. Při zkrácení poslední znaky popisu obsahují značku "[Zkráceno]" a celková velikost řetězce je 4096 znaků. Přítomnost značky označuje uživatelům, že došlo ke zkrácení. Všimněte si, že při zkrácení popis má méně než 4096 znaků z původního řetězce. |
| --okamžitá | Příznak, který označuje, zda má být sestava odeslána okamžitě. <br><br> Sestava stavu je odeslána do aplikace brány Service Fabric, která předá do úložiště stavu. Pokud immediate je nastavena na hodnotu true, sestava je odeslána okamžitě z brány HTTP do úložiště stavu, bez ohledu na nastavení klienta prostředků infrastruktury, které používá aplikace brány HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na časování a dalších podmínkách může odesílání sestavy stále nezdaří, například pokud je brána HTTP uzavřena nebo zpráva nedosáhne brány. Pokud immediate je nastavena na false, sestava je odeslána na základě nastavení klienta stavu z brány HTTP. Proto bude dávkově podle konfigurace HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje klientovi stavu optimalizovat zprávy o stavu do úložiště stavu, jakož i zpracování sestavy stavu. Ve výchozím nastavení nejsou sestavy odesílány okamžitě. |
| --remove-when-expired --remove-when-expired --remove-when-expired --remove | Hodnota, která označuje, zda je sestava odebrána z úložiště stavu po vypršení jeho platnosti. <br><br> Pokud je nastavena na hodnotu true, sestava je odebrána z úložiště stavu po vypršení jeho platnosti. Pokud je nastavena na hodnotu false, sestava je považována za chybu při vypršení platnosti. Hodnota této vlastnosti je ve výchozím nastavení false. Když klienti pravidelně hlásí, měli by nastavit RemoveWhenExpired false (výchozí). Tímto způsobem je reportér má problémy (např. zablokování) a nelze hlásit, entita je vyhodnocena při chybě při vypršení platnosti sestavy stavu. To označí entitu jako ve stavu Chyba. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestavy používá úložiště stavu ke zjištění zastaralých sestav. Pokud není zadán, pořadové číslo je automaticky generovánklientem stavu při přidání sestavy. |
| --časový čas -t | Výchozí\: 60. |
| --ttl | Doba, po kterou je tato zpráva o stavu platná. Toto pole používá pro určení doby trvání formát ISO8601. <br><br> Když klienti pravidelně hlásí, měli by odesílat sestavy s vyšší frekvencí než čas žít. Pokud klienti hlásí přechod, mohou nastavit čas žít na nekonečno. Když vyprší čas live, událost stavu, která obsahuje informace o stavu je buď odebrána z úložiště stavu, pokud RemoveWhenExpired je true, nebo vyhodnocena na chybu, pokud RemoveWhenExpired false. Pokud není zadán, čas žít výchozí nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-type"></a>typ aplikace sfctl
Získá seznam typů aplikací v clusteru Service Fabric odpovídající přesně zadaný název.

Vrátí informace o typech aplikací, které jsou zřízeny nebo právě zřizovány v clusteru Service Fabric. Tyto výsledky jsou typy aplikací, jejichž název přesně odpovídá tomu, který je zadán jako parametr a které jsou v souladu s parametry daného dotazu. Všechny verze typu aplikace odpovídající názvu typu aplikace jsou vráceny, přičemž každá verze je vrácena jako jeden typ aplikace. Odpověď obsahuje název, verzi, stav a další podrobnosti o typu aplikace. Toto je stránkovaný dotaz, což znamená, že pokud ne všechny typy aplikací se vejdou na stránku, je vrácena jedna stránka výsledků, stejně jako token pokračování, který lze použít k získání další stránky. Například pokud existuje 10 typů aplikací, ale stránka se hodí pouze první tři typy aplikací, nebo pokud je maximální výsledky nastavena na 3, pak tři je vrácena. Chcete-li získat přístup k ostatním výsledkům, načtěte následné stránky pomocí vráceného tokenu pokračování v dalším dotazu. Prázdný token pokračování je vrácena, pokud neexistují žádné další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název typu aplikace [Povinné] | Název typu aplikace. |
| --verze typu aplikace | Verze typu aplikace. |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters --exclude | Příznak, který určuje, zda parametry aplikace budou vyloučeny z výsledku. |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-type-list"></a>sfctl seznam typů aplikace
Získá seznam typů aplikací v clusteru Service Fabric.

Vrátí informace o typech aplikací, které jsou zřízeny nebo právě zřizovány v clusteru Service Fabric. Každá verze typu aplikace je vrácena jako jeden typ aplikace. Odpověď obsahuje název, verzi, stav a další podrobnosti o typu aplikace. Toto je stránkovaný dotaz, což znamená, že pokud ne všechny typy aplikací se vejdou na stránku, je vrácena jedna stránka výsledků, stejně jako token pokračování, který lze použít k získání další stránky. Například pokud existuje 10 typů aplikací, ale stránka se hodí pouze první tři typy aplikací, nebo pokud je maximální výsledky nastavena na 3, pak tři je vrácena. Chcete-li získat přístup k ostatním výsledkům, načtěte následné stránky pomocí vráceného tokenu pokračování v dalším dotazu. Prázdný token pokračování je vrácena, pokud neexistují žádné další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --application-type-definition-kind-filter --application-type-definition-filter --application-type-definition-filter -- | Slouží k filtrování na ApplicationTypeDefinitionKind, což je mechanismus používaný k definování typu aplikace Service Fabric.  <br> - Výchozí - Výchozí hodnota, která plní stejnou funkci jako výběr "Vše". Hodnota je 0.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou ApplicationTypeDefinitionKind. Hodnota je 65535.  <br> - ServiceFabricApplicationPackage - Filtr, který odpovídá vstupu s hodnotou ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. Hodnota je 1.  <br> - Compose - Filtr, který odpovídá vstupu s ApplicationTypeDefinitionKind hodnota Compose. Hodnota je 2. |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters --exclude | Příznak, který určuje, zda parametry aplikace budou vyloučeny z výsledku. |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-unprovision"></a>sfctl aplikace zrušení zřízení
Odebere nebo zruší registraci typu aplikace Service Fabric z clusteru.

Tuto operaci lze provést pouze v případě, že byly odstraněny všechny instance aplikace typu aplikace. Jakmile je typ aplikace neregistrovaný, nelze pro tento konkrétní typ aplikace vytvořit žádné nové instance aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název typu aplikace [Povinné] | Název typu aplikace. |
| --verze typu aplikace [Povinné] | Verze typu aplikace, jak je definována v manifestu aplikace. |
| --asynchronní parametr | Příznak označující, zda unprovision by mělo dojít asynchronně. Pokud je nastavena na hodnotu true, operace zrušení zřízení vrátí, když je požadavek přijat systémem a operace zrušení zřízení pokračuje bez časového limitu. Výchozí hodnota je False. Doporučujeme však nastavit na hodnotu true pro velké balíčky aplikací, které byly zřízeny. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade"></a>sfctl upgrade aplikace
Spustí upgrade aplikace v clusteru Service Fabric.

Ověří zadané parametry upgradu aplikace a spustí upgrade aplikace, pokud jsou parametry platné. Všimněte si, že popis upgradu nahrazuje popis existující aplikace. To znamená, že pokud nejsou zadány parametry, existující parametry v aplikacích budou přepsány prázdným seznamem parametrů. To by mělo za následek aplikace pomocí výchozí hodnoty parametrů z manifestu aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. <br><br> Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --verze aplikace [Povinné] | Verze cílového typu aplikace (nalezená v manifestu aplikace) pro upgrade aplikace. |
| --parametry [Povinné] | JSON kódovaný seznam parametrů aplikace přepíše, které mají být použity při upgradu aplikace. |
| --default-service-health-policy --default-service-health-policy --default-service-health-policy --default | JSON kódované specifikace zásady stavu používá ve výchozím nastavení k vyhodnocení stavu typu služby. |
| --selhání akce | Akce, která má být vpřípadě sledovaného upgradu zjištěna, narazí na zásady monitorování nebo porušení zásad stavu. |
| --force-restart | Vynuceně restartovat procesy během upgradu i v případě, že se nezměnila verze kódu. |
| --re-check-retry-timeout --health-check-retry-timeout --health-check-retry-timeout --health | Doba mezi pokusy o provedení kontroly stavu, pokud aplikace nebo cluster není v pořádku.  Výchozí\: PT0H10M0S. |
| --kontrola stavu-stabilní-trvání | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než upgrade přejde na další doménu upgradu.  Výchozí\: PT0H2M0S. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --kontrola stavu-čekání-doba trvání | Doba čekání po dokončení upgradu domény před zahájením procesu kontroly stavu.  Výchozí\: hodnota 0. |
| --max-nezdravé aplikace | Maximální povolené procento nefunkčních nasazených aplikací. Reprezentováno jako číslo mezi 0 a 100. |
| --režim | Režim používaný ke sledování stavu během postupné inovace.  Výchozí\: UnmonitoredAuto. |
| --replika-set-check-timeout | Maximální doba, po kterou je třeba zablokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti v případě neočekávaných problémů. Měřeno v sekundách. |
| --služba-zdravotní politika | JSON kódované mapy s politikou stavu typu služby podle názvu typu služby. Mapa je prázdná být výchozí. |
| --časový čas -t | Výchozí\: 60. |
| --upgrade-domain-timeout | Doba, po kterou musí být každá upgradovací doména dokončena před provedením akce FailureAction.  Výchozí\: p10675199DT02H48M05.4775807s. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upgrade-timeout | Doba, po kterou má být dokončena celková inovace, než je provedena akce Selhání.  Výchozí\: p10675199DT02H48M05.4775807s. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upozornění jako chyba | Označuje, zda jsou upozornění považována za chyby se stejnou závažností. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl aplikace upgrade-resume
Pokračuje v inovaci aplikace v clusteru Service Fabric.

Obnoví nemonitorovaný ruční upgrade aplikace Service Fabric. Service Fabric upgraduje jednu doménu upgradu najednou. U nemonitorovaných ručních inovací po dokončení upgradu domény Service Fabric čeká na volání tohoto rozhraní API před pokračováním na další doméně upgradu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --upgrade-název domény [Povinné] | Název domény upgradu, ve kterém chcete pokračovat v upgradu. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl aplikace upgrade-rollback
Spustí vrácení aktuálně probíhá inovace aplikace v clusteru Service Fabric.

Spustí vrácení aktuálního upgradu aplikace na předchozí verzi. Toto rozhraní API lze použít pouze k vrácení zpět aktuální in-progress upgrade, který se postupuje vpřed na novou verzi. Pokud aplikace není aktuálně upgradována, použijte rozhraní StartApplicationUpgrade API k upgradu na požadovanou verzi, včetně vrácení zpět na předchozí verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade-status"></a>sfctl stav upgradu aplikace
Získá podrobnosti o nejnovější upgrade provádí v této aplikaci.

Vrátí informace o stavu nejnovější inovace aplikace spolu s podrobnostmi pro podporu ladění problémů se stavem aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upload"></a>sfctl aplikace nahrát
Zkopírujte balíček aplikace Service Fabric do úložiště obrázků.

Volitelně zobrazit průběh nahrávání pro každý soubor v balíčku. Průběh nahrávání je `stderr`odeslán do aplikace .

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cesta [Povinné] | Cesta k balíčku místní aplikace. |
| --komprese | Platí pouze pro balíčky aplikací Service Fabric. Vytvořte novou složku obsahující komprimovaný balíček aplikace do výchozího umístění nebo do umístění určeného parametrem komprimovaného umístění a potom nahrajte nově vytvořenou složku. <br><br> Pokud již existuje komprimovaný soubor generovaný sfctl, bude přepsán, pokud je tento příznak nastaven. Pokud adresář není balíček aplikace, bude vrácena chyba. Pokud se již jedná o komprimovaný balíček aplikace, bude složka zkopírována tak, jak je. Ve výchozím nastavení bude nově vytvořený balíček komprimované aplikace po úspěšném nahrání odstraněn. Pokud nahrávání není úspěšné, ručně vyčistěte komprimovaný balíček podle potřeby. Odstranění neodebere žádné prázdné dirs, které mohou být vytvořeny, pokud komprimovaný parametr umístění odkazuje na neexistujíexistující adresáře. |
| --komprimované umístění | Umístění umístit balíček komprimované aplikace. <br><br> Pokud není k dispozici žádné umístění, komprimovaný balíček bude umístěn pod nově vytvořenou složku nazvanou sfctl_compressed_temp pod nadřazeným adresářem zadaným v argumentu cesty. Například pokud argument cesty má\:hodnotu C /FolderA/AppPkg, pak komprimovaný balíček bude přidán do C\:/FolderA/sfctl_compressed_temp/AppPkg. |
| --imagestore-string | Cílové úložiště obrázků pro nahrání balíčku aplikace.  Výchozí\: \:fabric ImageStore. <br><br> Chcete-li nahrát do umístění souboru, spusťte tento parametr souborem\:. V opačném případě by hodnota měla být připojovací řetězec úložiště bitových obrázků, například výchozí hodnota. |
| --keep-compressed | Zda zachovat generovaný komprimovaný balíček při úspěšném dokončení nahrávání. <br><br> Pokud není nastavena, pak po úspěšném dokončení budou komprimované balíčky aplikací odstraněny. Pokud nahrávání nebylo úspěšné, balíček aplikace bude vždy uložen ve výstupním adresáři pro opětovné nahrání. |
| --show-progress | Zobrazit průběh nahrávání souborů pro velké balíčky. |
| --časový čas -t | Celkový časový čas v sekundách. Nahrávání se nezdaří a vrátí chybu po uplynutí doby trvání časového času nahrávání. Tento časový rozsah se vztahuje na celý balíček aplikace a časové časové opony jednotlivých souborů se budou rovnat zbývající době časového plánu. Časový čas nezahrnuje čas potřebný ke kompresi balíčku aplikace.  Výchozí\: 300. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Setup](service-fabric-cli.md) the Service Fabric CLI.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
