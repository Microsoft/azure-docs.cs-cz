---
title: Povolení funkce Azure Event Hubs Capture prostřednictvím portálu | Dokumentace Microsoftu
description: K povolení funkce Event Hubs Capture použijte web Azure Portal.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: ff80bc2452c9826a5c51c146a957fddc72d2dbc2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "41920529"
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Povolení funkce Event Hubs Capture prostřednictvím webu Azure Portal

Azure [Event Hubs Capture][capture-overview] umožňuje automatické doručování streamovaných dat ve službě Event Hubs do služby [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nebo do účtu [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) podle vašeho výběru.

Ke konfiguraci funkce Capture v době vytvoření centra událostí můžete použít [Azure Portal](https://portal.azure.com). Data můžete zachytávat do kontejneru služby Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nebo do účtu [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Další informace najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Zachytávání dat do účtu služby Azure Storage  

Při vytváření centra událostí můžete povolit funkci Capture kliknutím na tlačítko **Zapnout** na obrazovce portálu **Vytvořit centrum událostí**. Potom zadáte účet úložiště a kontejner kliknutím na **Azure Storage** v poli **Zprostředkovatel zachytávání**. Vzhledem k tomu, že Event Hubs Capture u úložiště využívá ověřování služba-služba, není potřeba zadávat připojovací řetězec úložiště. Výběr prostředku automaticky vybere URI prostředku pro váš účet úložiště. Pokud používáte Azure Resource Manager, musíte tento identifikátor URI explicitně zadat jako řetězec.

Výchozí časový interval je 5 minut. Minimální hodnota je 1, maximální hodnota je 15. Okno **Velikost** má rozsah 10 až 500 MB.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Zachytávání dat do účtu Azure Data Lake Store

Pro zachytávání dat do služby Azure Data Lake Store vytvoříte účet Data Lake Store a centrum událostí:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Vytvoření účtu Azure Data Lake Store a složek

1. Vytvořte účet Data Lake Store podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Postupujte podle pokynů v části [Přiřazení oprávnění ke službě Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) a v rámci účtu Data Lake Store vytvořte složku, do které chcete zachytávat data ze služby Event Hubs, a službě Event Hubs přiřaďte oprávnění k zápisu dat do vašeho účtu Data Lake Store.  

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Nezapomeňte, že centrum událostí musí být ve stejném předplatném Azure jako služba Azure Data Lake Store, kterou jste právě vytvořili. Vytvořte centrum událostí kliknutím na tlačítko **Zapnout** v části **Capture** na stránce portálu **Vytvořit centrum událostí**. 
2. Na stránce portálu **Vytvořit centrum událostí** v poli **Zprostředkovatel zachytávání** vyberte **Azure Data Lake Store**.
3. V části **Vybrat službu Data Lake Store** zadejte účet Data Lake Store, který jste vytvořili dříve, a do pole **Cesta Data Lake** zadejte cestu ke složce s daty, kterou jste vytvořili.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Přidání nebo konfigurace funkce Capture v existujícím centru událostí

Funkci Capture můžete nakonfigurovat v existujících centrech událostí, která jsou v oborech názvů služby Event Hubs. Pokud chcete funkci Capture povolit ve stávajícím centru událostí nebo chcete změnit její nastavení, klikněte na obor názvů, aby se načetla obrazovka s přehledem, a pak klikněte na centrum událostí, kterému chcete povolit funkci Capture nebo změnit její nastavení. Nakonec na levé straně otevřené stránky klikněte na možnost **Zachycení** a upravte nastavení podle následujících obrázků:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Další kroky

- Další informace o funkci Event Hubs Capture najdete v tématu [Přehled funkce Event Hubs Capture][capture-overview].
- Ke konfiguraci funkce Event Hubs Capture můžete také použít šablony Azure Resource Manageru. Další informace najdete v tématu věnovaném [povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Začínáme s Azure Data Lake Store pomocí webu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md