---
title: Instalace a konfigurace Ansible pro použití s virtuálními počítači Azure | Microsoft Docs
description: Zjistěte, jak nainstalovat a nakonfigurovat Ansible pro správu prostředků Azure na SLES, Ubuntu a CentOS
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 262c72968ddb5985e0217bced1b83af6fdb7c03c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052602"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalace a konfigurace Ansible ke správě virtuálních počítačů v Azure

Ansible umožňuje automatizovat nasazení a konfigurace prostředků ve vašem prostředí. Ansible můžete použít ke správě virtuálních počítačů (VM) v Azure, stejně jako jiný prostředek. Tento článek podrobně popisuje postup instalace Ansible a požadované moduly Azure Python SDK pro některé z nejběžnějších distribucích systému Linux. Ansible můžete nainstalovat na jiné distribucích úpravou nainstalované balíčky podle vaší konkrétní platformu. Vytváření prostředků Azure zabezpečeným způsobem, můžete také zjistěte, jak vytvořit a definovat přihlašovací údaje pro Ansible používat.

Další možnosti instalace a kroky pro další platformy najdete v tématu [Průvodce instalací Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Nainstalujte Ansible

Jedním z nejjednodušších způsobů, jak používat Ansible se službou Azure je s prostředí cloudu Azure, prostředí shell založené na prohlížeči pro správu a vývoj prostředků Azure. Ansible je předinstalován v prostředí cloudu, můžete přeskočit pokyny o tom, jak nainstalovat Ansible a přejděte na [přihlašovací údaje Azure vytvořit](#create-azure-credentials). Seznam dalších nástrojů, které jsou k dispozici také v prostředí cloudu najdete v tématu [funkce a nástroje pro Bash v prostředí cloudu Azure](../../cloud-shell/features.md#tools).

Následující pokyny popisují, jak vytvořit virtuální počítač s Linuxem pro různé distribucích a pak nainstalujte Ansible. Pokud nepotřebujete k vytvoření virtuálního počítače s Linuxem, přeskočte tento první krok k vytvoření skupiny prostředků Azure. Pokud potřebujete k vytvoření virtuálního počítače, nejdříve vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nyní vyberte jednu z následujících distribucích pokyny o tom, jak v případě potřeby vytvořte virtuální počítač a pak nainstalujte Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

V případě potřeby vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH pro virtuální počítač pomocí `publicIpAddress` uvedeno ve výstupu z virtuálního počítače vytvořit operace:

```bash
ssh azureuser@<publicIpAddress>
```

Na vašem virtuálním počítači nainstalujte požadované balíčky pro moduly Azure Python SDK a Ansible následujícím způsobem:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Nyní se přesunout na [přihlašovací údaje Azure vytvořit](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

V případě potřeby vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH pro virtuální počítač pomocí `publicIpAddress` uvedeno ve výstupu z virtuálního počítače vytvořit operace:

```bash
ssh azureuser@<publicIpAddress>
```

Na vašem virtuálním počítači nainstalujte požadované balíčky pro moduly Azure Python SDK a Ansible následujícím způsobem:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Nyní se přesunout na [přihlašovací údaje Azure vytvořit](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

V případě potřeby vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH pro virtuální počítač pomocí `publicIpAddress` uvedeno ve výstupu z virtuálního počítače vytvořit operace:

```bash
ssh azureuser@<publicIpAddress>
```

Na vašem virtuálním počítači nainstalujte požadované balíčky pro moduly Azure Python SDK a Ansible následujícím způsobem:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Nyní se přesunout na [přihlašovací údaje Azure vytvořit](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Vytvořit přihlašovací údaje Azure

Ansible komunikuje se službou Azure pomocí uživatelského jména a hesla nebo hlavní název služby. Objektu zabezpečení služby Azure je identita zabezpečení, která můžete použít s aplikací, služeb a automatizace nástroje, například Ansible. Můžete řídit a definovat oprávnění, jaké operace objektu služby můžete provádět v Azure. Pokud chcete zvýšit zabezpečení přes právě poskytnutí uživatelského jména a hesla, tento příklad vytvoří základní služby hlavní.

Na hostitelském počítači nebo v prostředí cloudu Azure, vytvořit službu objektu zabezpečení pomocí [az ad sp vytvořit pro rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Přihlašovací údaje, které potřebuje Ansible se zobrazují na obrazovce:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Příklad výstupu z předchozích příkazů vypadá takto:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

K ověření do Azure, musíte také získat ID vašeho předplatného Azure pomocí [az účet zobrazit](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

V dalším kroku použijete výstup z těchto dvou příkazů.

## <a name="create-ansible-credentials-file"></a>Vytvořte soubor s přihlašovacími údaji Ansible

K zadání přihlašovacích údajů k Ansible, můžete definovat proměnné prostředí nebo vytvoření přihlašovacích údajů místního souboru. Další informace o tom, jak definovat Ansible pověření najdete v tématu [poskytování pověření moduly Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Vývojové prostředí, vytvářet *pověření* souboru Ansible na hostiteli virtuálního počítače. Vytvořte soubor přihlašovacích údajů na virtuálním počítači, kam jste nainstalovali Ansible v předchozím kroku:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*Pověření* samotný soubor kombinuje ID předplatného s výstupem vytvoření objektu služby. Výstup z předchozí [az ad sp vytvořit pro rbac](/cli/azure/ad/sp#create-for-rbac) příkaz je stejný jako potřebné pro *client_id*, *tajný klíč*, a *klienta*. Následující příklad *pověření* soubor znázorňuje tyto hodnoty odpovídající předchozí výstup. Zadejte vlastní hodnoty následujícím způsobem:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Uložte soubor a zavřete ho.

## <a name="use-ansible-environment-variables"></a>Použití proměnných prostředí Ansible

Pokud se chystáte použít nástroje, například Ansible věž nebo volaných, budete muset definovat proměnné prostředí. Tento krok můžete přeskočí, pokud právě se chystáte použít Ansible klienta a vytvoří soubor přihlašovacích údajů služby Azure v předchozím kroku. Proměnné prostředí definovat stejné informace jako soubor přihlašovacích údajů služby Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Další postup

Nyní máte Ansible a požadované moduly Azure Python SDK nainstalovat a přihlašovací údaje definované pro Ansible používat. Zjistěte, jak [vytvoření virtuálního počítače s Ansible](ansible-create-vm.md). Můžete si také přečíst postup [kompletní virtuální počítače Azure můžete vytvořit a podpůrné prostředky s Ansible](ansible-create-complete-vm.md).
