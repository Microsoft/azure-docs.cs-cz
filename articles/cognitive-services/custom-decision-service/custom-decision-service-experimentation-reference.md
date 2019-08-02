---
title: Experimentování – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Tento článek je průvodcem pro experimentování s Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: e6e8e7d0d5b969464ba9183ccae9080f58f786a0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707289"
---
# <a name="experimentation"></a>Experimentování

V rámci teoretického kontextu [Bandits ()](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/)Custom Decision Service opakovaně sleduje kontext, provádí akci a pro zvolenou akci sleduje odměnu. Příkladem je přizpůsobení obsahu: kontext popisuje uživatele, akce jsou kandidátské příběhy a odměňování se věnuje, kolik uživatelů se líbilo s doporučeným článkem.

Custom Decision Service vytváří zásadu, jak je mapována z kontextů na akce. S konkrétní cílovou zásadou chcete zjistit, že se očekávala její odměna. Jednou z možností, jak odhadnout odměnu, je použití zásady online a možnost zvolit akce (například doporučit uživatelům příběhy). Toto online hodnocení ale může být nákladné ze dvou důvodů:

* Zpřístupňuje uživatele netestovaným experimentálním zásadám.
* Neškáluje se tak, aby vyhodnotilo více cílových zásad.

Vyhodnocování mimo zásady je alternativní paradigma. Pokud máte protokoly ze stávajícího online systému, který se řídí zásadami protokolování, může vyhodnocení neplatných zásad odhadnout očekávané vynechání nových cílových zásad.

Při použití souboru protokolu se experimentování snaží najít zásadu s nejvyšším odhadem očekávané odměny. Zásady cíle jsou parametrizované argumenty [pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki) . Ve výchozím režimu skript testuje celou řadu argumentů pro dostupné připojením k `--base_command`. Skript provede následující akce:

* Automaticky detekuje obory názvů funkcí z prvních `--auto_lines` řádků vstupního souboru.
* Provede první Sweep přes Hyper-Parameters (`learning rate`, `L1 regularization` `power_t`a).
* Testuje vyhodnocení `--cb_type` zásad (inverzní`ips`výsledek () nebo zdvojnásobení odolné (`dr`). Další informace najdete v tématu [kontextová Bandit příklad](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testuje marže.
* Testy kvadratických funkcí interakce:
   * **fáze hrubá silou**: Testuje všechny kombinace s `--q_bruteforce_terms` páry nebo méně.
   * **hladová fáze**: Přidá nejlepší dvojici, dokud nedosáhnete žádného vylepšení pro `--q_greedy_stop` zaokrouhlení.
* Provede druhou operaci Sweep přes parametry Hyper-v`learning rate`( `L1 regularization`, `power_t`a).

Mezi parametry, které řídí tyto kroky, patří některé argumenty pro dostupné:
- Příklad možností manipulace:
  - sdílené obory názvů
  - obory názvů akcí
  - mezní obory názvů
  - kvadratické funkce
- Aktualizace možností pravidla
  - studijní frekvence
  - Pravidelnáace L1
  - t – hodnota mocniny

Podrobné vysvětlení výše uvedených argumentů najdete v tématu [argumenty příkazového řádku pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Požadavky
- Pro dostupné: Nainstalováno a do vaší cesty.
  - Windows: Použijte instalační program. [ `.msi` ](https://github.com/eisber/vowpal_wabbit/releases)
  - Jiné platformy: [Získejte zdrojový kód](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Nainstalováno a do vaší cesty.
- NumPy Použijte Správce balíčků dle vašeho výběru.
- Úložiště *Microsoft/MWT-DS* : [Naklonujte úložiště](https://github.com/Microsoft/mwt-ds).
- Soubor protokolu JSON služby pro rozhodování: Ve výchozím nastavení zahrnuje `--dsjson`základní příkaz, který umožňuje analýzu datových souborů vstupních dat ve službě Decision. [Získejte příklad tohoto formátu](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Použití
Přejít na `mwt-ds/DataScience` a spustit `Experimentation.py` s příslušnými argumenty, jak je popsáno v následujícím kódu:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Protokol výsledků je připojen k souboru *MWT-DS/datavěda/Experiments. csv* .

### <a name="parameters"></a>Parametry
| Vstup | Popis | Výchozí |
| --- | --- | --- |
| `-h`, `--help` | Zobrazí zprávu s upozorněním a ukončí. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Cesta k datovému`.json` souboru `.json.gz` (nebo formát-každý řádek `dsjson`je a). | Požadováno |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Základní příkaz pro dostupné  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Počet paralelních procesů, které se mají použít. | Logické procesory |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Sdílené obory názvů funkcí (například `abc` znamená obory `b`názvů `a`, `c`a).  | Automaticky rozpoznat z datového souboru |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Obory názvů funkce akce | Automaticky rozpoznat z datového souboru |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Mezní obory názvů funkce. | Automaticky rozpoznat z datového souboru |  
| `--auto_lines AUTO_LINES` | Počet řádků datových souborů, které se mají kontrolovat, aby se automaticky rozpoznaly obory názvů funkcí | `100` |  
| `--only_hp` | Pouze shrnout přes Hyper-Parameters (`learning rate`, `L1 regularization` `power_t`a). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Rozsah studijních kurzů jako kladné `min,max,steps`hodnoty. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Rozsah depravidelnosti L1 jako kladné `min,max,steps`hodnoty. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Power_t rozsah jako kladné hodnoty `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Počet kvadratických párů pro testování ve fázi hrubá silou. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Zaokrouhlí bez vylepšení, po kterém je zastavena kvadratická hladová fáze hledání. | `3` |  

### <a name="examples"></a>Příklady
Použití přednastavených výchozích hodnot:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Ekvivalentně pro dostupné může také `.json.gz` soubory ingestovat:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Chcete-li vymezit pouze přes`learning rate`Hyper `L1 regularization`-Parameters `power_t`(, a, zastavování po kroku 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
