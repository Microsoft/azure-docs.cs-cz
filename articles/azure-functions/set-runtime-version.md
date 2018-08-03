---
title: Jak se zaměřit na verze modulu runtime Azure Functions
description: Služba Azure Functions podporuje více verzí modulu runtime. Zjistěte, jak určit verzi modulu runtime aplikace funkcí hostované v Azure.
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
ms.openlocfilehash: 3efc09f93cf8b3e65d595c87d0cf25691386d6e2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434760"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak se zaměřit na verze modulu runtime Azure Functions

Aplikace function app se spouští na konkrétní verzi modulu runtime Azure Functions. Existují dvě hlavní verze: [1.x a 2.x](functions-versions.md). Tento článek vysvětluje, jak nakonfigurovat aplikaci function app v Azure pro spouštění na zvolené verzi. Informace o tom, jak nakonfigurovat místní vývojové prostředí pro konkrétní verzi, najdete v části [kódu a testování Azure Functions místně](functions-run-local.md).

>[!IMPORTANT]   
> Modul runtime služby Azure Functions 2.0 je ve verzi preview a aktuálně ne všechny funkce Azure Functions se nepodporuje. Další informace najdete v tématu [přehled verze modulu runtime Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Verze automatické a ruční aktualizace

Funkce umožňuje cílit na konkrétní verzi modulu runtime s použitím `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace function app. Aplikace function app se ukládají na zadaný hlavní verze, dokud se nerozhodnete explicitně přesunout na novou verzi.

Pokud zadáte jenom hlavní verzi (~ 1"pro 1.x) nebo"beta"pro 2.x, aplikace function app se automaticky aktualizují na nový dílčí verze modulu runtime, jakmile budou k dispozici. Nový dílčí verze není zavést rozbíjející změny. Pokud chcete zadat vedlejší verze (například "1.0.11360"), aplikace function app se ukládají na tuto verzi, dokud ji explicitně nezměníte. 

Když je veřejně dostupná nová verze, výzvu na portálu poskytuje možnost Přesunout nahoru na tuto verzi. Po přesunutí na novou verzi, můžete kdykoli použít `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace, které chcete vrátit k předchozí verzi.

Ke změně verze modulu runtime způsobí, že se aplikace function app k restartování.

Hodnoty můžete nastavit `FUNCTIONS_EXTENSION_VERSION` nastavení Povolit automatické aktualizace aplikace jsou v tuto chvíli "~ 1" pro modul runtime verze 1.x a 2.x "betatestování".

## <a name="view-the-current-runtime-version"></a>Zobrazit aktuální verze modulu runtime

Následující postup použijte k zobrazení verze modulu runtime momentálně používáte aplikaci function app. 

1. V [webu Azure portal](https://portal.azure.com), přejděte do aplikace function app a v části **nakonfigurovat funkce**, zvolte **fungovat nastavení aplikace**. 

    ![Vyberte nastavení aplikace function app](./media/functions-versions/add-update-app-setting.png)

2. V **fungovat nastavení aplikace** kartu, vyhledejte **verze modulu Runtime**. Poznamenejte si verzi modulu runtime specifické a požadovaný hlavní verze. V následujícím příkladu funkce\_rozšíření\_verze aplikace nastavená na `~1`.
 
   ![Vyberte nastavení aplikace function app](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Cílení na verzi pomocí portálu

Když budete potřebovat cílit na verzi než aktuální hlavní verze nebo verze 2.0, nastavte `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace.

1. V [webu Azure portal](https://portal.azure.com), přejděte do aplikace function app a v části **nakonfigurovat funkce**, zvolte **nastavení aplikace**.

    ![Vyberte nastavení aplikace function app](./media/functions-versions/add-update-app-setting1a.png)

2. V **nastavení aplikace** kartu, najdete `FUNCTIONS_EXTENSION_VERSION` nastavení a změňte hodnotu na platnou verzi modul runtime verze 1.x nebo `beta` pro verze 2.0. Tilda s hlavní verzí znamená, že používat nejnovější verzi Tato hlavní verze (například "~ 1"). 

    ![Nastavení aplikace – funkce](./media/functions-versions/add-update-app-setting2.png)

3. Klikněte na tlačítko **Uložit** se uložit nastavení aktualizace aplikace. 

## <a name="target-a-version-using-azure-cli"></a>Cílení na verzi pomocí Azure CLI

 Můžete také nastavit `FUNCTIONS_EXTENSION_VERSION` z příkazového řádku Azure. Pomocí rozhraní příkazového řádku Azure aktualizovat nastavení aplikace function app pomocí [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) příkazu.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
V tomto kódu nahraďte `<function_app>` s názvem aplikace function App. Také nahraďte `<my_resource_group>` s názvem skupiny prostředků pro vaši aplikaci function app. Nahraďte `<version>` s platnou verzi modul runtime verze 1.x nebo `beta` pro verze 2.0. 

Tento příkaz můžete spustit [Azure Cloud Shell](../cloud-shell/overview.md) výběrem **vyzkoušet** v předchozím příkladu kódu. Můžete také použít [rozhraní příkazového řádku Azure místně](/cli/azure/install-azure-cli) k provedení tohoto příkazu po provedení [az login](/cli/azure/reference-index#az-login) k přihlášení.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Cílový modul runtime 2.0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobrazit poznámky k verzím modulu runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)