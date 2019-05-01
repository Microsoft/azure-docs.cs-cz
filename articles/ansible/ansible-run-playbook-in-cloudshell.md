---
title: Rychlý start – spouštění runbooků Ansible prostřednictvím prostředí Bash ve službě Azure Cloud Shell | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak provádět různé úlohy Ansible bash ve službě Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: a0d0544601bfc6ce0c7ef642f576ed780c8642ba
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720213"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Rychlý start: Spouštění runbooků Ansible prostřednictvím prostředí Bash ve službě Azure Cloud Shell

Azure Cloud Shell je interaktivní, prohlížeč přístupné prostředí pro správu prostředků Azure. Cloud Shell poskytuje umožňuje k použití rozhraní příkazového řádku Bashe nebo Powershellu. V tomto článku použít Bash v Azure Cloud Shell spustit Ansible playbook.

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurace Azure Cloud Shell** – Pokud je pro vás nová do služby Azure Cloud Shell, naleznete v tématu [rychlý start pro Bash ve službě Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Když jste přihlášení ke službě Cloud Shell, ověřuje Ansible v Azure ke správě infrastruktury bez další konfigurace. 

Při práci s více předplatnými, zadejte předplatné Ansible používá tak, že vyexportujete `AZURE_SUBSCRIPTION_ID` proměnné prostředí. 

Chcete-li vypsat všechna vaše předplatná Azure, spusťte následující příkaz:

```azurecli-interactive
az account list
```

Pomocí ID předplatného Azure, nastavte `AZURE_SUBSCRIPTION_ID` následujícím způsobem:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Ověření konfigurace
Pokud chcete ověřit úspěšné konfiguraci, použijte Ansible k vytvoření skupiny prostředků Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Rychlé zprovoznění: Konfigurace virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)