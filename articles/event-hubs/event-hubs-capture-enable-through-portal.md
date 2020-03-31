---
title: Centra událostí – zachyťte události streamování pomocí portálu Azure
description: Tento článek popisuje, jak povolit digitalizaci událostí datových proudů přes Azure Event Hubs pomocí portálu Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187422"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Povolení zachytávání událostí streamovaných přes Azure Event Hubs

Azure [Event Hubs Capture][capture-overview] umožňuje automaticky doručovat streamovaná data v rozbočovačích událostí do úložiště objektů [blob Azure](https://azure.microsoft.com/services/storage/blobs/) nebo do účtu Azure Data Lake Storage [Gen1 nebo Gen 2](https://azure.microsoft.com/services/data-lake-store/) podle vašeho výběru.

Ke konfiguraci funkce Capture v době vytvoření centra událostí můžete použít [Azure Portal](https://portal.azure.com). Můžete buď zachytit data do kontejneru úložiště Azure [Blob](https://azure.microsoft.com/services/storage/blobs/) nebo na účet [Azure Data Lake Storage Gen 1 nebo Gen 2.](https://azure.microsoft.com/services/data-lake-store/)

Další informace najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Zachyťte data do Azure Storage

Při vytváření centra událostí můžete povolit funkci Capture kliknutím na tlačítko **Zapnout** na obrazovce portálu **Vytvořit centrum událostí**. Potom zadáte účet úložiště a kontejner kliknutím na **Azure Storage** v poli **Zprostředkovatel zachytávání**. Vzhledem k tomu, že Event Hubs Capture u úložiště využívá ověřování služba-služba, není potřeba zadávat připojovací řetězec úložiště. Výběr prostředku automaticky vybere URI prostředku pro váš účet úložiště. Pokud používáte Azure Resource Manager, musíte tento identifikátor URI explicitně zadat jako řetězec.

Výchozí časový interval je 5 minut. Minimální hodnota je 1, maximální hodnota je 15. Okno **Velikost** má rozsah 10 až 500 MB.

![Časové okno pro zachycení][1]

> [!NOTE]
> Můžete povolit nebo zakázat vyzařování prázdných souborů, pokud během okna zachycení nedojde k žádným událostem. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Zachyťte data do úložiště datových jezer Azure Gen 2 

1. Chcete-li vytvořit účet úložiště Azure, postupujte podle článku [Vytvoření účtu úložiště.](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) Nastavte **hierarchický obor názvů** na **povoleno** na kartě **Upřesnit,** aby byl účet Azure Data Lake Storage Gen 2.
2. Při vytváření centra událostí postupujte takto: 

    1. Vyberte **možnost Zapnuto** pro **zachycení**. 
    2. Jako zprostředkovatele sběru vyberte **Azure Storage.** Možnost **Úložiště datových jezer Azure,** kterou vidíte pro **poskytovatele sběru,** je pro Gen 1 azure úložiště datových jezer. Pokud chcete použít Gen 2 Azure Data Lake Storage, vyberte **Azure Storage**.
    2. Vyberte tlačítko **Vybrat kontejner.** 

        ![Povolit sběr dat ového úložiště jezera Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. V seznamu vyberte účet **Azure Data Lake Storage Gen 2.** 

    ![Vybrat úložiště datového jezera Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Vyberte **kontejner** (systém souborů v úložišti datového jezera Gen 2).

    ![Výběr systému souborů v úložišti](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na stránce **Vytvořit centrum událostí** vyberte **Vytvořit**. 

    ![Tlačítko Vybrat vytvořit](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Kontejner, který vytvoříte v Azure Data Lake Storage Gen 2 pomocí tohoto uživatelského rozhraní (UI), se zobrazí v části **Souborové systémy** v **Průzkumníku úložiště**. Podobně systém souborů, který vytvoříte v účtu Data Lake Storage Gen 2, se v tomto ui zobrazí jako kontejner. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Sběr dat do úložiště datových jezer Azure Gen 1 

Chcete-li zachytit data do azure data lake storage gen 1, vytvořte účet Storage Storage 1 data lake a centrum událostí:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Vytvoření účtu a složek Azure Data Lake Storage Gen 1

1. Vytvořte si účet úložiště datového jezera podle pokynů v [části Začínáme s Azure Data Lake Storage Gen 1 pomocí portálu Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Podle pokynů v části [Přiřadit oprávnění k centru událostí](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) vytvořte složku v rámci účtu Data Lake Storage Gen 1, ve kterém chcete zachytit data z centra událostí, a přiřaďte oprávnění centru událostí, aby mohl zapisovat data do vašeho účtu Storage 1 data data.  


### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Centrum událostí musí být ve stejném předplatném Azure jako účet Azure Data Lake Storage Gen 1, který jste vytvořili. Vytvořte centrum událostí kliknutím na tlačítko **Zapnout** v části **Capture** na stránce portálu **Vytvořit centrum událostí**. 
2. Na stránce portálu **Vytvořit centrum událostí** v poli **Zprostředkovatel zachytávání** vyberte **Azure Data Lake Store**.
3. V **rozevíracím** seznamu Vybrat úložiště vedle rozevíracího seznamu Úložiště **datových jezer** zadejte účet Data Lake Storage Gen 1, který jste vytvořili dříve, a do pole Cesta **datového jezera** zadejte cestu k vytvořené složce dat.

    ![Vybrat účet úložiště datového jezera][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Přidání nebo konfigurace funkce Capture v existujícím centru událostí

Funkci Capture můžete nakonfigurovat v existujících centrech událostí, která jsou v oborech názvů služby Event Hubs. Pokud chcete funkci Capture povolit ve stávajícím centru událostí nebo chcete změnit její nastavení, klikněte na obor názvů, aby se načetla obrazovka s přehledem, a pak klikněte na centrum událostí, kterému chcete povolit funkci Capture nebo změnit její nastavení. Nakonec na levé straně otevřené stránky klikněte na možnost **Zachycení** a upravte nastavení podle následujících obrázků:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurace úložiště objektů blob Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

![Konfigurace úložiště datového jezera Azure Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Úložiště datového jezera Azure Gen 1 

![Konfigurace úložiště datových jezer Azure][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Další kroky

- Další informace o funkci Event Hubs Capture najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].
- Ke konfiguraci funkce Event Hubs Capture můžete také použít šablony Azure Resource Manageru. Další informace najdete v tématu věnovaném [povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Zjistěte, jak vytvořit předplatné služby Azure Event Grid s oborem názvů služby Event Hubs jako zdrojem](store-captured-data-data-warehouse.md).
- [Začínáme s Azure Data Lake Store pomocí portálu Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
