---
title: Kurz – konfigurace Kanárských nasazení pro Azure Linux Virtual Machines
description: V tomto kurzu se naučíte nastavit kanál průběžného nasazování (CD). Tento kanál aktualizuje skupinu virtuálních počítačů Azure Linux pomocí strategie pro Kanárské nasazení.
author: moala
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552503"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace strategie pro Kanárské nasazení pro Azure Linux Virtual Machines

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako služba (IaaS) – konfigurace CI/CD

Azure Pipelines poskytuje plnohodnotnou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál nepřetržitého doručování pro virtuální počítač Azure můžete nakonfigurovat z Azure Portal.

V tomto článku se dozvíte, jak nastavit kanál CI/CD, který používá strategii pro nasazení ve více počítačích. Azure Portal také podporuje jiné strategie, jako je například [válcování za provozu](./tutorial-devops-azure-pipelines-classic.md) a [modrou zelenou](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurace CI/CD na virtuálních počítačích

Virtuální počítače můžete přidat jako cíle do [skupiny nasazení](/azure/devops/pipelines/release/deployment-groups). Pak je můžete směrovat na aktualizace pro více počítačů. Po nasazení na počítače můžete zobrazit **historii nasazení** v rámci skupiny nasazení. Toto zobrazení umožňuje trasovat z virtuálního počítače do kanálu a pak na potvrzení.

### <a name="canary-deployments"></a>Kanárské nasazení

Kanárské nasazení snižuje riziko zpomalit změny malých podmnožiny uživatelů. Když získáte jistotu v nové verzi, můžete ji uvolnit na více serverů v infrastruktuře a směrovat na ni více uživatelů.

Pomocí možnosti průběžné doručování můžete z Azure Portal nakonfigurovat Kanárské nasazení na vaše virtuální počítače. Tady je podrobný návod:

1. Přihlaste se k Azure Portal a přejděte k virtuálnímu počítači.
1. V levém podokně nastavení virtuálního počítače vyberte **průběžné doručování**. Pak vyberte **Konfigurovat**.

   ![Podokno nepřetržité doručování pomocí tlačítka konfigurovat](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Na panelu konfigurace vyberte možnost **organizace Azure DevOps** a zvolte existující účet, nebo vytvořte nový. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  

   ![Panel nepřetržitého doručování](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí. Příklady jsou dev, test, UAT a produkce. Můžete vytvořit novou skupinu pro nasazení nebo vybrat některou z existujících.
1. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Publikovaný balíček by měl mít skript nasazení s názvem deploy.ps1 nebo deploy.sh ve složce deployscripts v kořenové složce balíčku. Kanál spustí tento skript nasazení.
1. V **strategii nasazení** vyberte možnost **Kanárské**.
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

1. Ve výchozím nastavení úloha spustit skript nasazení spustí skript nasazení deploy.ps1 nebo deploy.sh. Skript se nachází ve složce deployscripts v kořenové složce publikovaného balíčku. Zajistěte, aby vybraný kanál sestavení publikoval nasazení v kořenové složce balíčku.

   ![Podokno artefakty zobrazující deploy.sh ve složce deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Další strategie nasazení
- [Konfigurace strategie postupné implementace](./tutorial-devops-azure-pipelines-classic.md)
- [Konfigurace strategie nasazení Blue-zelená](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Můžete snadno začít pracovat s Azure. Pomocí Azure DevOps Projects spusťte svou aplikaci v jakékoli službě Azure ve třech krocích, a to tak, že vyberete:

- Jazyk aplikace
- Modul runtime
- Služba Azure

[Další informace](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Další zdroje informací

- [Nasazení na virtuální počítače Azure pomocí Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)