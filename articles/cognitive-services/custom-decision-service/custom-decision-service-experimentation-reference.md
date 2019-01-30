---
title: Experimentování ve službě – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Tento článek představuje příručku pro experimentování s využitím služby Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: 90a99d4910b0afb885b415760f6a7ef1ca2aec33
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219817"
---
# <a name="experimentation"></a>Experimentování

Následující teorie [kontextové bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), Custom Decision Service opakovaně dodržuje kontextu, provede akci a dodržuje reward pro zvolené akci. Příkladem je přizpůsobení obsahu: Popisuje kontextu uživatele, akce jsou scénáře Release candidate a potřebu opatření, kolik uživatel líbilo Doporučené scénáře.

Custom Decision Service vytvoří zásadu, jak mapuje z kontextu akce. S konkrétní cílovou zásady potřebujete vědět jeho očekávaný potřebu. Jeden způsob, jak odhadnout potřebu je online pomocí zásad a ten zvolte akce (například doporučuje scénářů pro uživatele). Tyto online vyhodnocení však může být nákladné dvou důvodů:

* Poskytuje uživatelům neotestovaný, experimentální zásady.
* Neškáluje se na vaše rozhodnutí vyzkoušet několik cílit zásady.

Alternativní paradigma se vyžaduje vyhodnocení za vypnutí zásad. Pokud máte protokoly ze stávajícího systému online, postupujte podle zásad protokolování, vyhodnocení vypnutí zásad odhadnout očekávané výhody nové cílové zásady.

Pomocí souboru protokolu služby experimentování ve službě snaží najít zásada s nejvyšší reward odhadované, očekávané. Cílit zásady jsou parametrizován [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumenty. Ve výchozím režimu skript testuje širokou škálu Vowpal Wabbit argumenty přidáním `--base_command`. Skript provede následující akce:

* Automaticky zjistí funkce obory názvů od prvního `--auto_lines` řádky vstupní soubor.
* Provádí první úklidu hyperparametry (`learning rate`, `L1 regularization`, a `power_t`).
* Testuje vyhodnocení zásad `--cb_type` (inverzní tendence skóre (`ips`) nebo dvakrát robustní (`dr`). Další informace najdete v tématu [kontextové Bandit příklad](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testy slova.
* Funkce kvadratické interakce testy:
   * **fáze útoku hrubou silou**: Testuje všechny kombinace s `--q_bruteforce_terms` dvojice nebo méně.
   * **greedy fáze**: Přidá dvojici nejlepší, dokud nedojde k žádné vylepšení pro `--q_greedy_stop` zaokrouhlí.
* Provádí druhý úklidu hyperparametry (`learning rate`, `L1 regularization`, a `power_t`).

Parametry, které řídí tyto kroky zahrnují některé Vowpal Wabbit argumenty:
- Možnosti manipulace s příkladu:
  - sdílené obory názvů
  - obory názvů akce
  - okrajový obory názvů
  - kvadratické funkce
- Aktualizovat pravidlo možnosti
  - rychlost učení
  - L1 regularizace
  - Hodnota napájení t

Podrobné vysvětlení výše uvedené argumenty, najdete v článku [argumenty příkazového řádku Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Požadavky
- Vowpal Wabbit: Nainstalovaný a na vaší cestě.
  - Windows: [Použití `.msi` instalační program](https://github.com/eisber/vowpal_wabbit/releases).
  - Jiné platformy: [Získat zdrojový kód](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Nainstalovaný a na vaší cestě.
- NumPy: Pomocí Správce balíčků podle vašeho výběru.
- *Microsoft/mwt-ds* úložiště: [Naklonujte úložiště](https://github.com/Microsoft/mwt-ds).
- Soubor protokolu služby JSON rozhodnutí: Ve výchozím nastavení, zahrnuje základní příkaz `--dsjson`, což umožňuje analýza formátu JSON služby rozhodnutí o vstupní data. [Získat příkladem tento formát](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Využití
Přejděte na `mwt-ds/DataScience` a spusťte `Experimentation.py` s příslušnými argumenty, jak je uvedeno v následujícím kódu:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Protokol výsledků se připojí *mwt-ds/DataScience/experiments.csv* souboru.

### <a name="parameters"></a>Parametry
| Vstup | Popis | Výchozí |
| --- | --- | --- |
| `-h`, `--help` | Zobrazte zprávu nápovědy a ukončit program. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Cesta k souboru dat (`.json` nebo `.json.gz` formát: každý řádek představuje `dsjson`). | Požaduje se |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Základní příkaz Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Počet paralelních procesů používat. | Logické procesory |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Sdílené funkce obory názvů (například `abc` znamená, že obory názvů `a`, `b`, a `c`).  | Automaticky rozpoznat z datového souboru |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Funkce obory názvů akce. | Automaticky rozpoznat z datového souboru |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Obory názvů okrajového funkce. | Automaticky rozpoznat z datového souboru |  
| `--auto_lines AUTO_LINES` | Počet řádků dat souboru se prohledat k automatické rozpoznání funkce obory názvů. | `100` |  
| `--only_hp` | Jenom přes hyperparametry oblouku (`learning rate`, `L1 regularization`, a `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Frekvence rozsahu Learning jako kladné hodnoty `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Rozsah regularizace L1 jako kladné hodnoty `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Rozsah Power_t jako kladné hodnoty `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Počet dvojic kvadratické testování do fáze útoku hrubou silou. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Zaokrouhlí číslo bez vylepšení, po jejichž uplynutí je zastaveno fáze kvadratické greedy vyhledávání. | `3` |  

### <a name="examples"></a>Příklady
Použití předvolby výchozí hodnoty:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Ekvivalentně, můžete také přijímat Vowpal Wabbit `.json.gz` soubory:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Na čištění jenom přes hyperparametry (`learning rate`, `L1 regularization`, a `power_t`, zastavuje po provedení kroku 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
