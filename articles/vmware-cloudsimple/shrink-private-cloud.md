---
title: Zmenšit řešení Azure VMware CloudSimple privátního cloudu
description: Popisuje, jak zmenšit CloudSimple privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544514"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Zmenšit CloudSimple privátního cloudu

CloudSimple poskytuje flexibilitu při dynamicky zmenšit privátního cloudu.  Privátní Cloud se skládá z jednoho nebo více clusterech vSphere. Každý cluster může mít 3 až 16 uzlů. Při zmenšování privátního cloudu, odebrání uzlu ze stávajícího clusteru nebo odstranit celý cluster. 

## <a name="before-you-begin"></a>Než začnete

Musí být splněny následující podmínky pro zmenšení privátního cloudu.  Správa clusteru (první cluster) vytvořené při vytvoření privátního cloudu nelze odstranit.

* VSphere cluster musí mít tři uzly.  Cluster s pouze tři uzly nelze zmenšit.
* Celkové úložiště spotřebované nesmí přesáhnout celkovou kapacitu po zmenšení clusteru. 

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Zmenšit privátního cloudu 

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřít **prostředky** stránky.

3. Klikněte na privátní Cloud, který chcete zmenšit

4. Na stránce Souhrn klikněte na tlačítko **zmenšit**.

    ![Zmenšit privátního cloudu](media/shrink-private-cloud.png)

5. Vyberte cluster, který chcete zmenšit nebo odstranit. 

    ![Zmenšit privátního cloudu – výběr clusteru](media/shrink-private-cloud-select-cluster.png)

6. Vyberte **odebrat jeden uzel** nebo **odstranit celý cluster**. 

7. Ověření kapacity clusteru

8. Klikněte na tlačítko **odeslat** zmenšení privátního cloudu.

Spustí zmenšení privátního cloudu.  Můžete sledovat průběh úlohy.  Zmenšení proces může trvat několik hodin v závislosti na data, která musí být resynced na síti vSAN.

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních Cloudů](cloudsimple-private-cloud.md)