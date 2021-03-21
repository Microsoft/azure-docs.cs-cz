---
title: Odstranění řešení Azure VMware pomocí CloudSimple privátního cloudu
description: Naučte se, jak odstranit privátní cloud CloudSimple. Při odstranění privátního cloudu se odstraní všechny clustery.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7db967955dc86db39db4dcb2b3a2baf8906efb20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896256"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Odstranění privátního cloudu CloudSimple

CloudSimple poskytuje flexibilitu pro odstranění privátního cloudu.  Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při odstranění privátního cloudu se odstraní všechny clustery.

## <a name="before-you-begin"></a>Než začnete

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
