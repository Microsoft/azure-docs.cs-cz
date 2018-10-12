---
title: Přizpůsobení řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak přístup ke zdrojovému kódu pro akcelerátor řešení vzdálené monitorování uživatelského rozhraní a některé vlastní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2018
ms.topic: conceptual
ms.openlocfilehash: e8b3cd0e1e1d1f3940a103f614cf2746b2770e3f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092044"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Přizpůsobení akcelerátoru řešení vzdáleného monitorování

Tento článek obsahuje informace o tom, jak můžete přístup ke zdrojovému kódu a přizpůsobení uživatelského rozhraní vzdálené monitorování akcelerátorů řešení. Tento článek popisuje:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava prostředí pro místní vývoj uživatelského rozhraní

Akcelerátor řešení vzdálené monitorování kód uživatelského rozhraní je implementováno pomocí rozhraní React.js. Můžete najít zdrojový kód v [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) úložiště GitHub.

Pokud chcete provést změny v uživatelském rozhraní, můžete jeho kopii spustit místně. Místní kopie se připojí k instanci nasazené řešení k provedení akce, třeba načítání telemetrická data.

Následující kroky popisují postup nastavení místní prostředí pro vývoj uživatelského rozhraní:

1. Nasazení **základní** instanci pomocí akcelerátor řešení **počítače** rozhraní příkazového řádku. Poznamenejte si název vašeho nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace najdete v tématu [nasazení pomocí rozhraní příkazového řádku](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Pomocí webu Azure portal nebo [az rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) povolit přístup přes SSH k virtuálnímu počítači, který je hostitelem mikroslužeb ve vašem řešení. Příklad:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Přístup přes SSH byste měli povolit jenom během vývoj a testování. Pokud povolíte SSH, [byste měli znovu co nejdříve zakázat](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Pomocí webu Azure portal nebo [az rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) najít název a veřejnou IP adresu vašeho virtuálního počítače. Příklad:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se připojte k virtuálnímu počítači pomocí IP adresy z předchozího kroku a přihlašovací údaje, které jste zadali při spuštění **počítače** k nasazení řešení.

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

1. Na příkazovém řádku v místní kopii `azure-iot-pcs-remote-monitoring-webui` složky, spusťte následující příkazy a instalace potřebných knihoven a místní spuštění uživatelského rozhraní:

    ```cmd/sh
    npm install
    npm start
    ```

1. Předchozí příkaz se spustí místně na uživatelské rozhraní http://localhost:3000/dashboard. Můžete upravit jeho kód, zatímco je web spuštěný a zobrazit ji dynamicky aktualizovat.

## <a name="customize-the-layout"></a>Přizpůsobení rozložení

Každá stránka v řešení vzdáleného monitorování se skládá ze sady ovládacích prvků, které jsou označovány jako *panelů* ve zdrojovém kódu. Například **řídicí panel** stránky se skládá z pěti panely: Přehled, Map, alarmy, Telemetrie a klíčové ukazatele výkonu. Můžete najít zdrojový kód, který definuje každé stránce a jeho panelů v [počítače remote monitorování webui](https://github.com/Azure/pcs-remote-monitoring-webui) úložiště GitHub. Například kód, který definuje **řídicí panel** stránky, rozložení a panelů na stránce se nachází v [src/součásti/stránek/řídicí panel](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) složky.

Protože panely spravovat jejich vlastní rozložení a změna velikosti, můžete snadno upravit rozložení stránky. Například následující změny **PageContent** prvek `src/components/pages/dashboard/dashboard.js` souboru odkládacího umístění panelů mapy a telemetrii a změnit relativní šířku mapy a panelů klíčového ukazatele výkonu:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Změna rozložení panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> Mapa není nakonfigurovaná místní nasazení.

Můžete také přidat více instancí stejného panelu, nebo více verzí Pokud jste [duplicitní a přizpůsobit panel](#duplicate-and-customize-an-existing-control). Následující příklad ukazuje, jak přidat dvě instance panelu telemetrie úpravou `src/components/pages/dashboard/dashboard.js` souboru:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Pak můžete zobrazit jiné telemetrie každou panelu:

![Více panelů telemetrie](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Mapa není nakonfigurovaná místní nasazení.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicitní a přizpůsobit existující ovládací prvek

Následující kroky popisují způsob použití **alarmy** panelu jako příklad toho, jak duplikovat stávající panely, upravit a použít upravenou verzi:

1. V místní kopii úložiště, vytvořte kopii **alarmy** složky `src/components/pages/dashboard/panels` složky. Pojmenujte novou kopii **cust_alarms**.

1. V **alarmsPanel.js** soubor **cust_alarms** složku, upravit název třídy, která má být **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Přidejte následující řádek, který `src/components/pages/dashboard/panels/index.js` souboru:

    ```nodejs
    export * from './cust_alarms';
    ```

1. Nahraďte `AlarmsPanel` s `CustAlarmsPanel` v `src/components/pages/dashboard/dashboard.js` souboru:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Nyní jste nahradili původní **alarmy** panel s názvem kopií **CustAlarms**. Tato kopie je totožná s původní. Nyní můžete upravit kopii. Například, chcete-li změnit sloupce řazení **alarmy** panelu:

1. Otevřete soubor `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js`.

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

Následující snímek obrazovky ukazuje novou verzi **alarmy** panelu:

![Panel alarmy aktualizovat](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Přizpůsobení telemetrických dat grafu

Na graf telemetrických dat **řídicí panel** stránce je definován soubory v `src/components/pages/dashboard/panels/telemtry` složky. Uživatelské rozhraní načte telemetrická data z back-end řešení v `src/services/telemetryService.js` souboru. Následující kroky ukazují, jak změnit časové období, zobrazeny v grafu telemetrická data z 15 minut až 5 minut:

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

**Řídicí panel** stránce se zobrazí klíčové ukazatele výkonu v **klíčové ukazatele výkonu systému** panelu. Tyto klíčové ukazatele výkonu počítají v `src/components/pages/dashboard/dashboard.js` souboru. Klíčové ukazatele výkonu jsou vykreslovány `src/components/pages/dashboard/panels/kpis/kpisPanel.js` souboru. Následující kroky popisují, jak vypočítat a vykreslení novou hodnotu klíčového ukazatele výkonu **řídicí panel** stránky. Přidat novou změnu procenta varování klíčový ukazatel výkonu je, jak ukazuje příklad:

1. Otevřete soubor `src/components/pages/dashboard/dashboard.js`. Upravit **initialState** objektu, který chcete zahrnout **warningAlarmsChange** vlastnost následujícím způsobem:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Upravit **currentAlarmsStats** objektu, který chcete zahrnout **totalWarningCount** jako vlastnost:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Vypočítá nový klíčový ukazatel výkonu. Najdete výpočtu pro počet kritických alarmy. Duplicitní kód a upravit kopii následujícím způsobem:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Zahrnout nové **warningAlarmsChange** klíčového ukazatele výkonu v datovém proudu klíčového ukazatele výkonu:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });
    ```

1. Zahrnout nové **warningAlarmsChange** klíčový ukazatel výkonu v data o stavu použitý pro vykreslení uživatelského rozhraní:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Aktualizace dat předávaných do panelu klíčových ukazatelů výkonu:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Nyní jste dokončili změny v `src/components/pages/dashboard/dashboard.js` souboru. Následující kroky popisují změny provádět v `src/components/pages/dashboard/panels/kpis/kpisPanel.js` soubor k zobrazení nový klíčový ukazatel výkonu:

1. Upravte následující řádek kódu k načtení nové hodnoty klíčového ukazatele výkonu následujícím způsobem:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Upravte značky pro novou hodnotu klíčového ukazatele výkonu se zobrazí takto:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
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
* [Přidat vlastní průběžné out do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Přidat vlastní panel na řídicím panelu vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-panel.md)

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení mikroslužeb řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužby](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
