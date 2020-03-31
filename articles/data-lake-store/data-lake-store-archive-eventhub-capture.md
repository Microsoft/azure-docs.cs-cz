---
title: Zachyťte data z centra událostí do Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Použití Azure Data Lake Storage Gen1 k zachycení dat z event hubů
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265659"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Použití Azure Data Lake Storage Gen1 k zachycení dat z event hubů

Zjistěte, jak pomocí Azure Data Lake Storage Storage Gen1 používat k sběru dat přijatých v Azure Event Hubs.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete [v tématu Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Obor názvů Event Hubs**. Pokyny naleznete [v tématu Vytvoření oboru názvů Centra událostí](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Ujistěte se, že účet Data Lake Storage Gen1 a obor názvů Event Hubs jsou ve stejném předplatném Azure.


## <a name="assign-permissions-to-event-hubs"></a>Přiřazení oprávnění k centru událostí

V této části vytvoříte složku v rámci účtu, kde chcete zachytit data z centra událostí. Také přiřazujete oprávnění k event hubům, aby mohl zapisovat data do účtu Data Lake Storage Gen1. 

1. Otevřete účet Data Lake Storage Gen1, kde chcete zachytit data z centra událostí, a klikněte na **Průzkumník a dat .**

    ![Průzkumník dat Gen1 úložiště datového jezera](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Průzkumník dat Gen1 úložiště datového jezera")

1.  Klikněte na **Nová složka** a zadejte název složky, do které chcete data zachytit.

    ![Vytvoření nové složky v zařízení Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Vytvoření nové složky v zařízení Data Lake Storage Gen1")

1. Přiřaďte oprávnění v kořenovém adresáři Data Lake Storage Gen1. 

    a. Klepněte na **Průzkumník dat**, vyberte kořenový adresář účtu Data Lake Storage Gen1 a klepněte na tlačítko **Access**.

    ![Přiřazení oprávnění pro kořenový adresář Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Přiřazení oprávnění pro kořenový adresář Data Lake Storage Gen1")

    b. V **části Access**klepněte na tlačítko **Přidat**, klepněte na tlačítko Vybrat uživatele **nebo skupinu**a vyhledejte položku `Microsoft.EventHubs`. 

    ![Přiřazení oprávnění pro kořenový adresář Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Přiřazení oprávnění pro kořenový adresář Data Lake Storage Gen1")
    
    Klepněte na **tlačítko Vybrat**.

    c. V části **Přiřadit oprávnění**klepněte na **položku Vybrat oprávnění**. Nastavte **oprávnění** ke **spuštění**. Nastavit **přidat do** této **složky a všechny podřízené položky**. Nastavte **možnost Přidat jako** **položku přístupových oprávnění a výchozí položku oprávnění**.

    > [!IMPORTANT]
    > Při vytváření nové hierarchie složek pro sběr dat přijatých službou Azure Event Hubs je to snadný způsob, jak zajistit přístup k cílové složce.  Přidání oprávnění pro všechny podřízené složky nejvyšší úrovně s mnoha podřízenými soubory a složkami však může trvat dlouhou dobu.  Pokud kořenová složka obsahuje velký počet souborů a složek, může `Microsoft.EventHubs` být rychlejší přidat oprávnění ke **spuštění** jednotlivě do každé složky v cestě k cílové složce. 

    ![Přiřazení oprávnění pro kořenový adresář Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Přiřazení oprávnění pro kořenový adresář Data Lake Storage Gen1")

    Klikněte na tlačítko **OK**.

1. Přiřaďte oprávnění pro složku v účtu Data Lake Storage Gen1, kde chcete zachytit data.

    a. Klepněte na **Průzkumník dat**, vyberte složku v účtu Data Lake Storage Gen1 a klepněte na tlačítko **Access**.

    ![Přiřazení oprávnění pro složku Gen1 úložiště datového jezera](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Přiřazení oprávnění pro složku Gen1 úložiště datového jezera")

    b. V **části Access**klepněte na tlačítko **Přidat**, klepněte na tlačítko Vybrat uživatele **nebo skupinu**a vyhledejte položku `Microsoft.EventHubs`. 

    ![Přiřazení oprávnění pro složku Gen1 úložiště datového jezera](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Přiřazení oprávnění pro složku Gen1 úložiště datového jezera")
    
    Klepněte na **tlačítko Vybrat**.

    c. V části **Přiřadit oprávnění**klepněte na **položku Vybrat oprávnění**. Nastavte **oprávnění** ke **čtení, zápisu** a **spouštění**. Nastavit **přidat do** této **složky a všechny podřízené položky**. Nakonec nastavte **možnost Přidat jako** **položku přístupového oprávnění a výchozí položku oprávnění**.

    ![Přiřazení oprávnění pro složku Gen1 úložiště datového jezera](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Přiřazení oprávnění pro složku Gen1 úložiště datového jezera")
    
    Klikněte na tlačítko **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurace rozbočovačů událostí pro sběr dat do úložiště datového jezera Gen1

V této části vytvoříte centrum událostí v oboru názvů Event Hubs. Centrum událostí také nakonfigurujete tak, aby zaznamenávala data do účtu Azure Data Lake Storage Gen1. Tato část předpokládá, že jste již vytvořili obor názvů Event Hubs.

1. V podokně **Přehled** oboru názvů Event Hubs klikněte na **+ Event Hub**.

    ![Vytvoření centra událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Vytvoření centra událostí")

1. Zadejte následující hodnoty pro konfiguraci centra událostí pro sběr dat do data Lake Storage Gen1.

    ![Vytvoření centra událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Vytvoření centra událostí")

    a. Zadejte název centra událostí.
    
    b. V tomto kurzu nastavte **počet oddílů** a **uchovávání zpráv** na výchozí hodnoty.
    
    c. Nastavit **zachycení** **na zapnuto**. Nastavte **časové okno** (jak často se má zachytit) a **velikost ní (velikost** dat pro zachycení). 
    
    d. V **části Zprostředkovatel pro digitalizaci**vyberte Azure Data Lake **Store** a pak vyberte účet Data Lake Storage Gen1, který jste vytvořili dříve. V **části Cesta k datovému jezeru**zadejte název složky, kterou jste vytvořili, do účtu Data Lake Storage Gen1. Stačí zadat relativní cestu ke složce.

    e. Formáty **názvů souborů pro sběr vzorků ponechejte** na výchozí hodnotě. Tato možnost určuje strukturu složek, která je vytvořena ve složce pro digitalizaci.

    f. Klikněte na **Vytvořit**.

## <a name="test-the-setup"></a>Otestujte nastavení

Teď můžete otestovat řešení odesláním dat do Centra událostí Azure. Postupujte podle pokynů na [webu Odeslat události do centra Událostí Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Jakmile začnete odesílat data, uvidíte data, která se projeví v data Lake Storage Gen1 pomocí struktury složek, kterou jste zadali. Například se zobrazí struktura složek, jak je znázorněno na následujícím snímku obrazovky, ve vašem účtu Data Lake Storage Gen1.

![Ukázková data EventHubu v úložišti datového jezera Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Ukázková data EventHubu v úložišti datového jezera Gen1")

> [!NOTE]
> I v případě, že nemáte zprávy přicházející do event hubů, Event Hubs zapisuje prázdné soubory pouze s hlavičkami do účtu Gen1 úložiště datového jezera. Soubory jsou zapsány ve stejném časovém intervalu, který jste zadali při vytváření centra událostí.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analýza dat v úložišti datových jezer Gen1

Jakmile jsou data v Data Lake Storage Gen1, můžete spustit analytické úlohy pro zpracování a zpracování dat. Podívejte se na [příklad USQL Avro O](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) tom, jak to udělat pomocí Azure Data Lake Analytics.
  

## <a name="see-also"></a>Viz také
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
