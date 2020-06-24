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
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 3014a66c633a4293de8cd6eb325e962366c103b9
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208259"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rychlý Start: vytvoření centra oznámení Azure pomocí Azure CLI

Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace o této službě najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte centrum oznámení pomocí Azure CLI. První část obsahuje postup vytvoření oboru názvů centra oznámení.  Druhá část obsahuje postup vytvoření centra oznámení v existujícím oboru názvů.  Naučíte se také, jak vytvořit vlastní zásady přístupu.  

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs vyžaduje verzi rozhraní příkazového řádku Azure 2.0.67 nebo novější. Spusťte `az --version` pro vyhledání verze a závislých knihoven, které jsou nainstalovány. Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Přihlásit se.

   Přihlaste se pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) , pokud používáte místní instalaci rozhraní příkazového řádku.

    ```azurecli
    az login
    ```

    Proces ověřování dokončíte podle kroků zobrazených v terminálu.

2. Nainstalujte rozšíření Azure CLI.

   Když pracujete s odkazy na rozšíření pro rozhraní příkazového řádku Azure CLI, musíte nejdřív nainstalovat rozšíření.  Rozšíření Azure CLI poskytují přístup k experimentálním a předběžným příkazům, které ještě nebyly dodány jako součást základního rozhraní příkazového řádku.  Další informace o rozšířeních, včetně aktualizace a odinstalace, najdete v tématu [použití rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview).

   [Rozšíření pro Notification Hubs](/cli/azure/ext/notification-hub/notification-hub) nainstalujete spuštěním následujícího příkazu:

    ```azurecli
    az extension add --name notification-hub
   ```

3. Vytvořte skupinu prostředků.

   Azure Notification Hubs, stejně jako všechny prostředky Azure, musí být nasazené do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

   V tomto rychlém startu vytvořte skupinu prostředků s názvem _spnhubrg_ v umístění _eastus_ pomocí následujícího příkazu [AZ Group Create](/cli/azure/group#az-group-create) :

   ```azurecli
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Vytvoření oboru názvů centra oznámení

1. Vytvořte obor názvů pro vaše centra oznámení.

   Obor názvů obsahuje jeden nebo víc rozbočovačů a **název musí být jedinečný ve všech předplatných Azure a musí mít aspoň šest znaků**.  Pokud chcete ověřit dostupnost názvu, použijte příkaz [AZ Notification-hub obor názvů Namespace-Availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI bude reagovat na vaši žádost o dostupnost zobrazením následujícího výstupu konzoly:

   ```output
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

   Všimněte si druhého řádku odpovědi Azure CLI `"isAvailable": true` .  Tento řádek bude načten, `false` Pokud je požadovaný název, který jste zadali pro obor názvů, k dispozici.  Jakmile ověříte dostupnost tohoto názvu, spusťte příkaz [AZ Notification-hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) a vytvořte svůj obor názvů.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Pokud `--name` jste k příkazu uvedli, že není `az notification-hub namespace create` k dispozici, nebo nesplňuje [pravidla pojmenování a omezení pro prostředky Azure](/azure/azure-resource-manager/management/resource-name-rules), rozhraní příkazového řádku Azure CLI bude odpovídat následujícímu výstupu konzoly:

   ```output
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalied
   The specified service namespace is invalid.
   ```

   Pokud se křestní jméno, které jste zkusili, nezdařilo, vyberte jiný název pro nový obor názvů a spusťte `az notification-hub namespace create` příkaz znovu.

   > [!NOTE]
   > V tomto kroku budete muset `--namespace` v každém příkazu pro Azure CLI, který zkopírujete z tohoto rychlého startu, nahradit hodnotu parametru.

2. Získá seznam oborů názvů.

   Podrobnosti o vašem novém oboru názvů zobrazíte pomocí příkazu [AZ Notification-hub Namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace?view=azure-cli-latest#ext-notification-hub-az-notification-hub-namespace-list) .  `--resource-group`Parametr je nepovinný, pokud chcete zobrazit všechny obory názvů pro předplatné.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Vytvořit centra oznámení

1. Vytvořte své první centrum oznámení.

   V novém oboru názvů se teď dá vytvořit jedno nebo víc Center oznámení.  Spuštěním příkazu [AZ Notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) vytvořte centrum oznámení.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Vytvořte druhé centrum oznámení.

   V jednom oboru názvů se dá vytvořit víc Center oznámení.  Pokud chcete ve stejném oboru názvů vytvořit druhé centrum oznámení, spusťte `az notification-hub create` příkaz znovu s jiným názvem centra.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Získejte seznam Center oznámení.

   Azure CLI vrátí buď úspěch, nebo chybovou zprávu s každým provedeným příkazem; je ale možné dotazovat se na seznam Center oznámení, který se znovu zaručí.  Pro tento účel byl navržen příkaz [AZ Notification-hub list](/cli/azure/ext/notification-hub/notification-hub?view=azure-cli-latest#ext-notification-hub-az-notification-hub-list) .

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-notification-hub-access-policies"></a>Práce se zásadami přístupu centra oznámení

1. Vypíše zásady přístupu pro Centrum oznámení.

   Azure Notification Hubs používá [zabezpečení sdíleného přístupového podpisu](/azure/notification-hubs/notification-hubs-push-notification-security) pomocí zásad přístupu.  Při vytváření centra oznámení se vytvoří automaticky dvě zásady.  Připojovací řetězce z těchto zásad jsou potřeba ke konfiguraci nabízených oznámení.  Příkaz [AZ Notification-hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) nabízí seznam názvů zásad a jejich příslušných skupin prostředků.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > V aplikaci nepoužívejte zásady _DefaultFullSharedAccessSignature_ . Ten je určený pouze pro použití na back-endu.  Používejte `Listen` v klientské aplikaci pouze zásady přístupu.

2. Vytvoří nové autorizační pravidlo pro Centrum oznámení.

   Pokud chcete vytvořit další autorizační pravidla s smysluplnými názvy, můžete vytvořit a přizpůsobit vlastní zásadu přístupu pomocí příkazu [AZ Notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) .  `--rights`Parametr je seznam oprávnění oddělených mezerami, které chcete přiřadit.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Seznam klíčů a připojovacích řetězců pro zásady přístupu centra oznámení

   Pro každou zásadu přístupu existují dvě sady klíčů a připojovací řetězce.  Později je budete potřebovat ke [konfiguraci centra oznámení](/azure/notification-hubs/configure-notification-hub-portal-pns-settings).  Chcete-li zobrazit seznam klíčů a řetězců připojení pro zásady přístupu k centru oznámení, použijte příkaz [AZ Notification-hub Authorization-Rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) Policy Command-Keys.

   ```azurecli
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   #query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Obor názvů centra oznámení](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) a [Centrum oznámení](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mají samostatné zásady přístupu.  Ujistěte se, že používáte správný odkaz na rozhraní příkazového řádku Azure CLI při dotazování na klíče a připojovací řetězce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny související prostředky pomocí příkazu [AZ Group Delete](/cli/azure/group) .

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Další kroky

* V tomto rychlém startu jste vytvořili centrum oznámení. Informace o tom, jak nakonfigurovat nastavení centra pro PNS (Platform Notification System), najdete v tématu [nastavení nabízených oznámení v centru oznámení](configure-notification-hub-portal-pns-settings.md) .

* Seznamte se s rozsáhlými možnostmi správy centra oznámení pomocí Azure CLI.

  [Seznam úplných odkazů Notification Hubs](/cli/azure/ext/notification-hub/notification-hub)

  [Seznam odkazů na obor názvů Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Seznam odkazů na autorizační pravidlo Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs seznam odkazů na přihlašovací údaje](/cli/azure/ext/notification-hub/notification-hub/credential)
