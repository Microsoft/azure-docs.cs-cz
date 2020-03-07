---
title: Zaznamenání dat z Event Hubs do Azure Data Lake Storage Gen1 | Microsoft Docs
description: Použití Azure Data Lake Storage Gen1 k zaznamenání dat z Event Hubs
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397208"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Použití Azure Data Lake Storage Gen1 k zaznamenání dat z Event Hubs

Naučte se používat Azure Data Lake Storage Gen1 k zachycení dat přijatých službou Azure Event Hubs.

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Event Hubs obor názvů**. Pokyny najdete v tématu [Vytvoření oboru názvů Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Ujistěte se, že účet Data Lake Storage Gen1 a obor názvů Event Hubs jsou ve stejném předplatném Azure.


## <a name="assign-permissions-to-event-hubs"></a>Přiřazení oprávnění k Event Hubs

V této části vytvoříte složku v rámci účtu, ve kterém chcete data zachytit z Event Hubs. Také přiřadíte oprávnění Event Hubs, aby bylo možné zapisovat data do účtu Data Lake Storage Gen1. 

1. Otevřete Data Lake Storage Gen1 účet, ze kterého chcete data zachytit Event Hubs a potom klikněte na **Průzkumník dat**.

    ![Průzkumník dat Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Průzkumník dat Data Lake Storage Gen1")

1.  Klikněte na **Nová složka** a pak zadejte název složky, do které chcete zachytit data.

    ![Vytvoří novou složku v Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Vytvoří novou složku v Data Lake Storage Gen1")

1. Přiřaďte oprávnění v kořenu Data Lake Storage Gen1. 

    a. Klikněte na **Průzkumník dat**, vyberte kořen účtu Data Lake Storage Gen1 a pak klikněte na **přístup**.

    ![Přiřadit oprávnění pro kořen Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Přiřadit oprávnění pro kořen Data Lake Storage Gen1")

    b. V části **přístup**klikněte na **Přidat**, klikněte na **Vybrat uživatele nebo skupinu**a vyhledejte `Microsoft.EventHubs`. 

    ![Přiřadit oprávnění pro kořen Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Přiřadit oprávnění pro kořen Data Lake Storage Gen1")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**klikněte na **vybrat oprávnění**. Nastavte **oprávnění** na **spouštění**. Nastavte **Přidat do** **této složky a všech podřízených objektů**. Nastavení **Přidat jako** pro **položku oprávnění k přístupu a výchozí položku oprávnění**.

    > [!IMPORTANT]
    > Když vytváříte novou hierarchii složek pro zaznamenávání dat přijatých službou Azure Event Hubs, jedná se o snadný způsob, jak zajistit přístup do cílové složky.  Nicméně přidávání oprávnění do všech podřízených složek nejvyšší úrovně s mnoha podřízenými soubory a složkami může trvat dlouhou dobu.  Pokud kořenová složka obsahuje velký počet souborů a složek, může být rychlejší přidat do každé složky v cestě k konečné cílové složce oprávnění **Execute** `Microsoft.EventHubs` jednotlivě. 

    ![Přiřadit oprávnění pro kořen Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Přiřadit oprávnění pro kořen Data Lake Storage Gen1")

    Klikněte na tlačítko **OK**.

1. Přiřaďte oprávnění pro složku pod účtem Data Lake Storage Gen1, kde chcete zachytit data.

    a. Klikněte na **Průzkumník dat**, vyberte složku v účtu Data Lake Storage Gen1 a pak klikněte na **přístup**.

    ![Přiřadit oprávnění pro složku Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Přiřadit oprávnění pro složku Data Lake Storage Gen1")

    b. V části **přístup**klikněte na **Přidat**, klikněte na **Vybrat uživatele nebo skupinu**a vyhledejte `Microsoft.EventHubs`. 

    ![Přiřadit oprávnění pro složku Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Přiřadit oprávnění pro složku Data Lake Storage Gen1")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**klikněte na **vybrat oprávnění**. Nastavte **oprávnění** ke **čtení, zápisu** a **spouštění**. Nastavte **Přidat do** **této složky a všech podřízených objektů**. Nakonec nastavte položku **Přidat jako** do **položky oprávnění k přístupu a výchozí položku oprávnění**.

    ![Přiřadit oprávnění pro složku Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Přiřadit oprávnění pro složku Data Lake Storage Gen1")
    
    Klikněte na tlačítko **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurace Event Hubs k zaznamenání dat do Data Lake Storage Gen1

V této části vytvoříte centrum událostí v oboru názvů Event Hubs. Také nakonfigurujete centrum událostí, aby zachytával data na účet Azure Data Lake Storage Gen1. V této části se předpokládá, že jste již vytvořili obor názvů Event Hubs.

1. V podokně **přehledu** oboru názvů Event Hubs klikněte na **+ centrum událostí**.

    ![Vytvořit centrum událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Vytvoření centra událostí")

1. Zadejte následující hodnoty pro konfiguraci Event Hubs k zaznamenání dat do Data Lake Storage Gen1.

    ![Vytvořit centrum událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Vytvoření centra událostí")

    a. Zadejte název centra událostí.
    
    b. Pro tento kurz nastavte výchozí hodnoty **počet oddílů** a **uchování zpráv** .
    
    c. Nastavte **Capture** na **zapnuto**. Nastavte **časový interval** (jak často se má zachytit) a **velikost okna** (velikost dat k zachycení). 
    
    d. V poli **zprostředkovatel zachytávání**vyberte **Azure Data Lake Store** a potom vyberte účet Data Lake Storage Gen1, který jste vytvořili dříve. Do pole **cesta Data Lake**zadejte název složky, kterou jste vytvořili v účtu Data Lake Storage Gen1. Stačí zadat relativní cestu ke složce.

    e. Ponechte **vzorové soubory ve formátu zachycení** na výchozí hodnotu. Tato možnost určuje strukturu složek, která je vytvořena v rámci složky Capture.

    f. Klikněte na možnost **Vytvořit**.

## <a name="test-the-setup"></a>Otestování instalačního programu

Řešení teď můžete testovat odesláním dat do centra událostí Azure. Postupujte podle pokynů v tématu [odeslání událostí do Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Jakmile začnete odesílat data, zobrazí se data odrážející se v Data Lake Storage Gen1 pomocí zadané struktury složek. Například se zobrazí struktura složky, jak je znázorněno na následujícím snímku obrazovky, ve vašem účtu Data Lake Storage Gen1.

![Ukázková data EventHub v Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Ukázková data EventHub v Data Lake Storage Gen1")

> [!NOTE]
> I když nepřijdete o zprávy Event Hubs, Event Hubs zapisuje prázdné soubory s pouze hlavičkami do účtu Data Lake Storage Gen1. Soubory jsou zapsány ve stejném časovém intervalu, který jste zadali při vytváření Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analyzovat data v Data Lake Storage Gen1

Jakmile jsou data v Data Lake Storage Gen1, můžete spouštět analytické úlohy pro zpracování a zpracovávejteí dat. Postup k tomu, jak to udělat pomocí Azure Data Lake Analytics, najdete v tématu [USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) .
  

## <a name="see-also"></a>Viz také
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
