---
title: Jak mít verze modulu runtime Azure Functions
description: Azure Functions podporuje více verzí modulu runtime. Zjistěte, jak určit verzi modulu runtime funkce aplikace hostované v Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843387"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak mít verze modulu runtime Azure Functions

Funkce aplikace běží na konkrétní verzi modulu runtime Azure Functions. Existují dvě hlavní verze: [1.x a 2.x](functions-versions.md). Tento článek vysvětluje postup konfigurace funkce aplikace v Azure ke spuštění na verzi, kterou zvolíte. Informace o tom, jak nakonfigurovat místní vývojové prostředí pro konkrétní verzi najdete v tématu [kódu a testování Azure Functions místně](functions-run-local.md).

>[!IMPORTANT]   
> Azure Functions runtime 2.0 je ve verzi preview a aktuálně ne všechny funkce Azure Functions podporované. Další informace najdete v tématu [přehled verze modulu runtime Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Verze automatickou a ruční aktualizace

Funkce umožňuje cílení na konkrétní verzi modulu runtime s použitím `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace v aplikaci funkce. Funkce aplikace se ukládají na zadaný hlavní verzi, dokud se nerozhodnete explicitně přesunout na novou verzi.

Pokud zadáte jenom hlavní verzi (~ 1"pro 1.x) nebo"beta"pro 2.x, funkce aplikace se automaticky aktualizuje na nový dílčí verze modulu runtime, jakmile budou k dispozici. Nové podverze nevyplývají nejnovější změny. Pokud zadáte dílčí verzi (například "1.0.11360"), funkce aplikace je udržováno na tuto verzi, dokud ji explicitně změnit. 

Když je veřejně dostupná nová verze, výzvu na portálu vám dává možnost Přesunout nahoru na příslušnou verzi. Po přesunutí na novou verzi, můžete vždy použít `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace přejít zpět na předchozí verzi.

Ke změně verze modulu runtime způsobí, že funkce aplikaci restartovat.

Hodnoty můžete nastavit v `FUNCTIONS_EXTENSION_VERSION` aplikace nastavení Povolit automatické aktualizace se právě "~ 1" pro modul runtime 1.x a "beta" pro 2.x.

## <a name="view-the-current-runtime-version"></a>Zobrazit aktuální verzi modulu runtime

Následujícím postupem zobrazíte verzi modulu runtime, který je aktuálně používán aplikaci funkce. 

1. V [portál Azure](https://portal.azure.com), přejděte do aplikaci funkce a v části **nakonfigurované funkce**, zvolte **funkce nastavení aplikace**. 

    ![Vyberte nastavení aplikace – funkce](./media/functions-versions/add-update-app-setting.png)

2. V **funkce nastavení aplikace** kartě, vyhledejte **verzi modulu Runtime**. Poznámka: verze konkrétní runtime a požadovaný hlavní verzi. V následujícím příkladu funkce\_rozšíření\_verze aplikace nastavení je `~1`.
 
   ![Vyberte nastavení aplikace – funkce](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Cílové verze pomocí portálu

Pokud budete potřebovat cílové verze než aktuální hlavní verze nebo verze 2.0, nastavit `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace.

1. V [portál Azure](https://portal.azure.com), přejděte do aplikace pro funkce a v části **nakonfigurované funkce**, zvolte **nastavení aplikace**.

    ![Vyberte nastavení aplikace – funkce](./media/functions-versions/add-update-app-setting1a.png)

2. V **nastavení aplikace** kartě, vyhledejte `FUNCTIONS_EXTENSION_VERSION` nastavení a změňte hodnotu na platnou verzi modulu runtime 1.x nebo `beta` pro verze 2.0. Tildou hlavní verzí znamená, použijte nejnovější verzi danou hlavní verzi (například ~ 1"). 

    ![Nastavení aplikace – funkce](./media/functions-versions/add-update-app-setting2.png)

3. Klikněte na tlačítko **Uložit** uložit aktualizaci nastavení aplikace. 

## <a name="target-a-version-using-azure-cli"></a>Cílová verze, pomocí rozhraní příkazového řádku Azure

 Můžete také nastavit `FUNCTIONS_EXTENSION_VERSION` z příkazového řádku Azure. Použití Azure CLI, aktualizovat nastavení aplikace v aplikaci funkce pomocí [az functionapp konfigurace appsettings sadu](/cli/azure/functionapp/config/appsettings#set) příkaz.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
V tomto kódu nahraďte `<function_app>` s názvem aplikace funkce. Také nahraďte `<my_resource_group>` s názvem skupiny prostředků pro funkce aplikace. Nahraďte `<version>` s platnou verzi modulu runtime 1.x nebo `beta` pro verze 2.0. 

Můžete spustit tento příkaz z [prostředí cloudu Azure](../cloud-shell/overview.md) výběrem **vyzkoušet** v předchozí ukázce kódu. Můžete také [rozhraní příkazového řádku Azure místně](/cli/azure/install-azure-cli) k provedení tohoto příkazu po provedení [az přihlášení](/cli/azure/reference-index#az_login) k přihlášení.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Cílový modul runtime 2.0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Najdete v části poznámky k verzi pro verze modulu runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)