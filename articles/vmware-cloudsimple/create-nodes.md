---
title: Zřízení uzlů pro řešení VMware podle CloudSimple – Azure
description: Naučte se přidávat uzly do VMWare pomocí nasazení CloudSimple.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5806198968d98fea4c5cbf8731358ca4041f0935
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972873"
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
10. Vyberte typ uzlu. Můžete zvolit [možnost CS28 nebo CS36](cloudsimple-node.md). Tato možnost zahrnuje maximální kapacitu výpočetní kapacity a paměti.
11. Vyberte počet uzlů, které se mají zřídit.
12. Vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit nastavení, klikněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

* [Vytvoření privátního cloudu](create-private-cloud.md)
