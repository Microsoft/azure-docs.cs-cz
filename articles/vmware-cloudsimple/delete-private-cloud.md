---
title: Odstranění řešení Azure VMware pomocí CloudSimple privátního cloudu
description: Popisuje postup odstranění privátního cloudu CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024751"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Odstranění privátního cloudu CloudSimple

CloudSimple poskytuje flexibilitu pro odstranění privátního cloudu.  Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při odstranění privátního cloudu se odstraní všechny clustery.

## <a name="before-you-begin"></a>Před zahájením

Odstranění privátního cloudu odstraní celý privátní cloud.  Odstraní se všechny součásti privátního cloudu.  Pokud chcete zachovat všechna data, ujistěte se, že jste data zálohovali do místního úložiště nebo do úložiště Azure.

Mezi komponenty privátního cloudu patří:

* Uzly CloudSimple
* Virtuální počítače
* Sítě VLAN a podsítě
* Všechna uživatelská data uložená v privátním cloudu
* Všechna příloha pravidla brány firewall k síti VLAN nebo podsíti

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Odstranění privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** .

3. Klikněte na privátní cloud, který chcete odstranit.

4. Na stránce Souhrn klikněte na **Odstranit**.

    ![Odstranit privátní cloud](media/delete-private-cloud.png)

5. Na stránce potvrzení zadejte název privátního cloudu a klikněte na **Odstranit**. 

    ![Odstranění privátního cloudu – potvrzení](media/delete-private-cloud-confirm.png)

Privátní cloud je označený k odstranění.  Proces odstranění začíná tři hodiny a odstraní privátní cloud.

> [!CAUTION]
> Po odstranění privátního cloudu musí být uzly smazány.  Měření uzlů bude pokračovat v přenesení uzlů z vašeho předplatného.

## <a name="next-steps"></a>Další kroky

* [Odstranění uzlů](delete-nodes.md)
