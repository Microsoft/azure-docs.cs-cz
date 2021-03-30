---
title: Průběžné nasazování se službou Azure Functions
description: K publikování vašich funkcí použijte funkce průběžného nasazování Azure App Service.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83123657"
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

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Nastavení průběžného nasazování

Pokud chcete nakonfigurovat průběžné nasazování pro existující aplikaci Function App, proveďte tyto kroky. Kroky ukazují integraci s úložištěm GitHub, ale podobný postup platí pro Azure Repos nebo jiné úložiště zdrojového kódu.

1. Ve vaší aplikaci Function App v [Azure Portal](https://portal.azure.com)vyberte **centrum nasazení**, vyberte **GitHub** a potom vyberte **autorizovat**. Pokud jste už máte autorizovaný GitHub, vyberte **pokračovat** a přejděte k dalšímu kroku. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Centrum nasazení Azure App Service":::

3. V GitHubu vyberte **autorizovat AzureAppService**.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Autorizovat Azure App Service":::

    Zadejte své heslo GitHubu a pak vyberte **pokračovat**.

4. Vyberte jednoho z následujících poskytovatelů sestavení:

    * **Služba sestavení App Service**: nejlepší, pokud nepotřebujete sestavení, nebo pokud potřebujete obecné sestavení.
    * **Azure Pipelines (Preview)**: nejlepší, pokud potřebujete větší kontrolu nad sestavením. Tento zprostředkovatel je aktuálně ve verzi Preview.

    Vyberte **Pokračovat**.

5. Nakonfigurujte informace specifické pro zadanou možnost správy zdrojového kódu. V případě GitHubu je nutné zadat nebo vybrat hodnoty pro **organizaci**, **úložiště** a **větev**. Hodnoty jsou založené na umístění vašeho kódu. Pak vyberte **pokračovat**.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="Konfigurace GitHubu":::

6. Zkontrolujte všechny podrobnosti a pak vyberte **Dokončit** a dokončete konfiguraci nasazení.

Po dokončení procesu se do vaší aplikace nasadí veškerý kód ze zadaného zdroje. V tomto okamžiku změny ve zdroji nasazení aktivují nasazení těchto změn do aplikace Function App v Azure.

> [!NOTE]
> Po konfiguraci průběžné integrace už nebudete moct zdrojové soubory upravovat na portálu Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
