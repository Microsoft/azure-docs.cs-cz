---
title: Kurz – konfigurace kumulovaných nasazení pro virtuální počítače Azure Linux
description: V tomto kurzu se naučíte nastavit kanál průběžného nasazování (CD). Tento kanál postupně aktualizuje skupinu virtuálních počítačů Azure Linux pomocí strategie postupné implementace.
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
ms.openlocfilehash: f77cc85bd62deb2cb2cb74c42cf245a409904b3a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978301"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace strategie kumulativního nasazení pro virtuální počítače Azure Linux

Azure DevOps je integrovaná služba Azure, která automatizuje každou část procesu DevOps pro libovolný prostředek Azure. Ať už vaše aplikace používá virtuální počítače, webové aplikace, Kubernetes nebo jakýkoli jiný prostředek, můžete implementovat infrastrukturu jako kód (IaC), průběžnou integraci, průběžné testování, průběžné doručování a nepřetržité monitorování s využitím Azure a Azure DevOps.

![Azure Portal s Azure DevOps vybraný v části služby](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako služba (IaaS) – konfigurace CI/CD

Azure Pipelines poskytuje plnohodnotnou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál nepřetržitého doručování pro virtuální počítač Azure můžete nakonfigurovat z Azure Portal.

Tento článek popisuje, jak nastavit kanál CI/CD pro zavedení nasazení na více počítačů z Azure Portal. Azure Portal také podporuje jiné strategie, jako je například [Kanárské](./tutorial-azure-devops-canary-strategy.md) and [Blue-zelená](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurace CI/CD na virtuálních počítačích

Virtuální počítače můžete přidat jako cíle do [skupiny nasazení](/azure/devops/pipelines/release/deployment-groups). Pak je můžete směrovat na aktualizace pro více počítačů. Po nasazení na počítače můžete zobrazit **historii nasazení** v rámci skupiny nasazení. Toto zobrazení umožňuje trasovat z virtuálního počítače do kanálu a pak na potvrzení.

### <a name="rolling-deployments"></a>Postupné nasazení

V každé iteraci nahrazuje postupné nasazení instance předchozí verze aplikace. Nahrazuje je instancemi nové verze v pevně dané sadě počítačů (hromadná sada). Následující návod ukazuje, jak nakonfigurovat postupnou aktualizaci virtuálních počítačů.

Pomocí možnosti průběžné doručování můžete ve Azure Portal nakonfigurovat kumulativní aktualizace na své virtuální počítače. Tady je podrobný návod:

1. Přihlaste se k Azure Portal a přejděte k virtuálnímu počítači.
1. V levém podokně nastavení virtuálního počítače vyberte **průběžné doručování**. Pak vyberte **Konfigurovat**.

   ![Podokno nepřetržité doručování pomocí tlačítka konfigurovat](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Na panelu konfigurace vyberte možnost **organizace Azure DevOps** a zvolte existující účet, nebo vytvořte nový. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  

   ![Panel nepřetržitého doručování](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí. Příklady jsou dev, test, UAT a produkce. Můžete vytvořit novou skupinu pro nasazení nebo vybrat některou z existujících.
1. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Publikovaný balíček by měl mít skript nasazení s názvem deploy.ps1 nebo deploy.sh ve složce deployscripts v kořenové složce balíčku. Kanál spustí tento skript nasazení.
1. V **strategii nasazení**vyberte možnost **válcování**.
1. Volitelně můžete označit každý počítač jeho rolí. Příklady jsou značky "Web" a "DB". Tyto značky vám pomůžou cílit jenom na virtuální počítače, které mají konkrétní roli.
1. Vyberte **OK** a nakonfigurujte kanál průběžného doručování.
1. Po dokončení konfigurace máte k dispozici kanál nepřetržitého doručování, který je nakonfigurován pro nasazení na virtuální počítač.  

   ![Panel nepřetržitého doručování znázorňující historii nasazení](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Zobrazí se podrobnosti o nasazení pro virtuální počítač. Můžete vybrat odkaz pro přechod na kanál, **Release-1** pro zobrazení nasazení nebo **Upravit** pro úpravu definice kanálu vydání.

1. Pokud konfigurujete více virtuálních počítačů, opakujte kroky 2 až 4 pro další virtuální počítače, které chcete přidat do skupiny nasazení. Pokud vyberete skupinu nasazení, která už má spuštění kanálu, virtuální počítače se přidají jenom do skupiny nasazení. Nevytvoří se žádné nové kanály.
1. Po dokončení konfigurace vyberte definici kanálu, přejděte k organizaci Azure DevOps a vyberte **Upravit** pro kanál verze.

   ![Úprava postupného kanálu](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Vyberte **1 úlohu, 1 úloha** ve fázi **vývoje** . Vyberte fázi **nasazení** .

   ![Postup při zavádění úloh kanálu pomocí vybrané úlohy nasazení](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. V podokně Konfigurace úplně vpravo můžete zadat počet počítačů, které chcete paralelně nasadit v každé iteraci. Pokud chcete nasadit na více počítačů najednou, můžete pomocí posuvníku určit počet počítačů jako procenta.  

1. Ve výchozím nastavení úloha spustit skript nasazení spustí skript nasazení deploy.ps1 nebo deploy.sh. Skript se nachází ve složce deployscripts v kořenové složce publikovaného balíčku.

   ![Podokno artefakty zobrazující deploy.sh ve složce deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Další strategie nasazení

- [Konfigurovat strategii pro Kanárské nasazení](./tutorial-azure-devops-canary-strategy.md)
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