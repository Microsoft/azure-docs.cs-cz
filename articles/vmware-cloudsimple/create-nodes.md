---
title: Zřizování uzlů pro řešení VMware (AVS) – Azure
description: Naučte se přidávat uzly do VMWare pomocí nasazení služby AVS.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024802"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Zřizování uzlů pro řešení Azure VMware (AVS)

Zřídí uzly v Azure Portal. Pak můžete pro prostředí privátního cloudu pro funkci AVS nastavit kapacitu s průběžnými platbami.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Přidání uzlu do privátního cloudu služby AVS

1. Vyberte **Všechny služby**.
2. Vyhledejte **uzly služby AVS**.

   ![Hledat v uzlech AVS](media/create-cloudsimple-node-search.png)

3. Vyberte **uzly**pro funkci AVS.
4. Kliknutím na **Přidat** vytvořte uzly.

    ![Přidat uzly pro funkci AVS](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit uzly pro funkci AVS.
6. Vyberte skupinu prostředků pro uzly. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte předponu pro identifikaci uzlů.
8. Vyberte umístění pro prostředky uzlu.
9. Vyberte vyhrazené umístění pro hostování prostředků uzlu.
10. Vyberte [typ uzlu](cloudsimple-node.md).
11. Vyberte počet uzlů, které se mají zřídit.
12. Vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit nastavení, klikněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření privátního cloudu pro funkci AVS](create-private-cloud.md)
