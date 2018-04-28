---
title: 'Microsoft genomika: Průvodce odstraňováním potíží s | Microsoft Docs'
titleSuffix: Azure
description: Další informace o řešení potíží s strategie
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
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Tento přehled popisuje strategií, které řeší běžné problémy při použití služby Microsoft Genomics. Obecné – nejčastější dotazy, najdete v části [běžné otázky](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Jak zkontrolovat stav Moje úlohy?
Stav pracovního postupu můžete zkontrolovat voláním `msgen status` z příkazového řádku, jak je vidět. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Existují tři požadované argumenty:
* Adresa URL – základní identifikátor URI pro rozhraní API
* KLÍČ – přístupový klíč pro váš účet genomika. 
* ID – ID pracovního postupu

Pokud chcete vyhledat adresu URL a klíče, přejděte na portál Azure a otevřete stránku genomika účtu. V části **správy** záhlaví, vyberte **přístupové klíče**. Zde najít adresu rozhraní API a klíče pro přístup k.

Alternativně můžete zahrnout cestu k souboru konfigurace nemusí přímo zadávat adresu URL a klíč. Všimněte si, že pokud zahrnete tyto argumenty příkazového řádku, jakož i do konfiguračního souboru, argumenty příkazového řádku bude mít přednost. 

Po volání `msgen status`, se zobrazí uživatelsky přívětivý zpráva popisující, zda pracovním postupu byla úspěšně nebo udává příčinu selhání úlohy. 


## <a name="get-more-information-about-my-workflow-status"></a>Další informace o stavu pracovního postupu

Chcete-li získat další informace o proč nemusí mít úlohu úspěšné, můžete prozkoumat souborů protokolů během pracovního postupu. V kontejneru vaší výstup, měli byste vidět `[youroutputfilename].logs.zip` složky.  Rozbalení této složky, zobrazí se následující položky:

* outputFileList.txt – seznam výstupní soubory vytvořené během pracovního postupu
* StandardError.txt – Tento soubor je prázdný.
* standardoutput.txt – obsahuje nejvyšší úrovně protokolování pracovního postupu. 
* Soubory – všechny ostatní soubory v protokolu GATK `logs` složky

`standardoutput.txt` Souboru je vhodná k určení, proč nebylo úspěšné pracovní postup, zahrnuje informace nízké úrovně pracovního postupu. 

## <a name="common-issues-and-how-to-resolve-them"></a>Běžné problémy a jejich řešení
V této části jsou zdůrazněné stručně nejčastější problémy a jejich řešení.

### <a name="fastq-files-are-unmatched"></a>Fastq soubory neodpovídající
Soubory Fastq by se měly lišit pouze koncové /1 nebo /2 v identifikátoru ukázka. Pokud jste omylem odeslali neodpovídající FASTQ soubory, může se zobrazit následující chybové zprávy při volání metody `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

To vyřešit, zkontrolujte, pokud jsou soubory fastq odeslána do pracovního postupu ve skutečnosti odpovídající sadu. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Chyba při odesílání souboru .bam. Objekt blob výstup už existuje a volba přepisování byla nastavena na hodnotu False.
Pokud se zobrazí následující chybová zpráva, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, zadaná výstupní složka již obsahuje výstupního souboru se stejným názvem.  Odstraňte existující výstupní soubor nebo zapnout možnost přepisování v konfiguračním souboru. Potom odešlete znovu pracovního postupu.

### <a name="when-to-contact-microsoft-genomics-support"></a>Když se obrátit na podporu společnosti Microsoft Genomics
Pokud se zobrazí následující chybové zprávy, došlo k vnitřní chybě. 

* `Error locating input files on worker machine`
* `Process management failure`

Zkuste odeslat znovu pracovního postupu. Pokud nadále úlohy s chybami, nebo pokud máte další otázky, obraťte se na podporu společnosti Microsoft genomika z portálu Azure.

![Obraťte se na podporu na portálu Azure](./media/troubleshooting-guide/genomics-contact-support.png "obraťte se na podporu na portálu Azure")

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak k řešení běžných problémů se službou Microsoft Genomics. Další informace a další obecné – nejčastější dotazy najdete v tématu [běžné otázky](frequently-asked-questions-genomics.md). 