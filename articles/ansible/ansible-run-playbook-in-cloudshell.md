---
title: Spuštění Ansible bash ve službě Azure Cloud Shell
description: Zjistěte, jak provádět různé úlohy Ansible bash ve službě Azure Cloud Shell
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: quickstart
ms.openlocfilehash: 37426f1cdc51734c28b2c9739e26ad35f08e606f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791388"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Spuštění Ansible bash ve službě Azure Cloud Shell

V tomto kurzu se dozvíte, jak nakonfigurovat předplatné služby Azure jako váš pracovní prostor Ansible pomocí prostředí Bash ve službě Cloud Shell. 

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurace služby Azure Cloud Shell** – Pokud se službou Azure Cloud Shell teprve začínáte, v článku [Rychlý start pro Bash ve službě Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) se dozvíte, jak spustit a nakonfigurovat Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Když jste přihlášení ke službě Cloud Shell, ověřuje Ansible v Azure ke správě infrastruktury bez další konfigurace. Pokud máte více než jedno předplatné, které předplatné Ansible by měli spolupracovat se exportováním můžete `AZURE_SUBSCRIPTION_ID` proměnné prostředí. Chcete-li vypsat všechna vaše předplatná Azure, spusťte následující příkaz:

```azurecli-interactive
az account list
```

Použití **id** předplatného, se kterým chcete pracovat, nastavte **AZURE_SUBSCRIPTION_ID** následujícím způsobem:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Ověření konfigurace
Pokud chcete ověřit úspěšné konfiguraci, použijte Ansible k vytvoření skupiny prostředků.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Vytvoření základního virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)