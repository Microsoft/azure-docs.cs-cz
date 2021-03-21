---
title: Kurz – konfigurace Kanárských nasazení pro virtuální počítače Azure Linux
description: V tomto kurzu se naučíte nastavit kanál průběžného nasazování (CD). Tento kanál aktualizuje skupinu virtuálních počítačů Azure Linux pomocí strategie nasazení Blue-zelená.
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
ms.openlocfilehash: 4545891cce926f049673cd2c2380a8309f2e71a1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552583"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace strategie nasazení Blue-zelená pro virtuální počítače Azure Linux

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako služba (IaaS) – konfigurace CI/CD

Azure Pipelines poskytuje plnohodnotnou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál nepřetržitého doručování pro virtuální počítač Azure můžete nakonfigurovat z Azure Portal.

V tomto článku se dozvíte, jak nastavit kanál CI/CD, který používá strategii Blue-zelená pro nasazení ve více počítačích. Azure Portal také podporuje jiné strategie, jako je například [válcování](./tutorial-devops-azure-pipelines-classic.md) a [kanárskéie](./tutorial-azure-devops-canary-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurace CI/CD na virtuálních počítačích

Virtuální počítače můžete přidat jako cíle do [skupiny nasazení](/azure/devops/pipelines/release/deployment-groups). Pak je můžete směrovat na aktualizace pro více počítačů. Po nasazení na počítače můžete zobrazit **historii nasazení** v rámci skupiny nasazení. Toto zobrazení umožňuje trasovat z virtuálního počítače do kanálu a pak na potvrzení.

### <a name="blue-green-deployments"></a>Nasazení s modrou zeleným prostředím

Nasazování s modrou zelenou snižuje prostoje, protože má stejné pohotovostní prostředí. V jednom okamžiku je aktivní jenom jedno prostředí.

Při přípravě na novou verzi dokončete finální fázi testování v zeleném prostředí. Jakmile software funguje v zeleném prostředí, přepněte provoz tak, aby všechny příchozí požadavky přešly do zeleného prostředí. Modré prostředí je nečinné.

Pomocí možnosti průběžné doručování můžete na své virtuální počítače nakonfigurovat nasazení Blue-zelená z Azure Portal. Tady je podrobný návod:

1. Přihlaste se k Azure Portal a přejděte k virtuálnímu počítači.
1. V levém podokně nastavení virtuálního počítače vyberte **průběžné doručování**. Pak vyberte **Konfigurovat**.

   ![Podokno nepřetržité doručování pomocí tlačítka konfigurovat](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Na panelu konfigurace vyberte možnost **organizace Azure DevOps** a zvolte existující účet, nebo vytvořte nový. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  

   ![Panel nepřetržitého doručování](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí. Příklady jsou dev, test, UAT a produkce. Můžete vytvořit novou skupinu pro nasazení nebo vybrat některou z existujících.
1. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Publikovaný balíček by měl mít skript nasazení s názvem deploy.ps1 nebo deploy.sh ve složce deployscripts v kořenové složce balíčku. Kanál spustí tento skript nasazení.
1. V **strategii nasazení** vyberte **modrou zelenou**.
1. Přidejte "modrou" nebo "zelenou" značku do virtuálních počítačů, které mají být součástí nasazení s modrou zelenou verzí. Pokud je virtuální počítač pro roli v pohotovostním režimu, označte ho jako "zelený". V opačném případě jej označte jako "modrý".

   ![Na panelu nepřetržitého doručování se Blue-Green zvolila hodnota strategie nasazení.](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Výběrem **OK** nakonfigurujte kanál nepřetržitého doručování pro nasazení na virtuální počítač.

   ![Modrý zelený kanál](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Zobrazí se podrobnosti o nasazení pro virtuální počítač. Můžete vybrat odkaz pro přechod na kanál verze v Azure DevOps. V kanálu verze vyberte **Upravit** a zobrazte konfiguraci kanálu. Kanál má tyto tři fáze:

   1. Tato fáze je fáze nasazení-skupina. Aplikace se nasazují na pohotovostní virtuální počítače, které jsou označené jako "zelená".
   1. V této fázi kanál pozastaví a čeká na ruční zásah pro pokračování v běhu. Uživatelé mohou pokračovat v běhu kanálu, jakmile budou ručně zajištěny stabilitu nasazení na virtuální počítače označené jako "zelená".
   1. V této fázi se na virtuálních počítačích zamění značky "Blue" a "zelená". Tím se zajistí, že virtuální počítače se staršími verzemi aplikací jsou teď označené jako "zelená". Během dalšího spuštění kanálu se aplikace nasadí na tyto virtuální počítače.

      ![Podokno skupina nasazení pro úlohu nasazení Blue-Green](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Ve výchozím nastavení úloha spustit skript nasazení spustí skript nasazení deploy.ps1 nebo deploy.sh. Skript se nachází ve složce deployscripts v kořenové složce publikovaného balíčku. Zajistěte, aby vybraný kanál sestavení publikoval nasazení v kořenové složce balíčku.

   ![Podokno artefakty zobrazující deploy.sh ve složce deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Další strategie nasazení

- [Konfigurace strategie postupné implementace](./tutorial-devops-azure-pipelines-classic.md)
- [Konfigurovat strategii pro Kanárské nasazení](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Můžete snadno začít pracovat s Azure. Pomocí Azure DevOps Projects spusťte svou aplikaci v jakékoli službě Azure ve třech krocích, a to tak, že vyberete:

- Jazyk aplikace
- Modul runtime
- Služba Azure

[Další informace](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Další zdroje informací

- [Nasazení na virtuální počítače Azure pomocí Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)