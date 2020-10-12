---
title: Použití DevTest Labs v kanálech sestavování a vydávání verzí Azure Pipelines
description: Naučte se používat Azure DevTest Labs v Azure Pipelines vytváření a vydávání kanálů.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080330"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Použití DevTest Labs v kanálech sestavování a vydávání verzí Azure Pipelines
Tento článek poskytuje informace o tom, jak se DevTest Labs dají použít v Azure Pipelines kanálech sestavení a vydání. 

## <a name="overall-flow"></a>Celkový tok
Základním tokem je **vytvořit kanál sestavení** , který provede následující úlohy:

1. Sestavte kód aplikace.
1. Vytvořte základní prostředí v DevTest Labs.
1. Aktualizujte prostředí o vlastní informace.
1. Nasazení aplikace do prostředí DevTest Labs
1. Otestujte kód. 

Po úspěšném dokončení sestavení bude **kanál verze** používat artefakty sestavení k nasazení přípravy nebo výroby. 

Jedním z nezbytných míst je, že všechny informace potřebné k opětovnému vytvoření testovaného ekosystému jsou k dispozici v rámci artefaktů sestavení, včetně konfigurace prostředků Azure. Jelikož se prostředky Azure účtují při použití, mají společnosti možnost řídit nebo sledovat používání těchto prostředků. V některých situacích se Azure Resource Manager šablon používaných k vytváření a konfiguraci prostředků dá spravovat přes jiné oddělení, jako je IT. A tyto šablony mohou být uloženy v jiném úložišti. Vede na zajímavou situaci, kdy se vytvoří a otestuje sestavení, a jak kód, tak konfigurace budou muset být uloženy v rámci artefaktů sestavení, aby bylo možné správně znovu vytvořit systém v produkčním prostředí. 

Pomocí DevTest Labs během fáze sestavení/testování můžete přidat šablony Azure Resource Manager a podpůrné soubory do zdrojů sestavení tak, aby během fáze vydání byla přesná konfigurace, která se pro testování používala, nasadila v produkčním prostředí. Úloha **vytvoření Azure DevTest Labs prostředí** se správnou konfigurací uloží šablony Správce prostředků v rámci artefaktů sestavení. V tomto příkladu budete používat kód z [kurzu: Vytvoření webové aplikace .NET Core a SQL Database v Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md)pro nasazení a testování webové aplikace v Azure.

![Celkový tok](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure
Existuje několik položek, které je potřeba vytvořit předem:

- Dvě úložiště. První z nich s kódem z kurzu a šablony Správce prostředků se dvěma dalšími virtuálními počítači. Druhý bude obsahovat základní šablonu Azure Resource Manager (existující konfigurace).
- Skupina prostředků pro nasazení produkčního kódu a konfigurace.
- Je potřeba nastavit testovací prostředí s [připojením k úložišti konfigurace](devtest-lab-create-environment-from-arm.md) pro kanál sestavení. Šablonu Správce prostředků je třeba zkontrolovat do úložiště konfigurace jako azuredeploy.jss metadata.jsna, aby mohl DevTest Labs tuto šablonu rozpoznat a nasadit.

Kanál sestavení vytvoří prostředí DevTest Labs a nasadí kód pro testování.

## <a name="set-up-a-build-pipeline"></a>Nastavení kanálu sestavení
V Azure Pipelines vytvořte kanál sestavení pomocí kódu z [kurzu: Vytvoření webové aplikace .NET Core a SQL Database v Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md). Použijte šablonu **ASP.NET Core** , která naplní nezbytný úkol pro sestavení, testování a publikování kódu.

![Vybrat šablonu ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Abyste mohli vytvořit prostředí v DevTest Labs a nasadit ho do prostředí, musíte přidat tři další úlohy.

![Kanál se třemi úlohami](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Vytvořit úlohu prostředí
V úloze vytvoření prostředí (**Azure DevTest Labs vytvoření úlohy prostředí** ) použijte rozevírací seznam a vyberte následující hodnoty:

- Předplatné Azure
- název testovacího prostředí
- název úložiště
- název šablony (zobrazuje složku, ve které je prostředí uložené). 

Místo ručního zadávání informací doporučujeme používat na stránce rozevírací seznamy. Pokud ručně zadáte tyto informace, zadejte plně kvalifikované ID prostředků Azure. Tato úloha zobrazí popisné názvy namísto ID prostředků. 

Název prostředí je zobrazený název zobrazený v DevTest Labs. Pro každé sestavení by měl být jedinečný název. Příklad: **TestEnv $ (Build. buildId)**. 

Můžete zadat buď parametry souboru parametrů, nebo parametry k předání informací do šablony Správce prostředků. 

Vyberte možnost **vytvořit výstupní proměnné na základě výstupu šablony prostředí** a zadejte název odkazu. V tomto příkladu zadejte **BaseEnv** pro název odkazu. Tento BaseEnv použijete při konfiguraci dalšího úkolu. 

![Vytvořit úlohu prostředí Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Naplnit úlohu prostředí
Druhý úkol (**Azure DevTest Labs naplnit úlohu prostředí** ) je aktualizovat existující prostředí DevTest Labs. Výstupy úlohy vytvoření prostředí **BaseEnv. environmentResourceId** , které se používají ke konfiguraci názvu prostředí pro tento úkol. Šablona Správce prostředků pro tento příklad má dva parametry – **adminUserName** a **adminPassword**. 

![Naplnit úlohu Azure DevTest Labs prostředí](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Úloha nasazení App Service
Třetí úlohou je úloha **nasazení Azure App Service** . Typ aplikace je nastavený na **Webová aplikace** a název App Service je nastavený na **$ (Web)**.

![Úloha nasazení App Service](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Nastavení kanálu pro vydávání verzí
Vytvoříte kanál verze se dvěma úlohami: **nasazení Azure: vytvořit nebo aktualizovat skupinu prostředků** a **nasadit Azure App Service**. 

Pro první úlohu zadejte název a umístění skupiny prostředků. Umístění šablony je propojený artefakt. Pokud šablona Správce prostředků obsahuje propojené šablony, je nutné implementovat vlastní nasazení skupiny prostředků. Šablona je v publikovaném artefaktu zrušení. Přepsat parametry šablony pro šablonu Správce prostředků. Zbývající nastavení můžete nechat s výchozími hodnotami. 

Pro druhý **Azure App Service nasazení**úlohy zadejte předplatné Azure, jako **Typ aplikace**vyberte **Web App** a jako **název App Service** **$ (Web)** . Zbývající nastavení můžete nechat s výchozími hodnotami. 

## <a name="test-run"></a>Testovací běh
Teď, když jsou oba kanály nastavené, ručně zařadí sestavení do fronty a zjistí, že funguje. Dalším krokem je nastavení vhodné aktivační události pro sestavení a připojení sestavení k kanálu vydání.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Integrace Azure DevTest Labs do Azure Pipelines nepřetržité integrace a kanálu doručování](devtest-lab-integrate-ci-cd.md)
- [Integrace prostředí do Azure Pipelines kanálů CI/CD](integrate-environments-devops-pipeline.md)
