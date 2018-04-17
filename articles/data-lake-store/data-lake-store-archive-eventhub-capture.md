---
title: Zachycení dat ze služby Event Hubs do Azure Data Lake Store | Microsoft Docs
description: Použití Azure Data Lake Store k zaznamenání dat ze služby Event Hubs
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9f91acf8c26fdec0c8d128f598f218cff091c7aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Použití Azure Data Lake Store k zaznamenání dat ze služby Event Hubs

Naučte se používat Azure Data Lake Store k zaznamenání dat přijatých Azure Event Hubs.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Store**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Obor názvů Event Hubs**. Pokyny najdete v tématu [vytvoření oboru názvů Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Ujistěte se, že účet Data Lake Store a obor názvů služby Event Hubs jsou ve stejném předplatném Azure.


## <a name="assign-permissions-to-event-hubs"></a>Přiřazení oprávnění do centra událostí

V této části vytvoříte složku v rámci účtu, ve které chcete zaznamenat data ze služby Event Hubs. Můžete také přiřadit oprávnění do centra událostí tak, aby ho můžete zapsat data do účtu Data Lake Store. 

1. Otevřete účet Data Lake Store, místo zachycení dat ze služby Event Hubs a potom klikněte na **Průzkumníku dat**.

    ![Data Lake Store Průzkumníku dat](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Průzkumníku dat v Data Lake Store")

2.  Klikněte na tlačítko **novou složku** a pak zadejte název složky, kam chcete zaznamenat data.

    ![Vytvořte novou složku v Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "vytvořte novou složku v Data Lake Store")

3. Přiřazení oprávnění v kořenovém adresáři Data Lake Store. 

    a. Klikněte na tlačítko **Průzkumníku dat**, vyberte kořenového účtu Data Lake Store a pak klikněte na tlačítko **přístup**.

    ![Přiřazení oprávnění pro Data Lake Store kořenové](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "přiřadit oprávnění pro kořenový adresář Data Lake Store")

    b. V části **přístup**, klikněte na tlačítko **přidat**, klikněte na tlačítko **vybrat uživatele nebo skupinu**a poté vyhledejte `Microsoft.EventHubs`. 

    ![Přiřazení oprávnění pro Data Lake Store kořenové](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "přiřadit oprávnění pro kořenový adresář Data Lake Store")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**, klikněte na tlačítko **vyberte oprávnění**. Nastavit **oprávnění** k **provést**. Nastavit **přidat do** k **tuto složku a všechny podřízené objekty**. Nastavit **přidat jako** k **položka oprávnění k přístupu a výchozí položku oprávnění**.

> [!IMPORTANT]
> Při vytváření nové hierarchii složek pro zaznamenání dat přijatých Azure Event Hubs, to je snadný způsob, jak zajistit přístup k cílové složce.  Přidání oprávnění pro všechny podřízené objekty složku nejvyšší úrovně s mnoha podřízené soubory a složky, může trvat dlouhou dobu.  Kořenová složka obsahuje velký počet souborů a složek, může být rychlejší přidat **Execute** oprávnění pro `Microsoft.EventHubs` jednotlivě na všechny složky v cestě ke složce konečného cíle. 

    ![Assign permissions for Data Lake Store root](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for Data Lake Store root")

    Click **OK**.

4. Přiřazení oprávnění pro dílčí složku složky účtu Data Lake Store, ve které chcete zaznamenat data.

    a. Klikněte na tlačítko **Průzkumníku dat**, vyberte složku, v účtu Data Lake Store a pak klikněte na tlačítko **přístup**.

    ![Přiřazení oprávnění pro Data Lake Store složku](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "přiřadit oprávnění pro složku Data Lake Store")

    b. V části **přístup**, klikněte na tlačítko **přidat**, klikněte na tlačítko **vybrat uživatele nebo skupinu**a poté vyhledejte `Microsoft.EventHubs`. 

    ![Přiřazení oprávnění pro Data Lake Store složku](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "přiřadit oprávnění pro složku Data Lake Store")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**, klikněte na tlačítko **vyberte oprávnění**. Nastavit **oprávnění** k **číst, zapisovat,** a **provést**. Nastavit **přidat do** k **tuto složku a všechny podřízené objekty**. Nakonec nastavte **přidat jako** k **položka oprávnění k přístupu a výchozí položku oprávnění**.

    ![Přiřazení oprávnění pro Data Lake Store složku](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "přiřadit oprávnění pro složku Data Lake Store")
    
    Klikněte na **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Konfigurace služby Event Hubs k zaznamenání dat do Data Lake Store

V této části vytvoříte Centrum událostí v rámci oboru názvů Event Hubs. Můžete také nakonfigurovat centra událostí k zaznamenání dat k účtu Azure Data Lake Store. V této části se předpokládá, že jste již vytvořili na obor názvů služby Event Hubs.

2. Z **přehled** podokně oboru názvů služby Event Hubs, klikněte na tlačítko **+ centra událostí**.

    ![Vytvoření centra událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "vytvoření centra událostí")

3. Zadejte následující hodnoty pro konfiguraci služby Event Hubs k zaznamenání dat do Data Lake Store.

    ![Vytvoření centra událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "vytvoření centra událostí")

    a. Zadejte název centra událostí.
    
    b. V tomto kurzu nastavit **počet oddílů** a **uchování zpráv** na výchozí hodnoty.
    
    c. Nastavit **zaznamenat** k **na**. Nastavte **časové okno** (jak často se zaznamenat) a **velikost okna** (velikost dat k zachycení). 
    
    d. Pro **zaznamenání zprostředkovatele**, vyberte **Azure Data Lake Store** a vyberte Data Lake Store jste vytvořili dříve. Pro **Data Lake cesta**, zadejte název složky, kterou jste vytvořili v účtu Data Lake Store. Stačí zadat relativní cestu ke složce.

    e. Ponechte **ukázka zachycení formátů název** na výchozí hodnotu. Tato možnost řídí strukturu složek, který se vytvoří ve složce zachycení.

    f. Klikněte na možnost **Vytvořit**.

## <a name="test-the-setup"></a>Test nastavení

Nyní můžete otestovat řešení odesílá data do centra událostí Azure. Postupujte podle pokynů v [odesílat události do centra událostí Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po spuštění zasílání dat se zobrazí data zobrazená v Data Lake Store pomocí struktura složek, které zadáte. Například struktura složek, uvidíte, jak je znázorněno na následujícím snímku obrazovky v Data Lake Store.

![Ukázka dat pro EventHub v Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "EventHub ukázkových dat v Data Lake Store")

> [!NOTE]
> I když nemáte zprávy přicházející do centra událostí, Event Hubs prázdné soubory s pouze záhlaví zapisuje do účtu Data Lake Store. Soubory jsou zapsány ve stejném časovém intervalu, zda jste zadali při vytváření centra událostí.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analýza dat v Data Lake Store

Jakmile jsou data v Data Lake Store, můžete spustit analytické úlohy a proces, zpracujte data. V tématu [USQL Avro příklad](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) o tom, jak to provést pomocí Azure Data Lake Analytics.
  

## <a name="see-also"></a>Další informace najdete v tématech
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
