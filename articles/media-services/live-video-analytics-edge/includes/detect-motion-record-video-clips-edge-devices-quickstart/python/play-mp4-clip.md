---
ms.openlocfilehash: dfb887004cd29b5bd9f1d9886b7dfa5f43c83dbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99532014"
---
Soubory MP4 jsou zapsány do adresáře na hraničním zařízení, které jste nakonfigurovali v souboru *. env* pomocí klíče VIDEO_OUTPUT_FOLDER_ON_DEVICE. Pokud jste použili výchozí hodnotu, výsledky by se měly nacházet ve složce */var/Media/* .

Přehrání klipu MP4:

1. Přejít do skupiny prostředků, najděte virtuální počítač a pak se připojte pomocí Azure bastionu.

    ![Skupina prostředků](../../../media/quickstarts/resource-group.png)
    
    ![Virtuální počítač](../../../media/quickstarts/virtual-machine.png)
1. Přihlaste se pomocí přihlašovacích údajů, které jste vygenerovali při [nastavování prostředků Azure](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Na příkazovém řádku přejdete do odpovídajícího adresáře. Výchozí umístění je */var/Media*. Měli byste vidět soubory MP4 v adresáři.

    ![Výstup](../../../media/quickstarts/samples-output.png) 

1. Pomocí [zabezpečeného kopírování (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) zkopírujte soubory do místního počítače. 
1. Hrajte soubory pomocí [VLC Media Playeru](https://www.videolan.org/vlc/) nebo jiného přehrávače MP4.
