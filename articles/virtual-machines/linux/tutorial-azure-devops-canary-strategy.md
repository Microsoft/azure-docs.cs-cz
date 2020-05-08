---
title: Kurz – konfigurace Kanárských nasazení pro Azure Linux Virtual Machines
description: V tomto kurzu se naučíte nastavit kanál průběžného nasazování (CD). Tento kanál aktualizuje skupinu virtuálních počítačů Azure Linux pomocí strategie pro Kanárské nasazení.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: e0fb26896b79fb23bb0f784c0f23aa3af0593c22
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871851"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace strategie pro Kanárské nasazení pro Azure Linux Virtual Machines

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako služba (IaaS) – konfigurace CI/CD

Azure Pipelines poskytuje plnohodnotnou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál nepřetržitého doručování pro virtuální počítač Azure můžete nakonfigurovat z Azure Portal.

V tomto článku se dozvíte, jak nastavit kanál CI/CD, který používá strategii pro nasazení ve více počítačích. Azure Portal také podporuje jiné strategie, jako je například [válcování za provozu](https://aka.ms/AA7jlh8) a [modrou zelenou](https://aka.ms/AA83fwu).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurace CI/CD na virtuálních počítačích

Virtuální počítače můžete přidat jako cíle do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). Pak je můžete směrovat na aktualizace pro více počítačů. Po nasazení na počítače můžete zobrazit **historii nasazení** v rámci skupiny nasazení. Toto zobrazení umožňuje trasovat z virtuálního počítače do kanálu a pak na potvrzení.

### <a name="canary-deployments"></a>Kanárské nasazení

Kanárské nasazení snižuje riziko zpomalit změny malých podmnožiny uživatelů. Když získáte jistotu v nové verzi, můžete ji uvolnit na více serverů v infrastruktuře a směrovat na ni více uživatelů.

Pomocí možnosti průběžné doručování můžete z Azure Portal nakonfigurovat Kanárské nasazení na vaše virtuální počítače. Tady je podrobný návod:

1. Přihlaste se k Azure Portal a přejděte k virtuálnímu počítači.
1. V levém podokně nastavení virtuálního počítače vyberte **průběžné doručování**. Pak vyberte **Konfigurovat**.

   ![Podokno nepřetržité doručování pomocí tlačítka konfigurovat](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Na panelu konfigurace vyberte možnost **organizace Azure DevOps** a zvolte existující účet, nebo vytvořte nový. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  

   ![Panel nepřetržitého doručování](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí. Příklady jsou dev, test, UAT a produkce. Můžete vytvořit novou skupinu pro nasazení nebo vybrat některou z existujících.
1. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Publikovaný balíček by měl mít skript nasazení s názvem Deploy. ps1 nebo deploy.sh ve složce deployscripts v kořenové složce balíčku. Kanál spustí tento skript nasazení.
1. V **strategii nasazení**vyberte možnost **Kanárské**.
1. Přidejte značku "Kanárské" do virtuálních počítačů, které jsou součástí Kanárských nasazení. Přidejte značku "prod" do virtuálních počítačů, které jsou součástí nasazení provedených po úspěšném nasazení. Značky umožňují cílit jenom na virtuální počítače, které mají konkrétní roli.

   ![Panel nepřetržitého doručování se zvolenou hodnotou strategie nasazení](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Výběrem **OK** nakonfigurujte kanál nepřetržitého doručování pro nasazení na virtuální počítač.

   ![Kanál pro Kanárské ostrovy](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Zobrazí se podrobnosti o nasazení pro virtuální počítač. Můžete vybrat odkaz pro přechod na kanál verze v Azure DevOps. V kanálu verze vyberte **Upravit** a zobrazte konfiguraci kanálu. Kanál má tyto tři fáze:

   1. Tato fáze je fáze nasazení-skupina. Aplikace se nasazují do virtuálních počítačů, které jsou označené jako "Kanárské".
   1. V této fázi kanál pozastaví a čeká na ruční zásah pro pokračování v běhu.
   1. Toto je znovu fáze skupiny nasazení. Aktualizace je nyní nasazena do virtuálních počítačů označených jako "prod".

      ![Podokno skupina nasazení pro úlohu nasadit Kanárské](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Před pokračováním spuštění kanálu zajistěte, aby byl aspoň jeden virtuální počítač označený jako "prod". Ve třetí fázi kanálu se aplikace nasazují jenom na virtuální počítače, které mají značku "prod".

1. Úloha spustit skript pro nasazení ve výchozím nastavení spustí skript nasazení Deploy. ps1 nebo deploy.sh. Skript se nachází ve složce deployscripts v kořenové složce publikovaného balíčku. Zajistěte, aby vybraný kanál sestavení publikoval nasazení v kořenové složce balíčku.

   ![Podokno artefakty zobrazující deploy.sh ve složce deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Další strategie nasazení
- [Konfigurace strategie postupné implementace](https://aka.ms/AA7jlh8)
- [Konfigurace strategie nasazení Blue-zelená](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Můžete snadno začít pracovat s Azure. Pomocí Azure DevOps Projects spusťte svou aplikaci v jakékoli službě Azure ve třech krocích, a to tak, že vyberete:

- Jazyk aplikace
- Modul runtime
- Služba Azure

[Další informace](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Další zdroje

- [Nasazení na virtuální počítače Azure pomocí Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
