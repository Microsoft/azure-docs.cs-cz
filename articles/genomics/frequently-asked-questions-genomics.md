---
title: Časté otázky - nejčastější dotazy
titleSuffix: Microsoft Genomics
description: Získejte odpovědi na běžné otázky týkající se používání služby Microsoft Genomics, včetně technických informací, sla a fakturace.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986032"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Časté otázky

Tento článek uvádí hlavní dotazy, které mohou souviset s Microsoft Genomics. Další informace o službě Microsoft Genomics naleznete v tématu [What is Microsoft Genomics?](overview-what-is-genomics.md). Další informace o řešení potíží naleznete v [příručce k řešení potíží](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Jak spustím pracovní postupy GATK4 v microsoft genomice?
V souboru config.txt služby Microsoft Genomics `gatk4`zadejte process_name do souboru . Upozorňujeme, že se vám budou účtovat běžné fakturační sazby.

## <a name="how-do-i-enable-output-compression"></a>Jak povolím výstupní kompresi?
Můžete komprimovat výstupní vcf nebo gvcf pomocí volitelného argumentu pro výstupní kompresi. To je ekvivalentní `-bgzip` spuštění `-tabix` následuje na výstupu vcf nebo `.gz` gvcf, k `.tbi` výrobě (bgzip výstup) a (tabix výstup) soubory. `bgzip`komprimuje soubor vcf nebo `tabix` gvcf a vytvoří index pro komprimovaný soubor. Argument je logická hodnota, která `false` je ve výchozím nastavení `true` nastavena na hodnotu výstup vcf a ve výchozím nastavení pro výstup gcvf. Chcete-li použít na `-bz` příkazovém řádku, zadejte nebo `--bgzip-output` jako `true` (spustit bgzip a tabix) nebo `false`. Chcete-li tento argument použít v souboru `bgzip_output: true` `bgzip_output: false` config.txt, přidejte jej nebo do něj.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co je sla pro Microsoft Genomics?
Garantujeme, že 99,9 % času, po které bude služba Microsoft Genomics k dispozici pro příjem požadavků rozhraní API pracovního postupu. Další informace naleznete v tématu [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak se na mém účtu zobrazuje používání microsoft genomiky?
Účty Microsoft Genomics na základě počtu gigabáz zpracovaných na pracovní postup. Další informace naleznete v [tématu Ceny](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Kde najdu seznam všech možných příkazů a argumentů pro klienta? `msgen`
Úplný seznam dostupných příkazů a argumentů můžete `msgen help`získat spuštěním aplikace . Pokud nejsou k dispozici žádné další argumenty, zobrazí se seznam `submit`dostupných částí nápovědy, jeden pro každý z , `list`, `cancel`a `status`. Chcete-li získat nápovědu `msgen help command`pro konkrétní příkaz, zadejte ; zobrazí například `msgen help submit` všechny možnosti odeslání.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Jaké jsou nejčastěji používané příkazy `msgen` pro klienta?
Nejčastěji používané příkazy jsou argumenty `msgen` pro klienta patří: 

 |**Příkaz**          |  **Popis pole** |
 |:--------------------|:-------------         |
 |`list`               |Vrátí seznam úloh, které jste odeslali. Argumenty naleznete `msgen help list`v tématu .  |
 |`submit`             |Odešle žádost o pracovní postup do služby. Argumenty naleznete `msgen help submit`v tématu .|
 |`status`             |Vrátí stav pracovního postupu `--workflow-id`určeného aplikacem . Viz `msgen help status`také . |
 |`cancel`             |Odešle požadavek na zrušení zpracování `--workflow-id`pracovního postupu určeného společností . Viz `msgen help cancel`také . |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Kde získám hodnotu `--api-url-base`pro ?
Přejděte na portál Azure a otevřete stránku účtu Genomics. V záhlaví **Správa** zvolte **Přístupové klávesy**. Zde najdete adresu URL rozhraní API i přístupové klíče.

## <a name="where-do-i-get-the-value-for---access-key"></a>Kde získám hodnotu `--access-key`pro ?
Přejděte na portál Azure a otevřete stránku účtu Genomics. V záhlaví **Správa** zvolte **Přístupové klávesy**. Zde najdete adresu URL rozhraní API i přístupové klíče.

## <a name="why-do-i-need-two-access-keys"></a>Proč potřebuji dva přístupové klíče?
Potřebujete dva přístupové klíče v případě, že je chcete aktualizovat (regenerovat) bez přerušení používání služby. Chcete-li například aktualizovat první klíč, měli byste mít všechny nové pracovní postupy použít druhý klíč. Potom počkejte na všechny pracovní postupy pomocí prvního klíče k dokončení před aktualizací prvního klíče.

## <a name="do-you-save-my-storage-account-keys"></a>Ukládáte klíče mého účtu úložiště?
Klíč účtu úložiště se používá k vytvoření krátkodobých přístupových tokenů pro službu Microsoft Genomics pro čtení vstupních souborů a zápis výstupních souborů. Výchozí doba trvání tokenu je 48 hodin. Dobu trvání tokenu lze `-sas/--sas-duration` změnit pomocí možnosti příkazu submit; hodnota je v hodinách.

## <a name="what-genome-references-can-i-use"></a>Jaké genomové odkazy mohu použít?

Tyto odkazy jsou podporovány:

 |Odkaz              | Hodnota`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (bez alt analýzy) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Jak naformátuji argumenty příkazového řádku jako konfigurační soubor? 

msgen rozumí konfiguračním souborům v následujícím formátu:
* Všechny možnosti jsou k dispozici jako dvojice klíč-hodnota s hodnotami oddělenými od klíčů dvojtečkou.
  Prázdné znaky jsou ignorovány.
* Řádky začínající `#` s jsou ignorovány.
* Jakýkoli argument příkazového řádku v dlouhém formátu lze převést na klíč odstraněním jeho úvodních pomlček a nahrazením pomlček mezi slovy podtržítky. Zde jsou některé příklady konverzí:

  |Argument příkazového řádku            | Řádek konfiguračního souboru |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:KLÍČ*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Další kroky

Pro zahájení používání microsoft genomiky použijte následující zdroje:
- Můžete začít spuštěním prvního pracovního postupu prostřednictvím služby Microsoft Genomics. [Spuštění pracovního postupu prostřednictvím služby Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Odešlete vlastní data ke zpracování službou Microsoft Genomics: [spárovaný FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ nebo BAM](quickstart-input-multiple.md) 

