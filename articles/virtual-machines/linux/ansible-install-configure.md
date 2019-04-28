---
title: Rychlý start – Ansible instalace na virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak nainstalovat a nakonfigurovat pro správu prostředků Azure na Ubuntu, CentOS a SLES Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: e7a664be48d1e26e09faf4f330fd1267ec003315
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760743"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Rychlý start: Ansible nainstalovat na virtuální počítače s Linuxem v Azure

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Tento článek popisuje postup konfigurace Ansible pro některé nejběžnější distribuce Linuxu. Pokud chcete nainstalovat další distribuce Ansible, upravte nainstalovaných balíčků pro konkrétní platformu. 

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Přístup k Linuxu nebo virtuálnímu počítači s Linuxem** – Pokud nemáte počítač s Linuxem, vytvořte si [virtuální počítač s Linuxem](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalace Ansible na virtuálním počítači Azure s Linuxem

Přihlaste se ke svému počítači s Linuxem a výběrem některé z následujících distribucí přejděte k pokynům k instalaci Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

V této části nakonfigurujete CentOS použít Ansible.

1. Otevřete okno terminálu.

1. Zadejte následující příkaz k instalaci požadované balíčky pro moduly sady Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Zadejte následující příkaz k instalaci požadovaných balíčků Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Vytvoření přihlašovacích údajů Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

V této části nakonfigurujete Ubuntu pomocí Ansible.

1. Otevřete okno terminálu.

1. Zadejte následující příkaz k instalaci požadované balíčky pro moduly sady Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Zadejte následující příkaz k instalaci požadovaných balíčků Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Vytvoření přihlašovacích údajů Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

V této části nakonfigurujete SLES použít Ansible.

1. Otevřete okno terminálu.

1. Zadejte následující příkaz k instalaci požadované balíčky pro moduly sady Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Zadejte následující příkaz k instalaci požadovaných balíčků Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Zadejte následující příkaz k odebrání konfliktní balíček Python cryptography:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Vytvoření přihlašovacích údajů Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure

Pokud chcete nakonfigurovat přihlašovací údaje Ansible, budete potřebovat následující informace:

* ID vašeho předplatného Azure 
* Hodnoty instančního objektu služby

Pokud používáte Ansible Tower nebo Jenkinse, deklarujte hodnoty instančního objektu služby jako proměnné prostředí.

Nakonfigurujte Ansible pověření pomocí jedné z následujících postupů:

- [Vytvoření souboru s přihlašovacími údaji Ansible](#file-credentials)
- [Použití proměnných prostředí Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Vytvoření souboru s přihlašovacími údaji Ansible

V této části vytvoříte soubor místní přihlašovací údaje k zadání přihlašovacích údajů Ansible. 

Další informace o definování Ansible přihlašovací údaje, najdete v části [poskytuje přihlašovací údaje pro moduly Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Pro vývojové prostředí vytvořte soubor s názvem `credentials` na hostitele virtuálního počítače:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Vložte následující řádky do souboru. Nahraďte zástupné symboly hodnotami instančního objektu služby.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Uložte soubor a zavřete ho.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Použití proměnných prostředí Ansible

V této části exportujete hodnoty instančního objektu služby tak, aby nakonfigurujete své přihlašovací údaje Ansible.

1. Otevřete okno terminálu.

1. Exportujte hodnoty instančního objektu služby:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Zkontrolujte konfiguraci

Pokud chcete ověřit úspěšné konfiguraci, použijte Ansible k vytvoření skupiny prostředků Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Rychlé zprovoznění: Konfigurace virtuálního počítače s Linuxem v Azure pomocí Ansible](./ansible-create-vm.md)