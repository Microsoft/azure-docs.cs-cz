---
title: Zřízení uzlů pro řešení VMware podle CloudSimple – Azure
description: Naučte se, jak přidat uzly do VMWare s nasazením CloudSimple v Azure Portal. Pro prostředí privátního cloudu můžete nastavit kapacitu s průběžnými platbami.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140730"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Zřízení uzlů pro řešení Azure VMware podle CloudSimple

Zřídí uzly v Azure Portal. Pak můžete pro prostředí privátního cloudu CloudSimple nastavit kapacitu průběžných plateb.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Přidání uzlu do privátního cloudu CloudSimple

1. Vyberte **Všechny služby**.
2. Vyhledejte **uzly CloudSimple**.

   ![Hledat uzly CloudSimple](media/create-cloudsimple-node-search.png)

3. Vyberte **uzly CloudSimple**.
4. Kliknutím na **Přidat** vytvořte uzly.

    ![Přidat uzly CloudSimple](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, ve kterém chcete zřídit CloudSimple uzly.
6. Vyberte skupinu prostředků pro uzly. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte předponu pro identifikaci uzlů.
8. Vyberte umístění pro prostředky uzlu.
9. Vyberte vyhrazené umístění pro hostování prostředků uzlu.
10. Vyberte [typ uzlu](cloudsimple-node.md).
11. Vyberte počet uzlů, které se mají zřídit.
12. Vyberte **Zkontrolovat a vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit nastavení, klikněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření privátního cloudu](create-private-cloud.md)
