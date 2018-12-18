---
title: Zachycení streamovaných událostí pomocí webu Azure portal – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit sběr událostí streamování pomocí služby Azure Event Hubs pomocí webu Azure portal.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b485add894dbe99a524d16f891d001991c1ec7
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558375"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Povolit sběr událostí pomocí Azure Event Hubs

Azure [Event Hubs Capture][capture-overview] umožňuje automatické doručování streamovaných dat ve službě Event Hubs do služby [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nebo do účtu [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) podle vašeho výběru.

Ke konfiguraci funkce Capture v době vytvoření centra událostí můžete použít [Azure Portal](https://portal.azure.com). Data můžete zachytávat do kontejneru služby Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nebo do účtu [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Další informace najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Zachytávání dat do účtu služby Azure Storage  

Při vytváření centra událostí můžete povolit funkci Capture kliknutím na tlačítko **Zapnout** na obrazovce portálu **Vytvořit centrum událostí**. Potom zadáte účet úložiště a kontejner kliknutím na **Azure Storage** v poli **Zprostředkovatel zachytávání**. Vzhledem k tomu, že Event Hubs Capture u úložiště využívá ověřování služba-služba, není potřeba zadávat připojovací řetězec úložiště. Výběr prostředku automaticky vybere URI prostředku pro váš účet úložiště. Pokud používáte Azure Resource Manager, musíte tento identifikátor URI explicitně zadat jako řetězec.

Výchozí časový interval je 5 minut. Minimální hodnota je 1, maximální hodnota je 15. Okno **Velikost** má rozsah 10 až 500 MB.

![Časový interval pro zaznamenání][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Zachytávání dat do účtu Azure Data Lake Store

Pro zachytávání dat do služby Azure Data Lake Store vytvoříte účet Data Lake Store a centrum událostí:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Vytvoření účtu Azure Data Lake Store a složek

1. Vytvořte účet Data Lake Store podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Postupujte podle pokynů v části [Přiřazení oprávnění ke službě Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) a v rámci účtu Data Lake Store vytvořte složku, do které chcete zachytávat data ze služby Event Hubs, a službě Event Hubs přiřaďte oprávnění k zápisu dat do vašeho účtu Data Lake Store.  

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Nezapomeňte, že centrum událostí musí být ve stejném předplatném Azure jako služba Azure Data Lake Store, kterou jste právě vytvořili. Vytvořte centrum událostí kliknutím na tlačítko **Zapnout** v části **Capture** na stránce portálu **Vytvořit centrum událostí**. 
2. Na stránce portálu **Vytvořit centrum událostí** v poli **Zprostředkovatel zachytávání** vyberte **Azure Data Lake Store**.
3. V části **Vybrat službu Data Lake Store** zadejte účet Data Lake Store, který jste vytvořili dříve, a do pole **Cesta Data Lake** zadejte cestu ke složce s daty, kterou jste vytvořili.

    ![Vyberte účet Data Lake Storage][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Přidání nebo konfigurace funkce Capture v existujícím centru událostí

Funkci Capture můžete nakonfigurovat v existujících centrech událostí, která jsou v oborech názvů služby Event Hubs. Pokud chcete funkci Capture povolit ve stávajícím centru událostí nebo chcete změnit její nastavení, klikněte na obor názvů, aby se načetla obrazovka s přehledem, a pak klikněte na centrum událostí, kterému chcete povolit funkci Capture nebo změnit její nastavení. Nakonec na levé straně otevřené stránky klikněte na možnost **Zachycení** a upravte nastavení podle následujících obrázků:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurace úložiště objektů Blob v Azure][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Konfigurace úložiště Azure Data Lake][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Další postup

- Další informace o funkci Event Hubs Capture najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].
- Ke konfiguraci funkce Event Hubs Capture můžete také použít šablony Azure Resource Manageru. Další informace najdete v tématu věnovaném [povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Zjistěte, jak vytvořit předplatné služby Azure Event Grid s oborem názvů služby Event Hubs jako zdrojem](store-captured-data-data-warehouse.md).
- [Začínáme s Azure Data Lake Store pomocí webu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
