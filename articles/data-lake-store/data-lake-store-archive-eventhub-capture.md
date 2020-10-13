---
title: Zaznamenání dat z Event Hubs do Azure Data Lake Storage Gen1
description: Naučte se používat Azure Data Lake Storage Gen1 k zachycení dat přijatých službou Azure Event Hubs. Začněte tím, že ověříte požadavky.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578236"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Použití Azure Data Lake Storage Gen1 k zaznamenání dat z Event Hubs

Naučte se používat Azure Data Lake Storage Gen1 k zachycení dat přijatých službou Azure Event Hubs.

## <a name="prerequisites"></a>Požadavky

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

    ![Snímek obrazovky Průzkumníka dat s kořenem účtu a možností přístupu s názvem.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Přiřadit oprávnění pro kořen Data Lake Storage Gen1")

    b. V části **přístup**klikněte na **Přidat**, klikněte na **Vybrat uživatele nebo skupinu**a pak vyhledejte `Microsoft.EventHubs` . 

    ![Snímek obrazovky se stránkou pro přístup s možností přidat, vyberte možnost uživatel nebo skupina a možnost Microsoft Eventhubs se vyvolala.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Přiřadit oprávnění pro kořen Data Lake Storage Gen1")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**klikněte na **vybrat oprávnění**. Nastavte **oprávnění** na **spouštění**. Nastavte **Přidat do** **této složky a všech podřízených objektů**. Nastavení **Přidat jako** pro **položku oprávnění k přístupu a výchozí položku oprávnění**.

    > [!IMPORTANT]
    > Když vytváříte novou hierarchii složek pro zaznamenávání dat přijatých službou Azure Event Hubs, jedná se o snadný způsob, jak zajistit přístup do cílové složky.  Nicméně přidávání oprávnění do všech podřízených složek nejvyšší úrovně s mnoha podřízenými soubory a složkami může trvat dlouhou dobu.  Pokud kořenová složka obsahuje velký počet souborů a složek, může být rychlejší přidat do **Execute** `Microsoft.EventHubs` každé složky v cestě k konečné cílové složce oprávnění ke spuštění pro jednotlivé složky. 

    ![Snímek obrazovky s oddílem přiřazení oprávnění s názvem možnost vybrat oprávnění V oddílu vybrat oprávnění se vedle něho zobrazí možnost Execute, přidat k možnosti a přidat jako možnost.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Přiřadit oprávnění pro kořen Data Lake Storage Gen1")

    Klikněte na **OK**.

1. Přiřaďte oprávnění pro složku pod účtem Data Lake Storage Gen1, kde chcete zachytit data.

    a. Klikněte na **Průzkumník dat**, vyberte složku v účtu Data Lake Storage Gen1 a pak klikněte na **přístup**.

    ![Snímek obrazovky s Průzkumníkem dat se složkou v účtu a možností přístupu s názvem.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Přiřadit oprávnění pro složku Data Lake Storage Gen1")

    b. V části **přístup**klikněte na **Přidat**, klikněte na **Vybrat uživatele nebo skupinu**a pak vyhledejte `Microsoft.EventHubs` . 

    ![Snímek obrazovky s přístupem k Průzkumníku dat pomocí možnosti Přidat vyberte možnost uživatel nebo skupina a možnost Microsoft Eventhubs.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Přiřadit oprávnění pro složku Data Lake Storage Gen1")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**klikněte na **vybrat oprávnění**. Nastavte **oprávnění** ke **čtení, zápisu** a **spouštění**. Nastavte **Přidat do** **této složky a všech podřízených objektů**. Nakonec nastavte položku **Přidat jako** do **položky oprávnění k přístupu a výchozí položku oprávnění**.

    ![Snímek obrazovky s oddílem přiřazení oprávnění s názvem možnost vybrat oprávnění V oddílu vybrat oprávnění se vedle něho zobrazí možnosti čtení, zápis a spuštění, možnost přidat do a možnost přidat jako.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Přiřadit oprávnění pro složku Data Lake Storage Gen1")
    
    Klikněte na **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurace Event Hubs k zaznamenání dat do Data Lake Storage Gen1

V této části vytvoříte centrum událostí v oboru názvů Event Hubs. Také nakonfigurujete centrum událostí, aby zachytával data na účet Azure Data Lake Storage Gen1. V této části se předpokládá, že jste již vytvořili obor názvů Event Hubs.

1. V podokně **přehledu** oboru názvů Event Hubs klikněte na **+ centrum událostí**.

    ![Snímek obrazovky s podoknem přehled s možností centra událostí s názvem.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Vytvoření centra událostí")

1. Zadejte následující hodnoty pro konfiguraci Event Hubs k zaznamenání dat do Data Lake Storage Gen1.

    ![Snímek obrazovky dialogového okna vytvořit centrum událostí s textovým polem název, možností zachycení, možností poskytovatele zachycení, možností výběru Data Lake Store a možností cesty Data Lake.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Vytvoření centra událostí")

    a. Zadejte název centra událostí.
    
    b. Pro tento kurz nastavte výchozí hodnoty **počet oddílů** a **uchování zpráv** .
    
    c. Nastavte **Capture** na **zapnuto**. Nastavte **časový interval** (jak často se má zachytit) a **velikost okna** (velikost dat k zachycení). 
    
    d. V poli **zprostředkovatel zachytávání**vyberte **Azure Data Lake Store** a potom vyberte účet Data Lake Storage Gen1, který jste vytvořili dříve. Do pole **cesta Data Lake**zadejte název složky, kterou jste vytvořili v účtu Data Lake Storage Gen1. Stačí zadat relativní cestu ke složce.

    e. Ponechte **vzorové soubory ve formátu zachycení** na výchozí hodnotu. Tato možnost určuje strukturu složek, která je vytvořena v rámci složky Capture.

    f. Klikněte na **Vytvořit**.

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
