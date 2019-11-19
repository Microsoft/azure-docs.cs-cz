---
title: Rychlý Start – spuštění Ansible playbooky přes bash v Azure Cloud Shell
description: V tomto rychlém startu se dozvíte, jak provádět různé Ansible úlohy pomocí bash v Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: d04708be82a704c2ce20a928380fca1d325493da
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155970"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Rychlý Start: spuštění Ansible playbooky přes bash v Azure Cloud Shell

Azure Cloud Shell je interaktivní prostředí přístupné pro prohlížeč, které slouží ke správě prostředků Azure. Cloud Shell vám umožní použít buď příkaz bash, nebo příkazový řádek prostředí PowerShell. V tomto článku použijete bash v rámci Azure Cloud Shell ke spuštění Ansible PlayBook.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurace Azure Cloud Shell** – pokud s Azure Cloud Shell začínáte, přečtěte si téma [rychlý Start pro bash v Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Při přihlášení k Cloud Shell se Ansible ověřuje pomocí Azure za účelem správy infrastruktury bez jakékoli další konfigurace. 

Pokud pracujete s více předplatnými, určete odběr Ansible pomocí exportu proměnné prostředí `AZURE_SUBSCRIPTION_ID`. 

Pokud chcete zobrazit seznam všech předplatných Azure, spusťte následující příkaz:

```azurecli-interactive
az account list
```

Pomocí ID předplatného Azure nastavte `AZURE_SUBSCRIPTION_ID` následujícím způsobem:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Ověření konfigurace
Pokud chcete ověřit úspěšnou konfiguraci, pomocí Ansible vytvořte skupinu prostředků Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Rychlý Start: konfigurace virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)