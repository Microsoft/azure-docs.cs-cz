---
title: Aktivace dávkové úlohy pomocí funkcí Azure
description: Kurz – použití rozpoznávání OCR na naskenované dokumenty při jejich přidání do objektu blob úložiště
author: LauraBrenner
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: a967fdc14b85f294ee11cbcc57a8d2280dba38e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017186"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Kurz: Aktivace dávkové úlohy pomocí funkcí Azure

V tomto kurzu se dozvíte, jak spustit dávkovou úlohu pomocí funkce Azure. Projdeme si příklad, ve kterém dokumenty přidané do kontejneru objektů blob služby Azure Storage mají optické rozpoznávání znaků (OCR) aplikované na ně prostřednictvím Azure Batch. Chcete-li zjednodušit zpracování Rozpoznávání OCR, nakonfigurujeme funkci Azure, která spouští úlohu dávkového rozpoznávání OCR pokaždé, když je soubor přidán do kontejneru objektů blob.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Azure Batch a propojený účet Azure Storage. Další informace o vytváření a propojení účtů najdete v [tématu Vytvoření dávkového účtu.](quick-create-portal.md#create-a-batch-account)
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Průzkumník úložišť Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Vytvoření dávkového fondu a dávkové úlohy pomocí Průzkumníka dávek

V této části použijete Průzkumník a tím, že vytvoříte dávkový fond a dávkovou úlohu, která bude spouštět úlohy Rozpoznávání OCR. 

### <a name="create-a-pool"></a>Vytvoření fondu

1. Přihlaste se k Batch Exploreru pomocí přihlašovacích údajů Azure.
1. Vytvořte fond výběrem **bazénů** na levém panelu a potom tlačítkem **Přidat** nad vyhledávacím formulářem. 
    1. Zvolte ID a zobrazovaný název. Použijeme `ocr-pool` pro tento příklad.
    1. Nastavte typ měřítka na **Pevnou velikost**a nastavte počet vyhrazených uzlů na 3.
    1. Vyberte **Ubuntu 18.04-LTS** jako operační systém.
    1. Zvolte `Standard_f2s_v2` jako velikost virtuálního počítače.
    1. Povolte počáteční úlohu `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`a přidejte příkaz . Nezapomeňte nastavit identitu uživatele jako **výchozího uživatele úlohy (Správce),** která `sudo`umožňuje, aby úlohy při spuštění obsahovaly příkazy s .
    1. Vyberte **OK**.
### <a name="create-a-job"></a>Vytvoření úlohy

1. Vytvořte úlohu ve fondu výběrem **úlohy** na levé straně panelu, pak **přidat** tlačítko nad vyhledávacíformulář. 
    1. Zvolte ID a zobrazovaný název. Použijeme `ocr-job` pro tento příklad.
    1. Nastavte fond `ocr-pool`na , nebo jakýkoli název, který jste si vybrali pro váš fond.
    1. Vyberte **OK**.


## <a name="create-blob-containers"></a>Vytvoření kontejnerů objektů blob

Zde vytvoříte kontejnery objektů blob, které budou ukládat vstupní a výstupní soubory pro dávkovou úlohu Rozpoznávání OCR.

1. Přihlaste se k Průzkumníku úložiště pomocí přihlašovacích údajů Azure.
1. Pomocí účtu úložiště propojeného s vaším účtem Batch vytvořte dva kontejnery objektů blob (jeden pro vstupní soubory, jeden pro výstupní soubory) podle kroků v [části Vytvoření kontejneru objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

V tomto příkladu vstupní `input` kontejner je pojmenován a je-li všechny dokumenty bez OCR zpočátku odeslány ke zpracování. Výstupní kontejner je `output` pojmenován a je místo, kde dávková úloha zapisuje zpracované dokumenty s rozpoznáváním OCR.  
    * V tomto příkladu budeme volat `input`náš vstupní kontejner `output`a náš výstupní kontejner .  
    * Vstupní kontejner je místo, kde jsou zpočátku odeslány všechny dokumenty bez OCR.  
    * Výstupní kontejner je místo, kde dávková úloha zapisuje dokumenty s rozpoznáváním OCR.  

Vytvořte sdílený přístupový podpis pro výstupní kontejner v Průzkumníku úložiště. Proveďte tak, že kliknete pravým tlačítkem myši na výstupní kontejner a **vyberete možnost Získat sdílený přístupový podpis...**. V části **Oprávnění**zaškrtněte **políčko Zapsat**. Nejsou nutná žádná další oprávnění.  

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

V této části vytvoříte funkci Azure, která spustí dávkovou úlohu Rozpoznávání OCR při každém nahrání souboru do vstupního kontejneru.

1. Postupujte podle kroků v [části Vytvoření funkce aktivované úložištěm objektů blob Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) k vytvoření funkce.
    1. Po zobrazení výzvy k zadání účtu úložiště použijte stejný účet úložiště, který jste propojili se svým účtem Batch.
    1. Pro **zásobník za běhu**zvolte .NET. Napíšeme naši funkci v C# využít Batch .NET SDK.
1. Po vytvoření funkce aktivované objektem [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) blob, použijte a [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) z GitHub u funkce.
    * `run.csx`Je spuštěna při přidání nového objektu blob do vstupního kontejneru objektů blob.
    * `function.proj`zobrazí externí knihovny v kódu funkce, například sada Batch .NET SDK.
1. Změňte zástupné hodnoty proměnných `Run()` ve funkci `run.csx` souboru tak, aby odrážely vaše pověření batch a úložiště. Přihlašovací údaje k účtu Batch a úložiště najdete na webu Azure Portal v části **Klíče** ve vašem účtu Batch.
    * Načtěte přihlašovací údaje účtu Batch a úložiště na webu Azure Portal v části **Klíče** v účtu Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Spuštění funkce a načtení výsledků

Nahrajte některé nebo všechny naskenované [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) soubory z adresáře na GitHubu do vstupního kontejneru. Sledování Dávkového průzkumníka k potvrzení, že se `ocr-pool` pro každý soubor přidá úloha. Po několika sekundách je soubor s použitým rozpoznáváním OCR přidán do výstupního kontejneru. Soubor je pak viditelný a retrievable na Průzkumníka úložiště.

Kromě toho můžete sledovat soubor protokolů v dolní části okna webového editoru Azure Functions, kde uvidíte zprávy, jako je tento pro každý soubor, který nahrajete do vstupního kontejneru:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Chcete-li stáhnout výstupní soubory z Průzkumníka úložiště do místního počítače, nejprve vyberte požadované soubory a pak vyberte **stáhnout** na horním pásu karet. 

> [!TIP]
> Stažené soubory lze prohledávat, pokud jsou otevřeny ve čtečce PDF.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Vytvoření fondů a úloh pomocí Průzkumníka dávek
> * Vytvoření kontejnerů objektů blob a sdíleného přístupového podpisu (SAS) pomocí Průzkumníka úložiště
> * Vytvoření funkce Azure spouštěné objektem blob
> * Nahrání vstupních souborů do služby Storage
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

* Další příklady použití rozhraní .NET API k plánování a zpracování dávkových úloh najdete [v ukázkách na GitHubu](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Další aktivační události Azure Functions, které můžete použít ke spuštění dávkových úloh, najdete [v dokumentaci k funkcím Azure](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
