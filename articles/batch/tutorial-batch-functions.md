---
title: Aktivace dávkové úlohy pomocí Azure Functions
description: Kurz – použití rozpoznávání OCR u naskenovaných dokumentů při jejich přidání do objektu BLOB úložiště
services: batch
author: ju-shim
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 6e3cdb6c7e2774eeb29df6986088f822cbb894cf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029222"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Kurz: Aktivace dávkové úlohy pomocí Azure Functions

V tomto kurzu se dozvíte, jak aktivovat dávkovou úlohu pomocí Azure Functions. Provedeme si příklad, ve kterém dokumenty přidané do kontejneru objektů blob Azure Storage mají pro ně použit optické rozpoznávání znaků (OCR) prostřednictvím Azure Batch. Abychom zjednodušili zpracování optického rozpoznávání znaků, nakonfigurujeme funkci Azure, která spustí úlohu dávkového optického rozpoznávání při každém přidání souboru do kontejneru objektů BLOB.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Azure Batch a propojený účet Azure Storage. Další informace o tom, jak vytvořit a propojit účty, najdete v tématu [Vytvoření účtu Batch](quick-create-portal.md#create-a-batch-account) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Vytvoření fondu Batch a úlohy Batch pomocí Batch Explorer

V této části použijete Batch Explorer k vytvoření fondu Batch a úlohy Batch, která spustí úlohy optického rozpoznávání. 

### <a name="create-a-pool"></a>Vytvoření fondu

1. Přihlaste se k Batch Explorer pomocí svých přihlašovacích údajů Azure.
1. Vytvořte fond tak, že na levé straně vyberete **fondy** a pak tlačítko **Přidat** nad formulář pro hledání. 
    1. Vyberte ID a zobrazované jméno. Pro tento příklad použijeme `ocr-pool`.
    1. Nastavte typ škálování na **pevnou velikost**a nastavte počet vyhrazených uzlů na 3.
    1. Jako operační systém vyberte **Ubuntu 18,04-LTS** .
    1. Jako velikost virtuálního počítače vyberte `Standard_f2s_v2`.
    1. Povolte spouštěcí úkol a přidejte příkaz `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Ujistěte se, že jste nastavili identitu uživatele jako **výchozího uživatele úlohy (správce)** , což umožňuje, aby úlohy zahájení zahrnovaly příkazy `sudo`.
    1. Vyberte **OK**.
### <a name="create-a-job"></a>Vytvoření úlohy

1. Vytvořte úlohu ve fondu tak, že na levém panelu vyberete **úlohy** a pak na tlačítko **Přidat** nad formulářem pro hledání. 
    1. Vyberte ID a zobrazované jméno. Pro tento příklad použijeme `ocr-job`.
    1. Nastavte fond na `ocr-pool`nebo libovolný název, který jste zvolili pro fond.
    1. Vyberte **OK**.


## <a name="create-blob-containers"></a>Vytváření kontejnerů objektů BLOB

Tady vytvoříte kontejnery objektů blob, které budou ukládat vstupní a výstupní soubory pro dávkovou úlohu OCR.

1. Přihlaste se k Průzkumník služby Storage pomocí svých přihlašovacích údajů Azure.
1. Pomocí účtu úložiště propojeného s účtem Batch vytvořte dva kontejnery objektů BLOB (jeden pro vstupní soubory, jeden pro výstupní soubory) podle kroků v části [vytvoření kontejneru objektů BLOB](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

V tomto příkladu je vstupní kontejner pojmenovaný `input` a je tam, kde se zpočátku nahrály všechny dokumenty bez optického rozpoznávání znaků pro zpracování. Kontejner výstupu se nazývá `output` a je tam, kde dávková úloha zapisuje zpracované dokumenty s rozpoznáváním OCR.  
    * V tomto příkladu budeme volat náš vstupní kontejner `input`a náš výstupní kontejner `output`.  
    * Vstupní kontejner je místo, kde jsou původně nahrány všechny dokumenty bez optického rozpoznávání znaků.  
    * Výstupní kontejner je místo, kde dávková úloha zapisuje dokumenty s rozpoznáváním OCR.  

Vytvořte sdílený přístupový podpis pro svůj výstupní kontejner v Průzkumník služby Storage. Provedete to tak, že kliknete pravým tlačítkem na výstupní kontejner a vyberete **získat sdílený přístupový podpis...** . V části **oprávnění**zaškrtněte **zapisovat**. Žádná další oprávnění nejsou nezbytná.  

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

V této části vytvoříte funkci Azure, která aktivuje dávkovou úlohu OCR pokaždé, když se do vstupního kontejneru nahraje soubor.

1. Pomocí postupu v části [Vytvoření funkce aktivované službou Azure Blob Storage](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) vytvořte funkci.
    1. Po zobrazení výzvy k zadání účtu úložiště použijte stejný účet úložiště, který jste propojili s vaším účtem Batch.
    1. V případě **zásobníků modulu runtime**vyberte .NET. Napíšeme naši funkci v C# pro využití sady Batch .NET SDK.
1. Po vytvoření funkce aktivované objektem BLOB použijte [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) a [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) z GitHubu ve funkci.
    * `run.csx` se spustí při přidání nového objektu blob do vstupního kontejneru objektů BLOB.
    * `function.proj` uvádí externí knihovny v kódu funkce, například Batch .NET SDK.
1. Změňte hodnoty zástupných symbolů proměnných ve funkci `Run()` souboru `run.csx` tak, aby odrážely přihlašovací údaje pro dávku a úložiště. Přihlašovací údaje k účtu Batch a účtu úložiště najdete v Azure Portal v části **klíče** účtu Batch.
    * Přihlašovací údaje k účtu Batch a účtu úložiště načtěte v Azure Portal v části **klíče** účtu Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Aktivovat funkci a načíst výsledky

Nahrajte všechny naskenované soubory z [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) adresáře na GitHubu do vstupního kontejneru. Batch Explorer monitorování a potvrďte, že se úkol přidá do `ocr-pool` pro každý soubor. Po několika sekundách se soubor s použitým OCR přidá do výstupního kontejneru. Soubor je pak viditelný a získat na Průzkumník služby Storage.

Kromě toho můžete sledovat soubor protokolů v dolní části okna Azure Functions webového editoru, kde se zobrazí zprávy podobné tomuto souboru pro každý soubor, který nahrajete do vstupního kontejneru:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Chcete-li stáhnout výstupní soubory z Průzkumník služby Storage do místního počítače, vyberte nejprve požadované soubory a pak vyberte **stažení** na horním pásu karet. 

> [!TIP]
> Stažené soubory jsou v případě, že jsou otevřeny v čtečce PDF, prohledávatelné.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Použití Batch Explorer k vytváření fondů a úloh
> * Použití Průzkumník služby Storage k vytvoření kontejnerů objektů BLOB a sdíleného přístupového podpisu (SAS)
> * Vytvoření funkce Azure Function aktivované objektem BLOB
> * Nahrání vstupních souborů do služby Storage
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

* Další příklady použití rozhraní .NET API k plánování a zpracování úloh služby Batch najdete v [ukázkách na GitHubu](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Další Azure Functions triggery, které můžete použít ke spouštění dávkových úloh, najdete v [dokumentaci k Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
