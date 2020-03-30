---
title: Spouštějte Azure IoT Edge na virtuálních počítačích Ubuntu | Dokumenty společnosti Microsoft
description: Pokyny k nastavení Azure IoT Edge pro virtuální počítače Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349602"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Spouštějte Azure IoT Edge na virtuálních počítačích Ubuntu

Azure IoT Edge runtime je to, co změní zařízení na zařízení IoT Edge. Runtime lze nasadit na zařízení tak malá jako Raspberry Pi nebo velká jako průmyslový server. Jakmile je zařízení nakonfigurované s runtime IoT Edge, můžete do něj začít nasazovat obchodní logiku z cloudu.

Další informace o tom, jak funguje runtime IoT Edge a jaké součásti jsou zahrnuty, najdete [v tématu Principy runtime Azure IoT Edge a jeho architektury](iot-edge-runtime.md).

Tento článek uvádí postup nasazení virtuálního počítače Ubuntu 18.04 LTS s nainstalovanou a nakonfigurovanou runtime Azure IoT Edge pomocí předem zadávaného připojovacího řetězce zařízení. Nasazení se provádí pomocí šablony Azure [Resource Manager](../azure-resource-manager/templates/overview.md) založené na [cloud-init](../virtual-machines/linux/using-cloud-init.md
) udržované v úložišti projektů [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

Při prvním spuštění virtuální počítač Ubuntu 18.04 LTS [nainstaluje nejnovější verzi runtime Azure IoT Edge přes cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Také nastaví dodaný připojovací řetězec před spuštěním běhu, což vám umožní snadno konfigurovat a připojit zařízení IoT Edge bez nutnosti spouštět relaci SSH nebo vzdálené plochy. 

## <a name="deploy-using-deploy-to-azure-button"></a>Nasazení pomocí tlačítka Nasazení do Azure

[Tlačítko Nasazení do Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) umožňuje zjednodušené nasazení šablon Azure Resource [Manager](../azure-resource-manager/templates/overview.md) uchovávaných na GitHubu.  Tato část bude demonstrovat využití tlačítka Nasazení do Azure obsaženého v úložišti projektů [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)  


1. Nasadíme virtuální počítač s Linuxem s podporou Azure IoT Edge pomocí šablony Azure Resource Manager s podporou iotedge-vm.  Chcete-li začít, klikněte na tlačítko níže:

    [![Nasazení do tlačítka Azure pro nasazení iotedge-vm](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. V nově spuštěném okně vyplňte dostupná pole formuláře:

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky se šablonou iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Předplatné:** Aktivní předplatné Azure pro nasazení virtuálního počítače.

    **Skupina prostředků**: Existující nebo nově vytvořená skupina prostředků, která obsahuje virtuální počítač a přidružené prostředky.

    **Předpona popisku DNS**: Požadovaná hodnota podle vašeho výběru, která se používá k předponě názvu hostitele virtuálního počítače.

    **Uživatelské jméno správce**: Uživatelské jméno, které bude při nasazení poskytnuto root oprávnění.

    **Připojovací řetězec zařízení**: [Připojovací řetězec zařízení](how-to-register-device.md) pro zařízení, které bylo vytvořeno v rámci [zamýšleného centra IoT Hub](../iot-hub/about-iot-hub.md).

    **Velikost virtuálního počítače:** [Velikost](../cloud-services/cloud-services-sizes-specs.md) virtuálního počítače, který se má nasadit

    **Ubuntu OS Verze:** Verze Operačního systému Ubuntu, které mají být instalovány na základním virtuálním stroji.

    **Umístění**: [Geografická oblast,](https://azure.microsoft.com/global-infrastructure/locations/) do které se má virtuální počítač nasadit, je výchozí pro umístění vybrané skupiny prostředků.

    **Typ ověřování**: V závislosti na vašich preferencích zvolte **sshPublicKey** nebo **heslo.**

    **Admin Password or Key**: Hodnota veřejného klíče SSH nebo hodnota hesla v závislosti na volbě typu ověřování.

    Po vyplnění všech polí zaškrtněte políčko v dolní části stránky, chcete-li podmínky přijmout, a klepnutím na **tlačítko Nákup** zahájit nasazení.

1. Ověřte, zda bylo nasazení úspěšně dokončeno.  Prostředek virtuálního počítače měl být nasazen do vybrané skupiny prostředků.  Vezměte na vědomí název stroje, mělo `vm-0000000000000`by to být ve formátu . Všimněte si také přidruženého **názvu DNS**, `<dnsLabelPrefix>`který by měl být ve formátu . `<location>`.cloudapp.azure.com.

    **Název DNS** lze získat v části **Přehled** nově nasazeného virtuálního počítače v rámci portálu Azure.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky s názvem DNS iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Pokud chcete SSH do tohoto virtuálního počítači po nastavení, použijte přidružený **název DNS** s příkazem:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Nasazení z Azure CLI

1. Ujistěte se, že jste nainstalovali rozšíření Azure CLI iot s:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Dále pokud používáte Azure CLI na ploše, začněte přihlášením:

   ```azurecli-interactive
   az login
   ```

1. Pokud máte více předplatných, vyberte předplatné, které chcete použít:
   1. Zobrazte výpis vašich předplatných:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Zkopírujte pole SubscriptionID pro předplatné, které chcete použít.

   1. Nastavte pracovní předplatné na ID, které jste zkopírovali:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Vytvořte novou skupinu prostředků (nebo v dalších krocích zadejte existující skupinu):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Vytvoření nového virtuálního počítače:

    Chcete-li použít `password` **authenticationType** of , viz následující příklad:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Chcete-li ověřit pomocí klíče SSH, můžete tak učinit `sshPublicKey`zadáním **authenticationType** of , a pak zadejte hodnotu klíče SSH v parametru **adminPasswordOrKey.**  Příklad najdete níže.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Ověřte, zda bylo nasazení úspěšně dokončeno.  Prostředek virtuálního počítače měl být nasazen do vybrané skupiny prostředků.  Vezměte na vědomí název stroje, mělo `vm-0000000000000`by to být ve formátu . Všimněte si také přidruženého **názvu DNS**, `<dnsLabelPrefix>`který by měl být ve formátu . `<location>`.cloudapp.azure.com.

    **Název DNS** lze získat z výstupu ve formátu JSON předchozího kroku v části výstupy jako součást veřejné **položky** **SSH.**  Hodnotu této položky lze použít pro SSH do nově nasazeného počítače.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **Název DNS** lze také získat z části **Přehled** nově nasazeného virtuálního počítače v rámci portálu Azure.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky s názvem DNS iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Pokud chcete SSH do tohoto virtuálního počítači po nastavení, použijte přidružený **název DNS** s příkazem:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge zřízené s nainstalovaným modulem runtime, můžete [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací runtime IoT Edge správně, podívejte se na stránku [řešení potíží.](troubleshoot.md)

Informace o aktualizaci existující instalace na nejnovější verzi technologie IoT Edge naleznete v [tématu Aktualizace daemonu zabezpečení IoT Edge a runtime](how-to-update-iot-edge.md).

Pokud chcete otevřít porty pro přístup k virtuálnímu počítači prostřednictvím SSH nebo jiných příchozích připojení, podívejte se na dokumentaci k virtuálním počítačům Azure o [otevírání portů a koncových bodů pro virtuální počítač s Linuxem.](../virtual-machines/linux/nsg-quickstart.md)
