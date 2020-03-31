---
title: Zmenšení řešení Azure VMware podle cloudového privátního cloudu
description: Popisuje, jak zmenšit CloudSimple privátní cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014262"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Zmenšení cloudového jednoduchého privátního cloudu

CloudSimple poskytuje flexibilitu dynamicky zmenšit privátní cloud.  Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při zmenšování privátního cloudu odeberete uzel z existujícího clusteru nebo odstraníte celý cluster. 

## <a name="before-you-begin"></a>Než začnete

Pro zmenšení privátního cloudu musí být splněny následující podmínky.  Cluster pro správu (první cluster) vytvořený při vytvoření privátního cloudu nelze odstranit.

* Cluster vSphere musí mít tři uzly.  Cluster s pouze třemi uzly nelze ztíst.
* Celkové spotřebované úložiště by nemělo překročit celkovou kapacitu po zmenšení clusteru.
* Zkontrolujte, zda některá pravidla plánovače distribuovaných prostředků (DRS) brání vMotion virtuálního počítače.  Pokud jsou pravidla k dispozici, zakažte nebo odstraňte pravidla.  Pravidla DRS zahrnují virtuální počítač pro pravidla spřažení.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="shrink-a-private-cloud"></a>Zmenšení privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **Zdroje.**

3. Klikněte na privátní cloud, který chcete zmenšit

4. Na stránce souhrnu klikněte na **zmenšit**.

    ![Zmenšení privátního cloudu](media/shrink-private-cloud.png)

5. Vyberte cluster, který chcete zmenšit nebo odstranit. 

    ![Zmenšení privátního cloudu – výběr clusteru](media/shrink-private-cloud-select-cluster.png)

6. Vyberte **Odebrat jeden uzel** nebo **Odstranit celý cluster**. 

7. Ověření kapacity clusteru

8. Chcete-li soukromý cloud zmenšit, klepněte na **tlačítko Odeslat.**

Zmenšit privátního cloudu spustí.  Průběh úkolů můžete sledovat.  Proces zmenšení může trvat několik hodin v závislosti na datech, které je třeba resynced na vSAN.

> [!NOTE]
> 1. Pokud zmenšíte privátní cloud odstraněním posledního nebo jediného clusteru v datovém centru, datové centrum se neodstraní.
> 2. Pokud dojde k porušení pravidel DRS, uzel nebude odebrán z clusteru a popis úkolu ukazuje, že odebrání uzlu poruší pravidla DRS v clusteru.    


## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)
