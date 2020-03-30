---
title: Odstranění uzlů pro řešení VMware pomocí CloudSimple – Azure
description: Přečtěte si, jak odstranit uzly z vašeho VMWare pomocí nasazení CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024734"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Odstranění uzlů z řešení Azure VMware pomocí CloudSimple

CloudSimple uzly jsou měřeny po jejich vytvoření.  Uzly musí být odstraněny zastavit měření uzlů.  Odstraníte uzly, které se nepoužívají z webu Azure Portal.

## <a name="before-you-begin"></a>Než začnete

Uzel lze odstranit pouze za následujících podmínek:

* Privátní cloud vytvořený s uzly se odstraní.  Pokud chcete odstranit privátní cloud, [přečtěte si témat odstranění řešení VMware Azure pomocí cloudového privátního cloudu](delete-private-cloud.md).
* Uzel byl odebrán z privátního cloudu zmenšením privátního cloudu.  Pokud chcete zmenšit privátní cloud, [přečtěte si tématu Zmenšení řešení VMware azure podle cloudového privátního cloudu](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="delete-cloudsimple-node"></a>Odstranit uzel CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **cloudové jednoduché uzly**.

   ![Hledat cloudové jednoduché uzly](media/create-cloudsimple-node-search.png)

3. Vyberte **možnost CloudSimple Uzly**.

4. Vyberte uzly, které nepatří do privátního cloudu, chcete-li odstranit.  **Private CLOUD NÁZEV** sloupec zobrazuje název privátního cloudu, do kterého patří uzel.  Pokud uzel není používán privátním cloudem, bude hodnota prázdná. 

    ![Vybrat cloudové jednoduché uzly](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Odstranit lze pouze uzly, které nejsou součástí privátního cloudu.

## <a name="next-steps"></a>Další kroky

* Další informace o [privátním cloudu](cloudsimple-private-cloud.md)
