---
title: Obnovení odstraněných řídicích panelů v Azure Portal | Microsoft Docs
description: Pokud odstraníte publikovaný řídicí panel v Azure Portal, můžete řídicí panel obnovit.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7a443f24bfae8f3433107aa441d223b3639232da
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722730"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Obnovení odstraněných řídicích panelů v Azure Portal

Pokud odstraníte _publikovaný_ řídicí panel v Azure Portal, můžete tento řídicí panel obnovit během 90 dnů od odstranění. Pokud řídicí panel není publikovaný, nemůžete ho obnovit a musíte ho znovu sestavit. Další informace o publikování řídicího panelu najdete v tématu [publikování řídicího](azure-portal-dashboard-share-access.md#publish-dashboard)panelu. Pomocí těchto kroků obnovíte publikovaný řídicí panel:

1. V nabídce Azure Portal vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, do které jste řídicí panel publikovali (ve výchozím nastavení se jmenuje **řídicí panely**).

1. V části **Protokol aktivit**rozbalte operaci **Odstranit řídicí panel** . Vyberte kartu **historie změn** a potom vyberte **\<odstranit\>prostředků** .

    ![Snímek obrazovky s kartou Change History](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Vyberte a zkopírujte obsah v levém podokně a pak soubor uložte do textového souboru s příponou _. JSON_ . Portál používá soubor JSON k opětovnému vytvoření řídicího panelu.

    ![Snímek obrazovky s rozdílem v historii změn](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. V nabídce Azure Portal vyberte **řídicí panely**a pak vyberte **nahrát**.

    ![Snímek obrazovky s nahráváním řídicího panelu](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Vyberte soubor JSON, který jste uložili. Portál znovu vytvoří řídicí panel se stejným názvem a prvky jako odstraněný řídicí panel.

1. Vyberte **sdílet** , aby se řídicí panel publikoval a znovu se navázalo příslušné řízení přístupu.

    ![Snímek obrazovky sdílení řídicího panelu](media/recover-shared-deleted-dashboard/dashboard-share.png)
