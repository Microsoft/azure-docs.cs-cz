---
title: Spuštění Ansible bash ve službě Azure Cloud Shell
description: Zjistěte, jak provádět různé úlohy Ansible bash ve službě Azure Cloud Shell
ms.service: ansible
keywords: ansible v azure, devops, bash, cloud shell, playbooku, prostředí bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 9928f646905dd0da4b15166ec55e5d8a183cb210
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054409"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Spuštění Ansible bash ve službě Azure Cloud Shell

V tomto kurzu se dozvíte, jak nakonfigurovat předplatné služby Azure jako váš pracovní prostor Ansible pomocí prostředí Bash ve službě Cloud Shell. 

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurace Azure Cloud Shell** – Pokud jste novým uživatelem Azure Cloud Shell, článku, [rychlý start pro Bash ve službě Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), ukazuje, jak spustit a nakonfigurovat Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfigurace automatického přihlašovacích údajů

Když jste přihlášení ke službě Cloud Shell, ověřuje Ansible v Azure ke správě infrastruktury bez další konfigurace. Pokud máte více než jedno předplatné, které předplatné Ansible by měli spolupracovat se exportováním můžete `AZURE_SUBSCRIPTION_ID` proměnné prostředí. Chcete-li vypsat všechna vaše předplatná Azure, spusťte následující příkaz:

```azurecli-interactive
az account list
```

Použití **id** předplatného, se kterým chcete pracovat, nastavte **AZURE_SUBSCRIPTION_ID** následujícím způsobem:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Zkontrolujte konfiguraci
Pokud chcete ověřit úspěšné konfiguraci, použijte Ansible k vytvoření skupiny prostředků.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Vytvoření základního virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)