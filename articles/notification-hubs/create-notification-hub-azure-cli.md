---
title: Rychlý Start – vytvoření centra oznámení Azure pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: V tomto kurzu se naučíte vytvořit centrum oznámení Azure pomocí Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082448"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rychlý Start: vytvoření centra oznámení Azure pomocí Azure CLI

Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace o této službě najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte centrum oznámení pomocí Azure CLI. První část obsahuje pokyny k vytvoření oboru názvů centra oznámení a informace o zásadách přístupu k dotazům pro tento obor názvů. Druhá část obsahuje postup vytvoření centra oznámení v existujícím oboru názvů.  Naučíte se také, jak vytvořit vlastní zásady přístupu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs vyžaduje verzi rozhraní příkazového řádku Azure 2.0.67 nebo novější. Spusťte `az --version` pro vyhledání verze a závislých knihoven, které jsou nainstalovány. Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Přihlásit se.

   Přihlaste se pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) , pokud používáte místní instalaci rozhraní příkazového řádku.

    ```azurecli-interactive
    az login
    ```

    Proces ověřování dokončíte podle kroků zobrazených v terminálu.

2. Nainstalujte rozšíření Azure CLI.

   Pokud chcete spustit příkazy rozhraní příkazového řádku Azure CLI pro centra oznámení, nainstalujte rozšíření Azure CLI [pro Notification Hubs](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Vytvořte skupinu prostředků.

   Centra oznámení Azure, jako jsou všechny prostředky Azure, musí být nasazená do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

   V tomto rychlém startu vytvořte skupinu prostředků s názvem *spnhubrg* v umístění *eastus* pomocí následujícího příkazu [AZ Group Create](/cli/azure/group#az-group-create) :

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Vytvoření oboru názvů centra oznámení

1. Vytvoření oboru názvů pro vaše centra oznámení

   Obor názvů obsahuje jeden nebo víc Center a název musí být v rámci všech předplatných Azure jedinečný.  Pokud chcete ověřit dostupnost daného oboru názvů služby, použijte příkaz [AZ Notification-hub Namespace-Availability Namespace](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .  Spuštěním příkazu [AZ Notification-hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) vytvořte obor názvů.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Vypíše klíče a připojovací řetězce pro zásady přístupu k oboru názvů.

   Zásady přístupu s názvem **RootManageSharedAccessKey** se automaticky vytvoří pro nový obor názvů.  Každé zásady přístupu mají dvě sady klíčů a připojovací řetězce.  Chcete-li zobrazit seznam klíčů a připojovacích řetězců pro obor názvů, spusťte příkaz [AZ Notification-hub Namespace – seznam pravidel](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) – příkaz klíče.

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Vytvořit centra oznámení

1. Vytvořte své první centrum oznámení.

   Centrum oznámení se teď dá vytvořit v novém oboru názvů.  Spuštěním příkazu [AZ Notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) vytvořte centrum oznámení.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Vytvořte druhé centrum oznámení.

   V jednom oboru názvů se dá vytvořit víc Center oznámení.  Pokud chcete ve stejném oboru názvů vytvořit druhé centrum oznámení, spusťte `az notification-hub create` příkaz znovu s jiným názvem centra.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Práce se zásadami přístupu

1. Vytvoří nové autorizační pravidlo pro Centrum oznámení.

   Zásady přístupu se automaticky vytvoří pro každé nové centrum oznámení.  Pokud chcete vytvořit a přizpůsobit vlastní zásady přístupu, použijte příkaz [AZ Notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) .

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Vypíše zásady přístupu pro Centrum oznámení.

   K dotazování na to, jaké zásady přístupu existují pro Centrum oznámení, použijte příkaz [AZ Notification-hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) .

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > V aplikaci nepoužívejte zásady **DefaultFullSharedAccessSignature** . Ten je určený pouze pro použití na back-endu.  V klientské aplikaci použijte pouze zásady přístupu **naslouchání** .

3. Seznam klíčů a připojovacích řetězců pro zásady přístupu centra oznámení

   Pro každou zásadu přístupu existují dvě sady klíčů a připojovací řetězce.  Budete je potřebovat později pro zpracování nabízených oznámení.  Chcete-li zobrazit seznam klíčů a řetězců připojení pro zásady přístupu k centru oznámení, použijte příkaz [AZ Notification-hub Authorization-Rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) Policy Command-Keys.

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Obor názvů centra oznámení](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) a [Centrum oznámení](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mají samostatné zásady přístupu.  Ujistěte se, že používáte správný odkaz na rozhraní příkazového řádku Azure CLI při dotazování na klíče a připojovací řetězce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny související prostředky pomocí příkazu [AZ Group Delete](/cli/azure/group) .

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Viz také

Seznamte se s úplnými možnostmi pro správu Center oznámení pomocí Azure CLI.

* [Notification Hubs úplný seznam odkazů Azure CLI](/cli/azure/ext/notification-hub/notification-hub)
* [Seznam odkazů na obor názvů Azure CLI Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Seznam referenčních seznamů Azure CLI Notification Hubs autorizačního pravidla](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Seznam referenčních seznamů Azure CLI Notification Hubs přihlašovacích údajů](/cli/azure/ext/notification-hub/notification-hub/credential)
