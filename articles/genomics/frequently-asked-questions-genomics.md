---
title: 'Microsoft Genomics: Časté otázky – nejčastější dotazy | Dokumentace Microsoftu'
titleSuffix: Azure
description: Odpovědi na běžné dotazy zákazníků, požádejte o Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 804076fdc653622336ac3b99c15df0bc027510d9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730140"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Nejčastější dotazy

Tento článek obsahuje nejčastější dotazy, které může mít související s Microsoft Genomics. Další informace o službě Microsoft Genomics, najdete v části [co je služba Microsoft Genomics?](overview-what-is-genomics.md). Další informace o odstraňování potíží naleznete v tématu naše [Průvodce odstraňováním potíží](troubleshooting-guide-genomics.md). 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co je smlouva SLA pro Microsoft Genomics?
Garantujeme, že minimálně 99,9 % času služby Microsoft Genomics budou k dispozici pro příjem požadavků pracovního postupu rozhraní API. Další informace najdete v tématu [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak využití Microsoft Genomics objeví v mém vyúčtování?
Microsoft Genomics účtuje podle počtu zpracovaných gigabází na pracovní postup. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Kde můžu najít seznam všech možných příkazů a argumentů `msgen` klienta?
Úplný seznam dostupných příkazů a argumentů můžete získat spuštěním `msgen help`. Pokud žádné další argumenty jsou k dispozici zobrazuje seznam dostupných nápovědy části, jeden pro každou z `submit`, `list`, `cancel`, a `status`. Chcete-li získat nápovědu ke konkrétnímu příkazu, zadejte `msgen help command`; například `msgen help submit` jsou uvedeny všechny možnosti odeslání.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Co jsou nejčastěji používané příkazy pro `msgen` klienta?
Nejčastěji používané příkazy jsou argumenty `msgen` klient patří: 

 |**Příkaz**          |  **Popis pole** |
 |:--------------------|:-------------         |
 |`list`               |Vrátí seznam úloh, které jste odeslali. Argumenty, naleznete v tématu `msgen help list`.  |
 |`submit`             |Odešle žádost pracovní postup do služby. Argumenty, naleznete v tématu `msgen help submit`.|
 |`status`             |Vrátí stav pracovního postupu, určeném `--workflow-id`. Viz také `msgen help status`. |
 |`cancel`             |Odešle žádost o zrušení zpracování pracovního postupu, určeném `--workflow-id`. Viz také `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Kde můžu získat hodnotu pro `--api-url-base`?
Přejděte na webu Azure portal a otevřete stránku svého účtu Genomics. V části **správu** záhlaví, vyberte **přístupové klíče**. Tam najdete adresy URL rozhraní API a přístupové klíče.

## <a name="where-do-i-get-the-value-for---access-key"></a>Kde můžu získat hodnotu pro `--access-key`?
Přejděte na webu Azure portal a otevřete stránku svého účtu Genomics. V části **správu** záhlaví, vyberte **přístupové klíče**. Tam najdete adresy URL rozhraní API a přístupové klíče.

## <a name="why-do-i-need-two-access-keys"></a>Proč musím udělat dva přístupové klíče?
Potřebujete dva přístupové klíče v případě, že chcete aktualizovat (obnovení) je bez přerušení využívání služby. Například chcete aktualizovat první klíč. V takovém případě můžete přejít všech nových pracovních postupů na druhý klíč. Potom počkejte, dokud již spuštěné pracovní postupy pomocí první klíč jsou dokončené. Teprve potom aktualizujte klíč.

## <a name="do-you-save-my-storage-account-keys"></a>Uložit Moje klíče účtu úložiště?
Klíč účtu úložiště se používá k vytvoření krátkodobé přístupové tokeny pro služby Microsoft Genomics vstupní soubory číst a zapisovat výstupní soubory. Výchozí doba trvání tokenu je 48 hodin. Platnost tokenu je možné měnit pomocí `-sas/--sas-duration` možnost Odeslat příkaz; hodnota je v hodinách.

## <a name="what-genome-references-can-i-use"></a>Jaké genomu odkazuje můžu použít?

Podporují se tyto odkazy:
 |Referenční informace              | Hodnota `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (žádná alt analýza) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Jak formátovat Moje argumenty příkazového řádku jako konfigurační soubor? 

msgen rozumí konfigurační soubory v následujícím formátu:
* Všechny možnosti jsou k dispozici jako páry klíč hodnota s hodnotami z klíče oddělené dvojtečkou.
Prázdné znaky se ignoruje.
* Řádky začínající `#` jsou ignorovány.
* Některý argument příkazového řádku v dlouhém formátu můžete převést na klíč tak, že odstranění jeho přední pomlčky a nahrazení pomlčky mezi slovy podtržítky. Tady je několik příkladů převod:

 |argument příkazového řádku            | Řádek konfiguračního souboru |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:Key*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Další postup

Začínáme se službou Microsoft Genomics pomocí následující prostředky:
- Začínáme spuštěním svůj první pracovní postup službě Microsoft Genomics. [Spustit pracovní postup službě Microsoft Genomics ](quickstart-run-genomics-workflow-portal.md)
- Odeslat vlastní data pro zpracování ve službě Microsoft Genomics: [párované soubory FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [více souborů FASTQ nebo BAM](quickstart-input-multiple.md) 

