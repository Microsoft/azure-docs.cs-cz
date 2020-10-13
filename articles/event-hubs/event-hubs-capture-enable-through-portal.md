---
title: Event Hubs – zachycení událostí streamování pomocí Azure Portal
description: Tento článek popisuje, jak povolit zachycování událostí streamování prostřednictvím Azure Event Hubs pomocí Azure Portal.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 2381bfa627d00a78ed91af0ba81579588ee016ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613573"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Povolení zachytávání událostí streamování prostřednictvím Azure Event Hubs

Služba Azure [Event Hubs Capture][capture-overview] vám umožní automaticky doručovat streamovaná data v Event Hubs na účet služby [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nebo [Azure Data Lake Storage Gen1 nebo Gen 2](https://azure.microsoft.com/services/data-lake-store/) podle vašeho výběru.

Ke konfiguraci funkce Capture v době vytvoření centra událostí můžete použít [Azure Portal](https://portal.azure.com). Můžete buď zachytit data do kontejneru [úložiště objektů BLOB](https://azure.microsoft.com/services/storage/blobs/) v Azure, nebo na účet [Azure Data Lake Storage Gen 1 nebo Gen 2](https://azure.microsoft.com/services/data-lake-store/) .

Další informace najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].

> [!IMPORTANT]
> Cílový účet úložiště (Azure Storage nebo Azure Data Lake Storage) musí být ve stejném předplatném jako centrum událostí.

## <a name="capture-data-to-azure-storage"></a>Zaznamenání dat do Azure Storage

Při vytváření centra událostí můžete povolit funkci Capture kliknutím na tlačítko **Zapnout** na obrazovce portálu **Vytvořit centrum událostí**. Potom zadáte účet úložiště a kontejner kliknutím na **Azure Storage** v poli **Zprostředkovatel zachytávání**. Vzhledem k tomu, že Event Hubs Capture u úložiště využívá ověřování služba-služba, není potřeba zadávat připojovací řetězec úložiště. Výběr prostředku automaticky vybere URI prostředku pro váš účet úložiště. Pokud používáte Azure Resource Manager, musíte tento identifikátor URI explicitně zadat jako řetězec.

Výchozí časový interval je 5 minut. Minimální hodnota je 1, maximální hodnota je 15. Okno **Velikost** má rozsah 10 až 500 MB.

![Časové okno pro zachycení][1]

> [!NOTE]
> Můžete povolit nebo zakázat vygenerování prázdných souborů, pokud během okna sběru nedojde k událostem. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Zaznamenání dat do Azure Data Lake Storage Gen 2 

1. Podle článku [Vytvoření účtu úložiště](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) vytvořte účet Azure Storage. Nastavte **hierarchický obor názvů** **na kartu** **Upřesnit** , abyste si ho vytvořili Azure Data Lake Storage účet Gen 2.
2. Při vytváření centra událostí proveďte následující kroky: 

    1. Pro **zachytávání**vyberte **zapnuto** . 
    2. Jako poskytovatele Capture vyberte **Azure Storage** . Možnost **Azure Data Lake Store** , kterou vidíte pro **poskytovatele služby Capture** , je určena pro 1. generace Azure Data Lake Storage. Chcete-li použít Gen 2 Azure Data Lake Storage, vyberte **Azure Storage**.
    2. Vyberte tlačítko **Vybrat kontejner** . 

        ![Povolit zachytávání pro Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. V seznamu vyberte účet **Azure Data Lake Storage Gen 2** . 

    ![Vybrat Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Vyberte **kontejner** (systém souborů v Data Lake Storage Gen 2).

    ![Vybrat systém souborů v úložišti](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na stránce **vytvořit centrum událostí** vyberte **vytvořit**. 

    ![Vybrat tlačítko pro vytvoření](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Kontejner, který vytvoříte v Azure Data Lake Storage Gen 2 s použitím tohoto uživatelského rozhraní (UI), se zobrazí v části **systémy souborů** v **Průzkumník služby Storage**. Podobně se systém souborů, který vytvoříte v účtu Data Lake Storage Gen 2, zobrazí jako kontejner v tomto uživatelském rozhraní. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Zaznamenání dat na Azure Data Lake Storage Gen 1 

Pokud chcete zachytit data Azure Data Lake Storage 1. generace, vytvoříte Data Lake Storage účet 1. generace a centrum událostí:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Vytvořit Azure Data Lake Storage účet a složky 1. generace

1. Vytvořte účet Data Lake Storage podle pokynů v článku [Začínáme s Azure Data Lake Storage Gen 1 pomocí Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Podle pokynů v části [přiřazení oprávnění k Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) vytvořte složku v rámci účtu Data Lake Storage 1. generace, ve kterém chcete zachytit data z Event Hubs, a přiřaďte jim oprávnění Event Hubs, aby bylo možné zapisovat data do účtu Data Lake Storage 1. generace.  


### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Centrum událostí musí být ve stejném předplatném Azure jako účet Azure Data Lake Storage 1. generace, který jste vytvořili. Vytvořte centrum událostí kliknutím na tlačítko **Zapnout** v části **Capture** na stránce portálu **Vytvořit centrum událostí**. 
2. Na stránce portálu **Vytvořit centrum událostí** v poli **Zprostředkovatel zachytávání** vyberte **Azure Data Lake Store**.
3. V rozevíracím seznamu **vybrat úložiště** vedle **Data Lake Store** zadejte účet Data Lake Storage 1. generace, který jste předtím vytvořili, a do pole **Data Lake cesta** zadejte cestu k vytvořené složce dat.

    ![Vybrat Data Lake Storage účet][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Přidání nebo konfigurace funkce Capture v existujícím centru událostí

Funkci Capture můžete nakonfigurovat v existujících centrech událostí, která jsou v oborech názvů služby Event Hubs. Pokud chcete funkci Capture povolit ve stávajícím centru událostí nebo chcete změnit její nastavení, klikněte na obor názvů, aby se načetla obrazovka s přehledem, a pak klikněte na centrum událostí, kterému chcete povolit funkci Capture nebo změnit její nastavení. Nakonec na levé straně otevřené stránky klikněte na možnost **Zachycení** a upravte nastavení podle následujících obrázků:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurace Azure Blob Storage][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

![Konfigurace Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Konfigurace Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Další kroky

- Další informace o funkci Event Hubs Capture najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].
- Ke konfiguraci funkce Event Hubs Capture můžete také použít šablony Azure Resource Manageru. Další informace najdete v tématu věnovaném [povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Zjistěte, jak vytvořit předplatné služby Azure Event Grid s oborem názvů služby Event Hubs jako zdrojem](store-captured-data-data-warehouse.md).
- [Začínáme s Azure Data Lake Store pomocí Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
