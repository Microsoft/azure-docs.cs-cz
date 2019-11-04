---
title: Průvodce odstraňováním potíží
titleSuffix: Microsoft Genomics
description: Přečtěte si o strategiích řešení potíží s používáním Microsoft Genomics, včetně chybových zpráv a jejich řešení.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485995"
---
# <a name="troubleshooting-guide"></a>Průvodce řešením potíží

Tady je několik tipů pro řešení potíží s některými běžnými problémy, které se můžou při používání služby Microsoft Genomics MSGEN.

 Nejčastější dotazy, které se netýkají řešení potíží, najdete v tématu [Nejčastější dotazy](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1: Vyhledání kódů chyb přidružených k pracovnímu postupu

Můžete najít chybové zprávy přidružené k pracovnímu postupu:

1. Použití příkazového řádku a zadání `msgen status`
2. Prozkoumání obsahu StandardOutput. txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. použití příkazového řádku `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Existují tři povinné argumenty:

* URL – základní identifikátor URI pro rozhraní API
* KLÍČ – přístupový klíč pro váš účet genomiky
    * Adresu URL a klíč zjistíte tak, že přejdete na Azure Portal a otevřete stránku svého účtu Microsoft Genomics. Pod hlavičkou **správy** vyberte **přístupové klíče**. Tam najdete jak adresu URL rozhraní API, tak přístupové klávesy.

  
* ID – ID pracovního postupu
    * Typ ID pracovního postupu najdete v příkazu `msgen list`. Za předpokladu, že konfigurační soubor obsahuje adresu URL a přístupové klíče a je umístěn ve stejném umístění jako váš msgen exe, bude příkaz vypadat takto: 
        
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

Pro pracovní postup s ID 1001 a soubor config. txt umístěný ve stejné cestě jako spustitelný soubor msgen bude příkaz vypadat takto:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. prověřte obsah StandardOutput. txt. 
Vyhledejte kontejner výstupu pro daný pracovní postup. MSGEN vytvoří složku `[workflowfilename].logs.zip` po každém spuštění pracovního postupu. Rozbalte složku pro zobrazení jejího obsahu:

* outputFileList. txt – seznam výstupních souborů vyprodukovaných během pracovního postupu
* StandardError. txt – Tento soubor je prázdný.
* StandardOutput. txt – protokoluje všechny stavové zprávy nejvyšší úrovně včetně chyb, ke kterým došlo při spuštění pracovního postupu.
* Soubory protokolu GENOME – všechny ostatní soubory ve složce `logs`

V případě řešení potíží zkontrolujte obsah StandardOutput. txt a poznamenejte si všechny zobrazené chybové zprávy.


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
| BAM          | 201        |  Nelze číst soubor BAM [název_souboru].                                                                                      |Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 202        | Nelze číst soubor BAM [název_souboru]. Soubor je moc malý a záhlaví chybí.                                                                                        | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 203        |   Nelze číst soubor BAM [název_souboru]. Hlavička souboru je poškozená.                                                                                      |Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                           |
| BAM          | 204        |    Nelze číst soubor BAM [název_souboru]. Hlavička souboru je poškozená.                                                                                     | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                           |
| BAM          | 205        |    Nelze číst soubor BAM [název_souboru]. Hlavička souboru je poškozená.                                                                                     | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 206        |   Nelze číst soubor BAM [název_souboru]. Hlavička souboru je poškozená.                                                                                      | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 207        |  Nelze číst soubor BAM [název_souboru]. Zkrácený soubor v blízkosti posunu [offset].                                                                                       | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                            |
| BAM          | 208        |   Neplatný soubor BAM Připravená [Read_Id] nemá v souboru [název_souboru] žádnou sekvenci.                                                                                      | Ověřte formát souboru BAM.  Odešlete pracovní postup se správně formátovaným souborem.                                                                             |
| SOUBORŮ fastq        | 300        |  Nepovedlo se přečíst soubor souborů fastq. [Název_souboru] nekončí znakem nového řádku.                                                                                     | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                           |
| SOUBORŮ fastq        | 301        |   Nelze číst soubor souborů fastq [název_souboru]. Záznam souborů fastq je větší než velikost vyrovnávací paměti na posunu: [_Offset]                                                                                      | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 302        |     Chyba syntaxe souborů fastq Soubor [název_souboru] obsahuje prázdný řádek.                                                                                    | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 303        |       Chyba syntaxe souborů fastq Soubor [název_souboru] má neplatný počáteční znak na posunu: [_Offset], typ řádku: [line_type], znak: [_char].                                                                                  | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 304      |  Chyba syntaxe souborů fastq v připraveném [_ReadID].  První čtení dávky nemá v souboru [název_souboru] připravená koncová část/1.                                                                                       | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
| SOUBORŮ fastq        | 305        |  Chyba syntaxe souborů fastq v připraveném [_readID]. Druhé čtení dávky nemá v souboru [název_souboru] připravená koncová část/2.                                                                                      | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                          |
| SOUBORŮ fastq        | 306        |  Chyba syntaxe souborů fastq v připraveném [_ReadID]. První přečtený pár nemá ID, které končí v/1 souboru [název_souboru].                                                                                       | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                          |
| SOUBORŮ fastq        | 307        |   Chyba syntaxe souborů fastq v připraveném [_ReadID]. Připravenost nekončí na/1 nebo/2. Soubor [název_souboru] nelze použít jako spárovaný soubor souborů fastq.                                                                                      |Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                          |
| SOUBORŮ fastq        | 308        |  Chyba čtení souborů fastq Čtení obou konců reaguje jinak. Zvolili jste správné soubory souborů fastq?                                                                                       | Opravte formát souboru souborů fastq a znovu odešlete pracovní postup.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3: kontaktování podpory Microsoft Genomics

Pokud budete mít i nadále problémy s úlohou nebo máte nějaké jiné dotazy, obraťte se na podporu Microsoft Genomics Azure Portal. Další informace o tom, jak odeslat žádost o podporu, najdete [tady](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak řešit a řešit běžné problémy se službou Microsoft Genomics. Další informace a obecnější Nejčastější dotazy najdete v tématu [Nejčastější dotazy](frequently-asked-questions-genomics.md). 
