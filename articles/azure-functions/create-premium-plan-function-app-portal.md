---
title: Vytvoření plánu služby Azure Functions Premium na portálu
description: Naučte se používat Azure Portal k vytvoření aplikace Function App, která běží v plánu Premium.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676582"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Vytvoření aplikace funkcí plánu Premium v Azure Portal

Azure Functions nabízí škálovatelný plán Premium, který poskytuje připojení k virtuální síti, žádný studený start a špičkový hardware. Další informace najdete v tématu [plán Azure Functions Premium](functions-premium-plan.md). 

V tomto článku se naučíte, jak pomocí Azure Portal vytvořit aplikaci Function App v plánu Premium. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

V tuto chvíli můžete vytvořit funkce v nové aplikaci Function App. Tyto funkce můžou využít výhod [plánu Premium](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání funkce aktivované protokolem HTTP] (./functions-get-started.md
