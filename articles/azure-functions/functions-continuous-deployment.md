---
title: Průběžné nasazování pro službu Azure Functions | Dokumentace Microsoftu
description: Pomocí funkcí průběžného nasazování služby Azure App Service k publikování vašich funkcí.
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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594545"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions

Azure Functions můžete použít pro průběžné nasazování kódu s použitím [integrace správy zdrojového kódu](functions-deployment-technologies.md#source-control). Integrace správy zdrojového kódu povolí pracovní postup, ve kterém aktualizace kódu aktivuje nasazení do Azure. Pokud začínáte do služby Azure Functions, začněte tím, že zkontrolujete [přehled Azure Functions](functions-overview.md).

Průběžné nasazování je dobrou volbou pro projekty, kde můžete integrovat více a časté příspěvky. Při použití průběžného nasazování udržovat jednotný zdroj informací pro váš kód, který umožňuje snadno spolupracovat v týmu. Můžete nakonfigurovat průběžné nasazování ve službě Azure Functions z následujících umístění zdrojového kódu:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Aplikace function app je jednotka nasazení pro funkce v Azure. Všechny funkce v aplikaci function app se nasadí ve stejnou dobu. Po povolení průběžného nasazování přístup ke kódu funkce na webu Azure Portal je nakonfigurován jako *jen pro čtení* protože nastaven zdroj pravdivých informací jinde.

## <a name="requirements-for-continuous-deployment"></a>Požadavky pro průběžné nasazování

Pro průběžné nasazování úspěšné musí být kompatibilní s strukturu základní složka, která očekává, že Azure Functions strukturu.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Nastavení průběžného nasazování

Konfigurace průběžného nasazování pro existující aplikaci function app, proveďte tyto kroky. Kroky ukazují, integrace s úložišti GitHub, ale podobný postup platí pro úložiště Azure nebo jiných úložišť zdrojového kódu.

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com)vyberte **funkce platformy** > **Deployment Center**.

    ![Otevřete Centrum nasazení](./media/functions-continuous-deployment/platform-features.png)

2. V **Deployment Center**vyberte **Githubu**a pak vyberte **Authorize**. Pokud jste už oprávnění GitHub, vyberte **pokračovat**. 

    ![System Center nasazení Azure App Service](./media/functions-continuous-deployment/github.png)

3. V Githubu, vyberte **autorizovat AzureAppService** tlačítko. 

    ![Povolit službě Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    V **Deployment Center** na webu Azure Portal, vyberte **pokračovat**.

4. Vyberte jednu z následujících zprostředkovatelů sestavení:

    * **Vytvoření služby App Service**: Nejlepší, když není nutné sestavení, nebo pokud potřebujete obecné sestavení.
    * **Azure Pipelines (Preview)** : Nejlepší, když potřebujete větší kontrolu nad sestavení. Tento zprostředkovatel je aktuálně ve verzi preview.

    ![Vyberte poskytovatele sestavení](./media/functions-continuous-deployment/build.png)

5. Nakonfigurujte informace týkající se možnosti správy zdrojového kódu, který jste zadali. Pro GitHub, je nutné zadat nebo vybrat hodnoty pro **organizace**, **úložiště**, a **větev**. Hodnoty jsou založeny na umístění vašeho kódu. Vyberte **pokračovat**.

    ![Konfigurace Githubu](./media/functions-continuous-deployment/github-specifics.png)

6. Zkontrolujte všechny informace a pak vyberte **Dokončit** k dokončení konfigurace vašeho nasazení.

    ![Souhrn](./media/functions-continuous-deployment/summary.png)

Po dokončení procesu se veškerý kód ze zadaného zdroje bude nasazena do vaší aplikace. V tomto okamžiku změny ve zdroji nasazení aktivovala nasazení těchto změn do vaší aplikace funkcí v Azure.

## <a name="deployment-scenarios"></a>Scénáře nasazení

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Přesunout stávající funkce pro průběžné nasazování

Pokud jste už zadali funkce v [webu Azure portal](https://portal.azure.com) a chcete stáhnout obsah aplikace předtím, než přepnete na průběžné nasazování, přejděte na **přehled** kartu aplikace function App. Vyberte **stáhnout obsah aplikace** tlačítko.

![Stáhnout obsah aplikace](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po dokončení konfigurace průběžné integrace, nemůžete nadále upravovat zdrojových souborů na portálu funkcí.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
