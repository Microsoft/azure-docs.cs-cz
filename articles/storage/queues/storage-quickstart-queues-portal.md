---
title: 'Úvodní příručka: Vytvoření front úložiště Azure na portálu'
description: K vytvoření fronty použijte portál Azure. Potom pomocí portálu Azure přidejte zprávu, zobrazte vlastnosti zprávy a zprávu odložte do fronty.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: 2876d2a680aa41372557a9f04c1d6a5eed56b8ae
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74269249"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Úvodní příručka: Vytvoření fronty a přidání zprávy pomocí portálu Azure

V tomto rychlém startu se dozvíte, jak pomocí [portálu Azure](https://portal.azure.com/) vytvořit frontu ve službě Azure Storage a přidat a dequeue zprávy.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Vytvoření fronty

Pokud chcete vytvořit frontu na webu Azure Portal, postupujte takto:

1. Na webu Azure Portal přejděte k novému účtu úložiště.
2. V levé nabídce účtu úložiště přejděte do části **Služba fronty** a vyberte **Fronty**.
3. Vyberte tlačítko **+ Fronta.**
4. Zadejte název nové fronty. Název fronty musí být malý, musí začínat písmenem nebo číslem a může obsahovat pouze písmena, čísla a znak pomlčky (-).
6. Chcete-li vytvořit frontu, vyberte **ok.**

    ![Snímek obrazovky znázorňující, jak vytvořit frontu na webu Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Přidání zprávy

Dále přidejte zprávu do nové fronty. Zpráva může mít velikost až 64 kB.

1. Vyberte novou frontu ze seznamu front v účtu úložiště.
1. Chcete-li přidat zprávu do fronty, vyberte tlačítko **+ Přidat zprávu.** Zadejte zprávu do textového pole **Zpráva.** 
1. Určete, kdy vyprší platnost zprávy. Maximální doba, po kterou může zpráva zůstat ve frontě, je 7 dní.
1. Určete, zda má být zpráva zakódována jako Base64. Doporučuje se kódování binárních dat.
1. Chcete-li zprávu přidat, vyberte tlačítko **OK.**

    ![Snímek obrazovky s postupem přidání zprávy do fronty](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Zobrazit vlastnosti zprávy

Po přidání zprávy se na portálu Azure zobrazí seznam všech zpráv ve frontě. Můžete zobrazit ID zprávy, obsah zprávy, čas vložení zprávy a čas vypršení platnosti zprávy. Můžete také zjistit, kolikrát byla tato zpráva vyřazena z fronty.

![Snímek obrazovky s vlastnostmi zprávy](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Odstranění zprávy z fronty

Zprávu z fronty můžete z portálu Azure z fronty dequeue. Při vyřazení zprávy z fronty zpráva je odstraněna. 

Vyřazení do fronty vždy odebere nejstarší zprávu ve frontě. 

![Snímek obrazovky znázorňující, jak z fronty zprávy z portálu](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit frontu, přidat zprávu, zobrazit vlastnosti zprávy a dequeue zprávu na webu Azure Portal.

> [!div class="nextstepaction"]
> [Co jsou fronty Azure?](storage-queues-introduction.md)
