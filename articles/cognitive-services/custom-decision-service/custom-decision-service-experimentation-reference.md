---
title: Experimentování - kognitivní služby Azure | Microsoft Docs
description: Tento článek je Průvodce pro Azure Service rozhodnutí vlastní experimenty.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343555"
---
# <a name="experimentation"></a>Experimentování

Následující teorie [kontextové bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), vlastní rozhodnutí služby opakovaně dodržuje kontextu, provede akci a dodržuje potřebu pro zvolené akci. Příkladem je obsahu přizpůsobení: Popisuje kontextu uživatele, akce jsou candidate scénářů, a potřebu opatření, kolik uživatele líbilo Doporučené scénáře.

Vlastní rozhodnutí služba vytvoří zásadu, jak mapuje z kontextu akce. Pomocí zásad konkrétní cíl budete chtít vědět jeho očekávané potřebu. Jedním ze způsobů k zjištění přibližné hodnoty potřebu je online pomocí zásad a nechat ji zvolte akce (například doporučujeme scénářů pro uživatele). Takové online vyhodnocení však může být drahé dvou důvodů:

* Poskytuje uživatelům zásadu netestované, experimentální.
* Není škálovat vyhodnocení víc zásad cíl.

Vyhodnocení vypnout zásad je alternativní zlepší. Pokud máte protokoly ze stávajícího systému online, které postupujte podle zásad protokolování, můžete odhadnout vyhodnocení vypnout zásad očekávané výnosu nové zásady cíl.

Pomocí souboru protokolu experimentování snaží najít zásada s nejvyšší potřebu odhadované, očekávané. Cíl zásad jsou parametry podle [Vowpal k dispozici](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumenty. Ve výchozím režimu skript testy připojením k celou řadu argumenty Vowpal k dispozici `--base_command`. Skript provede následující akce:

* Automaticky zjistí funkce obory názvů v první `--auto_lines` řádky vstupní soubor.
* Provede první oblouku prostřednictvím technologie hyper parametry (`learning rate`, `L1 regularization`, a `power_t`).
* Testy vyhodnocení zásad `--cb_type` (inverzní tendenci skóre (`ips`) nebo dvakrát robustní (`dr`). Další informace najdete v tématu [příklad kontextové Bandit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testy slova.
* Testy kvadratické interakce funkce:
   * **fáze hrubou silou**: testy všechny kombinací s `--q_bruteforce_terms` páry nebo méně.
   * **fáze typu greedy**: Přidá nejlepší dvojici, dokud není k dispozici žádné zlepšení pro `--q_greedy_stop` zaokrouhlí.
* Provádí druhý oblouku prostřednictvím technologie hyper parametry (`learning rate`, `L1 regularization`, a `power_t`).

Parametry, které řídí tyto kroky zahrnují některé argumenty Vowpal k dispozici:
- Možnosti manipulace s příklad:
  - sdílené obory názvů
  - obory názvů akce
  - okrajového obory názvů
  - kvadratické funkce
- Možnosti aktualizace pravidel
  - rychlost učení
  - Regulaci L1
  - hodnota t napájení

Podrobné vysvětlení výše argumentů najdete v tématu [argumenty příkazového řádku k dispozici Vowpal](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Požadavky
- K dispozici Vowpal: Nainstalovat a na trase.
  - Windows: [použití `.msi` instalační program](https://github.com/eisber/vowpal_wabbit/releases).
  - Jiné platformy: [získat zdrojový kód](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Jazyk Python 3: Nainstalován a na trase.
- NumPy: Pomocí Správce balíčků podle svého výběru.
- *Microsoft/mwt-ds* úložiště: [Naklonujte úložiště](https://github.com/Microsoft/mwt-ds).
- Soubor protokolu služby JSON rozhodnutí: ve výchozím nastavení, základní příkaz obsahuje `--dsjson`, což umožňuje JSON služby rozhodnutí Analýza souboru vstupní data. [Příkladem tohoto formátu získat](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Využití
Přejděte na `mwt-ds/DataScience` a spusťte `Experimentation.py` s relevantní argumenty, jak je podrobně uvedeno v následujícím kódu:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Připojí se k protokolu výsledky *mwt-ds/DataScience/experiments.csv* souboru.

### <a name="parameters"></a>Parametry
| Vstup | Popis | Výchozí |
| --- | --- | --- |
| `-h`, `--help` | Zobrazte zprávu nápovědy a ukončení. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Cesta k souboru dat (`.json` nebo `.json.gz` formát: každý řádek představuje `dsjson`). | Požaduje se |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Základní příkaz Vowpal k dispozici.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Počet paralelních procesů používat. | Logické procesory |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Sdílené funkce obory názvů (například `abc` znamená obory názvů `a`, `b`, a `c`).  | Automaticky rozpoznat z datového souboru |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Funkce obory názvů akce. | Automaticky rozpoznat z datového souboru |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Okrajového funkce obory názvů. | Automaticky rozpoznat z datového souboru |  
| `--auto_lines AUTO_LINES` | Počet řádků dat souboru se prohledat k automatické rozpoznání funkce obory názvů. | `100` |  
| `--only_hp` | Oblouku jen přes technologie hyper parametry (`learning rate`, `L1 regularization`, a `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Učení rozsah rychlost jako kladné hodnoty `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Rozsah regulaci L1 jako kladné hodnoty `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Rozsah Power_t jako kladné hodnoty `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Počet dvojic kvadratické a otestovat ve fázi hrubou silou. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Zaokrouhlí bez vylepšení, po které fáze kvadratické typu greedy vyhledávání zastavit. | `3` |  

### <a name="examples"></a>Příklady
Chcete-li přednastavené výchozí hodnoty:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Ekvivalentně, můžete k dispozici Vowpal také ingestování `.json.gz` soubory:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

K oblouku jen přes technologie hyper parametry (`learning rate`, `L1 regularization`, a `power_t`, ukončení po kroku 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
