---
title: Kurz – integrované devops pro IaaS a PaaS v Azure
description: V tomto kurzu se dozvíte, jak nastavit průběžnou integraci (CI) a průběžné nasazování (CD) aplikace do virtuálních stránek Azure pomocí Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77912524"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Kurz: Integrované devOps pro IaaS a PaaS v Azure

S komplexními řešeními v Azure můžou týmy implementovat postupy DevOps v každé fázi životního cyklu aplikace: plánovat, vyvíjet, dodávat a provozovat. 

Níže jsou uvedeny některé služby Azure, které zjednodušují cloudové úlohy a lze je kombinovat tak, aby umožňovaly úžasně výkonné scénáře.
Tyto technologie v kombinaci s lidmi a procesy umožňují týmům neustále poskytovat zákazníkům hodnotu. 

- Azure: https://portal.azure.com – portál pro vytváření cloudových úloh. Správa a monitorování všeho od jednoduchých webových aplikací až po složité cloudové aplikace 
- Azure DevOps: https://dev.azure.com – Plánujte chytřeji, lépe spolupracujte a doručujte rychleji pomocí sady moderních dev služeb 
- Azure Machine Learning https://ml.azure.com studio: – příprava dat, trénování a nasazování modelů strojového učení 
 

Azure DevOps je integrovaná služba Azure, která automatizuje každou část procesu DevOps s průběžnou integrací a nepřetržitým doručováním pro všechny prostředky Azure.
Ať už vaše aplikace používá virtuální počítače, webové aplikace, Kubernetes nebo jiný prostředek, můžete implementovat infrastrukturu jako kód, průběžnou integraci, průběžné testování, průběžné doručování a průběžné monitorování pomocí Azure a Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Konfigurace CI/CD 
Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál průběžného doručování pro virtuální počítač Azure můžete nakonfigurovat přímo z portálu Azure. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD pro nasazení více počítačů z portálu Azure. Konfigurace CI/CD na virtuálních počítačích.

Virtuální počítače lze přidat jako cíle do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) a lze je zacílit na postupné aktualizace s více počítači. Zobrazení historie nasazení v rámci skupin nasazení poskytují sledovatelnost z virtuálního počítače do kanálu a potom do potvrzení. 
 
**Postupné aktualizace**: Postupné nasazení nahradí instance předchozí verze aplikace instancemi nové verze aplikace na pevné sadě počítačů (válcovací sada) v každé iteraci. Pojďme návod, jak můžete nakonfigurovat postupné aktualizace do virtuálních počítačů.  
Postupné aktualizace virtuálních**počítačů**můžete nakonfigurovat v rámci portálu Azure pomocí možnosti průběžného doručování. 

Zde je podrobný návod. 
1. Přihlaste se k portálu Azure a přejděte na virtuální počítač. 
2. V levém podokně virtuálního provozu přejděte do nabídky **průběžného doručování.**  Poté klepněte na **tlačítko Konfigurovat**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. V konfiguračním panelu klikněte na "Azure DevOps Organization" a vyberte existující účet nebo ho vytvořte. Pak vyberte projekt, ve kterém chcete nakonfigurovat kanál.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Skupina nasazení je logická sada cílových počítačů nasazení, které představují fyzické prostředí; například "Dev", "Test", "UAT" a "Výroba". Můžete vytvořit novou skupinu nasazení nebo vybrat existující skupinu nasazení. Volitelně můžete označit počítač s rolí. Například "web", "db" atd.  
5. Klepnutím na **tlačítko OK** v dialogovém okně nakonfigurujete kanál průběžného doručování. 
6. Po dokončení budete mít kanál průběžného doručování nakonfigurovaný pro nasazení do virtuálního počítače.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Uvidíte, že probíhá nasazení do virtuálního počítače. Kliknutím na odkaz přejdete do kanálu. Klikněte na **Release-1** pro zobrazení nasazení. Nebo můžete kliknout na **upravit** upravit definici kanálu vydání. 
8. Pokud máte více virtuálních počítačů, které mají být nakonfigurovány, opakujte kroky 2-5, pro ostatní virtuální počítače, které mají být přidány do skupiny nasazení. 
9. Po dokončení klikněte na definici kanálu, přejděte do organizace Azure DevOps a klikněte na **Upravit** kanál vydání. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Klikněte na odkaz **1 práce, 1 úkol** ve fázi **dev.** Klikněte na fázi **Nasazení.**  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. Z konfiguračního podokna vpravo vidíte, že ve výchozím nastavení je kanál nakonfigurován tak, aby souběžně provázkoval průběžnou aktualizaci všech cílů. Pomocí posuvníku můžete nakonfigurovat nasazení tak, aby k nim došlo po jednom nebo v procentech.  
  
  
**Kanárek** snižuje riziko tím, že pomalu zavádí změnu na malou podmnožinu uživatelů. Jak získáte větší důvěru v novou verzi, můžete ji začít vydávat na více serverů ve vaší infrastruktuře a směrovat na něj více uživatelů. Kanárská nasazení můžete nakonfigurovat na vašem "**virtuálních počítačích**" pomocí portálu Azure pomocí možnosti průběžného doručování. Zde je podrobný návod. 
1. Přihlaste se na svůj portál Azure a přejděte na virtuální počítač 
2. Podle pokynů 2-5 v předchozí části přidejte více virtuálních počítačů do skupiny nasazení. 
3. Přidejte vlastní značku do virtuálních počítače, které mají být součástí kanárských nasazení. Například "kanárek".
4. Po nakonfigurovaném kanálu pro virtuální počítače klikněte na kanál, spusťte organizaci Azure DevOps, **upravte** kanál a přejděte do fáze **dev.** Přidejte značku do filtru "kanárek". 
5. Přidejte další fázi skupiny nasazení, nakonfigurujte fázi pomocí značek tak, aby cílila na zbývající virtuální počítače ve skupině nasazení.  
6. Volitelně můžete nakonfigurovat krok ručního ověření, který může podpořit nebo odmítnout nasazení kanárských zařízení. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blue-Green** snižuje prostoje nasazení tím, že má stejné pohotovostní prostředí. V každém okamžiku je jedno z prostředí živé. Jak se připravujete na novou verzi, děláte poslední fázi testování v zeleném prostředí. Jakmile software pracuje v zeleném prostředí, přepněte provoz tak, aby všechny příchozí požadavky přecházejí do zeleného prostředí - modré prostředí je nyní nečinné.
Nasazení Blue-Green můžete nakonfigurovat na vašem "**virtuálních počítačích**" z portálu Azure pomocí možnosti průběžného doručování. 

Zde je podrobný návod. 

1. Přihlaste se na svůj portál Azure a přejděte na virtuální počítač 
2. Podle pokynů 2 až 5 v části **Rolling aktualizace** přidat více virtuálních počítačů do skupiny nasazení. Přidejte vlastní značku do virtuálních počítače, které mají být součástí modrozelené nasazení. Například "modrá" nebo "zelená" pro virtuální uživatele, které jsou pro pohotovostní roli. 
3. Po nakonfigurované montovny pro virtuální počítače klikněte na kanál, spusťte organizaci Azure DevOps, **upravte** kanál a přejděte na fázi **dev.** Přidejte značku do filtru "zelená". 
4. Přidejte fázi bez agenta, nakonfigurujte fázi s krokem ručního ověření a krok rozhraní API invoke-REST pro výměnu značek. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Projekt Azure DevOps 
S Azure můžete začít snadněji než kdy dřív.
 
S DevOps Projects můžete spustit aplikaci v libovolné službě Azure v pouhých třech krocích: vyberte jazyk aplikace, runtime a službu Azure.
 
[Další informace](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Další zdroje 
- [Nasazení do virtuálních počítačů Azure pomocí projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementace průběžného nasazování aplikace do škálovací sady virtuálních strojů Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
