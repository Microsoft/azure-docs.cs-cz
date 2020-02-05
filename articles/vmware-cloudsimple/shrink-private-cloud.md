---
title: Zmenšit privátní cloud řešení Azure VMware (AVS)
description: Popisuje, jak zmenšit privátní cloud služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014262"
---
# <a name="shrink-an-avs-private-cloud"></a>Zmenšení privátního cloudu služby AVS

AVS nabízí flexibilitu pro dynamické zmenšování privátního cloudu služby AVS. Privátní cloud služby AVS se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při zmenšování privátního cloudu služby AVS odeberete uzel z existujícího clusteru nebo odstraníte celý cluster. 

## <a name="before-you-begin"></a>Než začnete

Před zmenšením privátního cloudu služby AVS je nutné splnit následující podmínky. Cluster pro správu (první cluster) se vytvoří při vytvoření privátního cloudu služby AVS. Nedá se odstranit.

* Cluster vSphere musí mít tři uzly. Cluster se třemi uzly nelze zmenšit.
* Celkové spotřebované úložiště by nemělo po zmenšení clusteru překročit celkovou kapacitu.
* Ověřte, jestli některá pravidla plánovače distribuovaných zdrojů (DRS) brání vMotion virtuálního počítače. Pokud jsou pravidla k dispozici, zakažte nebo odstraňte pravidla. Pravidla DRS zahrnují pravidla vztahů mezi virtuálními počítači a hostiteli.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Zmenšení privátního cloudu služby AVS

1. [Přístup k portálu AVS](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** .

3. Klikněte na privátní cloud AVS, který chcete zmenšit.

4. Na stránce Souhrn klikněte na tlačítko **zmenšit**.

    ![Zmenšení privátního cloudu pro funkci AVS](media/shrink-private-cloud.png)

5. Vyberte cluster, který chcete zmenšit nebo odstranit. 

    ![Zmenšení privátního cloudu služby AVS – výběr clusteru](media/shrink-private-cloud-select-cluster.png)

6. Vyberte **odebrat jeden uzel** nebo **odstranit celý cluster**. 

7. Ověření kapacity clusteru

8. Klikněte na **Odeslat** a zmenšete privátní cloud AVS.

Spouští se zmenšování privátního cloudu pro funkci AVS. Průběh můžete sledovat v úlohách. Proces zmenšení může trvat několik hodin v závislosti na datech, která se musí znovu synchronizovat v síti vSAN.

> [!NOTE]
> 1. Pokud zmenšíte privátní cloud odstraněním posledního nebo jediného clusteru v datacentru, datacentrum se neodstraní.
> 2. Pokud dojde k nějakému porušení pravidla DRS, uzel nebude odebrán z clusteru a popis úlohy zobrazí, že při odebrání uzlu dojde k porušení pravidel DRS v clusteru.    


## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md) pro funkci AVS
