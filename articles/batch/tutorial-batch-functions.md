---
title: Spustit úlohu služby Batch pomocí služby Azure Functions
description: Kurz – použít OCR pro naskenované dokumenty, jak se přidají do úložiště objektu blob
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342027"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Kurz: Spustit úlohu služby Batch pomocí služby Azure Functions

V tomto kurzu se dozvíte, jak aktivovat úlohy služby Batch pomocí Azure Functions. Projdeme příklad, ve kterém dokumenty do kontejneru objektů blob v Azure Storage mají optické rozpoznávání znaků (OCR) použitý k nim prostřednictvím služby Azure Batch. Pro zjednodušení zpracování OCR, nakonfigurujeme funkci Azure, který spouští úlohu služby Batch OCR pokaždé, když se přidá soubor do kontejneru objektů blob.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Azure Batch a propojený účet Azure Storage. Zobrazit [vytvoření účtu Batch](quick-create-portal.md#create-a-batch-account) Další informace o tom, jak vytvořit a propojit účty.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Vytvoření fondu služby Batch a úlohu služby Batch pomocí Průzkumníka služby Batch

V této části použijete Průzkumníka služby Batch k vytvoření fondu služby Batch a úlohy služby Batch, který se spustí úlohy optické rozpoznávání znaků. 

### <a name="create-a-pool"></a>Vytvoření fondu

1. Přihlaste se do Průzkumníka služby Batch pomocí přihlašovacích údajů Azure.
1. Vytvoření fondu tak, že vyberete **fondy** na levém bočním panelu, pak bude **přidat** tlačítko nad formuláři hledání. 
    1. Vyberte ID a zobrazovaný název. Použijeme `ocr-pool` pro účely tohoto příkladu.
    1. Nastavte typ škálování na **pevnou velikost**a nastavte počet vyhrazených uzlů na 3.
    1. Vyberte **Ubuntu 18.04-LTS** jako operační systém.
    1. Zvolte `Standard_f2s_v2` jako velikost virtuálního počítače.
    1. Povolte spouštěcího úkolu a přidejte příkaz `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Nezapomeňte nastavit identitu uživatele jako **úloh výchozího uživatele (správce)** , což umožňuje spuštění úlohy zahrnout spolu s `sudo`.
    1. Vyberte **OK**.
### <a name="create-a-job"></a>Vytvoření úlohy

1. Vytvoření úlohy ve fondu tak, že vyberete **úlohy** na levém bočním panelu, pak bude **přidat** tlačítko nad formuláři hledání. 
    1. Vyberte ID a zobrazovaný název. Použijeme `ocr-job` pro účely tohoto příkladu.
    1. Nastavte fond `ocr-pool`, nebo cokoli, co název jste zvolili pro svůj fond.
    1. Vyberte **OK**.


## <a name="create-blob-containers"></a>Vytvoření kontejnerů objektů blob

Tady vytvoříte kontejnery objektů blob, které budou ukládání vašich vstupních a výstupních souborů pro úlohy Batch optické rozpoznávání znaků.

1. Přihlaste se do Průzkumníka služby Storage pomocí přihlašovacích údajů Azure.
1. Pomocí účtu úložiště, který je propojený s vaším účtem Batch, vytvořte dva kontejnery objektů blob (jeden pro vstupní soubory, jeden pro výstupní soubory) pomocí následujících kroků na [vytvořte kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

V tomto příkladu je název vstupního kontejneru `input` a kde jsou všechny dokumenty bez OCR prvním odeslání ke zpracování. Výstupní kontejner má název `output` a úlohy služby Batch, kam zapisuje zpracovaných dokumentů pomocí technologie OCR.  
    * V tomto příkladu, zavoláme vám naše vstupního kontejneru `input`a naše výstupní kontejner `output`.  
    * Vstupního kontejneru je, kde jsou všechny dokumenty bez OCR prvním odeslání.  
    * Výstupní kontejner je, kde úlohy služby Batch zapíše dokumentů pomocí technologie OCR.  

Vytvoření sdíleného přístupového podpisu pro výstupní kontejner v Průzkumníku služby Storage. To udělat tak, že kliknete pravým tlačítkem na výstupní kontejner a vyberete **získat sdílený přístupový podpis...** . V části **oprávnění**, zkontrolujte **zápisu**. Žádná další oprávnění jsou nezbytné.  

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

V této části vytvoříte funkce Azure Functions, která aktivuje optického rozpoznávání znaků dávkovou úlohu, když se do vstupního kontejneru nahraje soubor.

1. Postupujte podle kroků v [vytvoření funkce aktivované službou Azure Blob storage](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) vytvořit funkci.
    1. Po zobrazení výzvy pro účet úložiště, použijte stejný účet úložiště, který je propojený s vaším účtem Batch.
    1. Pro **zásobník modulu runtime**, zvolte .NET. Budeme psát naše funkce C# pro využití sady .NET SDK služby Batch.
1. Po vytvoření funkce aktivované objektů blob pomocí [ `run.csx` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) a [ `function.proj` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) z Githubu ve funkci.
    * `run.csx` se spustí, když se přidá nový objekt blob ke vstupnímu objektu blob kontejneru.
    * `function.proj` obsahuje seznam externích knihovnách v kódu funkce, například .NET SDK služby Batch.
1. Změnit zástupný symbol hodnoty proměnné `Run()` funkce `run.csx` souboru tak, aby odrážel vaše přihlašovací údaje Batch a storage. Přihlašovací údaje účtu Batch a storage najdete na webu Azure Portal v **klíče** části vašeho účtu Batch.
    * Načíst přihlašovací údaje účtu Batch a storage na webu Azure Portal v **klíče** části vašeho účtu Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Aktivovat funkci a načíst výsledky

Nahrát některých nebo všech skenovaných souborů z [ `input_files` ](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) do vstupního kontejneru adresáře na Githubu. Monitorování služby Batch Explorer potvrďte, že úloha bude přidána k `ocr-pool` pro každý soubor. Po několika sekundách se přidá soubor pomocí technologie OCR použita pro výstupní kontejner. Soubor je pak viditelné a získat v Průzkumníku služby Storage.

Kromě toho můžete sledovat souborů protokolů v dolní části okna Azure Functions webového editoru, kde se zobrazí zpráva podobná této pro každý soubor, že nahrajete do vstupního kontejneru:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Stažení výstupních souborů z Průzkumníka služby Storage do místního počítače, nejprve vyberte soubory a pak vyberte **Stáhnout** na horním pásu karet. 

> [!TIP]
> Stažené soubory je možné prohledávat, je-li otevřít ve čtení souborů PDF.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Vytváření fondů a úloh pomocí Průzkumníka služby Batch
> * Pomocí Průzkumníka služby Storage k vytvoření kontejnerů objektů blob a sdílený přístupový podpis (SAS)
> * Vytvoření funkce aktivované objektů blob v Azure
> * Nahrání vstupních souborů do služby Storage
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

* Další příklady použití rozhraní .NET API k plánování a zpracování úloh služby Batch najdete v tématu [ukázky na Githubu](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Další aktivační události Azure Functions, které můžete použít ke spuštění úlohy služby Batch najdete v tématu [dokumentaci ke službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
