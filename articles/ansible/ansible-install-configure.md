---
title: Úvodní příručka – Instalace ansible na virtuálních počítačích S Linuxem v Azure
description: V tomto rychlém startu se dozvíte, jak nainstalovat a nakonfigurovat Ansible pro správu prostředků Azure na Ubuntu, CentOS a SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202407"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Úvodní příručka: Instalace ansible na virtuálních počítačích S Linuxem v Azure

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Tento článek ukazuje, jak nakonfigurovat Ansible pro některé z nejběžnějších distribucí Linuxu. Chcete-li nainstalovat ansible na jiné distribuce, upravte nainstalované balíčky pro konkrétní platformu. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Přístup k Linuxu nebo virtuálnímu počítači s Linuxem** – Pokud nemáte počítač s Linuxem, vytvořte si [virtuální počítač s Linuxem](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalace Ansible na virtuálním počítači Azure s Linuxem

Přihlaste se ke svému počítači s Linuxem a výběrem některé z následujících distribucí přejděte k pokynům k instalaci Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

V této části nakonfigurujete CentOS používat Ansible.

1. Otevřete okno terminálu.

1. Chcete-li nainstalovat požadované balíčky pro moduly Sady Azure Python SDK, zadejte následující příkaz:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Chcete-li nainstalovat požadované balíčky Ansible, zadejte následující příkaz:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Vytvořte přihlašovací údaje Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

V této části nakonfigurujete Ubuntu pro použití Ansible.

1. Otevřete okno terminálu.

1. Chcete-li nainstalovat požadované balíčky pro moduly Sady Azure Python SDK, zadejte následující příkaz:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Chcete-li nainstalovat požadované balíčky Ansible, zadejte následující příkaz:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Vytvořte přihlašovací údaje Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

V této části nakonfigurujete SLES používat Ansible.

1. Otevřete okno terminálu.

1. Chcete-li nainstalovat požadované balíčky pro moduly Sady Azure Python SDK, zadejte následující příkaz:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Chcete-li nainstalovat požadované balíčky Ansible, zadejte následující příkaz:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Chcete-li odebrat konfliktní kryptografický balíček pythonu, zadejte následující příkaz:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Vytvořte přihlašovací údaje Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure

Chcete-li nakonfigurovat pověření Ansible, potřebujete následující informace:

* ID předplatného Azure 
* Hodnoty instančního objektu

Pokud používáte Ansible Tower nebo Jenkins, deklarujte hodnoty instančního objektu jako proměnné prostředí.

Nakonfigurujte pověření Ansible pomocí jedné z následujících technik:

- [Vytvoření souboru s přihlašovacími údaji Ansible](#file-credentials)
- [Použití proměnných prostředí Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Vytvoření souboru s přihlašovacími údaji Ansible

V této části vytvoříte soubor místních pověření, který poskytne pověření ansible. 

Další informace o definování ansible pověření, najdete [v tématu poskytování přihlašovacích údajů pro moduly Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Pro vývojové prostředí vytvořte `credentials` soubor pojmenovaný na hostitelském virtuálním počítači:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Do souboru vložte následující řádky. Nahraďte zástupné symboly hodnotami instančního objektu.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Uložte soubor a zavřete ho.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Použití proměnných prostředí Ansible

V této části exportujete hodnoty zaregistrovaný chod služby a nakonfigurujete pověření Ansible.

1. Otevřete okno terminálu.

1. Export hodnot hlavního povinného servisu:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Ověření konfigurace

Chcete-li ověřit úspěšnou konfiguraci, použijte Ansible k vytvoření skupiny prostředků Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Úvodní příručka: Konfigurace virtuálního počítače s Linuxem v Azure pomocí Ansible](./ansible-create-vm.md)