---
title: Úvodní příručka – vytvoření centra oznámení Azure pomocí azure cli | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit centrum oznámení Azure pomocí azure cli.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082448"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Úvodní příručka: Vytvoření centra oznámení Azure pomocí azure cli

Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace o službě najdete v tématu [Co je Centra oznámení Azure?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte centrum oznámení pomocí azure cli. První část obsahuje kroky k vytvoření oboru názvů centra oznámení a dotazu na informace o zásadách přístupu pro tento obor názvů. Druhá část obsahuje kroky k vytvoření centra oznámení v existujícím oboru názvů.  Dozvíte se také, jak vytvořit vlastní zásady přístupu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Centra oznámení vyžadují verzi 2.0.67 nebo novější v příkazovém příkazovém příkazu Azure. Spusťte `az --version` a vyhledejte nainstalovanou verzi a závislé knihovny. Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Přihlásit se.

   Pokud používáte místní instalaci příkazu CLI, přihlaste se pomocí příkazu [az login.](/cli/azure/reference-index#az-login)

    ```azurecli-interactive
    az login
    ```

    Postup ověřování dokončujte podle pokynů zobrazených v terminálu.

2. Nainstalujte rozšíření Azure CLI.

   Chcete-li spustit příkazy Azure CLI pro centra oznámení, nainstalujte rozšíření Azure CLI [pro centra oznámení](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Vytvořte skupinu prostředků.

   Centra oznámení Azure, stejně jako všechny prostředky Azure, musí být nasazená do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

   Pro tento rychlý start vytvořte skupinu prostředků s názvem *spnhubrg* v umístění *eastus* s následujícím [příkazem az group create:](/cli/azure/group#az-group-create)

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Vytvoření oboru názvů centra oznámení

1. Vytvoření oboru názvů pro centra oznámení

   Obor názvů obsahuje jedno nebo více rozbočovačů a název musí být jedinečný ve všech předplatných Azure.  Chcete-li zkontrolovat dostupnost daného oboru názvů služby, použijte příkaz [kontrola dostupnosti az notification-hub.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)  Spusťte příkaz [vytvoření oboru názvů centra oznámení az](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) avytvořte obor názvů.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Seznam klíčů a připojovacích řetězců pro zásady přístupu k oboru názvů.

   Pro nový obor názvů je automaticky vytvořena zásada přístupu s názvem **RootManageSharedAccessKey.**  Každá zásada přístupu má dvě sady klíčů a připojovacích řetězců.  Chcete-li vypsat klíče a připojovací řetězce pro obor názvů, spusťte příkaz [az notification-hub název-centrum autorizačního pravidla seznamu-klíče.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Vytvoření center oznámení

1. Vytvořte si první centrum oznámení.

   Centrum oznámení lze nyní vytvořit v novém oboru názvů.  Spusťte příkaz vytvořit centrum [oznámení a](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) vytvořte centrum oznámení.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Vytvořte druhé centrum oznámení.

   V jednom oboru názvů lze vytvořit více center oznámení.  Chcete-li vytvořit druhý rozbočovač oznámení `az notification-hub create` ve stejném oboru názvů, spusťte příkaz znovu pomocí jiného názvu rozbočovače.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Práce se zásadami přístupu

1. Vytvořte nové autorizační pravidlo pro centrum oznámení.

   Zásady přístupu se automaticky vytvoří pro každé nové centrum oznámení.  Chcete-li vytvořit a přizpůsobit vlastní zásady přístupu, použijte příkaz [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) .

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Seznam zásad přístupu pro centrum oznámení.

   Chcete-li se dotazovat, jaké zásady přístupu existují pro centrum oznámení, použijte příkaz [az notification-hub authorization-rule.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list)

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Nepoužívejte zásady **DefaultFullSharedAccessSignature** ve vaší aplikaci. Ten je určený pouze pro použití na back-endu.  Používejte pouze zásady přístupu **naslouchání** v klientské aplikaci.

3. Seznam klíčů a připojovacích řetězců pro zásady přístupu k centru oznámení

   Pro každou zásadu přístupu existují dvě sady klíčů a připojovacích řetězců.  Budete je později potřebovat ke zpracování nabízených oznámení.  Chcete-li vypsat řetězce klíčů a připojení pro zásady přístupu k centru oznámení, použijte příkaz [az authorization-rule-rule.To](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) list the keys and connections strings for a notification hub access policy, use the az notification hub authorization-rule list-keys command.

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Obor názvů centra oznámení](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) a centrum [oznámení](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mají samostatné zásady přístupu.  Ujistěte se, že používáte správný odkaz vpříkazkového propojení Azure při dotazování na klíče a připojovací řetězce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte příkaz [odstranění skupiny az](/cli/azure/group) k odebrání skupiny prostředků a všech souvisejících prostředků.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Viz také

Seznamte se s úplnými možnostmi správy center oznámení pomocí azure cli.

* [Úplný seznam odkazů azure cli centra pro centra oznámení](/cli/azure/ext/notification-hub/notification-hub)
* [Seznam odkazů Azure CLI oboru center oznámení](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Referenční seznam Azure CLI pravidla autorizace centra oznámení](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Seznam odkazů Azure CLI pověření centra oznámení](/cli/azure/ext/notification-hub/notification-hub/credential)
