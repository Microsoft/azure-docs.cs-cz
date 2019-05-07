---
title: Rychlý start Azure – vytvoření fronty ve službě Azure Storage pomocí webu Azure portal | Dokumentace Microsoftu
description: V tomto rychlém startu použijete Azure portal můžete vytvořit frontu. Pak použijete k přidání zprávy, zobrazit vlastnosti zprávy a odstranění z fronty zprávy na webu Azure portal.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/06/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.openlocfilehash: 3b355aa2f3fd5e381ca922ada1444dd281fe74ec
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138272"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Rychlý start: Vytvořit frontu a přidat zprávu pomocí webu Azure portal

V tomto rychlém startu se dozvíte, jak používat [webu Azure portal](https://portal.azure.com/) vytvoření fronty ve službě Azure Storage a k přidání a odstranění z fronty zpráv.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Vytvoření fronty

Můžete vytvořit frontu na webu Azure Portal, postupujte podle těchto kroků:

1. Na webu Azure Portal přejděte k novému účtu úložiště.
2. V levé nabídce pro účet úložiště, přejděte **služba front** a potom vyberte **fronty**.
3. Vyberte **+ fronta** tlačítko.
4. Zadejte název pro novou frontu. Název fronty musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a znak spojovníku (-).
6. Vyberte **OK** vytvořte frontu.

    ![Snímek obrazovky ukazující, jak vytvořit frontu na webu Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Přidat zprávu

V dalším kroku přidáte zprávu do fronty nová. Zpráva může být velikost až 64 KB.

1. Vyberte ze seznamu front v účtu úložiště novou frontu.
1. Vyberte **+ přidat zprávu** tlačítko pro přidání zprávy do fronty. Zadejte zprávu v **text zprávy** pole. 
1. Určete, kdy vyprší platnost zprávy. Maximální doba může zpráva zůstat fronty je 7 dní.
1. Označuje, jestli se má zakódovat zprávu jako Base64. Kódování binárních dat se doporučuje.
1. Vyberte **OK** tlačítko Přidat zprávu.

    ![Snímek obrazovky ukazující, jak přidat zprávu do fronty](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Zobrazení vlastností zpráv

Po přidání zprávy na webu Azure portal zobrazuje seznam všech zpráv ve frontě. Zobrazí se ID zprávy, obsah zprávy, zprávu vložení čas a čas vypršení platnosti zpráv. Můžete také zobrazit kolikrát byla tato zpráva dequeued.

![Snímek obrazovky znázorňující zprávu vlastnosti](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Odstranit zprávu z fronty

Odstranění z fronty zprávu od začátku fronty na webu Azure Portal. Při odstranění z fronty zprávu, odstraní zprávu. 

Dequeueing vždy odstraní nejstarší zprávy ve frontě. 

![Snímek obrazovky ukazující, jak vyřazovat je z ní zprávu z portálu](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit frontu, přidejte zprávu, zobrazit vlastnosti zprávy a odstranění z fronty zpráv na webu Azure Portal.

> [!div class="nextstepaction"]
> [Co jsou fronty Azure?](storage-queues-introduction.md)
