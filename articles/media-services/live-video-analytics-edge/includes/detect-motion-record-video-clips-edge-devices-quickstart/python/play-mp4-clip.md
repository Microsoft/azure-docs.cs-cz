---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682186"
---
Soubory MP4 jsou zapsány do adresáře na hraničním zařízení, které jste nakonfigurovali v souboru *. env* pomocí klíče OUTPUT_VIDEO_FOLDER_ON_DEVICE. Pokud jste použili výchozí hodnotu, výsledky by se měly nacházet ve složce */var/Media/* .

Přehrání klipu MP4:

1. Přejít do skupiny prostředků, najděte virtuální počítač a pak se připojte pomocí Azure bastionu.

    ![Skupina prostředků](../../../media/quickstarts/resource-group.png)
    
    ![Virtuální počítač](../../../media/quickstarts/virtual-machine.png)
1. Přihlaste se pomocí přihlašovacích údajů, které jste vygenerovali při [nastavování prostředků Azure](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Na příkazovém řádku přejdete do odpovídajícího adresáře. Výchozí umístění je */var/Media*. Měli byste vidět soubory MP4 v adresáři.

    ![Výstup](../../../media/quickstarts/samples-output.png) 

1. Pomocí [zabezpečeného kopírování (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) zkopírujte soubory do místního počítače. 
1. Hrajte soubory pomocí [VLC Media Playeru](https://www.videolan.org/vlc/) nebo jiného přehrávače MP4.
