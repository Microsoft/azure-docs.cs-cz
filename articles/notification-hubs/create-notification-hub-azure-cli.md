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
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ebed70a99e8e906db802099e052e69ab2575d737
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875019"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rychlý Start: vytvoření centra oznámení Azure pomocí Azure CLI

Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace o této službě najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte centrum oznámení pomocí Azure CLI. První část obsahuje kroky pro vytvoření oboru názvů Notification Hubs. Druhá část obsahuje postup vytvoření centra oznámení v existujícím oboru názvů. Naučíte se také, jak vytvořit vlastní zásady přístupu.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs vyžaduje verzi rozhraní příkazového řádku Azure 2.0.67 nebo novější. Spuštěním [AZ Version](/cli/azure/reference-index?#az_version) vyhledejte verzi a závislé knihovny, které jsou nainstalovány. Pokud chcete upgradovat na nejnovější verzi, spusťte příkaz [AZ upgrade](/cli/azure/reference-index?#az_upgrade).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure Notification Hubs, stejně jako všechny prostředky Azure, musí být nasazené do skupiny prostředků.  Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.  Další informace o skupinách prostředků najdete v tématu [co je Azure Resource Manager](/azure/azure-resource-manager/management/overview) .

V tomto rychlém startu vytvořte skupinu prostředků s názvem **spnhubrg** v umístění **eastus** pomocí následujícího příkazu [AZ Group Create](/cli/azure/group#az-group-create) .

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Vytvoření oboru názvů Notification Hubs

1. Vytvořte obor názvů pro vaše centra oznámení.

   Obor názvů obsahuje jeden nebo víc rozbočovačů a název musí být jedinečný ve všech předplatných Azure a musí mít aspoň šest znaků. Pokud chcete ověřit dostupnost názvu, použijte příkaz [AZ Notification-hub obor názvů Namespace-Availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI odpoví na vaši žádost o dostupnost zobrazením následujícího výstupu konzoly:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Všimněte si druhého řádku odpovědi Azure CLI `"isAvailable": true` . Tento řádek se načte, `false` Pokud požadovaný název, který jste zadali pro obor názvů, není k dispozici. Jakmile ověříte dostupnost tohoto názvu, spusťte příkaz [AZ Notification-hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) a vytvořte svůj obor názvů.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Pokud `--name` jste příkaz, který jste zadali k `az notification-hub namespace create` příkazu, není k dispozici nebo nesplňuje [pravidla pojmenování a omezení pro prostředky Azure](../azure-resource-manager/management/resource-name-rules.md), rozhraní příkazového řádku Azure CLI odpoví následujícímu výstupu konzoly:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Pokud se křestní jméno, které jste zkusili, nezdařilo, vyberte jiný název pro nový obor názvů a spusťte `az notification-hub namespace create` příkaz znovu.

   > [!NOTE]
   > Od tohoto kroku předejte hodnotu `--namespace` parametru v každém příkazu Azure CLI, který jste zkopírovali z tohoto rychlého startu.

2. Získá seznam oborů názvů.

   Podrobnosti o vašem novém oboru názvů zobrazíte pomocí příkazu [AZ Notification-hub Namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) . `--resource-group`Parametr je nepovinný, pokud chcete zobrazit všechny obory názvů pro předplatné.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Vytvořit centra oznámení

1. Vytvořte své první centrum oznámení.

   V novém oboru názvů se teď dá vytvořit jedno nebo víc Center oznámení. Spuštěním příkazu [AZ Notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) vytvořte centrum oznámení.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Vytvořte druhé centrum oznámení.

   V jednom oboru názvů se dá vytvořit víc Center oznámení. Pokud chcete ve stejném oboru názvů vytvořit druhé centrum oznámení, spusťte `az notification-hub create` příkaz znovu s jiným názvem centra.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Získejte seznam Center oznámení.

   Azure CLI vrátí buď úspěch, nebo chybovou zprávu s každým provedeným příkazem; je ale možné dotazovat se na seznam Center oznámení, který se znovu zaručí. Pro tento účel byl navržen příkaz [AZ Notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) .

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Práce se zásadami přístupu

1. Azure Notification Hubs používá [zabezpečení sdíleného přístupového podpisu](./notification-hubs-push-notification-security.md) pomocí zásad přístupu. Při vytváření centra oznámení se vytvoří automaticky dvě zásady. Připojovací řetězce z těchto zásad jsou potřeba ke konfiguraci nabízených oznámení. Příkaz [AZ Notification-hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) nabízí seznam názvů zásad a jejich příslušných skupin prostředků.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > V aplikaci nepoužívejte zásady _DefaultFullSharedAccessSignature_ . Tato zásada je určena pouze pro použití v back-endu. Používejte `Listen` v klientské aplikaci pouze zásady přístupu.

2. Pokud chcete vytvořit další autorizační pravidla s smysluplnými názvy, můžete vytvořit a přizpůsobit vlastní zásadu přístupu pomocí příkazu [AZ Notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) . `--rights`Parametr je seznam oprávnění oddělených mezerami, které chcete přiřadit.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Pro každou zásadu přístupu existují dvě sady klíčů a připojovací řetězce. Později je budete potřebovat ke [konfiguraci centra oznámení](./configure-notification-hub-portal-pns-settings.md). Chcete-li zobrazit seznam klíčů a připojovacích řetězců pro zásady přístupu Notification Hubs, použijte příkaz [AZ Notification-hub Authorization-Rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) Policy.

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Notification Hubs obor názvů](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) a [Centrum oznámení](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mají samostatné zásady přístupu. Ujistěte se, že používáte správný odkaz na rozhraní příkazového řádku Azure CLI při dotazování na klíče a připojovací řetězce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny související prostředky pomocí příkazu [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Další kroky

* V tomto rychlém startu jste vytvořili centrum oznámení. Informace o tom, jak nakonfigurovat nastavení centra pro PNS (Platform Notification System), najdete v tématu [nastavení nabízených oznámení v centru oznámení](configure-notification-hub-portal-pns-settings.md) .

* Seznamte se s rozsáhlými možnostmi správy Center oznámení pomocí Azure CLI:

  [Seznam úplných odkazů Notification Hubs](/cli/azure/ext/notification-hub/notification-hub)

  [Seznam odkazů na obor názvů Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Seznam odkazů na autorizační pravidlo Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs seznam odkazů na přihlašovací údaje](/cli/azure/ext/notification-hub/notification-hub/credential)
