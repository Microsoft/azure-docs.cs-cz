---
title: Odstranění uzlů pro řešení VMware (AVS) – Azure
description: Naučte se odstraňovat uzly z VMWare pomocí nasazení služby AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024734"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Odstranění uzlů z řešení Azure VMware pomocí služby AVS

Po vytvoření se uzly pro funkci AVS monitorují. Uzly musí být odstraněny, aby se zastavilo měření uzlů. Odstraníte uzly, které se nepoužívají z Azure Portal.

## <a name="before-you-begin"></a>Než začnete

Uzel se dá odstranit jenom za následujících podmínek:

* Privátní cloud služby AVS vytvořený pomocí uzlů je odstraněný. Pokud chcete odstranit privátní cloud služby AVS, přečtěte si téma [Odstranění řešení Azure VMware pomocí služby AVS privátní cloud](delete-private-cloud.md).
* Uzel byl odebrán z privátního cloudu služby AVS tím, že se zmenší privátní cloud služby AVS. Pokud chcete zmenšit privátní cloud služby AVS, přečtěte si téma [zmenšení řešení Azure VMware pomocí služby AVS Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Odstranit uzel AVS

1. Vyberte **Všechny služby**.

2. Vyhledejte **uzly služby AVS**.

   ![Hledat v uzlech AVS](media/create-cloudsimple-node-search.png)

3. Vyberte **uzly**pro funkci AVS.

4. Vyberte uzly, které nepatří do privátního cloudu služby AVS k odstranění. Sloupec **název privátního cloudu** pro funkci AVS zobrazuje název privátního cloudu pro funkci AVS, ke kterému uzel patří. Pokud uzel není používán privátním cloudem služby AVS, hodnota bude prázdná. 

    ![Výběr uzlů pro funkci AVS](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Odstranit lze pouze uzly, které nejsou součástí privátního cloudu pro funkci AVS.

## <a name="next-steps"></a>Další kroky

* Informace o [privátním cloudu](cloudsimple-private-cloud.md) pro funkci AVS
