---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982416"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Vytvoření virtuálního počítače s podporou Docker

Pro účely testování použijte k přístupu ke službě Azure Container Registry virtuální počítač Ubuntu s podporou Docker. Pokud chcete v registru použít Azure Active Directory ověřování, nainstalujte také rozhraní příkazového [řádku Azure CLI][azure-cli] na virtuálním počítači. Pokud už máte virtuální počítač Azure, přeskočte tento krok vytvoření.

Můžete použít stejnou skupinu prostředků pro váš virtuální počítač a registr kontejneru. Tato instalace zjednodušuje vyčištění na konci, ale nevyžaduje se. Pokud se rozhodnete vytvořit samostatnou skupinu prostředků pro virtuální počítač a virtuální síť, spusťte příkaz [AZ Group Create][az-group-create]. Následující příklad předpokládá, že jste nastavili proměnné prostředí pro název skupiny prostředků a umístění registru:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Teď nasaďte výchozí Ubuntu virtuální počítač Azure pomocí [AZ VM Create][az-vm-create]. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu si poznamenejte `publicIpAddress` zobrazené v Azure CLI. Tato adresa slouží k vytvoření připojení SSH k virtuálnímu počítači.

### <a name="install-docker-on-the-vm"></a>Instalace Docker na virtuálním počítači

Po spuštění virtuálního počítače vytvořte připojení SSH k virtuálnímu počítači. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkazy pro instalaci Docker na virtuálním počítači s Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz, který ověří, jestli je na virtuálním počítači správně spuštěný Docker:

```bash
sudo docker run -it hello-world
```

Výstup:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalace Azure CLI

Podle postupu v části [instalace Azure CLI pomocí apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) nainstalujte rozhraní příkazového řádku Azure do svého virtuálního počítače s Ubuntu. Například:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Ukončete připojení SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group