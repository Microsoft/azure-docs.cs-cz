---
title: Spustit Azure IoT Edge v Virtual Machines Ubuntu | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge Ubuntu 18,04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1cd89f3f772effce4997fb69b37858ce2077c1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201090"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Spuštění Azure IoT Edge v Ubuntu Virtual Machines

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku.

Další informace o tom, jak modul runtime IoT Edge funguje a jaké součásti jsou zahrnuté, najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro nasazení virtuálního počítače s Ubuntu 18,04 LTS s nainstalovaným modulem runtime Azure IoT Edge a nakonfigurovaným pomocí předem dodaného připojovacího řetězce zařízení. Nasazení se provádí pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) založené na [cloudu](../virtual-machines/linux/using-cloud-init.md
) , která je udržovaná v úložišti projektu [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .

Při prvním spuštění nainstaluje virtuální počítač Ubuntu 18,04 LTS [nejnovější verzi modulu Azure IoT Edge runtime přes Cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Také nastaví dodaný připojovací řetězec před spuštěním modulu runtime, který umožňuje snadnou konfiguraci a připojení zařízení IoT Edge, aniž by bylo nutné spouštět relaci SSH nebo vzdálené plochy.

>[!NOTE]
>Šablona použitá pro tento článek nainstaluje IoT Edge verze 1,1.

## <a name="deploy-using-deploy-to-azure-button"></a>Nasazení pomocí tlačítka nasadit do Azure

[Tlačítko nasadit do Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) umožňuje jednodušší nasazení [Azure Resource Manager šablon](../azure-resource-manager/templates/overview.md) udržovaných na GitHubu.  V této části se dozvíte, jak používat tlačítko nasadit do Azure obsažené v úložišti projektů [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .  


1. Nasadíme Azure IoT Edge povolený virtuální počítač Linux pomocí šablony Azure Resource Manager iotedge-VM-Deploy.  Začněte tím, že kliknete na tlačítko níže:

    [![Tlačítko Nasazení do Azure pro iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. V nově otevřeném okně vyplňte dostupná pole formuláře:

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky zobrazující šablonu iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Předplatné**: aktivní předplatné Azure, do kterého se má virtuální počítač nasadit.

    **Skupina prostředků**: existující nebo nově vytvořená skupina prostředků, která bude obsahovat virtuální počítač a jeho přidružené prostředky.

    **Předpona popisku DNS**: požadovaná hodnota, která se používá k vytvoření předpony názvu hostitele virtuálního počítače.

    **Uživatelské jméno správce**: uživatelské jméno, které bude poskytovat oprávnění root pro nasazení.

    **Připojovací řetězec zařízení**: [připojovací řetězec zařízení](./how-to-register-device.md) pro zařízení, které bylo vytvořeno v rámci zamýšleného [IoT Hub](../iot-hub/about-iot-hub.md).

    **Velikost virtuálního** počítače: [Velikost](../cloud-services/cloud-services-sizes-specs.md) virtuálního počítače, který se má nasadit.

    **Verze OS Ubuntu**: verze Ubuntu operačního systému, která se má nainstalovat na základní virtuální počítač.

    **Umístění**: [geografická oblast](https://azure.microsoft.com/global-infrastructure/locations/) , do které se má virtuální počítač nasadit, tato hodnota se nastaví jako výchozí umístění vybrané skupiny prostředků.

    **Typ ověřování**: v závislosti na vaší preferenci vyberte **sshPublicKey** nebo **Password** .

    **Heslo nebo klíč správce**: hodnota veřejného klíče SSH nebo hodnota hesla v závislosti na zvoleném typu ověřování.

    Po vyplnění všech polí zaškrtněte políčko v dolní části stránky, aby se podmínky přijaly, a výběrem **koupit** zahájíte nasazení.

1. Ověřte, že se nasazení úspěšně dokončilo.  Do vybrané skupiny prostředků by měl být nasazený prostředek virtuálního počítače.  Poznamenejte si název počítače, který by měl být ve formátu `vm-0000000000000` . Dále si poznamenejte související **název DNS**, který by měl být ve formátu `<dnsLabelPrefix>`.`<location>`. cloudapp.azure.com.

    **Název DNS** můžete získat v části **Přehled** nově nasazeného virtuálního počítače na webu Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky zobrazující název DNS virtuálního počítače s iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Pokud chcete k tomuto virtuálnímu počítači po instalaci použít SSH, použijte k tomu přidružený **název DNS** pomocí příkazu:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Nasazení z Azure CLI

1. Ujistěte se, že máte nainstalované rozšíření Azure CLI IoT pomocí:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Pokud v počítači používáte rozhraní příkazového řádku Azure CLI, začněte přihlášením:

   ```azurecli-interactive
   az login
   ```

1. Pokud máte více předplatných, vyberte předplatné, které chcete použít:
   1. Zobrazte výpis vašich předplatných:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Zkopírujte pole SubscriptionID pro předplatné, které chcete použít.

   1. Nastavte své pracovní předplatné s ID, které jste zkopírovali:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Vytvořte novou skupinu prostředků (nebo v dalších krocích určete existující):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Vytvořit nový virtuální počítač:

    Pokud chcete použít **AuthenticationType** `password` , přečtěte si níže uvedený příklad:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Chcete-li provést ověření pomocí klíče SSH, můžete tak učinit zadáním **AuthenticationType** `sshPublicKey` a pak v parametru **ADMINPASSWORDORKEY** zadat hodnotu klíče SSH.  Příklad najdete níže.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Ověřte, že se nasazení úspěšně dokončilo.  Do vybrané skupiny prostředků by měl být nasazený prostředek virtuálního počítače.  Poznamenejte si název počítače, který by měl být ve formátu `vm-0000000000000` . Dále si poznamenejte související **název DNS**, který by měl být ve formátu `<dnsLabelPrefix>`.`<location>`. cloudapp.azure.com.

    **Název DNS** se dá získat z výstupu z předchozího kroku ve formátu JSON, v části **výstupy** jako součást **veřejné položky SSH** .  Hodnotu této položky lze použít pro SSH do nově nasazeného počítače.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **Název DNS** se dá taky získat z oddílu **Přehled** nově nasazeného virtuálního počítače v rámci Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky zobrazující název DNS virtuálního počítače s iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Pokud chcete k tomuto virtuálnímu počítači po instalaci použít SSH, použijte k tomu přidružený **název DNS** pomocí příkazu:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Další kroky

Teď, když máte nainstalovanou IoT Edge zařízení s nainstalovaným modulem runtime, můžete [nasadit IoT Edge moduly](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací modulu runtime IoT Edge správně, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Pokud chcete otevřít porty pro přístup k virtuálnímu počítači přes SSH nebo jiná příchozí připojení, přečtěte si dokumentaci k Azure Virtual Machines v tématu [otevření portů a koncových bodů na virtuálním počítači Linux](../virtual-machines/linux/nsg-quickstart.md) .