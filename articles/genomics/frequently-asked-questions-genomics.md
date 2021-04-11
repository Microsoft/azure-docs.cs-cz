---
title: Časté otázky – Nejčastější dotazy
titleSuffix: Microsoft Genomics
description: Získejte odpovědi na běžné otázky týkající se používání služby Microsoft Genomics, včetně technických informací, smlouvy SLA a fakturace.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 40fff49aa3ed46417cdd9a6190f928707e41accb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608608"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: běžné otázky

Tento článek obsahuje seznam nejčastějších dotazů, které jste pravděpodobně v souvislosti s Microsoft Genomics. Další informace o službě Microsoft Genomics najdete v tématu [co je Microsoft Genomics?](overview-what-is-genomics.md). Další informace o řešení potíží najdete v našem [Průvodci odstraňováním potíží](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Návody spouštět pracovní postupy GATK4 v Microsoft Genomics?
V souboru config.txt služby Microsoft Genomics zadejte process_name do `gatk4` . Všimněte si, že se vám budou účtovat pravidelné fakturační sazby.

## <a name="how-do-i-enable-output-compression"></a>Návody povolit kompresi výstupu?
Výstupní VCF nebo gvcf můžete zkomprimovat pomocí volitelného argumentu pro výstupní kompresi. Jedná se o ekvivalent spuštění `-bgzip` následovaný `-tabix` na výstupu VCF nebo gvcf, aby se vytvořily `.gz` (bgzip Output) a `.tbi` (tabix Output) soubory. `bgzip` zkomprimuje soubor VCF nebo gvcf a `tabix` vytvoří index komprimovaného souboru. Argument je logická hodnota, která je standardně nastavená na `false` výstup VCF a `true` ve výchozím nastavení pro výstup gcvf. Chcete-li použít příkaz v příkazovém řádku, zadejte `-bz` nebo `--bgzip-output` jako `true` (spusťte bgzip a tabix) nebo `false` . Chcete-li použít tento argument v souboru config.txt, přidejte `bgzip_output: true` nebo `bgzip_output: false` do souboru.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Jaká je smlouva SLA pro Microsoft Genomics?
Garantujeme, že 99,9% času Microsoft Genomics služby bude k dispozici pro příjem požadavků rozhraní API pracovního postupu. Další informace najdete v tématu [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak se Microsoft Genomics zobrazuje na faktuře?
Microsoft Genomics faktury na základě počtu zpracovaných gigabází na pracovní postup. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Kde najdu seznam všech možných příkazů a argumentů pro `msgen` klienta?
Úplný seznam dostupných příkazů a argumentů můžete získat spuštěním příkazu `msgen help` . Pokud nejsou zadány žádné další argumenty, zobrazí seznam dostupných sekcí pro nápovědu, jeden pro každý z `submit` , `list` , `cancel` a `status` . Chcete-li získat nápovědu ke konkrétnímu příkazu, zadejte příkaz, `msgen help command` například `msgen help submit` seznam všech možností odeslání.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Jaké jsou nejběžněji používané příkazy pro `msgen` klienta?
Nejčastěji používané příkazy jsou argumenty pro klienta, které `msgen` zahrnují: 

 |**Příkaz**          |  **Popis pole** |
 |:--------------------|:-------------         |
 |`list`               |Vrátí seznam úloh, které jste odeslali. Argumenty najdete v tématu `msgen help list` .  |
 |`submit`             |Odešle službě požadavek pracovního postupu. Argumenty najdete v tématu `msgen help submit` .|
 |`status`             |Vrátí stav pracovního postupu určeného parametrem `--workflow-id` . Viz také `msgen help status` . |
 |`cancel`             |Odešle požadavek na zrušení zpracování pracovního postupu, který určil `--workflow-id` . Viz také `msgen help cancel` . |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Kde získám hodnotu `--api-url-base` ?
Přejít na Azure Portal a otevřít stránku vašeho účtu genomiky. Pod hlavičkou **správy** vyberte **přístupové klíče**. Tam najdete jak adresu URL rozhraní API, tak přístupové klávesy.

## <a name="where-do-i-get-the-value-for---access-key"></a>Kde získám hodnotu `--access-key` ?
Přejít na Azure Portal a otevřít stránku vašeho účtu genomiky. Pod hlavičkou **správy** vyberte **přístupové klíče**. Tam najdete jak adresu URL rozhraní API, tak přístupové klávesy.

## <a name="why-do-i-need-two-access-keys"></a>Proč potřebuji dva přístupové klíče?
Pro případ, že chcete aktualizovat (znovu vygenerovat), potřebujete dva přístupové klíče, aniž byste museli přerušovat využívání služby. Pokud například chcete aktualizovat první klíč, měli byste všechny nové pracovní postupy používat druhý klíč. Potom před aktualizací prvního klíče počkejte na dokončení všech pracovních postupů pomocí prvního klíče.

## <a name="do-you-save-my-storage-account-keys"></a>Ukládáte svoje klíče účtu úložiště?
Váš klíč účtu úložiště se používá k vytvoření krátkodobého přístupového tokenu pro službu Microsoft Genomics ke čtení vstupních souborů a zápisu výstupních souborů. Výchozí doba trvání tokenu je 48 hodin. Dobu trvání tokenu lze změnit pomocí `-sas/--sas-duration` Možnosti příkazu Odeslat. hodnota je v hodinách.

## <a name="does-microsoft-genomics-store-customer-data"></a>Ukládá Microsoft Genomics zákaznická data?

No. Microsoft Genomics neukládají žádná zákaznická data.

## <a name="what-genome-references-can-i-use"></a>Jaké odkazy na genom můžu použít?

Jsou podporovány tyto odkazy:

 |Reference              | Hodnota `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (žádná analýza ALT) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Návody formátovat argumenty příkazového řádku jako konfigurační soubor? 

msgen rozumí konfiguračním souborům v následujícím formátu:
* Všechny možnosti jsou k dispozici jako páry klíč-hodnota s hodnotami oddělenými od klíčů dvojtečkou.
  Prázdný znak se ignoruje.
* Řádky začínající na `#` jsou ignorovány.
* Jakýkoli argument příkazového řádku v dlouhém formátu lze převést na klíč odstraněním počátečních pomlček a nahrazením spojovníků mezi slovy pomocí podtržítek. Tady jsou některé příklady převodu:

  |Argument příkazového řádku            | Řádek konfiguračního souboru |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key: klíč*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Další kroky

Pomocí následujících zdrojů můžete začít s Microsoft Genomics:
- Začněte spuštěním prvního pracovního postupu prostřednictvím služby Microsoft Genomics. [Spuštění pracovního postupu prostřednictvím služby Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Odeslání vlastních dat ke zpracování službou Microsoft Genomics: [Spárovaná souborů fastq](quickstart-input-pair-FASTQ.md)  |  [BAM](quickstart-input-BAM.md)s  |  [více souborů fastq nebo BAM](quickstart-input-multiple.md) 

