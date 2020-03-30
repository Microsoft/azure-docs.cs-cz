---
title: Obnovení odstraněného řídicího panelu na webu Azure Portal | Dokumenty společnosti Microsoft
description: Pokud odstraníte publikovaný řídicí panel na webu Azure Portal, můžete řídicí panel obnovit.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133297"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Obnovení odstraněného řídicího panelu na webu Azure Portal

Pokud jste ve veřejném cloudu Azure a odstraníte _publikovaný_ řídicí panel na webu Azure Portal, můžete tento řídicí panel obnovit do 14 dnů od odstranění. Pokud jste v cloudu azure vlády nebo řídicí panel není publikovaný, nemůžete ho obnovit a musíte ho znovu sestavit. Další informace o publikování řídicího panelu naleznete v tématu [Publikování řídicího panelu](azure-portal-dashboard-share-access.md#publish-dashboard). Chcete-li obnovit publikovaný řídicí panel, postupujte takto:

1. V nabídce Portál Azure vyberte **skupiny prostředků**a vyberte skupinu prostředků, do které jste řídicí panel publikovali (ve výchozím nastavení se jmenuje **řídicí panely).**

1. V části **Protokol aktivit**rozbalte operaci Odstranit **řídicí panel.** Vyberte kartu **Změnit historii** a pak ** \<vyberte odstraněný prostředek\>**.

    ![Snímek obrazovky s kartou Historie změn](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Vyberte a zkopírujte obsah levého podokna a uložte jej do textového souboru s příponou _JSON._ Portál používá soubor JSON k opětovnému vytvoření řídicího panelu.

    ![Snímek obrazovky s rozdílem v historii změn](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. V nabídce Portál Azure vyberte **Řídicí panely**a pak vyberte **Nahrát**.

    ![Snímek obrazovky s nahráním řídicího panelu](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Vyberte uložený soubor JSON. Portál znovu vytvoří řídicí panel se stejným názvem a prvky jako odstraněný řídicí panel.

1. Vyberte **Sdílet,** chcete-li řídicí panel publikovat a znovu vytvořit příslušný ovládací prvek přístupu.

    ![Snímek obrazovky se sdílením řídicího panelu](media/recover-shared-deleted-dashboard/dashboard-share.png)
