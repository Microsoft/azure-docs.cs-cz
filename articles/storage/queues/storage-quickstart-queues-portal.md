---
title: 'Rychlý Start: Vytvoření front Azure Storage na portálu'
description: Vytvořte frontu pomocí Azure Portal. Pak použijte Azure Portal k přidání zprávy, zobrazení vlastností zprávy a vyřazení zprávy do fronty.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: 2876d2a680aa41372557a9f04c1d6a5eed56b8ae
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269249"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Rychlý Start: vytvoření fronty a přidání zprávy s Azure Portal

V tomto rychlém startu se dozvíte, jak pomocí [Azure Portal](https://portal.azure.com/) vytvořit frontu v Azure Storage a přidat a vyřadit zprávy z fronty.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Vytvoření fronty

Pokud chcete vytvořit frontu v Azure Portal, postupujte podle těchto kroků:

1. Na webu Azure Portal přejděte k novému účtu úložiště.
2. V levé nabídce účtu úložiště přejděte do části **Služba front** a pak vyberte **fronty**.
3. Klikněte na tlačítko **+ fronta** .
4. Zadejte název nové fronty. Název fronty musí být malými písmeny, musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a spojovníky (-).
6. Vyberte **OK** a vytvořte frontu.

    ![Snímek obrazovky ukazující, jak vytvořit frontu v Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Přidat zprávu

V dalším kroku přidejte do nové fronty zprávu. Zpráva může mít velikost až 64 KB.

1. Vyberte novou frontu ze seznamu front v účtu úložiště.
1. Vyberte tlačítko **+ přidat zprávu** a přidejte zprávu do fronty. Zadejte zprávu do pole **text zprávy** . 
1. Určete, kdy vyprší platnost zprávy. Maximální doba, po kterou může zpráva zůstat ve frontě, je 7 dní.
1. Určuje, zda se má zpráva kódovat jako base64. Doporučuje se kódování binárních dat.
1. Kliknutím na tlačítko **OK** přidejte zprávu.

    ![Snímek obrazovky ukazující, jak přidat zprávu do fronty](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Zobrazit vlastnosti zprávy

Po přidání zprávy Azure Portal zobrazí seznam všech zpráv ve frontě. Můžete zobrazit ID zprávy, obsah zprávy, čas vložení zprávy a čas vypršení platnosti zprávy. Můžete si také prohlédnout, kolikrát byla tato zpráva odstraněna z fronty.

![Snímek obrazovky se zobrazením vlastností zprávy](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Vyřadit zprávu do fronty

Z Azure Portal můžete vyřadit zprávu z fronty před frontou. Když vyřadíte zprávu z fronty, zpráva se odstraní. 

Při zrušení zařazení do fronty se vždy odebere nejstarší zpráva ve frontě. 

![Snímek obrazovky ukazující, jak vyřadit z fronty zprávu z portálu](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit frontu, přidat zprávu, zobrazit vlastnosti zprávy a odstranit zprávu ve frontě Azure Portal.

> [!div class="nextstepaction"]
> [Co jsou fronty Azure?](storage-queues-introduction.md)
