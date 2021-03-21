---
title: Kurz – aktivace dávkové úlohy pomocí Azure Functions
description: Kurz – použití rozpoznávání OCR u naskenovaných dokumentů při jejich přidání do objektu BLOB úložiště
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b441b4c4fcbeb089cef24c3a84fa33021e7840de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106378"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Kurz: Aktivace dávkové úlohy pomocí Azure Functions

V tomto kurzu se dozvíte, jak aktivovat dávkovou úlohu pomocí [Azure Functions](../azure-functions/functions-overview.md). Provedeme si příklad, ve kterém dokumenty přidané do kontejneru objektů blob Azure Storage mají pro ně použit optické rozpoznávání znaků (OCR) prostřednictvím Azure Batch. Abychom zjednodušili zpracování optického rozpoznávání znaků, nakonfigurujeme funkci Azure, která spustí úlohu dávkového optického rozpoznávání při každém přidání souboru do kontejneru objektů BLOB. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití Batch Explorer k vytváření fondů a úloh
> * Použití Průzkumník služby Storage k vytvoření kontejnerů objektů BLOB a sdíleného přístupového podpisu (SAS)
> * Vytvoření funkce Azure Function aktivované objektem BLOB
> * Nahrání vstupních souborů do služby Storage
> * Monitorování provádění úkolů
> * Načtení výstupních souborů

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Azure Batch a propojený účet Azure Storage. Další informace o tom, jak vytvořit a propojit účty, najdete v tématu [Vytvoření účtu Batch](quick-create-portal.md#create-a-batch-account) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Vytvoření fondu Batch a úlohy Batch pomocí Batch Explorer

V této části použijete Batch Explorer k vytvoření fondu Batch a úlohy Batch, která spustí úlohy optického rozpoznávání. 

### <a name="create-a-pool"></a>Vytvoření fondu

1. Přihlaste se k Batch Explorer pomocí svých přihlašovacích údajů Azure.
1. Vytvořte fond tak, že na levé straně vyberete **fondy** a pak tlačítko **Přidat** nad formulář pro hledání. 
    1. Vyberte ID a zobrazované jméno. `ocr-pool`V tomto příkladu budeme používat.
    1. Nastavte typ škálování na **pevnou velikost** a nastavte počet vyhrazených uzlů na 3.
    1. Jako operační systém vyberte **Ubuntu 18,04-LTS** .
    1. Vyberte `Standard_f2s_v2` Velikost virtuálního počítače.
    1. Povolte spouštěcí úkol a přidejte příkaz `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"` . Ujistěte se, že jste nastavili identitu uživatele jako **výchozího uživatele úlohy (správce)**, což umožňuje, aby úlohy pro začátek zahrnovaly příkazy `sudo` .
    1. Vyberte **OK**.
### <a name="create-a-job"></a>Vytvoření úlohy

1. Vytvořte úlohu ve fondu tak, že na levém panelu vyberete **úlohy** a pak na tlačítko **Přidat** nad formulářem pro hledání. 
    1. Vyberte ID a zobrazované jméno. `ocr-job`V tomto příkladu budeme používat.
    1. Nastavte fond na `ocr-pool` nebo libovolný název, který jste zvolili pro fond.
    1. Vyberte **OK**.


## <a name="create-blob-containers"></a>Vytváření kontejnerů objektů BLOB

Tady vytvoříte kontejnery objektů blob, které budou ukládat vstupní a výstupní soubory pro dávkovou úlohu OCR.

1. Přihlaste se k Průzkumník služby Storage pomocí svých přihlašovacích údajů Azure.
1. Pomocí účtu úložiště propojeného s účtem Batch vytvořte dva kontejnery objektů BLOB (jeden pro vstupní soubory, jeden pro výstupní soubory) podle kroků v části [vytvoření kontejneru objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).

V tomto příkladu je vstupní kontejner pojmenovaný `input` a je tam, kde se zpočátku nahrály všechny dokumenty bez optického rozpoznávání znaků pro zpracování. Kontejner výstupu je pojmenován `output` a je tam, kde dávková úloha zapisuje zpracované dokumenty s rozpoznáváním OCR.  
    * V tomto příkladu budeme volat náš vstupní kontejner `input` a náš výstupní kontejner `output` .  
    * Vstupní kontejner je místo, kde jsou původně nahrány všechny dokumenty bez optického rozpoznávání znaků.  
    * Výstupní kontejner je místo, kde dávková úloha zapisuje dokumenty s rozpoznáváním OCR.  

Vytvořte sdílený přístupový podpis pro svůj výstupní kontejner v Průzkumník služby Storage. Provedete to tak, že kliknete pravým tlačítkem na výstupní kontejner a vyberete **získat sdílený přístupový podpis...**. V části **oprávnění** zaškrtněte **zapisovat**. Žádná další oprávnění nejsou nezbytná.  

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure Function

V této části vytvoříte funkci Azure, která aktivuje dávkovou úlohu OCR pokaždé, když se do vstupního kontejneru nahraje soubor.

1. Pomocí postupu v části [Vytvoření funkce aktivované službou Azure Blob Storage](../azure-functions/functions-create-storage-blob-triggered-function.md) vytvořte funkci.
    1. Po zobrazení výzvy k zadání účtu úložiště použijte stejný účet úložiště, který jste propojili s vaším účtem Batch.
    1. V případě **zásobníků modulu runtime** vyberte .NET. Napíšeme naši funkci v jazyce C#, abychom využili sadu Batch .NET SDK.
1. Jakmile je funkce aktivovaná objektem BLOB vytvořená, použijte [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) ve funkci a z GitHubu.
    * `run.csx` se spustí při přidání nového objektu blob do vstupního kontejneru objektů BLOB.
    * `function.proj` obsahuje seznam externích knihoven v kódu funkce, například sadu Batch .NET SDK.
1. Změňte zástupné hodnoty proměnných ve `Run()` funkci `run.csx` souboru tak, aby odrážely přihlašovací údaje pro dávku a úložiště. Přihlašovací údaje k účtu Batch a účtu úložiště najdete v Azure Portal v části **klíče** účtu Batch.
    * Přihlašovací údaje k účtu Batch a účtu úložiště načtěte v Azure Portal v části **klíče** účtu Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Aktivovat funkci a načíst výsledky

Nahrajte všechny naskenované soubory z [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) adresáře na GitHubu do vstupního kontejneru. Batch Explorer monitorování a ověřte, že se do `ocr-pool` každého souboru přidal úkol. Po několika sekundách se soubor s použitým OCR přidá do výstupního kontejneru. Soubor je pak viditelný a získat na Průzkumník služby Storage.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Poplatky se účtují za fond, ve kterém jsou spuštěné uzly, i když nejsou naplánované žádné úlohy. Až fond nebudete potřebovat, odstraňte ho. V zobrazení účtu vyberte **Fondy** a název fondu. Vyberte **Odstranit**. Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech. Výstupní soubory ale zůstanou v účtu úložiště. Pokud už je nepotřebujete, můžete také odstranit účet Batch a účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití Batch Explorer k vytváření fondů a úloh
> * Použití Průzkumník služby Storage k vytvoření kontejnerů objektů BLOB a sdíleného přístupového podpisu (SAS)
> * Vytvoření funkce Azure Function aktivované objektem BLOB
> * Nahrání vstupních souborů do služby Storage
> * Monitorování provádění úkolů
> * Načtení výstupních souborů


Pokračujte tím, že prozkoumáte aplikace pro vykreslování dostupné prostřednictvím Batch Explorer v části **Galerie** . Pro každou aplikaci je k dispozici několik šablon a jejich počet se časem bude rozšiřovat. Pro Blender například existují šablony, které rozdělí obrázek na čtverce, aby bylo možné části obrázku vykreslit paralelně.

Další příklady použití rozhraní .NET API k plánování a zpracování úloh služby Batch najdete v ukázkách na GitHubu.

> [!div class="nextstepaction"]
> [Ukázky pro službu Batch v jazyce C#](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
