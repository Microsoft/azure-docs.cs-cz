---
title: Uvolnění poznámky pro přehledy aplikací | Dokumenty společnosti Microsoft
description: Přidejte značky nasazení nebo sestavení do grafů průzkumníků metrik v Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010703"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky k grafům metrik v přehledech aplikací

Poznámky ukazují, kde jste nasadili nové sestavení nebo jiné významné události. Poznámky usnadňují zjištění, zda změny měly nějaký vliv na výkon aplikace. Mohou být automaticky vytvořeny systémem sestavení [Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Můžete také vytvořit poznámky k označení libovolné události se vám líbí jejich vytvořením z Prostředí PowerShell.

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


   > [!NOTE]
   > Poznámky k vydání nejsou v současné době k dispozici v podokně Metriky application insights

Teď, když použijete šablonu verze k nasazení nové verze, je do Application Insights odeslána anotace. Poznámky lze zobrazit v následujících umístěních:

Podokno použití, ve kterém můžete také ručně vytvářet poznámky k vydání:

![Snímek obrazovky pruhového grafu s počtem návštěv uživatelů zobrazených v průběhu hodin. Uvolněte poznámky se zobrazí jako zelené značky zaškrtnutí nad grafem označující okamžik v čase, kdy došlo k uvolnění](./media/annotations/usage-pane.png)

V libovolném dotazu sešitu založeném na protokolu, kde vizualizace zobrazuje čas podél osy x.

![Snímek obrazovky podokna sešitů s dotazem na základě protokolu časových řad se zobrazenými anotacemi](./media/annotations/workbooks-annotations.png)

Pokud chcete v sešitu povolit poznámky, přejděte na **Upřesnit nastavení** a vyberte **Zobrazit poznámky**.

![Snímek obrazovky nabídky Upřesnit nastavení se slovy zobrazuje poznámky zvýrazněné zaškrtnutím vedle nastavení, které ho povoluje.](./media/annotations/workbook-show-annotations.png)

Vyberte libovolnou značku poznámky, chcete-li otevřít podrobnosti o verzi, včetně osazení, větve správy zdrojového kódu, kanálu vydání a prostředí.

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
