---
title: Odstranění řešení Azure VMware pomocí privátního cloudu CloudSimple
description: Popisuje, jak odstranit CloudSimple privátní cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024751"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Odstranění cloudového jednoduchého privátního cloudu

CloudSimple poskytuje flexibilitu k odstranění privátního cloudu.  Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Když odstraníte privátní cloud, budou odstraněny všechny clustery.

## <a name="before-you-begin"></a>Než začnete

Odstranění majevku privátního cloudu odstraní celý privátní cloud.  Všechny součásti privátního cloudu budou odstraněny.  Pokud chcete zachovat některá data, ujistěte se, že jste zálohovali data do místního úložiště nebo úložiště Azure.

Mezi součásti privátního cloudu patří:

* CloudJednoduché uzly
* Virtual Machines
* Sítě VLAN a podsítě
* Všechna uživatelská data uložená v privátním cloudu
* Všechny přílohy pravidel brány firewall k síti VLAN/podsíti

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="delete-a-private-cloud"></a>Odstranění privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **Zdroje.**

3. Klikněte na privátní cloud, který chcete odstranit

4. Na stránce souhrnu klepněte na **tlačítko Odstranit**.

    ![Odstranění privátního cloudu](media/delete-private-cloud.png)

5. Na stránce s potvrzením zadejte název privátního cloudu a klepněte na **tlačítko Odstranit**. 

    ![Odstranit privátní cloud - potvrdit](media/delete-private-cloud-confirm.png)

Privátní cloud je označen k odstranění.  Proces odstranění začíná po třech hodinách a odstraní privátní cloud.

> [!CAUTION]
> Uzly musí být odstraněny po odstranění privátního cloudu.  Měření uzlů bude pokračovat, dokud se uzly neodstraní z vašeho předplatného.

## <a name="next-steps"></a>Další kroky

* [Odstranění uzlů](delete-nodes.md)
