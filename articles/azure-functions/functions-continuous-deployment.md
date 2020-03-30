---
title: Průběžné nasazování se službou Azure Functions
description: K publikování funkcí použijte funkce průběžného nasazení služby Azure App Service.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277021"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions

Funkce Azure můžete použít k nasazení kódu nepřetržitě pomocí [integrace správy zdrojového kódu](functions-deployment-technologies.md#source-control). Integrace správy zdrojového kódu umožňuje pracovní postup, ve kterém aktualizace kódu aktivuje nasazení do Azure. Pokud s Funkcemi Azure tečte, můžete začít na přehledu [funkcí Azure](functions-overview.md).

Průběžné nasazování je dobrou volbou pro projekty, kde integrujete více a časté příspěvky. Při použití průběžnénasazení, udržovat jeden zdroj pravdy pro váš kód, který umožňuje týmům snadno spolupracovat. Průběžné nasazování ve funkcích Azure můžete konfigurovat z následujících umístění zdrojového kódu:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Jednotka nasazení pro funkce v Azure je aplikace funkce. Všechny funkce v aplikaci funkce jsou nasazeny ve stejnou dobu. Po povolení průběžného nasazení je přístup ke kódu funkcí na webu Azure Portal nakonfigurován jako *jen pro čtení,* protože zdroj pravdy je nastaven ý na jiné místo.

## <a name="requirements-for-continuous-deployment"></a>Požadavky na průběžné nasazení

Aby bylo nepřetržité nasazování úspěšné, musí být vaše adresářová struktura kompatibilní se základní strukturou složek, kterou azure functions očekává.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Průběžné nasazení ještě není podporováno pro linuxové aplikace spuštěné v plánu Spotřeba. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Nastavení průběžného nasazování

Chcete-li nakonfigurovat průběžné nasazení pro existující aplikaci funkcí, proveďte tyto kroky. Kroky ukazují integraci s úložištěm GitHub, ale podobné kroky platí pro Azure Repos nebo jiné úložiště zdrojového kódu.

1. V aplikaci funkce na [portálu Azure](https://portal.azure.com)vyberte Centrum pro nasazení **funkcí** > **platformy**.

    ![Otevřít Centrum nasazení](./media/functions-continuous-deployment/platform-features.png)

2. V **Centru nasazení**vyberte **GitHub**a pak vyberte **Autorizovat**. Pokud jste už GitHub autorizovali, vyberte **Pokračovat**. 

    ![Centrum nasazení služby Azure App Service](./media/functions-continuous-deployment/github.png)

3. V GitHubu vyberte **tlačítko Autorizovat AzureAppService.** 

    ![Autorizace služby Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    V **Centru nasazení** na webu Azure Portal vyberte **Pokračovat**.

4. Vyberte jednoho z následujících poskytovatelů sestavení:

    * **Služba sestavení služby App Service**: Nejlepší, když nepotřebujete sestavení nebo pokud potřebujete obecné sestavení.
    * **Azure Pipelines (Preview)**: Nejlepší, když potřebujete větší kontrolu nad sestavením. Tento zprostředkovatel je aktuálně ve verzi Preview.

    ![Výběr zprostředkovatele sestavení](./media/functions-continuous-deployment/build.png)

5. Nakonfigurujte informace specifické pro zadanou možnost správy zdrojového kódu. Pro GitHub musíte zadat nebo vybrat hodnoty pro **Organizace**, **Úložiště**a **Branch**. Hodnoty jsou založeny na umístění kódu. Potom vyberte **Pokračovat**.

    ![Konfigurace GitHubu](./media/functions-continuous-deployment/github-specifics.png)

6. Zkontrolujte všechny podrobnosti a pak vyberte **Dokončit,** abyste dokončili konfiguraci nasazení.

    ![Souhrn](./media/functions-continuous-deployment/summary.png)

Po dokončení procesu se do vaší aplikace nasadí veškerý kód ze zadaného zdroje. V tomto okamžiku změny ve zdroji nasazení aktivační nasazení těchto změn do aplikace funkce v Azure.

## <a name="deployment-scenarios"></a>Scénáře nasazení

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Přesunutí existujících funkcí do průběžného nasazení

Pokud jste už na [webu Azure Portal](https://portal.azure.com) napsali funkce a chcete si před přechodem na průběžné nasazení stáhnout obsah aplikace, přejděte na kartu **Přehled** aplikace s funkcí. Vyberte tlačítko **Stáhnout obsah aplikace.**

![Stažení obsahu aplikace](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po konfiguraci průběžné integrace již nelze upravovat zdrojové soubory na portálu Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
