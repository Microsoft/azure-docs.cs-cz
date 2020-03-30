---
title: Průvodce odstraňováním potíží
titleSuffix: Microsoft Genomics
description: Přečtěte si o strategiích řešení potíží s používáním microsoft genomiky, včetně chybových zpráv a jejich řešení.
keywords: řešení potíží, chyba, ladění
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73485995"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Zde je několik tipů pro řešení potíží pro některé běžné problémy, které mohou čelit při používání služby Microsoft Genomics, MSGEN.

 Nejčastější dotazy, které nesouvisí s odstraňováním potíží, naleznete [v tématu Běžné otázky](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1: Vyhledání kódů chyb přidružených k pracovnímu postupu

Chybové zprávy přidružené k pracovnímu postupu můžete vyhledat takto:

1. Použití příkazového řádku a psaní`msgen status`
2. Zkoumání obsahu standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Použití příkazového řádku`msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Existují tři požadované argumenty:

* URL - základní identifikátor URI pro rozhraní API
* KEY - přístupový klíč pro váš účet Genomics
    * Pokud chcete najít adresu URL a klíč, přejděte na portál Azure a otevřete stránku účtu Microsoft Genomics. V záhlaví **Správa** zvolte **Přístupové klávesy**. Zde najdete adresu URL rozhraní API i přístupové klíče.

  
* ID - ID pracovního postupu
    * Chcete-li najít typ `msgen list` ID pracovního postupu v příkazu. Za předpokladu, že váš konfigurační soubor obsahuje adresu URL a přístupové klávesy a je umístěn na stejném místě jako váš msgen exe, příkaz bude vypadat takto: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Výstup z tohoto příkazu bude vypadat takto :
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Případně můžete místo přímého zadávání adresy URL a klíče zahrnout cestu k konfiguračnímu souboru. Pokud tyto argumenty zahrnete do příkazového řádku i do konfiguračního souboru, budou mít přednost argumenty příkazového řádku.  

Pro ID pracovního postupu 1001 a soubor config.txt umístěný ve stejné cestě jako spustitelný soubor msgen bude příkaz vypadat takto:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Zkontrolujte obsah souboru standardoutput.txt 
Vyhledejte výstupní kontejner pro daný pracovní postup. MSGEN vytvoří `[workflowfilename].logs.zip` složku, po každém spuštění pracovního postupu. Chcete-li zobrazit její obsah, rozbalte složku:

* outputFileList.txt - seznam výstupních souborů vytvořených během pracovního postupu
* standarderror.txt - tento soubor je prázdný.
* standardoutput.txt - protokoluje všechny stavové zprávy nejvyšší úrovně včetně chyb, ke kterým došlo při spuštění pracovního postupu.
* GATK log files - všechny `logs` ostatní soubory ve složce

Při řešení potíží zkontrolujte obsah souboru standardoutput.txt a poznamenejte si všechny chybové zprávy, které se zobrazí.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Krok 2: Vyzkoušejte doporučené kroky pro běžné chyby

Tato část stručně upozorňuje na běžné chyby výstupu služby Microsoft Genomics (msgen) a strategie, které můžete použít k jejich vyřešení. 

Služba Microsoft Genomics (msgen) může vyvolat následující dva druhy chyb:

1. Chyby interní služby: Chyby, které jsou interní služby, které nemusí být vyřešeny stanovením parametrů nebo vstupních souborů. Někdy může opětovné odeslání pracovního postupu tyto chyby opravit.
2. Vstupní chyby: Chyby, které lze vyřešit pomocí správných argumentů nebo opravovat formáty souborů.

### <a name="1-internal-service-errors"></a>1. Interní chyby služby

Vnitřní chyba služby nelze uživatelem najednat. Pracovní postup můžete odeslat znovu, ale pokud to nepomůže, obraťte se na podporu společnosti Microsoft Genomics.

| Chybová zpráva                                                                                                                            | Doporučený postup při řešení potíží                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Došlo k vnitřní chybě. Zkuste znovu odeslat pracovní postup. Pokud se tato chyba zobrazí znovu, obraťte se na podporu microsoft genomics o pomoc | Odešlete pracovní postup znovu. Obraťte se na podporu Microsoft Genomics o pomoc, pokud problém přetrvává vytvořením [lístku](file-support-ticket-genomics.md )podpory . |

### <a name="2-input-errors"></a>2. Vstupní chyby

Tyto chyby lze použít pro uživatele. Na základě typu souboru a kódu chyby vypíše služba Microsoft Genomics odlišné kódy chyb. Postupujte podle níže uvedených doporučených kroků pro řešení potíží.

| Typ souboru | Kód chyby | Chybová zpráva                                                                           | Doporučený postup při řešení potíží                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Všechny          | 701        | Read [readId] má základy [numberOfBases], ale limit je [maxReadLength]           | Nejčastějším důvodem této chyby je poškození souboru vedoucí ke zřetězení dvou čtení. Zkontrolujte vstupní soubory. |
| Bam          | 200        |   Soubor [yourFileName]nelze přečíst.                                                                                       | Zkontrolujte formát souboru BAM. Odešlete pracovní postup znovu se správně naformátovanou souborem.                                                                           |
| Bam          | 201        |  Soubor BAM nelze přečíst [File_name].                                                                                      |Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                            |
| Bam          | 202        | Soubor BAM nelze přečíst [File_name]. Soubor je příliš malý a chybí záhlaví.                                                                                        | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                            |
| Bam          | 203        |   Soubor BAM nelze přečíst [File_name]. Záhlaví souboru bylo poškozeno.                                                                                      |Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                           |
| Bam          | 204        |    Soubor BAM nelze přečíst [File_name]. Záhlaví souboru bylo poškozeno.                                                                                     | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                           |
| Bam          | 205        |    Soubor BAM nelze přečíst [File_name]. Záhlaví souboru bylo poškozeno.                                                                                     | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                            |
| Bam          | 206        |   Soubor BAM nelze přečíst [File_name]. Záhlaví souboru bylo poškozeno.                                                                                      | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                            |
| Bam          | 207        |  Soubor BAM nelze přečíst [File_name]. Soubor zkrácen v blízkosti posunu [posun].                                                                                       | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                            |
| Bam          | 208        |   Neplatný soubor BAM. ReadID [Read_Id] nemá v souboru [File_name] žádnou sekvenci.                                                                                      | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup se správně naformátovanou složkou.                                                                             |
| FASTQ        | 300        |  Soubor FASTQ nelze přečíst. [File_name] nekončí s novou linií.                                                                                     | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                           |
| FASTQ        | 301        |   Soubor FASTQ [File_name] nelze přečíst. Záznam FASTQ je větší než velikost vyrovnávací paměti při posunu: [_offset]                                                                                      | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                         |
| FASTQ        | 302        |     Chyba syntaxe FASTQ. Soubor [File_name] má prázdný řádek.                                                                                    | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                         |
| FASTQ        | 303        |       Chyba syntaxe FASTQ. Soubor[File_name] má neplatný počáteční znak při posunu: [_offset], typ řádku: [line_type], znak: [_char]                                                                                  | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                         |
| FASTQ        | 304      |  FastQ Syntax chyba na readID [_ReadID].  První čtení dávky nemá readID končící na /1 v souboru [File_name]                                                                                       | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                         |
| FASTQ        | 305        |  FastQ Syntax chyba na readID [_readID]. Druhé čtení dávky nemá readID končící na /2 v souboru [File_name]                                                                                      | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                          |
| FASTQ        | 306        |  FastQ Syntax chyba na readID [_ReadID]. První čtení páru nemá ID, které končí v /1 v souboru [File_name]                                                                                       | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                          |
| FASTQ        | 307        |   FastQ Syntax chyba na readID [_ReadID]. ReadID nekončí /1 nebo/2. Soubor [File_name] nelze použít jako spárovaný soubor FASTQ.                                                                                      |Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                          |
| FASTQ        | 308        |  Chyba čtení FASTQ. Čtení obou konců reagovalo odlišně. Vybrali jste správné fastq soubory?                                                                                       | Opravte formát souboru FASTQ a odešlete pracovní postup znovu.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3: Kontaktujte podporu Microsoft Genomics

Pokud máte i nadále selhání úloh nebo máte nějaké další otázky, obraťte se na podporu Microsoft Genomics z portálu Azure. Další informace o tom, jak odeslat žádost o podporu, naleznete [zde](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak řešit a řešit běžné problémy se službou Microsoft Genomics. Další informace a obecnější nejčastější dotazy naleznete [v běžných otázkách](frequently-asked-questions-genomics.md). 
