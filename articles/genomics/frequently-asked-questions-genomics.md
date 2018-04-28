---
title: 'Microsoft Genomics: Časté otázky | Microsoft Docs'
titleSuffix: Azure
description: Odpovědi na běžné otázky zákazníků, požádejte o Genomics společnosti Microsoft.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: b3f30dc5d185615370a8273f71554f784d286cd9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="microsoft-genomics-common-questions"></a>Genomics Microsoft: Časté otázky

Tento článek obsahuje seznam nejčastějších dotazů, které vám mohou mít s Genomics společnosti Microsoft. Další informace o službě Microsoft Genomics najdete v tématu [co je Microsoft Genomics?](overview-what-is-genomics.md). Další informace o řešení problémů najdete v tématu naše [Průvodce odstraňováním potíží](troubleshooting-guide-genomics.md). 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co je SLA pro Microsoft Genomics?
Zaručujeme 99,9 % času Genomics Microsoft služby bude k dispozici pro příjem požadavků pracovního postupu rozhraní API. Další informace najdete v tématu [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak využití Microsoft Genomics objeví na Moje faktury?
Microsoft Genomics faktur na základě počtu gigabases zpracovaných za pracovního postupu. Další informace najdete v tématu [cenová](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Kde najdu seznam všech možných příkazy a argumenty pro `msgen` klienta?
Úplný seznam argumentů a dostupné příkazy můžete získat spuštěním `msgen help`. Pokud žádné další argumenty jsou k dispozici, zobrazí se seznam dostupných nápovědy částech, jeden pro každou `submit`, `list`, `cancel`, a `status`. Chcete-li získat nápovědu pro konkrétní příkaz, zadejte `msgen help command`, například `msgen help submit` zobrazí seznam všech možností odeslání.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Jaké jsou nejčastěji používané příkazy pro `msgen` klienta?
Nejčastěji používané příkazy jsou argumenty pro `msgen` klienta zahrnují: 

 |**Příkaz**          |  **Popis pole** |
 |:--------------------|:-------------         |
 |`list`               |Vrátí seznam úloh, které jste odeslali. Argumenty, najdete v části `msgen help list`.  |
 |`submit`             |Odešle žádost o pracovní postup ke službě. Argumenty, najdete v části `msgen help submit`.|
 |`status`             |Vrátí stav pracovního postupu určeného `--workflow-id`. Viz také `msgen help status`. |
 |`cancel`             |Odešle požadavek na zrušení zpracování pracovního postupu určeného `--workflow-id`. Viz také `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Kde získat hodnotu `--api-url-base`?
Přejděte na portál Azure a otevřete stránku genomika účtu. V části **správy** záhlaví, vyberte **přístupové klíče**. Zde najít adresu rozhraní API a klíče pro přístup k.

## <a name="where-do-i-get-the-value-for---access-key"></a>Kde získat hodnotu `--access-key`?
Přejděte na portál Azure a otevřete stránku genomika účtu. V části **správy** záhlaví, vyberte **přístupové klíče**. Zde najít adresu rozhraní API a klíče pro přístup k.

## <a name="why-do-i-need-two-access-keys"></a>Proč musím dva přístupové klíče?
Budete potřebovat dva přístupové klíče v případě, že chcete aktualizovat (znovu vygenerovat) je bez přerušení využití služby. Například chcete aktualizovat první klíč. V takovém případě přejít všechny nové pracovní postupy k používání druhý klíč. Potom počkejte, dokud dokončení již spuštěné pracovní postupy pomocí první klíče. Jenom tehdy aktualizujte klíč.

## <a name="do-you-save-my-storage-account-keys"></a>Uložit mé klíče účtu úložiště?
Klíč účtu úložiště se používá k vytvoření krátkodobé přístupových tokenů pro službu Microsoft Genomics číst vaše vstupní soubory a zapisovat výstupní soubory. Platnost tokenu výchozí je 48 hodin. Platnost tokenu lze změnit pomocí `-sas/--sas-duration` možnost Odeslat příkaz; hodnota je v hodinách.

## <a name="what-genome-references-can-i-use"></a>Jaké genomu odkazuje mohu použít?

Podporují se tyto odkazy:
 |Referenční informace              | Hodnota `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (žádné alternativní analýza) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Formátování Moje argumenty příkazového řádku jako konfigurační soubor 

msgen rozumí konfigurační soubory v následujícím formátu:
* Všechny možnosti jsou k dispozici jako páry klíč hodnota s hodnoty z klíče oddělené dvojtečkou.
Prázdné znaky se ignoruje.
* Řádky začínajícími `#` jsou ignorovány.
* Všechny argument příkazového řádku v dlouhém formátu lze převést na klíč odstraňování jeho počáteční pomlčky a nahraďte pomlčky mezi slovy s podtržítka. Zde jsou některé příklady převod:

 |Argument příkazového řádku            | Řádek konfiguračního souboru |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:Key*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Další postup

Začínáme s Microsoft Genomics pomocí následující prostředky:
- Začínáme spuštěním prvního pracovního postupu pomocí služby Microsoft Genomics. [Spuštění pracovního postupu pomocí služby Microsoft Genomics ](quickstart-run-genomics-workflow-portal.md)
- Odeslat vlastní data pro zpracování pomocí služby Microsoft Genomics: [spárovat FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [více FASTQ nebo BAM](quickstart-input-multiple.md) 

