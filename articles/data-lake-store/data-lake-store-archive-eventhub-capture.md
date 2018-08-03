---
title: Zachytávání dat ze služby Event Hubs do Azure Data Lake Store | Dokumentace Microsoftu
description: Použití Azure Data Lake Store k zachytávání dat ze služby Event Hubs
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bda52acc12aad3cad20143c319f557f11d760c42
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435148"
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Použití Azure Data Lake Store k zachytávání dat ze služby Event Hubs

Další informace o použití Azure Data Lake Store k zaznamenání dat přijatých ze služby Azure Event Hubs.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Store**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Obor názvů služby Event Hubs**. Pokyny najdete v tématu [vytvořte obor názvů služby Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Ujistěte se, že účet Data Lake Store a obor názvů Event Hubs jsou ve stejném předplatném Azure.


## <a name="assign-permissions-to-event-hubs"></a>Přiřazení oprávnění pro službu Event Hubs

V této části vytvoříte v rámci účtu do složky, ve které chcete zachytávat data ze služby Event Hubs. Můžete také přiřadit oprávnění služby Event Hubs, která zápisu dat do účtu Azure Data Lake Store. 

1. Otevřete účet Data Lake Store, ve které chcete zachytávat data ze služby Event Hubs a potom klikněte na **Průzkumník dat**.

    ![Průzkumník dat data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Průzkumník dat Data Lake Store")

1.  Klikněte na tlačítko **novou složku** a potom zadejte název pro složku, ve které chcete zaznamenat data.

    ![Vytvořte novou složku v Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "vytvořte novou složku v Data Lake Store")

1. Přiřadíte oprávnění v kořenovém adresáři Data Lake Store. 

    a. Klikněte na tlačítko **Průzkumník dat**vyberte kořenový účet Data Lake Store a pak klikněte na tlačítko **přístup**.

    ![Přiřadit oprávnění pro Data Lake Store kořenové](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "přiřadit oprávnění pro kořenový adresář Data Lake Store")

    b. V části **přístup**, klikněte na tlačítko **přidat**, klikněte na tlačítko **vybrat uživatele nebo skupinu**a poté vyhledejte `Microsoft.EventHubs`. 

    ![Přiřadit oprávnění pro Data Lake Store kořenové](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "přiřadit oprávnění pro kořenový adresář Data Lake Store")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**, klikněte na tlačítko **oprávnění Select**. Nastavte **oprávnění** k **provést**. Nastavte **přidat** k **tato složka a všechny podřízené objekty**. Nastavte **přidat jako** k **položka oprávnění k přístupu a výchozí položka oprávnění**.

    > [!IMPORTANT]
    > Při vytváření nové hierarchii složek pro sběr dat přijatých ze služby Azure Event Hubs, je to snadný způsob, jak zajistit přístup k cílové složce.  Přidání oprávnění pro všechny podřízené složky nejvyšší úrovně s mnoha podřízené soubory a složky může trvat dlouhou dobu.  Pokud kořenová složka obsahuje velké množství souborů a složek, může to být rychlejší přidat **Execute** oprávnění pro `Microsoft.EventHubs` jednotlivě na všechny složky v cestě ke složce konečného místa určení. 

    ![Přiřadit oprávnění pro Data Lake Store kořenové](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "přiřadit oprávnění pro kořenový adresář Data Lake Store")

    Klikněte na **OK**.

1. Přiřadíte oprávnění pro složku v rámci účtu Data Lake Store, ve které chcete zachytávat data.

    a. Klikněte na tlačítko **Průzkumník dat**, vyberte složku, v účtu Data Lake Store a pak klikněte na tlačítko **přístup**.

    ![Přiřazení oprávnění pro Data Lake Store složku](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "přiřadit oprávnění pro složky Data Lake Store")

    b. V části **přístup**, klikněte na tlačítko **přidat**, klikněte na tlačítko **vybrat uživatele nebo skupinu**a poté vyhledejte `Microsoft.EventHubs`. 

    ![Přiřazení oprávnění pro Data Lake Store složku](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "přiřadit oprávnění pro složky Data Lake Store")
    
    Klikněte na **Vybrat**.

    c. V části **přiřadit oprávnění**, klikněte na tlačítko **oprávnění Select**. Nastavte **oprávnění** k **číst, zapisovat,** a **provést**. Nastavte **přidat** k **tato složka a všechny podřízené objekty**. Nakonec nastavte **přidat jako** k **položka oprávnění k přístupu a výchozí položka oprávnění**.

    ![Přiřazení oprávnění pro Data Lake Store složku](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "přiřadit oprávnění pro složky Data Lake Store")
    
    Klikněte na **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Konfigurovat službu Event Hubs k zachytávání dat do Data Lake Store

V této části vytvoříte Centrum událostí v rámci oboru názvů Event Hubs. Můžete také nakonfigurovat Centrum událostí k zachytávání dat do účtu Azure Data Lake Store. V této části se předpokládá, že jste již vytvořili obor názvů služby Event Hubs.

1. Z **přehled** podokně oboru názvů služby Event Hubs, klikněte na tlačítko **+ Centrum událostí**.

    ![Vytvoření centra událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "vytvoříte Centrum událostí")

1. Zadejte následující hodnoty pro konfiguraci služby Event Hubs k zachytávání dat do Data Lake Store.

    ![Vytvoření centra událostí](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "vytvoříte Centrum událostí")

    a. Zadejte název centra událostí.
    
    b. Pro účely tohoto kurzu nastavte **počet oddílů** a **uchovávání zpráv** na výchozí hodnoty.
    
    c. Nastavte **zachycení** k **na**. Nastavte **časový interval** (jak často chcete zaznamenat) a **velikost okna** (velikost dat k zachycení). 
    
    d. Pro **zprostředkovatel zachytávání**vyberte **Azure Data Lake Store** a vyberte Data Lake Store jste vytvořili dříve. Pro **cesta k Data Lake**, zadejte název složky, kterou jste vytvořili v účtu Data Lake Store. Stačí zadat relativní cestu ke složce.

    e. Nechte **ukázkové formáty názvů souborů zachytávání** na výchozí hodnotu. Tato možnost řídí, který je vytvořen ve složce zachycení strukturu složek.

    f. Klikněte na možnost **Vytvořit**.

## <a name="test-the-setup"></a>Test nastavení

Řešení teď můžete otestovat odesláním dat do centra událostí Azure. Postupujte podle pokynů na adrese [odesílání událostí do služby Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po spuštění odesílání dat, zobrazí data zobrazená v Data Lake Store pomocí strukturu složek, které jste zadali. Například struktury složek, uvidíte, jak je znázorněno na následujícím snímku obrazovky v vaše Data Lake Store.

![Ukázková data pro EventHub v Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "dat pro EventHub ukázka v Data Lake Store")

> [!NOTE]
> I když nemáte zprávy přicházející do služby Event Hubs, služby Event Hubs zapíše prázdné soubory s pouze záhlaví do účtu Data Lake Store. Soubory jsou zapsány ve stejnou dobu, že jste zadali při vytváření služby Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analýza dat v Data Lake Store

Jakmile jsou data v Data Lake Store, můžete spustit analytických úloh na procesu a zpracování dat. Zobrazit [USQL Avro příklad](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) o tom, jak to udělat pomocí Azure Data Lake Analytics.
  

## <a name="see-also"></a>Další informace najdete v tématech
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Kopírování dat z Azure Storage BLOB do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
