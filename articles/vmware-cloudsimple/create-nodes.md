---
title: Zřizování uzlů pro řešení VMware podle CloudSimple – Azure
description: Zjistěte, jak přidat uzly do vašeho VMWare s nasazením CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024802"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Zřízení uzlů pro řešení Azure VMware podle CloudSimple

Zřizování uzlů na webu Azure Portal. Pak můžete nastavit průběžnou kapacitu pro vaše privátní cloudové prostředí CloudSimple.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Přidání uzlu do privátního cloudu CloudSimple

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché uzly**.

   ![Hledat cloudové jednoduché uzly](media/create-cloudsimple-node-search.png)

3. Vyberte **možnost CloudSimple Uzly**.
4. Chcete-li vytvořit uzly, klepněte na **tlačítko Přidat.**

    ![Přidat cloudové jednoduché uzly](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, kde chcete zřídit cloudové jednoduché uzly.
6. Vyberte skupinu prostředků pro uzly. Chcete-li přidat novou skupinu prostředků, klepněte na tlačítko **Vytvořit nový**.
7. Zadejte předponu pro identifikaci uzlů.
8. Vyberte umístění prostředků uzlu.
9. Vyberte vyhrazené umístění pro hostování prostředků uzlu.
10. Vyberte [typ uzlu](cloudsimple-node.md).
11. Vyberte počet uzlů, které chcete zřídit.
12. Vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit všechna nastavení, klepněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření privátního cloudu](create-private-cloud.md)
