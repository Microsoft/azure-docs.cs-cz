---
ms.openlocfilehash: 6b1df49ed0f2c543706d7584a1ade5dc5554a564
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750026"
---
Tento kurz vyžaduje následující prostředky Azure:

* IoT Hub
* Účet úložiště
* Účet Azure Media Services
* Virtuální počítač Linux v Azure s nainstalovaným [modulem runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md)

Pro tento rychlý Start doporučujeme, abyste k nasazení požadovaných prostředků ve vašem předplatném Azure použili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . To můžete provést pomocí těchto kroků:

1. Otevřete [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Pokud používáte Cloud Shell poprvé, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů. Vyberte **vytvořit úložiště** a vytvořte účet úložiště pro informace o cloud Shell relaci. Tento účet úložiště je oddělený od účtu, který vytvoří skript pro použití s vaším účtem Azure Media Services.
1. V rozevírací nabídce na levé straně okna Cloud Shell vyberte **bash** jako své prostředí.

    ![Výběr prostředí](../../../media/quickstarts/env-selector.png)
1. Spusťte následující příkaz.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Po úspěšném dokončení skriptu byste měli vidět všechny požadované prostředky v rámci vašeho předplatného. Skript nastaví celkem 12 prostředků:
    1. **Koncový bod streamování** – to vám pomůže při přehrávání zaznamenaného prostředku AMS.
    1. **Virtuální počítač** – jedná se o virtuální počítač, který bude fungovat jako hraniční zařízení.
    1. **Disk** – jedná se o disk úložiště, který je připojený k virtuálnímu počítači pro ukládání médií a artefaktů.
    1. **Skupina zabezpečení sítě** – slouží k filtrování síťového provozu do a z prostředků Azure ve službě Azure Virtual Network.
    1. **Síťové rozhraní** – umožňuje virtuálnímu počítači Azure komunikovat s internetem, Azure a dalšími prostředky.
    1. **Připojení bastionu** – to vám umožní připojit se k virtuálnímu počítači pomocí prohlížeče a Azure Portal.
    1. **Veřejná IP adresa** – umožňuje prostředkům Azure komunikovat s internetem a veřejně přístupnými službami Azure.
    1. **Virtuální síť** – Tato možnost umožňuje mezi sebou zabezpečit mnoho typů prostředků Azure, jako je třeba virtuální počítač, aby bylo možné bezpečně komunikovat mezi sebou, internetem a místními sítěmi. Další informace o [virtuálních sítích](../../../../../virtual-network/virtual-networks-overview.md)
    1. **IoT Hub** – slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT, IoT Edge moduly a zařízeními, která spravuje.
    1. **Účet Media Service** – to pomáhá se správou a streamování mediálního obsahu v Azure.
    1. **Účet úložiště** – musíte mít jeden primární účet úložiště a k vašemu Media Services účtu můžete mít k dispozici libovolný počet sekundárních účtů úložiště. Další informace najdete v tématu [účty Azure Storage s účty Azure Media Services](../../../../latest/storage-account-concept.md).
    1. **Registr kontejnerů** – to pomáhá ukládat a spravovat vaše soukromé image kontejnerů Docker a související artefakty.

1. Po dokončení skriptu vyberte složené závorky a vystavte strukturu složek. V adresáři *~/clouddrive/lva-Sample* se zobrazí několik souborů. V tomto rychlém startu jsou důležité tyto:

     * ***~/clouddrive/lva-Sample/Edge-Deployment/.env*** – tento soubor obsahuje vlastnosti, které Visual Studio Code používá k nasazení modulů do hraničního zařízení.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code používá tento soubor ke spuštění ukázkového kódu.
     
    Tyto soubory budete potřebovat při nastavení vývojového prostředí v Visual Studio Code v další části. Můžete je teď chtít zkopírovat do místního souboru.
    
    ![Nastavení aplikace](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Pokud narazíte na problémy s prostředky Azure, které se vytvoří, přečtěte si náš **[Průvodce odstraňováním potíží](../../../troubleshoot-how-to.md#common-error-resolutions)** a vyřešte některé běžně zjištěné problémy.