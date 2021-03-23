---
title: Obnovení odstraněného řídicího panelu na webu Azure Portal
description: Pokud odstraníte publikovaný řídicí panel v Azure Portal, můžete řídicí panel obnovit.
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.openlocfilehash: 2d0c8ad9ce93f3812dd810361e10a26232e29685
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774894"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Obnovení odstraněného řídicího panelu na webu Azure Portal

Pokud jste v globálním cloudu Azure a odstraníte _publikovaný_ řídicí panel v Azure Portal, můžete tento řídicí panel obnovit do 14 dnů od odstranění. Pokud se nacházíte v cloudu Azure Government nebo řídicí panel není publikovaný, nemůžete ho obnovit a musíte ho znovu sestavit. Další informace o publikování řídicího panelu najdete v tématu [publikování řídicího](azure-portal-dashboard-share-access.md#publish-a-dashboard)panelu. Pomocí těchto kroků obnovíte publikovaný řídicí panel:

1. V nabídce Azure Portal vyberte **skupiny prostředků** a pak vyberte skupinu prostředků, do které jste řídicí panel publikovali (ve výchozím nastavení se jmenuje **řídicí panely**).

1. V části **Protokol aktivit** rozbalte operaci **Odstranit řídicí panel** . Vyberte kartu **historie změn** a pak vyberte **\<deleted resource\>** .

    ![Snímek obrazovky s kartou Change History](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Vyberte a zkopírujte obsah v levém podokně a pak soubor uložte do textového souboru s příponou _. JSON_ . Portál používá soubor JSON k opětovnému vytvoření řídicího panelu.

    ![Snímek obrazovky s rozdílem v historii změn](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. V nabídce Azure Portal vyberte **řídicí panely** a pak vyberte **nahrát**.

    ![Snímek obrazovky s nahráváním řídicího panelu](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Vyberte soubor JSON, který jste uložili. Portál znovu vytvoří řídicí panel se stejným názvem a prvky jako odstraněný řídicí panel.

1. Vyberte **sdílet** , aby se řídicí panel publikoval a znovu se navázalo příslušné řízení přístupu.

    ![Snímek obrazovky sdílení řídicího panelu](media/recover-shared-deleted-dashboard/dashboard-share.png)
