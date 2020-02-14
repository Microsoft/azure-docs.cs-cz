---
title: Rychlý Start – instalace Ansible na virtuální počítače se systémem Linux v Azure
description: V tomto rychlém startu se dozvíte, jak nainstalovat a nakonfigurovat Ansible pro správu prostředků Azure na Ubuntu, CentOS a SLES.
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202407"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Rychlý Start: instalace Ansible na virtuální počítače se systémem Linux v Azure

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Tento článek popisuje, jak nakonfigurovat Ansible pro některé z nejběžnějších distribuce pro Linux. Pokud chcete nainstalovat Ansible na jiné distribuce, upravte nainstalované balíčky pro konkrétní platformu. 

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Přístup k Linuxu nebo virtuálnímu počítači s Linuxem** – Pokud nemáte počítač s Linuxem, vytvořte si [virtuální počítač s Linuxem](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalace Ansible na virtuálním počítači Azure s Linuxem

Přihlaste se ke svému počítači s Linuxem a výběrem některé z následujících distribucí přejděte k pokynům k instalaci Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

V této části nakonfigurujete CentOS tak, aby používal Ansible.

1. Otevřete okno terminálu.

1. Zadejte následující příkaz pro instalaci požadovaných balíčků pro moduly Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Zadejte následující příkaz pro instalaci požadovaných balíčků Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Vytvořte přihlašovací údaje Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

V této části nakonfigurujete Ubuntu tak, aby používal Ansible.

1. Otevřete okno terminálu.

1. Zadejte následující příkaz pro instalaci požadovaných balíčků pro moduly Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Zadejte následující příkaz pro instalaci požadovaných balíčků Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Vytvořte přihlašovací údaje Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

V této části nakonfigurujete SLES tak, aby používal Ansible.

1. Otevřete okno terminálu.

1. Zadejte následující příkaz pro instalaci požadovaných balíčků pro moduly Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Zadejte následující příkaz pro instalaci požadovaných balíčků Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Zadáním následujícího příkazu odeberte konfliktní balíček kryptografie v Pythonu:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Vytvořte přihlašovací údaje Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure

K nakonfigurování přihlašovacích údajů Ansible potřebujete následující informace:

* ID vašeho předplatného Azure 
* Hodnoty hlavního objektu služby

Pokud používáte Ansible Tower nebo Jenkinse, deklarujte hlavní hodnoty služby jako proměnné prostředí.

Přihlašovací údaje Ansible nakonfigurujte pomocí některého z následujících postupů:

- [Vytvoření souboru s přihlašovacími údaji Ansible](#file-credentials)
- [Použití proměnných prostředí Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Vytvoření souboru s přihlašovacími údaji Ansible

V této části vytvoříte místní soubor s přihlašovacími údaji pro zadání přihlašovacích údajů do Ansible. 

Další informace o definování přihlašovacích údajů pro Ansible najdete v tématu [Poskytnutí přihlašovacích údajů do modulů Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Pro vývojové prostředí vytvořte soubor s názvem `credentials` na virtuálním počítači hostitele:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Do souboru vložte následující řádky. Zástupné symboly nahraďte hodnotami objektu služby.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Uložte soubor a zavřete ho.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Použití proměnných prostředí Ansible

V této části vyexportujete hodnoty instančního objektu a nakonfigurujete přihlašovací údaje Ansible.

1. Otevřete okno terminálu.

1. Exportujte hlavní hodnoty služby:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Ověření konfigurace

Pokud chcete ověřit úspěšnou konfiguraci, pomocí Ansible vytvořte skupinu prostředků Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Rychlý Start: konfigurace virtuálního počítače se systémem Linux v Azure pomocí Ansible](./ansible-create-vm.md)