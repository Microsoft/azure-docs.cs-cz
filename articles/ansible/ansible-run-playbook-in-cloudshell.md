---
title: Úvodní příručka – Spuštění herních příruček Ansible přes Bash v prostředí Azure Cloud Shell
description: V tomto rychlém startu se dozvíte, jak provádět různé ansible úkoly s Bash v Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78247895"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Úvodní příručka: Spusťte ansible playbooky přes Bash v Azure Cloud Shellu

Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je dostupné z webového prohlížeče. Cloud Shell poskytuje umožňuje použít buď Bash nebo PowerShell příkazový řádek. V tomto článku pomocí Bash v rámci Azure Cloud Shell spustit ansible playbook.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurace Azure Cloud Shell** – Pokud jste s Azure Cloud Shell teče tezí, [přečtěte si první postup pro bash v Azure Cloud Shellu](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Při přihlášení do cloudového prostředí se Ansible ověřuje pomocí Azure a spravuje infrastrukturu bez jakékoli další konfigurace. 

Při práci s více odběry, zadejte předplatné Ansible použití exportem `AZURE_SUBSCRIPTION_ID` proměnné prostředí. 

Chcete-li zobrazit seznam všech předplatných Azure, spusťte následující příkaz:

```azurecli-interactive
az account list
```

Pomocí ID předplatného Azure `AZURE_SUBSCRIPTION_ID` nastavte následující:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Ověření konfigurace
Chcete-li ověřit úspěšnou konfiguraci, použijte Ansible k vytvoření skupiny prostředků Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Úvodní příručka: Konfigurace virtuálního počítače v Azure pomocí Ansible](./ansible-create-vm.md)