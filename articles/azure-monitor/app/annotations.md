---
title: Poznámky k verzi pro Application Insights | Microsoft Docs
description: Přidejte značky nasazení nebo sestavení do grafů Průzkumníka metrik v Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 58f6603687838713fafbf4cd5cc3f100e22b7401
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993716"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky na grafech metrik v Application Insights

Poznámky ukazují, kde jste nasadili nové sestavení nebo jiné významné události. Poznámky usnadňují zjištění, zda vaše změny měly vliv na výkon vaší aplikace. Mohou být automaticky vytvořeny [Azure Pipelinesm](/azure/devops/pipelines/tasks/) systémem sestavení. Můžete také vytvořit poznámky k označení libovolné události, kterou chcete, vytvořením z PowerShellu.

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

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaši aplikaci. Pokud ho ještě nemáte, [vytvořte nový prostředek Application Insights](./app-insights-overview.md).
   
1. Otevřete kartu **přístup přes rozhraní API** a zkopírujte **ID Application Insights**.
   
   ![V části přístup k rozhraní API zkopírujte ID aplikace.](./media/annotations/2-app-id.png)

1. V samostatném okně prohlížeče otevřete nebo vytvořte šablonu vydané verze, která spravuje nasazení Azure Pipelines.
   
1. Vyberte **Přidat úlohu** a potom z nabídky vyberte úlohu **poznámky k verzi Application Insights** .
   
   ![Vyberte přidat úlohu a vyberte Application Insights poznámky k verzi.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Úloha poznámky k verzi aktuálně podporuje jenom agenty založené na Windows. neběží na Linux, macOS nebo jiných typech agentů.
   
1. V části **ID aplikace** vložte Application Insights ID, které jste zkopírovali z karty **přístup k rozhraní API** .
   
   ![Vložit ID Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Zpátky v okně Application Insights **přístup k rozhraní API** vyberte **vytvořit klíč rozhraní API**. 
   
   ![Na kartě přístup k rozhraní API vyberte vytvořit klíč rozhraní API.](./media/annotations/5-create-api-key.png)
   
1. V okně **vytvořit klíč rozhraní API** zadejte popis, vyberte **zápis poznámek** a pak vyberte **vygenerovat klíč**. Zkopírujte nový klíč.
   
   ![V okně vytvořit klíč rozhraní API zadejte popis, vyberte zápis poznámek a pak vyberte vygenerovat klíč.](./media/annotations/6-create-api-key.png)
   
1. V okně Šablona verze vyberte na kartě **proměnné** možnost **Přidat** a vytvořte definici proměnné pro nový klíč rozhraní API.

1. Do pole **název** zadejte `ApiKey` a v části **hodnota** vložte klíč rozhraní API, který jste zkopírovali z karty **přístup přes rozhraní API** .
   
   ![Na kartě proměnné DevOps Azure vyberte Přidat, pojmenujte proměnnou ApiKey a vložte klíč rozhraní API pod hodnotu.](./media/annotations/7-paste-api-key.png)
   
1. Vyberte **Uložit** v hlavním okně šablony vydané verze a uložte šablonu.


   > [!NOTE]
   > Omezení pro klíče rozhraní API jsou popsána v [dokumentaci omezení rychlosti REST API](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Zobrazit poznámky


   > [!NOTE]
   > Poznámky k verzi nejsou aktuálně k dispozici v podokně metriky Application Insights

Když teď k nasazení nové verze použijete šablonu verze, pošle se Application Insights Poznámka. Poznámky lze zobrazit v následujících umístěních:

Podokno použití, kde máte také možnost ručně vytvořit poznámky k verzi:

![Snímek obrazovky pruhového grafu s počtem návštěv uživatele zobrazených v průběhu několika hodin Poznámky k verzi se zobrazí jako zelené zaškrtnutí nad grafem, který indikuje moment v čase, kdy k vydání došlo.](./media/annotations/usage-pane.png)

V jakémkoli dotazu na sešit založený na protokolu, kde vizualizace zobrazuje čas podél osy x.

![Snímek obrazovky podokna sešitů s dotazem na základě protokolu Time Series se zobrazenými poznámkami](./media/annotations/workbooks-annotations.png)

Pokud chcete povolit poznámky v sešitu, přejít na **Upřesnit nastavení** a vyberte **Zobrazit poznámky**.

![Snímek obrazovky s nabídkou pokročilých nastavení s slovy zobrazit poznámky zvýrazněné vedle nastavení, které se má povolit](./media/annotations/workbook-show-annotations.png)

Vyberte libovolnou značku poznámky a otevřete tak podrobnosti o vydané verzi, včetně žadatele, větve správy zdrojového kódu, kanálu vydání a prostředí.

## <a name="create-custom-annotations-from-powershell"></a>Vytváření vlastních poznámek z PowerShellu
Pomocí skriptu prostředí PowerShell pro [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) z GitHubu můžete vytvářet poznámky z libovolného procesu, který chcete, bez použití Azure DevOps. 

1. Vytvořte místní kopii [CreateReleaseAnnotation.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Pomocí kroků v předchozím postupu můžete získat ID Application Insights a na kartě **přístup k rozhraní api** Application Insights vytvořit klíč rozhraní API.
   
1. Zavolejte skript prostředí PowerShell s následujícím kódem a nahraďte zástupné symboly v závorkách hodnotami. Rozhraní `-releaseProperties` jsou volitelná. 
   
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

* [vytvářet pracovní položky,](./diagnostic-search.md#create-work-item)
* [Automatizace v prostředí PowerShell](./powershell.md)

