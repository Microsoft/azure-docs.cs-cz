---
title: Použití DevTest Labs v kanálech sestavování a vydávání verzí Azure Pipelines
description: Zjistěte, jak používat Azure DevTest Labs v Azure Pipelines sestavení a vydání kanály.
services: devtest-lab, lab-services
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
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169226"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Použití DevTest Labs v kanálech sestavování a vydávání verzí Azure Pipelines
Tento článek obsahuje informace o tom, jak devTest Labs lze použít v Azure pipelines sestavení a uvolnění kanály. 

## <a name="overall-flow"></a>Celkový průtok
Základní tok je mít **kanál sestavení,** který provádí následující úkoly:

1. Vytvořte kód aplikace.
1. Vytvořte základní prostředí v DevTest Labs.
1. Aktualizujte prostředí vlastními informacemi.
1. Nasazení aplikace do prostředí DevTest Labs
1. Otestujte kód. 

Po úspěšném dokončení sestavení bude **kanál vydání** používat artefakty sestavení k nasazení pracovní nebo produkční. 

Jedním z nezbytných předpokladů je, že všechny informace potřebné k opětovnému vytvoření testovaného ekosystému jsou k dispozici v rámci artefaktů sestavení, včetně konfigurace prostředků Azure. Vzhledem k tomu, že prostředky Azure vznikají náklady při použití, společnosti chtějí řídit nebo sledovat použití těchto prostředků. V některých situacích mohou být šablony Azure Resource Manageru, které se používají k vytvoření a konfiguraci prostředků, spravovány jiným oddělením, jako je IT. A tyto šablony mohou být uloženy v jiném úložišti. To vede k zajímavé situaci, kdy sestavení bude vytvořena a testována a kód a konfigurace bude muset být uloženy v rámci artefakty sestavení správně znovu vytvořit systém v produkčním prostředí. 

Pomocí DevTest Labs během fáze sestavení/testování můžete přidat šablony Azure Resource Manager a podpůrné soubory do zdrojů sestavení tak, aby během fáze vydání přesná konfigurace použitá k testování se nasazuje do produkčního prostředí. **Úloha Vytvořit prostředí Azure DevTest Labs** se správnou konfigurací uloží šablony Správce prostředků v rámci artefaktů sestavení. V tomto příkladu budete používat kód z [kurzu: Vytvoření webové aplikace .NET Core a SQL Database ve službě Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md), k nasazení a testování webové aplikace v Azure.

![Celkový průtok](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure
Existuje několik položek, které je třeba vytvořit předem:

- Dvě repozitáře. První s kódem z kurzu a šablonou Správce prostředků se dvěma dalšími virtuálními počítačemi. Druhá bude obsahovat základní šablonu Azure Resource Manager (existující konfigurace).
- Skupina prostředků pro nasazení produkčního kódu a konfigurace.
- Testovací prostředí musí být nastaveno s [připojením k úložišti konfigurace](devtest-lab-create-environment-from-arm.md) pro kanál sestavení. Šablona Správce prostředků musí být vrácena se změnami do úložiště konfigurace jako azuredeploy.json s metadata.json, aby devTest Labs mohla rozpoznat a nasadit šablonu.

Kanál sestavení vytvoří prostředí DevTest Labs a nasadí kód pro testování.

## <a name="set-up-a-build-pipeline"></a>Nastavení kanálu sestavení
V Azure Pipelines vytvořte kanál sestavení pomocí kódu z [kurzu: Vytvoření webové aplikace .NET Core a SQL Database ve službě Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md). Použijte **šablonu ASP.NET Core,** která naplní potřebný úkol k sestavení, testování a publikování kódu.

![Výběr ASP.NET šablony](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Je třeba přidat tři další úkoly k vytvoření prostředí v DevTest Labs a nasadit do prostředí.

![Kanál se třemi úkoly](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Vytvořit úlohu prostředí
V úloze vytvořit prostředí (Azure**DevTest Labs vytvořit prostředí** úkol) použijte rozevírací seznamy vybrat následující hodnoty:

- Předplatné Azure
- název laboratoře
- název úložiště
- název šablony (která zobrazuje složku, ve které je prostředí uloženo). 

Doporučujeme použít rozevírací seznamy na stránce namísto ručního zadávání informací. Pokud zadáte informace ručně, zadejte plně kvalifikovaná ID prostředků Azure. Úkol zobrazí popisné názvy namísto ID prostředků. 

Název prostředí je zobrazený název zobrazený v devTest Labs. Měl by se jedná o jedinečný název pro každé sestavení. Například: **TestEnv$(Build.BuildId)**. 

Můžete zadat buď parametry souboru nebo parametry pro předávání informací do šablony Správce prostředků. 

Vyberte volbu Vytvořit výstupní proměnné na základě výstupní **volby šablony prostředí** a zadejte název odkazu. V tomto příkladu zadejte **BaseEnv** pro název odkazu. Tento BaseEnv použijete při konfiguraci dalšího úkolu. 

![Vytvoření úlohy prostředí Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Naplnit úlohu prostředí
Druhý úkol **(Azure DevTest Labs naplnit** prostředí úkol) je aktualizace existující hosto. Create environment task outputs **BaseEnv.environmentResourceId** that's used to configure environment name for this task. Šablona Správce prostředků pro tento příklad má dva parametry – **adminUserName** a **adminPassword**. 

![Naplnění úlohy prostředí Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Úloha nasazení služby App Service
Třetí úlohou je úloha **nasazení služby Azure App Service.** The app type is set to **Web App** and the App Service name is set to **$(WebSite)**.

![Úloha nasazení služby App Service](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Nastavit kanál vydání
Vytvoříte kanál vydání se dvěma úkoly: **Nasazení Azure: Vytvořit nebo aktualizovat skupinu prostředků** a **nasadit službu Azure App Service**. 

Pro první úkol zadejte název a umístění skupiny prostředků. Umístění šablony je propojený artefakt. Pokud šablona Správce prostředků obsahuje propojené šablony, je třeba implementovat vlastní nasazení skupiny prostředků. Šablona je v publikovaném artefaktu přetažení. Přepsat parametry šablony pro šablonu Správce prostředků. Zbývající nastavení můžete ponechat s výchozími hodnotami. 

Pro druhý úkol **nasazení služby Azure App Service**zadejte předplatné Azure, vyberte Web **App** pro **typ aplikace**a **$(WebSite)** pro **název služby App Service**. Zbývající nastavení můžete ponechat s výchozími hodnotami. 

## <a name="test-run"></a>Zkušební běh
Teď, když jsou nastaveny oba kanály, ručně zařazujte sestavení do fronty a uvidíte, že funguje. Dalším krokem je nastavení příslušné aktivační události pro sestavení a připojení sestavení k kanálu vydání.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Integrace azure devtest labs do vašeho kanálu Azure průběžné integrace a doručovací kanál](devtest-lab-integrate-ci-cd-vsts.md)
- [Integrace prostředí do vašich kanálů CI/CD Azure Pipelines](integrate-environments-devops-pipeline.md)
