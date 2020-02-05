---
title: Odstranění privátního cloudu řešení Azure VMware (AVS)
description: Popisuje postup odstranění privátního cloudu služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024751"
---
# <a name="delete-an-avs-private-cloud"></a>Odstranění privátního cloudu služby AVS

AVS nabízí flexibilitu při odstraňování privátního cloudu služby AVS. Privátní cloud služby AVS se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při odstranění privátního cloudu služby AVS dojde k odstranění všech clusterů.

## <a name="before-you-begin"></a>Než začnete

Odstranění privátního cloudu služby AVS odstraní celý privátní cloud služby AVS. Odstraní se všechny součásti privátního cloudu AVS. Pokud chcete zachovat všechna data, ujistěte se, že jste data zálohovali do místního úložiště nebo do úložiště Azure.

Mezi komponenty privátního cloudu služby AVS patří:

* Uzly pro funkci AVS
* Virtuální počítače
* SÍTĚ VLAN/podsítě
* Všechna uživatelská data uložená v privátním cloudu pro funkci AVS
* Všechna příloha pravidla brány firewall k síti VLAN nebo podsíti

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Odstranění privátního cloudu služby AVS

1. [Přístup k portálu AVS](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** .

3. Klikněte na privátní cloud AVS, který chcete odstranit.

4. Na stránce Souhrn klikněte na **Odstranit**.

    ![Odstranit privátní cloud AVS](media/delete-private-cloud.png)

5. Na stránce potvrzení zadejte název privátního cloudu AVS a klikněte na **Odstranit**. 

    ![Odstranění privátního cloudu AVS – potvrzení](media/delete-private-cloud-confirm.png)

Privátní cloud služby AVS je označený k odstranění. Proces odstranění začíná tři hodiny a odstraní privátní cloud AVS.

> [!CAUTION]
> Po odstranění privátního cloudu pro funkci AVS se musí odstranit uzly. Měření uzlů bude pokračovat, dokud se uzly z vašeho předplatného neodstraní.

## <a name="next-steps"></a>Další kroky

* [Odstranit uzly](delete-nodes.md)
