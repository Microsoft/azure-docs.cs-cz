---
title: 'Microsoft Genomics: Průvodce odstraňováním potíží | Dokumentace Microsoftu'
titleSuffix: Azure
description: Další informace o strategií pro řešení potíží
keywords: řešení potíží s chybou, ladění
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: d3991bdbcd9c3dcd08572dc92cc75aaebb02b133
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "34627360"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Tento přehled popisuje strategie pro řešení běžných potíží s při použití služby Microsoft Genomics. Nejčastější dotazy týkající se obecné, naleznete v tématu [běžné otázky](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Jak můžu zkontrolovat stav úlohy?
Můžete zkontrolovat stav pracovního postupu pomocí volání `msgen status` z příkazového řádku, jak je znázorněno. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Existují tři povinné argumenty:
* Adresa URL – základní identifikátor URI pro rozhraní API
* KLÍČ – přístupový klíč pro svůj účet Genomics. 
* ID – ID pracovního postupu

Najít adresu URL a klíč, přejděte na webu Azure portal a otevřete stránku svého účtu Genomics. V části **správu** záhlaví, vyberte **přístupové klíče**. Tam najdete adresy URL rozhraní API a přístupové klíče.

Alternativně můžete zahrnout cesty do konfiguračního souboru nemusí přímo zadávat adresu URL a klíč. Všimněte si, že pokud zahrnete tyto argumenty příkazového řádku, jakož i konfiguračním souboru, argumenty příkazového řádku bude mít přednost. 

Po volání `msgen status`, se zobrazí uživatelsky přívětivou zpráva popisující, zda pracovní postup proběhl úspěšně nebo pojmenováním důvod selhání úlohy. 


## <a name="get-more-information-about-my-workflow-status"></a>Získejte další informace o stavu pracovního postupu

Pokud chcete získat další informace o proč nemusí být úspěšné úlohy, můžete prozkoumat soubory protokolu vytvořené během pracovního postupu. Ve výstupní kontejner byste měli vidět `[youroutputfilename].logs.zip` složky.  Rozzipování tuto složku, zobrazí se následující položky:

* outputFileList.txt – seznam výstupní soubory vytvořené během pracovního postupu
* StandardError.txt – Tento soubor je prázdný.
* standardoutput.txt – obsahuje nejvyšší úrovně protokolování pracovního postupu. 
* Soubory – všechny ostatní soubory protokolu GATK `logs` složky

`standardoutput.txt` Soubor je dobrým začátkem určit, proč pracovního postupu se nezdařilo, protože zahrnuje další údaje nízké úrovně pracovního postupu. 

## <a name="common-issues-and-how-to-resolve-them"></a>Běžné problémy a jejich řešení
Tato část se stručně zaměřuje běžné problémy a jejich řešení.

### <a name="fastq-files-are-unmatched"></a>Jsou bezkonkurenční souborů Fastq
Souborů Fastq by se měly lišit pouze koncové /1 nebo /2 v ukázkový identifikátor. Pokud jste omylem odeslali bezkonkurenční souborů FASTQ, může se zobrazit následující chybové zprávy při volání metody `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Chcete-li tento problém vyřešit, zkontrolujte, pokud jsou soubory fastq odeslané do pracovního postupu ve skutečnosti odpovídající sady. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Chyba při nahrávání souborů .bam souboru. Výstupní objekt blob již existuje a možnost přepsat byl nastaven na hodnotu False.
Pokud se zobrazí následující chybová zpráva, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, výstupní složka již obsahuje výstupní soubor se stejným názvem.  Odstraňte existující výstupní soubor nebo zapnout možnost přepsat v konfiguračním souboru. Odešlete váš workflow.

### <a name="when-to-contact-microsoft-genomics-support"></a>Při kontaktování podpory Microsoft Genomics
Pokud se zobrazí následující chybové zprávy, došlo k vnitřní chybě. 

* `Error locating input files on worker machine`
* `Process management failure`

Zkuste se znovu spustit pracovní postup. Pokud budete nadále došlo k selhání úlohy, nebo pokud máte nějaké dotazy, obraťte se na podporu Microsoft Genomics na webu Azure Portal. Další informace o tom, jak odeslat žádost o podporu můžete najít [tady](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak odstraňovat potíže a řešit běžné problémy se službou Microsoft Genomics. Další informace a další obecné – nejčastější dotazy najdete v tématu [běžné otázky](frequently-asked-questions-genomics.md). 