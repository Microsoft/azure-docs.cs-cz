---
title: Instalace Ansible na virtuálních počítačích Azure
description: Zjistěte, jak nainstalovat a nakonfigurovat Ansible pro správu prostředků Azure v Ubuntu, CentOS a SLES.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 9ad18397a3463fc845692c79b5e1f817d0912a8e
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43663663"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Instalace Ansible na virtuálních počítačích Azure

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Pomocí Ansible můžete spravovat virtuální počítače v Azure stejně jako jakékoli jiné prostředky. Tento článek podrobně popisuje, jak nainstalovat Ansible a požadované moduly Azure Python SDK pro několik nejběžnějších distribucí Linuxu. V ostatních distribucích můžete Ansible nainstalovat tak, že upravíte nainstalované balíčky, aby odpovídaly vaší konkrétní platformě. Abyste mohli prostředky Azure vytvářet bezpečným způsobem, naučíte se také vytvořit a definovat přihlašovací údaje, které bude Ansible používat. Seznam dalších nástrojů dostupných ve službě Cloud Shell najdete v tématu [Funkce a nástroje pro Bash ve službě Azure Cloud Shell](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Přístup k Linuxu nebo virtuálnímu počítači s Linuxem** – Pokud nemáte počítač s Linuxem, vytvořte si [virtuální počítač s Linuxem](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Instanční objekt Azure:** Postupujte podle pokynů v části **Vytvoření instančního objektu** v článku [Vytvoření instančního objektu Azure pomocí Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Poznamenejte si hodnoty **appId**, **displayName**, **password** a **tenant**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalace Ansible na virtuálním počítači Azure s Linuxem

Přihlaste se ke svému počítači s Linuxem a výběrem některé z následujících distribucí přejděte k pokynům k instalaci Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Nainstalujte požadované balíčky pro moduly Azure Python SDK a Ansible zadáním následujících příkazů v okně terminálu nebo prostředí Bash:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Postupujte podle pokynů uvedených v části [Vytvoření přihlašovacích údajů Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Nainstalujte požadované balíčky pro moduly Azure Python SDK a Ansible zadáním následujících příkazů v okně terminálu nebo prostředí Bash:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Postupujte podle pokynů uvedených v části [Vytvoření přihlašovacích údajů Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Nainstalujte požadované balíčky pro moduly Azure Python SDK a Ansible zadáním následujících příkazů v okně terminálu nebo prostředí Bash:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Postupujte podle pokynů uvedených v části [Vytvoření přihlašovacích údajů Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure

Kombinace ID předplatného a informací vrácených při vytváření instančního objektu slouží ke konfiguraci přihlašovacích údajů Ansible jedním ze dvou způsobů:

- [Vytvoření souboru s přihlašovacími údaji Ansible](#file-credentials)
- [Použití proměnných prostředí Ansible](#env-credentials)

Pokud plánujete používat nástroje, jako je Ansible Tower nebo Jenkins, budete muset využít možnost deklarovat hodnoty instančního objektu jako proměnné prostředí.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Vytvoření souboru s přihlašovacími údaji Ansible

Tato část vysvětluje, jak vytvořit místní soubor s přihlašovacími údaji, který bude poskytovat přihlašovací údaje Ansible. Další informace o definování přihlašovacích údajů Ansible najdete v článku věnovaném [poskytování přihlašovacích údajů modulům Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

V případě vývojového prostředí následujícím způsobem vytvořte pro Ansible na virtuálním počítači hostitele soubor *credentials*:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Do souboru *credentials* vložte následující řádky a zástupné hodnoty nahraďte informacemi, které jste použili při vytváření instančního objektu.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Uložte soubor a zavřete ho.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Použití proměnných prostředí Ansible

Tato část vysvětluje, jak nakonfigurovat přihlašovací údaje Ansible tím, že je exportujete jako proměnné prostředí.

V okně terminálu nebo prostředí Bash zadejte následující příkazy:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Ověření konfigurace
Pokud chcete ověřit úspěšnou konfiguraci, můžete teď pomocí Ansible vytvořit skupinu prostředků.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Použití Ansible k vytvoření virtuálního počítače s Linuxem v Azure](./ansible-create-vm.md)