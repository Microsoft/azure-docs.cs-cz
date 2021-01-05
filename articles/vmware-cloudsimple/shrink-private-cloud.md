---
title: Zmenšení řešení Azure VMware pomocí CloudSimple privátního cloudu
description: Přečtěte si, jak dynamicky zmenšovat privátní cloud v CloudSimple odebráním uzlu z existujícího clusteru vSphere nebo odebráním celého clusteru.
author: Ajayan1008
ms.author: v-hborys
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a99b9b56f17b78a98f37d47dcefab26dd9c859de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899129"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Zmenšení privátního cloudu CloudSimple

CloudSimple poskytuje flexibilitu pro dynamické zmenšování privátního cloudu.  Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při zmenšování privátního cloudu odeberete uzel z existujícího clusteru nebo odstraníte celý cluster. 

## <a name="before-you-begin"></a>Než začnete

Pro zmenšení privátního cloudu musí být splněné následující podmínky.  Cluster pro správu (první cluster) vytvořený při vytvoření privátního cloudu nelze odstranit.

* Cluster vSphere musí mít tři uzly.  Cluster se třemi uzly nelze zmenšit.
* Celkové spotřebované úložiště by nemělo po zmenšení clusteru překročit celkovou kapacitu.
* Ověřte, jestli některá pravidla plánovače distribuovaných zdrojů (DRS) brání vMotion virtuálního počítače.  Pokud jsou pravidla k dispozici, zakažte nebo odstraňte pravidla.  Pravidla DRS zahrnují pravidla vztahů mezi virtuálními počítači a hostiteli.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Zmenšení privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** .

3. Klikněte na privátní cloud, který chcete zmenšit.

4. Na stránce Souhrn klikněte na tlačítko **zmenšit**.

    ![Zmenšit privátní cloud](media/shrink-private-cloud.png)

5. Vyberte cluster, který chcete zmenšit nebo odstranit. 

    ![Zmenšit privátní cloud – vybrat cluster](media/shrink-private-cloud-select-cluster.png)

6. Vyberte **odebrat jeden uzel** nebo **odstranit celý cluster**. 

7. Ověření kapacity clusteru

8. Kliknutím na **Odeslat** zmenšíte privátní cloud.

Spustí se zmenšení privátního cloudu.  Průběh můžete sledovat v úlohách.  Proces zmenšení může trvat několik hodin v závislosti na datech, která se musí znovu synchronizovat v síti vSAN.

> [!NOTE]
> 1. Pokud zmenšíte privátní cloud odstraněním posledního nebo jediného clusteru v datacentru, datacentrum se neodstraní.
> 2. Pokud dojde k nějakému porušení pravidla DRS, uzel nebude odebrán z clusteru a popis úlohy zobrazí, že při odebrání uzlu dojde k porušení pravidel DRS v clusteru.    


## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)
