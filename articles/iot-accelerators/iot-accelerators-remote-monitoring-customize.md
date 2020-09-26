---
title: Přizpůsobení uživatelského rozhraní řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak získat přístup ke zdrojovému kódu uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování a udělat si některá vlastní nastavení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2789ed642979616a4491a61d146d8468552ec2e5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318445"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Přizpůsobení akcelerátoru řešení vzdáleného monitorování

Tento článek poskytuje informace o tom, jak můžete získat přístup ke zdrojovému kódu a přizpůsobit uživatelské rozhraní akcelerátoru řešení vzdáleného monitorování.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava místního vývojového prostředí pro uživatelské rozhraní

Kód uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování je implementován pomocí rozhraní React.js Framework. Zdrojový kód najdete v úložišti GitHub [Azure-IoT-PC-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) .

Chcete-li změnit uživatelské rozhraní, můžete spustit jeho kopii místně. K dokončení akcí, jako je načítání telemetrie, se místní kopie připojí k nasazené instanci řešení.

Následující kroky popisují proces nastavení místního prostředí pro vývoj uživatelského rozhraní:

1. Nasaďte **základní** instanci akcelerátoru řešení pomocí rozhraní příkazového řádku pro **počítače** . Poznamenejte si název vašeho nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace najdete v tématu [nasazení pomocí rozhraní](iot-accelerators-remote-monitoring-deploy-cli.md)příkazového řádku.

1. Pokud chcete povolit přístup SSH k virtuálnímu počítači, který je hostitelem mikroslužeb ve vašem řešení, použijte Azure Portal nebo Azure Cloud Shell. Příklad:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Povolit přístup přes SSH jenom během testu a vývoje. Pokud povolíte SSH, [měli byste ho po dokončení používání zakázat](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Pomocí Azure Portal nebo Azure Cloud Shell vyhledejte název a veřejnou IP adresu vašeho virtuálního počítače. Příklad:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se připojte k virtuálnímu počítači. Použijte IP adresu z předchozího kroku a přihlašovací údaje, které jste zadali při spuštění **počítačů** k nasazení řešení. `ssh`Příkaz je k dispozici v Azure Cloud Shell.

1. Pokud chcete místnímu UŽIVATELSKÉmu prostředí připojit, spusťte v prostředí bash ve virtuálním počítači následující příkazy:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Až se zobrazí příkaz dokončí se a web se spustí, můžete se z virtuálního počítače odpojit.

1. V místní kopii úložiště [Azure-IoT-PC-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) upravte soubor **. env** a přidejte adresu URL vašeho nasazeného řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Na příkazovém řádku přejděte do místní kopie `azure-iot-pcs-remote-monitoring-webui` složky.

1. Chcete-li nainstalovat požadované knihovny a spustit uživatelské rozhraní lokálně, spusťte následující příkazy:

    ```cmd/sh
    npm install
    npm start
    ```

1. Předchozí příkaz spustí uživatelské rozhraní lokálně na adrese http: \/ /localhost: 3000/řídicí panel. V době, kdy je web spuštěný, můžete kód upravovat a dynamicky se aktualizuje.

## <a name="customize-the-layout"></a>Přizpůsobení rozložení

Každá stránka v řešení vzdáleného monitorování se skládá ze sady ovládacích prvků, které jsou označovány jako *panely* ve zdrojovém kódu. Stránka **řídicího panelu** se skládá z pěti panelů: Přehled, mapa, výstrahy, telemetrie a analýza. Zdrojový kód definující každou stránku a její panely najdete v úložišti GitHub [PC-Remote-Monitoring-WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) . Například kód, který definuje stránku **řídicího panelu** , jeho rozložení a panely na stránce, se nachází ve složce [Src/Components/Pages/řídicí panel](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) .

Vzhledem k tomu, že panely spravují své vlastní rozložení a velikost, můžete snadno upravit rozložení stránky. Proveďte následující změny elementu **PageContent** v `src/components/pages/dashboard/dashboard.js` souboru na:

* Proměňte pozice v rámci mapy a panelů telemetrie.
* Změna relativní šířky panelů map a analýz

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Změnit rozložení panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Můžete také přidat několik instancí stejného panelu nebo několik verzí, pokud [duplikujete a přizpůsobíte panel](#duplicate-and-customize-an-existing-control). Následující příklad ukazuje, jak přidat dvě instance panelu telemetrie. Chcete-li provést tyto změny, upravte `src/components/pages/dashboard/dashboard.js` soubor:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Pak můžete zobrazit různé telemetrie na jednotlivých panelech:

![Několik panelů telemetrie](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplikovat a přizpůsobit existující ovládací prvek

Následující kroky popisují, jak duplikovat existující panel, upravit ho a pak použít upravenou verzi. Postup použijte jako příklad panel **výstrahy** :

1. V místní kopii úložiště vytvořte kopii složky **výstrahy** ve `src/components/pages/dashboard/panels` složce. Pojmenujte **cust_alerts**nového kopírování.

1. V souboru **alertsPanel.js** ve složce **cust_alerts** upravte název třídy, která se má **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Do souboru přidejte následující řádek `src/components/pages/dashboard/panels/index.js` :

    ```javascript
    export * from './cust_alerts';
    ```

1. Nahradit `alertsPanel` za `CustAlertsPanel` v `src/components/pages/dashboard/dashboard.js` souboru:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Nyní jste nahradili původní panel **Upozornění** pomocí kopie s názvem **CustAlerts**. Tato kopie je stejná jako původní. Nyní můžete upravit kopii. Například pro změnu pořadí sloupců na panelu **výstrahy** :

1. Otevřete soubor `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Upravte definice sloupců, jak je znázorněno v následujícím fragmentu kódu:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Na následujícím snímku obrazovky vidíte novou verzi panelu **výstrahy** :

![panel výstrah aktualizován](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Přizpůsobení grafu telemetrie

Soubory ve `src/components/pages/dashboard/panels/telemtry` složce definují graf telemetrie na stránce **řídicího panelu** . Uživatelské rozhraní načte telemetrii z back-endu řešení v `src/services/telemetryService.js` souboru. Následující kroky ukazují, jak změnit časový interval zobrazený v grafu telemetrie z 15 na 5 minut:

1. V `src/services/telemetryService.js` souboru vyhledejte funkci s názvem **getTelemetryByDeviceIdP15M**. Vytvořte kopii této funkce a upravte kopii následujícím způsobem:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Chcete-li tuto novou funkci použít k naplnění grafu telemetrie, otevřete `src/components/pages/dashboard/dashboard.js` soubor. Vyhledejte řádek, který inicializuje datový proud telemetrie, a upravte ho následujícím způsobem:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Graf telemetrie teď zobrazuje pět minut dat telemetrie:

![Graf telemetrie znázorňující jeden den](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Přidat nový klíčový ukazatel výkonu

Na stránce **řídicího panelu** se zobrazí klíčové ukazatele výkonu na panelu **Analýza** . Tyto klíčové ukazatele výkonu jsou vypočítány v `src/components/pages/dashboard/dashboard.js` souboru. Klíčové ukazatele výkonu jsou vykreslovány `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` souborem. Následující postup popisuje, jak vypočítat a vykreslit novou hodnotu klíčového ukazatele výkonu na stránce **řídicího panelu** . Zobrazený příklad je přidání nové procentuální změny v KUV výstrahy upozornění:

1. Otevřete soubor `src/components/pages/dashboard/dashboard.js`. Upravte objekt **initialState** tak, aby obsahoval vlastnost **warningAlertsChange** , jak je znázorněno níže:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Upravte objekt **currentAlertsStats** tak, aby zahrnoval **totalWarningCount** jako vlastnost:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Vypočítá nový klíčový ukazatel výkonu. Vyhledá výpočet pro počet kritických výstrah. Duplikovat kód a upravit kopii následujícím způsobem:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Zahrňte nový klíčový ukazatel výkonu **warningAlertsChange** do streamu klíčových ukazatelů výkonu:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Zahrňte nový klíčový ukazatel výkonu **warningAlertsChange** do dat stavu použitých k vykreslení uživatelského rozhraní:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Aktualizujte data předaná na panel klíčových ukazatelů výkonu:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Právě jste dokončili změny v `src/components/pages/dashboard/dashboard.js` souboru. Následující kroky popisují změny, které se mají v souboru udělat, `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` aby se zobrazil nový klíčový ukazatel výkonu:

1. Upravte následující řádek kódu k načtení nové hodnoty klíčového ukazatele výkonu následujícím způsobem:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Upravte kód tak, aby se zobrazila nová hodnota klíčového ukazatele výkonu následujícím způsobem:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

Na stránce **řídicího panelu** se teď zobrazí nová hodnota klíčového ukazatele výkonu:

![Klíčový ukazatel výkonu pro upozornění](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Přizpůsobení mapy

Podrobnosti o komponentách map v řešení najdete na stránce [Přizpůsobení mapy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) na GitHubu.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Další možnosti přizpůsobení

Chcete-li dále upravit vrstvu prezentace a vizualizace v řešení vzdáleného monitorování, můžete kód upravit. Relevantní úložiště GitHub:

* [Konfigurace mikroslužby pro řešení Azure IoT (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Konfigurace mikroslužeb pro řešení Azure IoT (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Webové uživatelské rozhraní vzdáleného monitorování počítačů IoT Azure](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných zdrojích, které vám pomůžou přizpůsobit webové uživatelské rozhraní v akcelerátoru řešení vzdáleného monitorování. Další informace o přizpůsobení uživatelského rozhraní najdete v následujících článcích:

* [Přidat vlastní stránku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md)
* [Přidání vlastní služby do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md)
* [Přidání vlastní mřížky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md)
* [Přidat vlastní informační rámeček do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Přidání vlastního panelu na řídicí panel ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-panel.md)

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md) .

Další informace o přizpůsobení mikroslužeb řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužeb](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
