---
title: Přizpůsobení řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak přístup ke zdrojovému kódu pro akcelerátor řešení vzdálené monitorování uživatelského rozhraní a některé vlastní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0609a653327640c542457822e41143b9b39dd6d4
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462195"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Přizpůsobení akcelerátoru řešení vzdáleného monitorování

Tento článek obsahuje informace o tom, jak můžete přístup ke zdrojovému kódu a přizpůsobení uživatelského rozhraní vzdálené monitorování akcelerátorů řešení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava prostředí pro místní vývoj uživatelského rozhraní

Akcelerátor řešení vzdálené monitorování kód uživatelského rozhraní je implementováno pomocí rozhraní React.js. Můžete najít zdrojový kód v [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) úložiště GitHub.

Pokud chcete provést změny v uživatelském rozhraní, můžete jeho kopii spustit místně. Dokončit akce, třeba načítání telemetrická data, místní kopii připojí k instanci nasazeného řešení.

Následující kroky popisují postup nastavení místní prostředí pro vývoj uživatelského rozhraní:

1. Nasazení **základní** instanci pomocí akcelerátor řešení **počítače** rozhraní příkazového řádku. Poznamenejte si název vašeho nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace najdete v tématu [nasazení pomocí rozhraní příkazového řádku](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Pokud chcete povolit přístup přes SSH k virtuálnímu počítači, který je hostitelem mikroslužeb ve vašem řešení, pomocí webu Azure portal nebo Azure Cloud Shell. Příklad:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Povolte přístup přes SSH pouze během vývoj a testování. Pokud povolíte SSH, [byste měli zakázat, jakmile budete hotovi, jeho použití](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Najít název a veřejnou IP adresu vašeho virtuálního počítače pomocí webu Azure portal nebo Azure Cloud Shell. Příklad:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se připojte ke svému virtuálnímu počítači. Použít IP adresu z předchozího kroku a přihlašovací údaje, které jste zadali při spuštění **počítače** k nasazení řešení. `ssh` Příkaz je k dispozici ve službě Azure Cloud Shell.

1. Pokud chcete povolit místní uživatelského rozhraní pro připojení, spusťte následující příkazy v prostředí bash ve virtuálním počítači:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Jakmile ověříte, že se příkaz dokončí a spustí webovou stránku, můžete odpojit od virtuálního počítače.

1. V místní kopii [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) úložiště, upravit **.env** soubor a přidejte adresu URL vašeho nasazeného řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Na příkazovém řádku přejděte do místní kopie `azure-iot-pcs-remote-monitoring-webui` složky.

1. Instalace potřebných knihoven a místní spuštění uživatelského rozhraní, spusťte následující příkazy:

    ```cmd/sh
    npm install
    npm start
    ```

1. Předchozí příkaz se spustí místně na uživatelské rozhraní http://localhost:3000/dashboard. Můžete upravit jeho kód, zatímco je web spuštěný a zobrazit ji dynamicky aktualizovat.

## <a name="customize-the-layout"></a>Přizpůsobení rozložení

Každá stránka v řešení vzdáleného monitorování se skládá ze sady ovládacích prvků, které jsou označovány jako *panelů* ve zdrojovém kódu. **Řídicí panel** stránky se skládá z pěti panelů: Přehled, Map, upozornění, Telemetrie a analýzy. Můžete najít zdrojový kód, který definuje každé stránce a jeho panelů v [počítače remote monitorování webui](https://github.com/Azure/pcs-remote-monitoring-webui) úložiště GitHub. Například kód, který definuje **řídicí panel** stránky, rozložení a panelů na stránce se nachází v [src/součásti/stránek/řídicí panel](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) složky.

Protože panely spravovat jejich vlastní rozložení a změna velikosti, můžete snadno upravit rozložení stránky. Proveďte následující změny **PageContent** prvek `src/components/pages/dashboard/dashboard.js` do souboru:

* Odkládacího umístění panelů mapy a telemetrie.
* Změňte relativní šířku panelů mapy a analýzy.

```nodejs
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

Můžete také přidat více instancí stejného panelu, nebo několik verzí Pokud jste [duplicitní a přizpůsobit panel](#duplicate-and-customize-an-existing-control). Následující příklad ukazuje, jak přidat dvě instance panelu telemetrická data. Chcete-li tyto změny udělat, upravte `src/components/pages/dashboard/dashboard.js` souboru:

```nodejs
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

Pak můžete zobrazit jiné telemetrie každou panelu:

![Více panelů telemetrie](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicitní a přizpůsobit existující ovládací prvek

Následující kroky popisují, jak duplicitní stávající panely, upravte ho a pak použít upravenou verzi. Použijte postup **výstrahy** panelu jako příklad:

1. V místní kopii úložiště, vytvořte kopii **výstrahy** složky `src/components/pages/dashboard/panels` složky. Pojmenujte novou kopii **cust_alerts**.

1. V **alertsPanel.js** soubor **cust_alerts** složku, upravit název třídy, která má být **CustAlertsPanel**:

    ```nodejs
    export class CustAlertsPanel extends Component {
    ```

1. Přidejte následující řádek, který `src/components/pages/dashboard/panels/index.js` souboru:

    ```nodejs
    export * from './cust_alerts';
    ```

1. Nahraďte `alertsPanel` s `CustAlertsPanel` v `src/components/pages/dashboard/dashboard.js` souboru:

    ```nodejs
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

Jste teď nahrazuje původní **výstrahy** panel s názvem kopií **CustAlerts**. Tato kopie je stejný jako původní. Nyní můžete upravit kopii. Například, chcete-li změnit sloupce řazení **výstrahy** panelu:

1. Otevřete soubor `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Upravte definice sloupců, jak je znázorněno v následujícím fragmentu kódu:

    ```nodejs
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

Následující snímek obrazovky ukazuje novou verzi **výstrahy** panelu:

![aktualizovat panel výstrah](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Přizpůsobení telemetrických dat grafu

Soubory v `src/components/pages/dashboard/panels/telemtry` složky definovat telemetrická data grafu pro **řídicí panel** stránky. Uživatelské rozhraní načte telemetrická data z back-end řešení v `src/services/telemetryService.js` souboru. Následující kroky ukazují, jak změnit časové období, zobrazeny v grafu telemetrická data z 15 až 5 minut:

1. V `src/services/telemetryService.js` souboru, vyhledejte volaná funkce **getTelemetryByDeviceIdP15M**. Vytvořte kopii této funkce a upravit kopii následujícím způsobem:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Chcete-li použít tuto novou funkci pro naplnění telemetrická data grafu, otevřete `src/components/pages/dashboard/dashboard.js` souboru. Vyhledejte řádek, který inicializuje datový proud telemetrických dat a upravte následujícím způsobem:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetrie graf teď zobrazuje pět minut telemetrická data:

![Telemetrie graf zobrazující jeden den](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Přidat nový klíčový ukazatel výkonu

**Řídicí panel** stránce se zobrazí klíčové ukazatele výkonu v **Analytics** panelu. Tyto klíčové ukazatele výkonu počítají v `src/components/pages/dashboard/dashboard.js` souboru. Klíčové ukazatele výkonu jsou vykreslovány `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` souboru. Následující kroky popisují, jak vypočítat a vykreslení novou hodnotu klíčového ukazatele výkonu **řídicí panel** stránky. Jak ukazuje příklad je přidat novou procentuální změnu ve výstrahách upozornění klíčového ukazatele výkonu:

1. Otevřete soubor `src/components/pages/dashboard/dashboard.js`. Upravit **initialState** objektu, který chcete zahrnout **warningAlertsChange** vlastnost následujícím způsobem:

    ```nodejs
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

1. Upravit **currentAlertsStats** objektu, který chcete zahrnout **totalWarningCount** jako vlastnost:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Vypočítá nový klíčový ukazatel výkonu. Najdete výpočtu pro počet kritických výstrah. Duplicitní kód a upravit kopii následujícím způsobem:

    ```nodejs
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Zahrnout nové **warningAlertsChange** klíčového ukazatele výkonu v datovém proudu klíčového ukazatele výkonu:

    ```nodejs
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

1. Zahrnout nové **warningAlertsChange** klíčový ukazatel výkonu v data o stavu použitý pro vykreslení uživatelského rozhraní:

    ```nodejs
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

1. Aktualizace dat předávaných do panelu klíčových ukazatelů výkonu:

    ```node.js
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

Nyní jste dokončili změnami `src/components/pages/dashboard/dashboard.js` souboru. Následující kroky popisují změny provádět v `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` soubor k zobrazení nový klíčový ukazatel výkonu:

1. Upravte následující řádek kódu k načtení nové hodnoty klíčového ukazatele výkonu následujícím způsobem:

    ```nodejs
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Upravte značky pro novou hodnotu klíčového ukazatele výkonu se zobrazí takto:

    ```nodejs
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

**Řídicí panel** stránka nyní zobrazuje novou hodnotu klíčového ukazatele výkonu:

![Upozornění klíčového ukazatele výkonu](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Přizpůsobení mapy

Najdete v článku [vlastní mapy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) stránku na Githubu pro podrobnosti o součásti mapy v řešení.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Další možnosti vlastního nastavení

Pokročilejší úpravy vrstva prezentace a vizualizace v řešení vzdáleného monitorování, můžete upravit kód. Jsou příslušné úložiště GitHub:

* [Konfigurace mikroslužeb pro řešení Azure IoT (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Konfigurace mikroslužeb pro řešení Azure IoT (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Počítače s Azure IoT vzdálené monitorování webového uživatelského rozhraní](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro vám umožní přizpůsobit uživatelské rozhraní v akcelerátoru řešení vzdáleného monitorování. Další informace o přizpůsobení uživatelského rozhraní, naleznete v následujících článcích:

* [Přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-page.md)
* [Přidat vlastní služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-service.md)
* [Přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md)
* [Přidat vlastní rozevírací nabídka pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Přidat vlastní panel na řídicím panelu vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-panel.md)

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení mikroslužeb řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužby](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
