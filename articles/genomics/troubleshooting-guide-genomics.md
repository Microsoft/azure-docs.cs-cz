---
title: 'Microsoft Genomics: Průvodce odstraňováním potíží | Dokumentace Microsoftu'
titleSuffix: Azure
description: Další informace o strategií pro řešení potíží
keywords: řešení potíží s chybou, ladění
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 2c10259e4b9fa180d09ceef0359e7ec99e8200b1
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239895"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tady jsou některé tipy pro některé běžné problémy, které může setkat při používání služby Microsoft Genomics, MSGEN odstraňování potíží.

 Nejčastější dotazy, nesouvisí s řešením problémů, najdete v tématu [běžné otázky](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1: Vyhledání kódy chyb přidružené pracovní postup

Můžete najít chybové zprávy přidružené k pracovnímu postupu podle:

1. Pomocí příkazového řádku a zadejte text  `msgen status`
2. Prověřování obsahu standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Pomocí příkazového řádku `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Existují tři povinné argumenty:

* Adresa URL – základní identifikátor URI pro rozhraní API
* KLÍČ – přístupový klíč pro svůj účet Genomics
    * Najít adresu URL a klíč, přejděte na webu Azure portal a otevřete stránku účtu Microsoft Genomics. V části **správu** záhlaví, vyberte **přístupové klíče**. Tam najdete adresy URL rozhraní API a přístupové klíče.

  
* ID – ID pracovního postupu
    * Najít typ ID vašeho pracovního postupu v `msgen list` příkazu. Za předpokladu, že konfigurační soubor obsahuje adresu URL a přístupové klíče a nachází se ve stejném umístění jako váš soubor exe msgen bude příkaz vypadat například takto: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Výstup tohoto příkazu bude vypadat například takto:
        
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
 >  Případně můžete zahrnout cesty do konfiguračního souboru nemusí přímo zadávat adresu URL a klíč. Pokud zahrnete tyto argumenty příkazového řádku, jakož i konfiguračním souboru, argumenty příkazového řádku bude mít přednost.  

Pro pracovní postup ID 1001 a umístěn ve stejné cestě jako msgen spustitelného souboru config.txt bude příkaz vypadat například takto:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Zkontrolovat obsah standardoutput.txt 
Vyhledejte výstupní kontejner v pracovním postupu. Vytvoří MSGEN, `[workflowfilename].logs.zip` složky po každé provedení pracovního postupu. Rozbalte složky a zobrazit jeho obsah:

* outputFileList.txt – seznam výstupní soubory vytvořené během pracovního postupu
* StandardError.txt – Tento soubor je prázdný.
* standardoutput.txt – protokoluje všechny nejvyšší úrovně stavové zprávy, včetně chyb, ke kterým došlo při spouštění pracovního postupu.
* Soubory – všechny ostatní soubory protokolu GATK `logs` složky

S řešením problémů, zkontrolovat obsah standardoutput.txt a mějte na paměti, které se zobrazí chybové zprávy.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Krok 2: Zkuste doporučené kroky pro běžné chyby

Tato část se stručně zaměřuje běžné chyby výstupu pomocí služby Microsoft Genomics (msgen) a strategií, které můžete použít k jejich řešení. 

Ve službě Microsoft Genomics (msgen) může vyvolat tyto dva druhy chyb:

1. Vnitřní chyby služby: Chyby, které jsou interní služba, která nemusí být přeloženy opravou parametry nebo vstupní soubory. Někdy se znovu spouští pracovní postup může tyto chyby opravit.
2. Chyby vstupu: Chyby, které se dají vyřešit pomocí správné argumenty nebo opravě formáty souborů.

### <a name="1-internal-service-errors"></a>1. Interní služba chyby

Vnitřní chybě služby není uživatelem užitečné. Může znovu spustit pracovní postup, ale pokud to nepomůže, obraťte se na podporu společnosti Microsoft Genomics

| Chybová zpráva                                                                                                                            | Doporučený postup při řešení potíží                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Došlo k vnitřní chybě. Zkuste se znovu spouští pracovní postup. Pokud se zobrazí tato chyba, požádejte o pomoc podporu společnosti Microsoft Genomics | Odeslání pracovního postupu znova. Kontaktujte Microsoft Genomics podporu o pomoc Pokud se problém nevyřeší tak, že vytvoříte podporu [lístek](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Chyby vstupu

Tyto chyby jsou užitečné uživatele. Podle typu souboru a kód chyby, služby Microsoft Genomics vypíše různé chybové kódy. Postupujte podle níže uvedených doporučený postup řešení problémů.

| Typ souboru | Kód chyby | Chybová zpráva                                                                           | Doporučený postup při řešení potíží                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Všechny          | 701        | Čtení [readId] má základních tříd [numberOfBases], ale limit je [maxReadLength]           | Nejčastější příčinou této chyby je poškození souboru, což vede ke zřetězení dvou čtení. Zkontrolujte vstupní soubory. |                                |
| BAM          | 200        |   Nelze načíst soubor "[yourFileName]".                                                                                       | Zkontrolujte formát souboru BAM. Znovu odešlete pracovní postup s správně formátovaného souboru.                                                                           |
| BAM          | 201        |  Nelze načíst soubor BAM [název_souboru].                                                                                      |Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                            |
| BAM          | 202        | Nelze načíst soubor BAM [název_souboru]. Příliš malé a chybějící záhlaví souboru.                                                                                        | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                            |
| BAM          | 203        |   Nelze načíst soubor BAM [název_souboru]. Záhlaví souboru je poškozená.                                                                                      |Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                           |
| BAM          | 204        |    Nelze načíst soubor BAM [název_souboru]. Záhlaví souboru je poškozená.                                                                                     | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                           |
| BAM          | 205        |    Nelze načíst soubor BAM [název_souboru]. Záhlaví souboru je poškozená.                                                                                     | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                            |
| BAM          | 206        |   Nelze načíst soubor BAM [název_souboru]. Záhlaví souboru je poškozená.                                                                                      | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                            |
| BAM          | 207        |  Nelze načíst soubor BAM [název_souboru]. Soubor zkrácen blízko posunu [posun].                                                                                       | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                            |
| BAM          | 208        |   Neplatný soubor BAM. ReadID [Read_Id] nemá žádné pořadí v souboru [název_souboru].                                                                                      | Zkontrolujte formát souboru BAM.  Odešlete pracovní postup s správně formátovaného souboru.                                                                             |
| SOUBORY FASTQ        | 300        |  Nelze načíst soubor souborů FASTQ. [Název_souboru] nekončí nový řádek.                                                                                     | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                           |
| SOUBORY FASTQ        | 301        |   Soubor nelze číst soubory FASTQ [název_souboru]. Soubory FASTQ záznamu je větší než velikost vyrovnávací paměti na posunu: [_offset]                                                                                      | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                         |
| SOUBORY FASTQ        | 302        |     Chyba syntaxe souborů FASTQ. Soubor [název_souboru] má prázdný řádek.                                                                                    | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                         |
| SOUBORY FASTQ        | 303        |       Chyba syntaxe souborů FASTQ. Soubor [název_souboru] má neplatný počáteční znak na pozici: Typ [_offset], řádek: [line_type] znaků: [_char]                                                                                  | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                         |
| SOUBORY FASTQ        | 304      |  Chyba syntaxe souborů FASTQ readID [_ReadID].  První čtení dávky nemá readID s koncovkou /1 v souboru [název_souboru]                                                                                       | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                         |
| SOUBORY FASTQ        | 305        |  Chyba syntaxe souborů FASTQ readID [_readID]. Druhý čtení dávky nemá readID s koncovkou /2 v souboru [název_souboru]                                                                                      | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                          |
| SOUBORY FASTQ        | 306        |  Chyba syntaxe souborů FASTQ readID [_ReadID]. První čtení z dvojice nemá ID, které končí na /1 v souboru [název_souboru]                                                                                       | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                          |
| SOUBORY FASTQ        | 307        |   Chyba syntaxe souborů FASTQ readID [_ReadID]. ReadID nekončí /1 nebo / 2. Soubor [název_souboru] nelze použít jako soubor párovaných souborů FASTQ.                                                                                      |Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                          |
| SOUBORY FASTQ        | 308        |  Chyba při čtení souborů FASTQ. Čtení z obou koncích odpověděl odlišně. Zvolíte správné souborů FASTQ?                                                                                       | Opravte formát souboru FASTQ a znovu odešlete pracovní postup.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3: Podpora Contact Microsoft Genomics

Pokud budete nadále došlo k selhání úlohy, nebo pokud máte nějaké dotazy, obraťte se na podporu Microsoft Genomics na webu Azure Portal. Další informace o tom, jak odeslat žádost o podporu můžete najít [tady](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak odstraňovat potíže a řešit běžné problémy se službou Microsoft Genomics. Další informace a další obecné – nejčastější dotazy najdete v tématu [běžné otázky](frequently-asked-questions-genomics.md). 
