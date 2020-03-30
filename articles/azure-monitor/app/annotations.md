---
title: Uvolnění poznámky pro přehledy aplikací | Dokumenty společnosti Microsoft
description: Přidejte značky nasazení nebo sestavení do grafů průzkumníků metrik v Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666509"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky k grafům metrik v přehledech aplikací

Poznámky v grafech [Průzkumníka metrik](../../azure-monitor/app/metrics-explorer.md) ukazují, kde jste nasadili nové sestavení nebo jiné významné události. Poznámky usnadňují zjištění, zda změny měly nějaký vliv na výkon aplikace. Mohou být automaticky vytvořeny systémem sestavení [Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Můžete také vytvořit poznámky k označení libovolné události se vám líbí jejich vytvořením z Prostředí PowerShell.

> [!NOTE]
> Tento článek odráží zastaralé **klasické metriky zkušenosti**. Poznámky jsou v současné době k dispozici pouze v klasickém prostředí a v **[sešitech](../../azure-monitor/app/usage-workbooks.md)**. Další informace o aktuálním prostředí metrik najdete v [tématu Pokročilé funkce Průzkumníka metrik Azure](../../azure-monitor/platform/metrics-charts.md).

![Příklad poznámky](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Uvolnění poznámky s Azure Pipelines sestavení

Poznámky k vydání jsou funkcí cloudové služby Azure Pipelines azure devops.

### <a name="install-the-annotations-extension-one-time"></a>Instalace rozšíření poznámky (jednou)
Abyste mohli vytvářet poznámky k verzi, budete muset nainstalovat jedno z mnoha rozšíření Azure DevOps, které je dostupné na webu Visual Studio Marketplace.

1. Přihlaste se k projektu [Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. Na stránce [rozšíření Anotace pro vydání](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) visual studia Marketplace vyberte organizaci Azure DevOps a pak vyberte **Instalovat** a přidejte rozšíření do vaší organizace Azure DevOps.
   
   ![Vyberte organizaci Azure DevOps a pak vyberte Instalovat.](./media/annotations/1-install.png)
   
Rozšíření je potřeba nainstalovat pouze jednou pro vaši organizaci Azure DevOps. Nyní můžete nakonfigurovat poznámky k verzi pro libovolný projekt ve vaší organizaci.

### <a name="configure-release-annotations"></a>Konfigurace poznámky k verzi

Vytvořte samostatný klíč rozhraní API pro každou šablonu verze Azure Pipelines.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaši aplikaci. Nebo pokud ho nemáte, [vytvořte nový prostředek Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Otevřete kartu **Přístup k rozhraní API** a zkopírujte **ID přehledů aplikací**.
   
   ![V části API Access zkopírujte ID aplikace.](./media/annotations/2-app-id.png)

1. V samostatném okně prohlížeče otevřete nebo vytvořte šablonu vydání, která spravuje vaše nasazení Azure Pipelines.
   
1. Vyberte **Přidat úkol**a pak z nabídky vyberte **úlohu anotace uvolnění informací** o aplikacích.
   
   ![Vyberte Přidat úkol a vyberte Anotace verze Application Insights.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Úloha anotace vydání aktuálně podporuje pouze agenty systému Windows. nebude fungovat na Linuxu, macOS nebo jiných typech agentů.
   
1. V části **ID aplikace**vložte ID přehledů aplikací, které jste zkopírovali z karty **Přístup k rozhraní API.**
   
   ![Vložení ID přehledů aplikací](./media/annotations/4-paste-app-id.png)
   
1. V okně **Access rozhraní API** pro přehledy aplikací vyberte vytvořit klíč rozhraní **API**. 
   
   ![Na kartě Přístup k rozhraní API vyberte Vytvořit klíč rozhraní API.](./media/annotations/5-create-api-key.png)
   
1. V okně **Vytvořit klíč rozhraní API** zadejte popis, vyberte Napsat **poznámky**a pak vyberte **Generovat klíč**. Zkopírujte nový klíč.
   
   ![V okně Vytvořit klíč rozhraní API zadejte popis, vyberte Napsat poznámky a pak vyberte Generovat klíč.](./media/annotations/6-create-api-key.png)
   
1. V okně verze šablony na kartě **Proměnné** vyberte **Přidat** a vytvořte definici proměnné pro nový klíč rozhraní API.

1. V **části** `ApiKey`Název zadejte a **v části Hodnota**vložte klíč rozhraní API, který jste zkopírovali, na kartě Přístup k rozhraní **API.**
   
   ![Na kartě Proměnné Azure DevOps vyberte Přidat, pojmenujte proměnnou ApiKey a vložte klíč rozhraní API v části Hodnota.](./media/annotations/7-paste-api-key.png)
   
1. Vyberte **Uložit** v okně hlavní verze šablony, chcete-li šablonu uložit.

## <a name="view-annotations"></a>Zobrazení poznámky
Teď, když použijete šablonu verze k nasazení nové verze, je do Application Insights odeslána anotace. Poznámky se zobrazují v grafech v **Průzkumníku metrik**.

Vyberte libovolnou značku poznámky (světle šedá šipka), chcete-li otevřít podrobnosti o verzi, včetně osazení, větve správy zdrojového kódu, kanálu vydání a prostředí.

![Vyberte značku anotace uvolnění.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Vytvoření vlastních anotací z PowerShellu
Pomocí skriptu [PowerShellu CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) z GitHubu můžete vytvořit poznámky z libovolného procesu, který se vám líbí, bez použití Azure DevOps. 

1. Vytvořte místní kopii [souboru CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Pomocí kroků v předchozím postupu získejte ID přehledů aplikací a vytvořte klíč rozhraní API z karty Application Insights **API Access** .
   
1. Zavolejte skript prostředí PowerShell s následujícím kódem a nahraďte zástupné symboly s úhlovými závorkami vašimi hodnotami. Jsou `-releaseProperties` volitelné. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Skript můžete upravit například pro vytvoření anotací z minulosti.

## <a name="next-steps"></a>Další kroky

* [vytvářet pracovní položky,](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizace v prostředí PowerShell](../../azure-monitor/app/powershell.md)
