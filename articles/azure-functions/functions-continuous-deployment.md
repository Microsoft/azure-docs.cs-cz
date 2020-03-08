---
title: Průběžné nasazování se službou Azure Functions
description: K publikování vašich funkcí použijte funkce průběžného nasazování Azure App Service.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372375"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions

Můžete použít Azure Functions pro průběžné nasazování kódu pomocí [integrace správy zdrojového](functions-deployment-technologies.md#source-control)kódu. Integrace správy zdrojového kódu umožňuje pracovní postup, ve kterém aktualizace kódu aktivuje nasazení do Azure. Pokud Azure Functions, začněte tím, že si prohlédnete [přehled Azure Functions](functions-overview.md).

Průběžné nasazování je dobrou možností pro projekty, ve kterých můžete integrovat více a častých příspěvků. Při použití průběžného nasazování udržujete pro svůj kód jeden zdroj pravdy, který týmům umožňuje snadnou spolupráci. Průběžné nasazování můžete nakonfigurovat v Azure Functions z následujících umístění zdrojového kódu:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Jednotka nasazení funkcí v Azure je aplikace Function App. Všechny funkce aplikace Function App jsou nasazeny současně. Po povolení průběžného nasazování je přístup k kódu funkce v Azure Portal nakonfigurován jako jen *pro čtení* , protože zdroj pravdy je nastaven na jiné místo.

## <a name="requirements-for-continuous-deployment"></a>Požadavky pro průběžné nasazování

Aby průběžné nasazování bylo úspěšné, musí být vaše adresářová struktura kompatibilní se základní strukturou složek, kterou Azure Functions očekává.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Průběžné nasazování ještě není podporované pro aplikace pro Linux spuštěné v plánu spotřeby. 

## <a name="credentials"></a>Nastavení průběžného nasazování

Pokud chcete nakonfigurovat průběžné nasazování pro existující aplikaci Function App, proveďte tyto kroky. Kroky ukazují integraci s úložištěm GitHub, ale podobný postup platí pro Azure Repos nebo jiné úložiště zdrojového kódu.

1. Ve vaší aplikaci Function App v [Azure Portal](https://portal.azure.com)vyberte **funkce platformy** > **centrum nasazení**.

    ![Otevřít centrum nasazení](./media/functions-continuous-deployment/platform-features.png)

2. V **centru nasazení**vyberte **GitHub**a potom vyberte **autorizovat**. Pokud jste už máte autorizovaný GitHub, vyberte **pokračovat**. 

    ![Centrum nasazení Azure App Service](./media/functions-continuous-deployment/github.png)

3. V GitHubu vyberte tlačítko **autorizovat AzureAppService** . 

    ![Autorizovat Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    V části **centrum nasazení** v Azure Portal vyberte **pokračovat**.

4. Vyberte jednoho z následujících poskytovatelů sestavení:

    * **Služba sestavení App Service**: nejlepší, pokud nepotřebujete sestavení, nebo pokud potřebujete obecné sestavení.
    * **Azure Pipelines (Preview)** : nejlepší, pokud potřebujete větší kontrolu nad sestavením. Tento zprostředkovatel je aktuálně ve verzi Preview.

    ![Vybrat poskytovatele sestavení](./media/functions-continuous-deployment/build.png)

5. Nakonfigurujte informace specifické pro zadanou možnost správy zdrojového kódu. V případě GitHubu je nutné zadat nebo vybrat hodnoty pro **organizaci**, **úložiště**a **větev**. Hodnoty jsou založené na umístění vašeho kódu. Pak vyberte **pokračovat**.

    ![Konfigurace GitHubu](./media/functions-continuous-deployment/github-specifics.png)

6. Zkontrolujte všechny podrobnosti a pak vyberte **Dokončit** a dokončete konfiguraci nasazení.

    ![Souhrn](./media/functions-continuous-deployment/summary.png)

Po dokončení procesu se do vaší aplikace nasadí veškerý kód ze zadaného zdroje. V tomto okamžiku změny ve zdroji nasazení aktivují nasazení těchto změn do aplikace Function App v Azure.

## <a name="deployment-scenarios"></a>Scénáře nasazení

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Přesunout existující funkce do průběžného nasazování

Pokud jste už napsali funkce v [Azure Portal](https://portal.azure.com) a chcete před přechodem na průběžné nasazování stáhnout obsah vaší aplikace, přejděte na kartu **Přehled** aplikace Function App. Vyberte tlačítko **Stáhnout obsah aplikace** .

![Stáhnout obsah aplikace](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po konfiguraci průběžné integrace už nebudete moct zdrojové soubory upravovat na portálu Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
