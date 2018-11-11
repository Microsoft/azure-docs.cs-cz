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
ms.openlocfilehash: 0129f186736ad2e4d6ea5c94c632bab73b92002c
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514685"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Nejčastější dotazy

Tento článek obsahuje nejčastější dotazy, které může mít související s Microsoft Genomics. Další informace o službě Microsoft Genomics, najdete v části [co je služba Microsoft Genomics?](overview-what-is-genomics.md). Další informace o odstraňování potíží naleznete v tématu naše [Průvodce odstraňováním potíží](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>Co je podpora GATK 4 služby Microsoft Genomics?
Až do konce kalendářního roku 2018 ve službě Microsoft Genomics je nabídka 20 WGS spuštění s GATK4 bez poplatků. K účasti v registru této nabídky [tady](https://aka.ms/msgatk4). 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Jaké jsou běžné problémy, které můžu může dojít při spuštění Microsoft Genomics služby GATK4 povýšení
Tady je seznam běžných chyb, které se můžete setkat a jejich doporučená řešení:

| **Zpráva**                                                                                                                                                                                    | **Příčina**                                                                                                    | **Řešení**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` pro váš účet není povoleno. Další informace najdete v tématu https://docs.microsoft.com/en-us/azure/genomics/frequently-asked-questions-genomics                               | Pokoušíte se spustit GATK4 pracovní postupy ve službě Microsoft Genomics bez aktivace.       | Navštivte prosím [tady](https://aka.ms/msgatk4) aktivovat svůj účet. Všimněte si, že vyprší platnost zkušební verze na konci kalendářního roku 2018. Nebudete moci uživatel aktivovat svůj účet propagační spuštění po tomto datu. |
| Děkujeme za vyzkoušení `gatk4-promo`. Vaše zkušební období skončilo. Další informace https://docs.microsoft.com/en-us/azure/genomics/frequently-asked-questions-genomics                  | Vypršela platnost zkušební verze GATK4 na konci kalendářní rok a pokoušíte se vyvolat `gatk4-promo` název_procesu.  | Přepnout parametr název_procesu, `gatk4`, namísto `gatk4-promo`. Toto je oficiální gatk4 verze a jejich pracovního postupu se účtuje, pokud použijete tento parametr.                                         |
| Děkujeme za vyzkoušení `gatk4-promo` jste už použili všechny přidělené spuštění. Další informace najdete v tématu https://docs.microsoft.com/en-us/azure/genomics/frequently-asked-questions-genomics | Úspěšně jste odeslali všech vašich 20 propagační spuštění pro GATK4.                               | Odešlete všechny nové gatk4 běží s argumentem název_procesu nastaveným na hodnotu `gatk4` místo `gatk4-promo`. Použijete-li tento parametr se bude účtovat pracovního postupu.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Můžu spouštět pracovní postupy GATK4 na Microsoft Genomics bez nutnosti registrace GATK4 podporu?
Ano, v souboru config.txt ve službě Microsoft Genomics, zadejte název_procesu k `gatk4`. Všimněte si, že vám budou účtovány za běžné fakturační sazby a 20 free spouští se nevztahuje k vašemu účtu Microsoft Genomics.



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
Potřebujete dva přístupové klíče v případě, že chcete aktualizovat (obnovení) je bez přerušení využívání služby. Například pokud chcete aktualizovat první klíč, měli byste všech nových pracovních postupů, použijte druhý klíč. Potom počkejte všechny pracovní postupy pomocí první klíč dokončit před aktualizací první klíč.

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

