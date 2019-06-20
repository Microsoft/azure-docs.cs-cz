---
title: Průběžné nasazování pro službu Azure Functions | Dokumentace Microsoftu
description: Použití zařízení průběžného nasazování služby Azure App Service k publikování vašich funkcí.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 2dc5fab0966c2ead0276cd8b23ea764bd4f9ef59
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190483"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions

Služba Azure Functions umožňuje nasadit váš kód nepřetržitě až [integrace správy zdrojového kódu](functions-deployment-technologies.md#source-control). To umožňuje kde kód aktualizuje triggeru pracovního postupu nasazení do Azure. Pokud začínáte do služby Azure Functions, začněte [přehled Azure Functions](functions-overview.md).

Průběžné nasazování je skvělá možnost pro projekty, kde máte integraci více a časté příspěvky. Můžete ho taky udržovat jednotný zdroj informací pro kód vaší funkce. Můžete nakonfigurovat průběžné nasazování ve službě Azure Functions z následujících umístění zdrojového kódu:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Aplikace function app je jednotka nasazení pro službu Azure functions. To znamená, že jsou všechny funkce v aplikaci function app nasazené ve stejnou dobu. Po povolení průběžného nasazování přístup ke kódu funkce na webu Azure Portal je nakonfigurován jako *jen pro čtení*, protože zdroj pravdivých informací je nastaven jako jinde.

## <a name="requirements-for-continuous-deployment"></a>Požadavky pro průběžné nasazování

Pro průběžné nasazování úspěšné musí být kompatibilní s následující strukturou základní složka, která očekává, že Azure Functions adresářovou strukturu:

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Nastavení průběžného nasazování

Pomocí tohoto postupu konfigurace průběžného nasazování pro existující aplikaci function app. Tyto kroky ukazují, integrace s úložišti GitHub, ale podobný postup platí pro úložiště Azure nebo jiných úložišť zdrojového kódu.

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com)vyberte **funkce platformy** > **Centrum nasazení**.

    ![Otevírá Centrum pro nasazení](./media/functions-continuous-deployment/platform-features.png)

2. V **Deployment Center**vyberte **Githubu**a pak vyberte **Authorize**. Nebo, pokud jste už oprávnění GitHub, vyberte **pokračovat**. 

    ![Nasazení System center](./media/functions-continuous-deployment/github.png)

3. V Githubu, vyberte **autorizovat AzureAppService**. 

    ![Autorizace](./media/functions-continuous-deployment/authorize.png)
    
    Na webu Azure Portal **Deployment Center**vyberte **pokračovat**.

4. Vyberte jednu z následujících zprostředkovatelů sestavení:

    * **Vytvoření služby App Service** – nejlepší, když není nutné sestavení, nebo pokud potřebujete obecné sestavení.
    * **Kanály Azure (Preview)** – nejlépe, když potřebujete větší kontrolu nad sestavení. Tento zprostředkovatel je aktuálně ve verzi preview.

    ![Výběr sestavení zprostředkovatele](./media/functions-continuous-deployment/build.png)

5. Nakonfigurujte informace týkající se možnosti správy zdrojového kódu, který jste zadali. Pro GitHub, je nutné zadat **organizace**, **úložiště**, a **větev** kde žije váš kód. Vyberte **pokračovat**.

    ![Konfigurace Githubu](./media/functions-continuous-deployment/github-specifics.png)

6. A konečně, zkontrolujte všechny informace a vyberte **Dokončit** k dokončení konfigurace vašeho nasazení.

    ![Souhrn](./media/functions-continuous-deployment/summary.png)

Po dokončení procesu veškerý kód ze zadaného zdroje bude nasazena do vaší aplikace. V tomto okamžiku změny ve zdroji nasazení aktivovala nasazení těchto změn do vaší aplikace funkcí v Azure.

## <a name="deployment-scenarios"></a>Scénáře nasazení

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Přesunout stávající funkce pro průběžné nasazování

Pokud jste už zadali funkce v [webu Azure portal](https://portal.azure.com) a chcete stáhnout obsah aplikace před přepnutím do průběžné nasazování, musí přejít do **přehled** kartu vaší funkce aplikace a kliknutím **stáhnout obsah aplikace** tlačítko.

![Stahuje se obsah aplikace](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po dokončení konfigurace průběžné integrace, nemůžete nadále upravovat zdrojových souborů na portálu funkcí.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
