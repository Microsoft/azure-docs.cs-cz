---
title: Poznámky k verzi pro Application Insights | Microsoft Docs
description: Přidejte značky nasazení nebo sestavení do grafů Průzkumníka metrik v Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: 9dbdd683a8545e0f8c573dfba60daa96ef5ff08d
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677856"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky na grafech metrik v Application Insights

Poznámky v [Průzkumník metrikch](../../azure-monitor/app/metrics-explorer.md) grafech ukazují, kde jste nasadili nové sestavení, nebo jiné významné události. Poznámky usnadňují zjištění, zda vaše změny měly vliv na výkon vaší aplikace. Mohou být automaticky vytvořeny [Azure Pipelinesm](https://docs.microsoft.com/azure/devops/pipelines/tasks/) systémem sestavení. Můžete také vytvořit poznámky k označení libovolné události, kterou chcete, vytvořením z PowerShellu.

> [!NOTE]
> Tento článek odráží nepoužívané **klasické prostředí metrik**. Poznámky jsou aktuálně k dispozici pouze v klasickém prostředí a v **[sešitech](../../azure-monitor/app/usage-workbooks.md)** . Další informace o aktuálním prostředí metrik najdete v tématu [Pokročilé funkce služby Azure Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md).

![Příklad poznámek](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Poznámky k verzi s Azure Pipelines Build

Poznámky k verzi jsou součástí cloudové Azure Pipelines služby Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalace rozšíření pro poznámky (jednorázově)
Aby bylo možné vytvářet poznámky k verzi, budete muset nainstalovat jednu z mnoha rozšíření Azure DevOps, která jsou k dispozici v Visual Studio Marketplace.

1. Přihlaste se ke svému projektu [Azure DevOps](https://azure.microsoft.com/services/devops/) .
   
1. Na stránce [rozšíření pro poznámky k verzi](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) Visual Studio Marketplace vyberte vaši organizaci Azure DevOps a pak výběrem **nainstalovat** přidejte rozšíření do vaší organizace Azure DevOps.
   
   ![Vyberte organizaci Azure DevOps a pak vyberte nainstalovat.](./media/annotations/1-install.png)
   
Pro vaši organizaci Azure DevOps stačí rozšíření nainstalovat jenom jednou. Nyní můžete nakonfigurovat poznámky k verzi pro libovolný projekt ve vaší organizaci.

### <a name="configure-release-annotations"></a>Konfigurovat poznámky k verzi

Vytvořte samostatný klíč rozhraní API pro každou ze šablon verze Azure Pipelines.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaši aplikaci. Pokud ho ještě nemáte, [vytvořte nový prostředek Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Otevřete kartu **přístup přes rozhraní API** a zkopírujte **ID Application Insights**.
   
   ![V části přístup k rozhraní API zkopírujte ID aplikace.](./media/annotations/2-app-id.png)

1. V samostatném okně prohlížeče otevřete nebo vytvořte šablonu vydané verze, která spravuje nasazení Azure Pipelines.
   
1. Vyberte **Přidat úlohu**a potom z nabídky vyberte úlohu **poznámky k verzi Application Insights** .
   
   ![Vyberte přidat úlohu a vyberte Application Insights poznámky k verzi.](./media/annotations/3-add-task.png)
   
1. V části **ID aplikace**vložte Application Insights ID, které jste zkopírovali z karty **přístup k rozhraní API** .
   
   ![Vložit ID Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Zpátky v okně Application Insights **přístup k rozhraní API** vyberte **vytvořit klíč rozhraní API**. 
   
   ![Na kartě přístup k rozhraní API vyberte vytvořit klíč rozhraní API.](./media/annotations/5-create-api-key.png)
   
1. V okně **vytvořit klíč rozhraní API** zadejte popis, vyberte **zápis poznámek**a pak vyberte **vygenerovat klíč**. Zkopírujte nový klíč.
   
   ![V okně vytvořit klíč rozhraní API zadejte popis, vyberte zápis poznámek a pak vyberte vygenerovat klíč.](./media/annotations/6-create-api-key.png)
   
1. V okně Šablona verze vyberte na kartě **proměnné** možnost **Přidat** a vytvořte definici proměnné pro nový klíč rozhraní API.

1. Do pole **název**zadejte `ApiKey` a pod položkou **hodnota**vložte klíč rozhraní API, který jste zkopírovali z karty **přístup k rozhraní API** .
   
   ![Na kartě proměnné DevOps Azure vyberte Přidat, pojmenujte proměnnou ApiKey a vložte klíč rozhraní API pod hodnotu.](./media/annotations/7-paste-api-key.png)
   
1. Vyberte **Uložit** v hlavním okně šablony vydané verze a uložte šablonu.

## <a name="view-annotations"></a>Zobrazit poznámky
Když teď k nasazení nové verze použijete šablonu verze, pošle se Application Insights Poznámka. Poznámky se zobrazí v grafech v **Průzkumník metrik**.

Vyberte libovolnou značku poznámky (slabě šedá šipka) a otevřete tak podrobnosti o vydané verzi, včetně žadatele, větve správy zdrojového kódu, kanálu vydání a prostředí.

![Vyberte značku poznámky k verzi.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Vytváření vlastních poznámek z PowerShellu
Pomocí skriptu prostředí PowerShell pro [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) z GitHubu můžete vytvářet poznámky z libovolného procesu, který chcete, bez použití Azure DevOps. 

1. Vytvořte místní kopii [CreateReleaseAnnotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Pomocí kroků v předchozím postupu můžete získat ID Application Insights a na kartě **přístup k rozhraní api** Application Insights vytvořit klíč rozhraní API.
   
1. Zavolejte skript prostředí PowerShell s následujícím kódem a nahraďte zástupné symboly v závorkách hodnotami. @No__t_0 jsou volitelné. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Skript můžete upravit například tak, aby bylo možné vytvořit poznámky za minulosti.

## <a name="next-steps"></a>Další kroky

* [Vytváření pracovních položek](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizace pomocí PowerShellu](../../azure-monitor/app/powershell.md)
