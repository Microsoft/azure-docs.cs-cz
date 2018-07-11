---
title: Instalace a konfigurace Ansible pro použití s Azure virtual machines | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat pro správu prostředků Azure na Ubuntu, CentOS a SLES Ansible
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930065"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalace a konfigurace Ansible ke správě virtuálních počítačů v Azure

Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí. Ansible můžete použít ke správě virtuálních počítačů (VM) v Azure, stejně jako byste to udělali jiný prostředek. Tento článek podrobně popisuje, jak nainstalovat Ansible a požadované moduly sady Azure Python SDK pro některé nejběžnější distribuce Linuxu. Ansible můžete nainstalovat v jiné distribucích úpravou nainstalované balíčky podle konkrétní platformy. Vytváření prostředků Azure v podobě zabezpečení, se také dozvíte, jak vytvořit a definovat přihlašovací údaje pro Ansible k použití.

Další možnosti instalace a kroky pro další platformy, najdete v článku [Ansible Instalační příručka](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Nainstalujte Ansible

Jedním z nejjednodušších způsobů, jak použít Ansible v Azure je s Azure Cloud Shell, založené na prohlížeči příkazové prostředí ke správě a vývoji prostředků Azure. Ansible je předem nainstalovaných ve službě Cloud Shell, takže nemusíte pokyny o tom, jak nainstalovat Ansible a [přihlašovací údaje Azure vytvořit](#create-azure-credentials). Seznam dalších nástrojů, které jsou také k dispozici ve službě Cloud Shell, naleznete v tématu [funkcí a nástrojů pro Bash ve službě Azure Cloud Shell](../../cloud-shell/features.md#tools).

Následující pokyny ukazují, jak vytvořit virtuální počítač s Linuxem pro různé distribuce a pak nainstalujte Ansible. Pokud není nutné k vytvoření virtuálního počítače s Linuxem, přeskočte tento první krok k vytvoření skupiny prostředků Azure. Pokud potřebujete k vytvoření virtuálního počítače, nejprve vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nyní vyberte jednu z následujících distribucích pokyny o tom, jak v případě potřeby vytvořte virtuální počítač a pak nainstalujte Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

V případě potřeby vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

K virtuálnímu počítači pomocí SSH `publicIpAddress` uvedené ve výstupu z virtuálního počítače operace vytvoření:

```bash
ssh azureuser@<publicIpAddress>
```

Na svém virtuálním počítači nainstalujte požadované balíčky pro moduly sady Azure Python SDK a Ansible následujícím způsobem:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Teď přejděte na [přihlašovací údaje Azure vytvořit](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

V případě potřeby vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

K virtuálnímu počítači pomocí SSH `publicIpAddress` uvedené ve výstupu z virtuálního počítače operace vytvoření:

```bash
ssh azureuser@<publicIpAddress>
```

Na svém virtuálním počítači nainstalujte požadované balíčky pro moduly sady Azure Python SDK a Ansible následujícím způsobem:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Teď přejděte na [přihlašovací údaje Azure vytvořit](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

V případě potřeby vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

K virtuálnímu počítači pomocí SSH `publicIpAddress` uvedené ve výstupu z virtuálního počítače operace vytvoření:

```bash
ssh azureuser@<publicIpAddress>
```

Na svém virtuálním počítači nainstalujte požadované balíčky pro moduly sady Azure Python SDK a Ansible následujícím způsobem:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Teď přejděte na [přihlašovací údaje Azure vytvořit](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Vytvořit přihlašovací údaje Azure

Ansible komunikuje se službou Azure s využitím uživatelského jména a hesla nebo instanční objekt služby. Instanční objekt Azure je identita zabezpečení, který vám pomůže s aplikací, služeb a automatizační nástroje, jako jsou Ansible. Kontrolou a můžete definovat oprávnění, jaké operace lze provádět instanční objekt služby v Azure. Pro zlepšení zabezpečení přes právě a uživatelského jména a hesla, tento příklad vytvoří základní služby instančního objektu.

Na hostitelském počítači nebo ve službě Azure Cloud Shell vytvořte službu objektu zabezpečení pomocí [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Přihlašovací údaje, které potřebuje Ansible se zobrazují na obrazovce:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Příklad výstupu z výše uvedených příkazů vypadá takto:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Pro ověření do Azure, budete potřebovat k získání ID vašeho předplatného Azure pomocí [zobrazit účet az](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Výstup z těchto dvou příkazů použijete v dalším kroku.

## <a name="create-ansible-credentials-file"></a>Vytvořte soubor s přihlašovacími údaji Ansible

K zadání přihlašovacích údajů k Ansible, definovat proměnné prostředí nebo vytvoření souboru místní přihlašovací údaje. Další informace o tom, jak definovat Ansible přihlašovací údaje, najdete v části [poskytuje přihlašovací údaje pro moduly Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Vývojové prostředí, vytvářet *pověření* soubor pro Ansible v hostiteli virtuálního počítače. Vytvořte soubor přihlašovacích údajů na virtuálním počítači, kam jste nainstalovali Ansible v předchozím kroku:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*Pověření* samotný soubor kombinuje ID předplatného s výstupem vytvoření instančního objektu. Výstup z předchozího [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) příkaz je stejné, jako třeba *client_id*, *tajný kód*, a *tenanta*. Následující příklad *pověření* zobrazuje tyto hodnoty odpovídající předchozí výstupní soubor. Zadejte vlastní hodnoty následujícím způsobem:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Uložte soubor a zavřete ho.

## <a name="use-ansible-environment-variables"></a>Použití proměnných prostředí pro Ansible

Pokud se chystáte používat nástroje, jako jsou Ansible Tower nebo Jenkinse, musíte definovat proměnné prostředí. Tento krok můžete přeskočit, pokud se právě se chystáte použít Ansible klienta a vytvoří soubor přihlašovacích údajů Azure v předchozím kroku. Proměnné prostředí definují stejné informace jako soubor přihlašovacích údajů Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Další postup

Teď máte Ansible a požadované moduly sady Azure Python SDK nainstalovat a přihlašovací údaje definované pro Ansible k použití. Zjistěte, jak [vytvoření virtuálního počítače pomocí Ansible](ansible-create-vm.md). Můžete také zjistíte, jak [vytvoření kompletního virtuálního počítače Azure a podpůrné prostředky pomocí Ansible](ansible-create-complete-vm.md).
