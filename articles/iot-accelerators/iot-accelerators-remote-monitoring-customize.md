---
title: Přizpůsobení uživatelského nastavení řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak získat přístup ke zdrojovému kódu pro uživatelské rozhraní akcelerátoru řešení vzdáleného monitorování a provést některá vlastní nastavení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608000"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Přizpůsobení akcelerátoru řešení vzdáleného monitorování

Tento článek obsahuje informace o tom, jak získat přístup ke zdrojovému kódu a přizpůsobit uživatelské uživatelské nastavení akcelerátoru řešení vzdáleného monitorování.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava prostředí místního rozvoje pro ui

Kód uživatelského rozhraní urychlovače řešení vzdáleného monitorování je implementován pomocí rozhraní React.js. Zdrojový kód najdete v úložišti [GitHub azure-iot-pcs-remote-monitoring-webui.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui)

Chcete-li provést změny v ui, můžete spustit jeho kopii místně. Chcete-li dokončit akce, jako je například načítání telemetrie, místní kopie se připojí k nasazené instanci řešení.

Následující kroky popisují proces nastavení místního prostředí pro vývoj nového prostředí:

1. Nasazení **základní** instance akcelerátoru řešení pomocí **příkazového příkazového příkazu pcs.** Poznamenejte si název nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace naleznete v [tématu Deploy using the CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Chcete-li povolit přístup SSH k virtuálnímu počítači, který hostuje mikroslužeb ve vašem řešení, použijte portál Azure nebo Azure Cloud Shell. Například:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Povolte přístup SSH pouze během testování a vývoje. Pokud povolíte SSH, [měli byste jej zakázat, jakmile ji dokončíte .](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines)

1. Pomocí portálu Azure nebo Azure Cloud Shell vyhledejte název a veřejnou IP adresu vašeho virtuálního počítače. Například:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se připojte k virtuálnímu počítači. Použijte IP adresu z předchozího kroku a přihlašovací údaje, které jste zadali při spuštění **počítačů** k nasazení řešení. Příkaz `ssh` je k dispozici v prostředí Azure Cloud Shell.

1. Chcete-li povolit připojení místního uživatelského prostředí, spusťte následující příkazy v prostředí bash ve virtuálním počítači:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po dokončení příkazu a spuštění webu se můžete odpojit od virtuálního počítače.

1. V místní kopii úložiště [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) upravte soubor **.env** a přidejte adresu URL nasazeného řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Na příkazovém řádku přejděte na `azure-iot-pcs-remote-monitoring-webui` místní kopii složky.

1. Chcete-li nainstalovat požadované knihovny a spustit uživatelské prostředí místně, spusťte následující příkazy:

    ```cmd/sh
    npm install
    npm start
    ```

1. Předchozí příkaz spustí ui místně na\/http: /localhost:3000/dashboard. Kód můžete upravit, když je web spuštěný, a vidět, jak se aktualizuje dynamicky.

## <a name="customize-the-layout"></a>Přizpůsobení rozložení

Každá stránka v řešení vzdáleného monitorování se skládá ze sady ovládacích prvků, označovaných jako *panely* ve zdrojovém kódu. Stránka **Řídicí panel** se skládá z pěti panelů: Přehled, Mapa, Výstrahy, Telemetrie a Analýza. Zdrojový kód, který definuje každou stránku a její panely, najdete v úložišti github u [vzdáleného monitorování pcs.](https://github.com/Azure/pcs-remote-monitoring-webui) Například kód, který definuje stránku **řídicího panelu,** její rozložení a panely na stránce, je umístěn ve složce [src/components/pages/dashboard.](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)

Vzhledem k tomu, že panely spravují vlastní rozložení a velikost, můžete rozložení stránky snadno upravit. Proveďte následující změny prvku **PageContent** v souboru `src/components/pages/dashboard/dashboard.js` takto:

* Vyměňte pozice mapových a telemetrických panelů.
* Změňte relativní šířky panelů mapy a analýzy.

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

![Změna rozložení panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Můžete také přidat několik instancí stejného panelu nebo několik verzí, pokud [panel duplikujete a přizpůsobíte](#duplicate-and-customize-an-existing-control). Následující příklad ukazuje, jak přidat dvě instance panelu telemetrie. Chcete-li tyto změny `src/components/pages/dashboard/dashboard.js` provést, upravte soubor:

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

V každém panelu pak můžete zobrazit různé telemetrie:

![Více telemetrických panelů](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplikování a přizpůsobení existujícího ovládacího prvku

Následující kroky popisují, jak duplikovat existující panel, upravit ho a pak použít upravenou verzi. Kroky používají jako příklad panel **výstrah:**

1. V místní kopii úložiště vytvořte kopii **alerts** složky `src/components/pages/dashboard/panels` výstrah ve složce. Pojmenujte novou **kopii cust_alerts**.

1. V souboru **alertsPanel.js** ve složce **cust_alerts** upravte název třídy, která má být **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Do `src/components/pages/dashboard/panels/index.js` souboru přidejte následující řádek:

    ```javascript
    export * from './cust_alerts';
    ```

1. Nahradit `alertsPanel` `CustAlertsPanel` v `src/components/pages/dashboard/dashboard.js` souboru:

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

Nyní jste nahradili původní panel **upozornění** kopií nazvanou **CustAlerts**. Tato kopie je stejná jako originál. Nyní můžete upravit kopii. Chcete-li například změnit pořadí sloupců v panelu **výstrah:**

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

Následující snímek obrazovky ukazuje novou verzi panelu **upozornění:**

![panel výstrah byl aktualizován](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Přizpůsobení telemetrického grafu

Soubory ve `src/components/pages/dashboard/panels/telemtry` složce definují telemetrický graf na stránce **Řídicí panel.** UI načte telemetrická data z back-endu řešení v souboru. `src/services/telemetryService.js` Následující kroky ukazují, jak změnit časové období zobrazené v telemetrickém grafu z 15 na 5 minut:

1. V `src/services/telemetryService.js` souboru vyhledejte funkci nazvanou **getTelemetryByDeviceIdP15M**. Vytvořte kopii této funkce a upravte kopii takto:

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

1. Chcete-li použít tuto novou funkci k `src/components/pages/dashboard/dashboard.js` naplnění telemetrického grafu, otevřete soubor. Vyhledejte řádek, který inicializuje datový proud telemetrie, a upravte ho následujícím způsobem:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Graf telemetrie nyní zobrazuje pět minut telemetrických dat:

![Graf telemetrie znázorňující jeden den](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Přidání nového klíčového ukazatele výkonu

Stránka **Řídicí panel** zobrazuje hlavní nastavení výkonu v panelu **Analytics.** Tyto hlavní výčitek `src/components/pages/dashboard/dashboard.js` a hlavní výčitek jsou vypočteny v souboru. KKu jsou vykreslovány souborem. `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` Následující kroky popisují, jak vypočítat a vykreslit novou hodnotu klíčového ukazatele výkonu na stránce **Řídicí panel.** Zobrazený příklad je přidání nové procentuální změny v klíčovém ukazateli výkonu upozornění na upozornění:

1. Otevřete soubor `src/components/pages/dashboard/dashboard.js`. Upravte objekt **initialState** tak, aby obsahoval vlastnost **warningAlertsChange** následujícím způsobem:

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

1. Vypočítejte nový klíčový ukazatel výkonu. Najděte výpočet počtu kritických výstrah. Duplikujte kód a upravte kopii takto:

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

1. Zahrňte nové **upozorněníAlertsZměna** klíčového ukazatele výkonu v datovém proudu klíčového ukazatele výkonu:

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

1. Zahrňte nové **upozorněníAlertsChange** KPI ve stavu dat použitých k vykreslení ui:

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

1. Aktualizace dat předaná na panel u ks:

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

Nyní jste dokončili změny v `src/components/pages/dashboard/dashboard.js` souboru. Následující kroky popisují změny, které `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` je třeba provést v souboru a zobrazit nový klíčový ukazatel výkonu:

1. Chcete-li načíst novou hodnotu klíčového ukazatele výkonu následujícím způsobem, upravte následující řádek kódu:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Upravte značku tak, aby se nová hodnota klíčového ukazatele výkonu zobrazovala následovně:

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

Stránka **Řídicí panel** nyní zobrazuje novou hodnotu klíčového ukazatele výkonu:

![Výstražný klíčový ukazatel výkonu](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Přizpůsobení mapy

Podrobnosti o součástech mapy v řešení najdete na stránce [Přizpůsobit mapu](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) na GitHubu.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Další možnosti přizpůsobení

Chcete-li dále upravit vrstvu prezentace a vizualizací v řešení vzdáleného monitorování, můžete upravit kód. Příslušné úložiště GitHub jsou:

* [Konfigurační mikroslužba pro řešení Azure IoT Solutions (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Konfigurační mikroslužba pro řešení Azure IoT Solutions (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Webové uživatelské nastavení vzdáleného monitorování Azure IoT PCS](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných prostředcích, které vám pomohou přizpůsobit webové uživatelské rozhraní v akcelerátoru řešení vzdáleného monitorování. Další informace o přizpůsobení hlavního nastavení najdete v následujících článcích:

* [Přidání vlastní stránky do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md)
* [Přidání vlastní služby do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md)
* [Přidání vlastní mřížky do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md)
* [Přidání vlastního informačního rámečku do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Přidání vlastního panelu do řídicího panelu ve webovém uživatelském uživatelském uživatelském uživatelském panelu akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-panel.md)

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení mikroslužeb řešení vzdáleného monitorování naleznete v [tématu Přizpůsobení a opětovné nasazení mikroslužeb](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
