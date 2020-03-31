---
title: Integrace prostředí do Azure Pipelines v Azure DevTest Labs
description: Zjistěte, jak integrovat prostředí Azure DevTest Labs do vašich kanálů průběžné integrace Azure DevOps (CI) a průběžného doručování (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169425"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrace prostředí do vašich kanálů Azure DevOps CI/CD
Pomocí rozšíření Azure DevTest Labs Tasks, které je nainstalované ve službách Azure DevOps Services (dříve označované jako Visual Studio Team Services), můžete snadno integrovat vaše průběžné integrace (CI)/ průběžné doručování (CD) sestavení a vydání kanálu s Azure DevTest Labs. Tato rozšíření usnadňují rychlé nasazení [prostředí](devtest-lab-test-env.md) pro konkrétní testovací úlohu a po dokončení testu jej odstraňte. 

Tento článek ukazuje, jak vytvořit a nasadit prostředí, pak odstranit prostředí, vše v jednom kompletní matné kanál. Obvykle byste provádět každý z těchto úkolů jednotlivě ve vlastním sestavení test nasazení kanálu. Rozšíření použitá v tomto článku jsou navíc k těmto [úlohám vytvoření a odstranění virtuálního virtuálního soudu DTL](devtest-lab-integrate-ci-cd-vsts.md):

- Vytvoření prostředí
- Odstranění prostředí

## <a name="before-you-begin"></a>Než začnete
Než budete moci integrovat kanál CI/CD s Azure DevTest Labs, nainstalujte rozšíření [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) z webu Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Vytvoření a konfigurace testovacího prostředí pro prostředí
Tato část popisuje, jak vytvořit a nakonfigurovat testovací prostředí, do kterého se bude nasazovat prostředí Azure.

1. [Vytvořte testovací prostředí,](devtest-lab-create-lab.md) pokud ho ještě nemáte. 
2. Nakonfigurujte testovací prostředí a vytvořte šablonu prostředí podle následujících pokynů z tohoto článku: [Vytvoření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. V tomto příkladu použijte existující [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)šablonu Azure QuickStart .
4. Zkopírujte složku **201-web-app-redis-cache-sql-database** do složky **ArmTemplate** v úložišti nakonfigurovaném v kroku 2.

## <a name="create-a-release-definition"></a>Vytvoření definice verze
Chcete-li vytvořit definici verze, postupujte takto:

1.  Na kartě **Zprávy** v **centru Build & Release vyberte**tlačítko **znaménko plus (+).**
2.  V okně **Vytvořit definici vydání** vyberte šablonu **Prázdné** a pak vyberte **Další**.
3.  Vyberte **Zvolit později**a pak vyberte **Vytvořit,** chcete-li vytvořit novou definici verze s jedním výchozím prostředím a bez propojených artefaktů.
4.  Chcete-li otevřít místní nabídku, vyberte v nové definici verze **tři tečky (...)** vedle názvu prostředí a pak vyberte **Konfigurovat proměnné**.
5.  V okně **Konfigurovat prostředí** zadejte pro proměnné, které používáte v úlohách definice verze, následující hodnoty:
1.  Pro **administratorLogin**zadejte přihlašovací jméno správce SQL.
2.  Pro **administratorLoginPassword**zadejte heslo, které má být použito přihlášením správce SQL. Pomocí ikony "visací zámek" skryjte a zabezpečte heslo.
3.  Pro **název databáze**zadejte název databáze SQL.
4.  Tyto proměnné jsou specifické pro ukázková prostředí, různá prostředí mohou mít různé proměnné.

## <a name="create-an-environment"></a>Vytvoření prostředí
Další fází nasazení je vytvoření prostředí, které se použije pro účely vývoje nebo testování.

1. V definici verze vyberte **Přidat úkoly**.
2. Na kartě **Úkoly** přidejte úlohu vytvoření prostředí Azure DevTest Labs. Nakonfigurujte úlohu následujícím způsobem:
    1. U **předplatného Azure RM**vyberte připojení v seznamu **Dostupná připojení ke službám Azure** nebo vytvořte omezenější připojení k předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. V **části Název testovacího prostředí**vyberte název instance, kterou jste vytvořili dříve*.
3. V **části Název úložiště**vyberte úložiště, do kterého byla šablona Správce prostředků (201) posunuta*.
4. V **části Název šablony**vyberte název prostředí, které jste uložili do úložiště zdrojového kódu*. 
5. **Název testovacího prostředí**, název **úložiště**a **název šablony** jsou popisné reprezentace ID prostředků Azure. Ruční zadání popisný název způsobí selhání, použijte rozevírací seznamy pro výběr informací.
6. Do **pole Název prostředí**zadejte název, který jednoznačně identifikuje instanci prostředí v rámci testovacího prostředí.  Musí to být jedinečné v laboratoři.
7. **Soubor parametrů** a **Parametry**umožňují předání vlastních parametrů prostředí. K nastavení hodnot parametrů lze použít jeden nebo oba. V tomto příkladu bude použita část Parametry. Použijte názvy proměnných, které jste definovali v prostředí, například:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Informace v rámci šablony prostředí lze předat ve výstupní části šablony. Zaškrtněte **políčko Vytvořit výstupní proměnné na základě výstupu šablony prostředí,** aby ostatní úkoly mohly data používat. `$(Reference name.Output Name)`je vzor následovat. Například pokud název odkazu byl DTL a výstupní název v šabloně bylo umístění proměnné by `$(DTL.location)`.

## <a name="delete-the-environment"></a>Odstranit prostředí
Poslední fáze je odstranit prostředí, které jste nasadili v instanci Azure DevTest Labs. Byste obvykle odstranit prostředí po provedení dev úlohy nebo spustit testy, které potřebujete na nasazené prostředky.

V definici verze vyberte **Přidat úkoly**a potom na kartě **Deploy** přidejte úlohu **Azure DevTest Labs Delete Environment.** Nakonfigurujte ji takto:

1. Pokud chcete virtuální počítač odstranit, přečtěte si část [Úlohy laboratoří Azure DevTest :](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
    1. U **předplatného Azure RM**vyberte připojení v seznamu **Dostupná připojení ke službám Azure** nebo vytvořte omezenější připojení k předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. V **části Název testovacího prostředí**vyberte testovací prostředí, ve kterém prostředí existuje.
    3. Do **pole Název prostředí**zadejte název prostředí, které má být odebráno.
2. Zadejte název definice verze a uložte ji.

## <a name="next-steps"></a>Další kroky
Viz následující články: 
- [Vytvořte prostředí s více virtuálními počítačemi pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md).
- Šablony Správce prostředků rychlého startu pro automatizaci DevTest Labs z [úložiště GitHub DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Stránka Řešení potíží v VSTS](/azure/devops/pipelines/troubleshooting)

