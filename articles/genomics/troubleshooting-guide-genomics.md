---
title: Průvodce odstraňováním potíží
titleSuffix: Microsoft Genomics
description: Přečtěte si o strategiích řešení potíží s používáním Microsoft Genomics, včetně chybových zpráv a jejich řešení.
keywords: řešení potíží, chyba, ladění
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82100902"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tady je několik tipů pro řešení potíží s některými běžnými problémy, které se můžou při používání služby Microsoft Genomics MSGEN.

 Nejčastější dotazy, které se netýkají řešení potíží, najdete v tématu [Nejčastější dotazy](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1: Vyhledání kódů chyb přidružených k pracovnímu postupu

Můžete najít chybové zprávy přidružené k pracovnímu postupu:

1. Použití příkazového řádku a psaní  `msgen status`
2. Prozkoumání obsahu standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. používání příkazového řádku `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Existují tři povinné argumenty:

* URL – základní identifikátor URI pro rozhraní API
* KLÍČ – přístupový klíč pro váš účet genomiky
    * Adresu URL a klíč zjistíte tak, že přejdete na Azure Portal a otevřete stránku svého účtu Microsoft Genomics. Pod hlavičkou **správy** vyberte **přístupové klíče**. Tam najdete jak adresu URL rozhraní API, tak přístupové klávesy.

  
* ID – ID pracovního postupu
    * Typ ID pracovního postupu zjistíte v `msgen list` příkazu. Za předpokladu, že konfigurační soubor obsahuje adresu URL a přístupové klíče a je umístěn ve stejném umístění jako váš msgen exe, bude příkaz vypadat takto: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Výstup z tohoto příkazu bude vypadat takto:
        
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
  >  Případně můžete místo přímého zadání adresy URL a klíče použít cestu k konfiguračnímu souboru. Pokud zahrnete tyto argumenty do příkazového řádku i do konfiguračního souboru, budou mít argumenty příkazového řádku přednost.  

Pro pracovní postup s ID 1001 a config.txt soubor umístěný ve stejné cestě jako spustitelný soubor msgen bude příkaz vypadat takto:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Projděte si obsah standardoutput.txt 
Vyhledejte kontejner výstupu pro daný pracovní postup. MSGEN vytvoří   `[workflowfilename].logs.zip` složku po každém spuštění pracovního postupu. Rozbalte složku pro zobrazení jejího obsahu:

* outputFileList.txt – seznam výstupních souborů vyprodukovaných během pracovního postupu
* standarderror.txt – tento soubor je prázdný.
* standardoutput.txt – protokoluje všechny stavové zprávy nejvyšší úrovně včetně chyb, ke kterým došlo při spuštění pracovního postupu.
* Soubory protokolu GENOME – všechny ostatní soubory ve `logs` složce

Při řešení potíží zkontrolujte obsah standardoutput.txt a poznamenejte si všechny zobrazené chybové zprávy.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Krok 2: Vyzkoušejte doporučené kroky pro běžné chyby

V této části je stručně zvýrazněný výstup běžných chyb Microsoft Genomics služby (msgen) a strategiích, které můžete použít k jejich řešení. 

Služba Microsoft Genomics (msgen) může vyvolat následující dva druhy chyb:

1. Vnitřní chyby služby: chyby, které jsou interní pro službu, které nemusejí být vyřešeny opravou parametrů nebo vstupních souborů. Někdy se může stát, že se tyto chyby vyřeší opětovným odesláním pracovního postupu.
2. Chyby vstupu: chyby, které lze vyřešit pomocí správných argumentů nebo oprav formátů souborů.

### <a name="1-internal-service-errors"></a>1. interní chyby služby

Interní chyba služby není uživatelem nastavená akce. Pracovní postup můžete znovu odeslat, ale pokud to nefunguje, obraťte se na podporu Microsoft Genomics.

| Chybová zpráva                                                                                                                            | Doporučený postup při řešení potíží                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Došlo k vnitřní chybě. Zkuste znovu odeslat pracovní postup. Pokud se tato chyba zobrazí znovu, požádejte o pomoc Microsoft Genomics podporu. | Odešlete pracovní postup znovu. Pokud potíže potrvají, požádejte o pomoc Microsoft Genomics podporu. Pokud se problém opakuje, vytvořte [lístek](file-support-ticket-genomics.md )podpory. |

### <a name="2-input-errors"></a>2. chyby vstupu

Tyto chyby jsou uživatelem akce. Na základě typu souboru a kódu chyby služba Microsoft Genomics výstupy mají odlišné kódy chyb. Postupujte podle doporučených kroků pro řešení potíží uvedených níže.

| Typ souboru | Kód chyby | Chybová zpráva                                                                           | Doporučený postup při řešení potíží                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Všechny          | 701        | Čtení [připraveno] má [numberOfBases] základ, ale limit je [maxReadLength].           | Nejběžnějším důvodem této chyby je poškození souboru vedoucí ke zřetězení dvou čtení. Ověřte vstupní soubory. |
| BAM          | 200        |   Nelze číst soubor ' [yourFileName] '.                                                                                       | Ověřte formát souboru BAM. Odešlete pracovní postup znovu se správným formátovaným souborem.                                                                           |
| BAM          | 201        |  Nelze číst soubor BAM [File_name].                                                                                      |Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 202        | Nelze číst soubor BAM [File_name]. Soubor je moc malý a záhlaví chybí.                                                                                        | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 203        |   Nelze číst soubor BAM [File_name]. Hlavička souboru je poškozená.                                                                                      |Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                           |
| BAM          | 204        |    Nelze číst soubor BAM [File_name]. Hlavička souboru je poškozená.                                                                                     | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                           |
| BAM          | 205        |    Nelze číst soubor BAM [File_name]. Hlavička souboru je poškozená.                                                                                     | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 206        |   Nelze číst soubor BAM [File_name]. Hlavička souboru je poškozená.                                                                                      | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 207        |  Nelze číst soubor BAM [File_name]. Zkrácený soubor v blízkosti posunu [offset].                                                                                       | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 208        |   Neplatný soubor BAM Připravená [Read_Id] nemá v souboru [File_name] žádnou sekvenci.                                                                                      | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                             |
| SOUBORŮ fastq        | 300        |  Nepovedlo se přečíst soubor souborů fastq. [File_name] nekončí novým řádkem.                                                                                     | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                           |
| SOUBORŮ fastq        | 301        |   Nelze číst soubor souborů fastq [File_name]. Záznam souborů fastq je větší než velikost vyrovnávací paměti na posunu: [_offset]                                                                                      | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 302        |     Chyba syntaxe souborů fastq Soubor [File_name] obsahuje prázdný řádek.                                                                                    | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 303        |       Chyba syntaxe souborů fastq Soubor [File_name] má neplatný počáteční znak na posunu: [_offset], typ řádku: [line_type], znak: [_char].                                                                                  | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 304      |  SOUBORŮ fastq Chyba syntaxe v připraveném [_ReadID].  První čtení dávky nemá připraven konec/1 v souboru [File_name].                                                                                       | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 305        |  SOUBORŮ fastq Chyba syntaxe v připraveném [_readID]. Druhé čtení dávky nemá v souboru [File_name] připraveno ukončení/2.                                                                                      | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                          |
| SOUBORŮ fastq        | 306        |  SOUBORŮ fastq Chyba syntaxe v připraveném [_ReadID]. První přečtený pár nemá ID, které končí v/1 souboru [File_name].                                                                                       | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                          |
| SOUBORŮ fastq        | 307        |   SOUBORŮ fastq Chyba syntaxe v připraveném [_ReadID]. Připravenost nekončí na/1 nebo/2. Soubor [File_name] nelze použít jako spárované soubory souborů fastq.                                                                                      |Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                          |
| SOUBORŮ fastq        | 308        |  Chyba čtení souborů fastq Čtení obou konců reaguje jinak. Zvolili jste správné soubory souborů fastq?                                                                                       | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3: kontaktování podpory Microsoft Genomics

Pokud budete mít i nadále problémy s úlohou nebo máte nějaké jiné dotazy, obraťte se na podporu Microsoft Genomics Azure Portal. Další informace o tom, jak odeslat žádost o podporu, najdete [tady](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak řešit a řešit běžné problémy se službou Microsoft Genomics. Další informace a obecnější Nejčastější dotazy najdete v tématu [Nejčastější dotazy](frequently-asked-questions-genomics.md). 
