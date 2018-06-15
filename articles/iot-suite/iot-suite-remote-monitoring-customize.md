---
title: Přizpůsobení vzdálené monitorování řešení uživatelské rozhraní – Azure | Microsoft Docs
description: Tento článek obsahuje informace o tom, jak můžete přístup ke zdrojovému kódu pro řešení akcelerátoru vzdálené monitorování uživatelského rozhraní a provádět některé úpravy.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: be20d45b380f66208884f15f4644f36f2a403837
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774046"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Přizpůsobení akcelerátoru řešení vzdáleného monitorování

Tento článek obsahuje informace o tom, jak můžete přístup ke zdrojovému kódu a přizpůsobení uživatelského rozhraní vzdálené monitorování akcelerátoru řešení. Článek popisuje:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava prostředí pro místní vývoj uživatelského rozhraní

Řešení akcelerátoru vzdálené monitorování kód uživatelského rozhraní je implementováno pomocí rozhraní React.js. Zdrojový kód v můžete najít [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) úložiště GitHub.

Provést změny uživatelského rozhraní, můžete jeho kopii spustit místně. Místní kopie se připojí k nasazenou instanci řešení pro provedení akcí, například načítání telemetrie.

Následující kroky popisují proces nastavení místního prostředí pro vývoj uživatelského rozhraní:

1. Nasazení **základní** instanci akcelerátoru řešení pomocí **počítačů** rozhraní příkazového řádku. Poznamenejte si název vašeho nasazení a přihlašovacích údajů, které jste zadali pro virtuální počítač. Další informace najdete v tématu [nasadit pomocí rozhraní příkazového řádku](iot-suite-remote-monitoring-deploy-cli.md).

1. Pomocí portálu Azure nebo [az rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) umožňující přístup SSH pro virtuální počítač, který je hostitelem mikroslužeb ve vašem řešení. Příklad:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Pomocí portálu Azure nebo [az rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) najít název a veřejnou IP adresu virtuálního počítače. Příklad:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Použití SSH se připojit k virtuálnímu počítači pomocí IP adresy z předchozího kroku a zadaná pověření při spuštění **počítače** k nasazení řešení.

1. Povolit místní UX pro připojení, spusťte následující příkazy v prostředí bash ve virtuálním počítači:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Jakmile ověříte, že se příkaz dokončí a spustí na webu, můžete odpojit z virtuálního počítače.

1. V místní kopii [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) úložiště, upravit **.env** souboru zadejte adresu URL nasazené řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Na příkazovém řádku ve vaší místní kopii `azure-iot-pcs-remote-monitoring-webui` složky, spusťte následující příkazy instalace potřebných knihoven a místní spuštění uživatelského rozhraní:

    ```cmd/sh
    npm install
    npm start
    ```

1. Předchozí příkaz spustí místně na rozhraní http://localhost:3000/dashboard. Můžete upravit kód spuštěného webu a jeho dynamicky aktualizovat zobrazení.

## <a name="customize-the-layout"></a>Přizpůsobení rozložení

Každé stránce v řešení vzdáleného monitorování se skládá z sadu ovládacích prvků, označuje jako *panelů* ve zdrojovém kódu. Například **řídicí panel** stránky se skládá z pěti panelů: Přehled, Map, výstrahy, Telemetrie a klíčových ukazatelů výkonu. Zdrojový kód, který definuje každé stránce a jeho panely v můžete najít [počítačů vzdálené monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) úložiště GitHub. Například kód, který definuje **řídicí panel** stránky, jeho rozložení a panelů na stránce se nachází v [src nebo součástí nebo stránek/řídicí panel](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) složky.

Protože panely spravovat jejich vlastní rozložení a změna velikosti, můžete snadno upravit rozložení stránky. Například následující změny **PageContent** element v `src/components/pages/dashboard/dashboard.js` souboru prohození pozice panelů mapy a telemetrie a změnit relativní šířku mapy a panelů klíčového ukazatele výkonu:

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

![Rozložení panelu změn](media/iot-suite-remote-monitoring-customize/layout.png)

> [!NOTE]
> Mapy není nakonfigurovaný v místním nasazení.

Můžete také přidat více instancí stejného panelu nebo několik verzí Pokud jste [duplicitní a přizpůsobení panelu](#duplicate-and-customize-an-existing-control). Následující příklad ukazuje, jak přidat dvě instance panelu telemetrie úpravou `src/components/pages/dashboard/dashboard.js` souboru:

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

Potom můžete zobrazit různé telemetrie každý panelu:

![Více panelů telemetrie](media/iot-suite-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Mapy není nakonfigurovaný v místním nasazení.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicitní a přizpůsobit existující ovládací prvek

Následující kroky popisují způsob použití **výstrahy** panely jako příklad toho, jak duplicitní stávajícího panelu, upravit a použít upravenou verzi:

1. Ve vaší místní kopii úložiště vytvořit kopii **výstrahy** složku `src/components/pages/dashboard/panels` složky. Název nové kopie **cust_alarms**.

1. V **alarmsPanel.js** v soubor **cust_alarms** složku, upravit název třídy, která má být **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Přidejte následující řádek na `src/components/pages/dashboard/panels/index.js` souboru:

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

Teď můžete nahradit původní **výstrahy** panelů s názvem kopie **CustAlarms**. Tato kopie je stejný jako původní. Nyní můžete upravit kopie. Pro příklad, chcete-li změnit pořadí v sloupců **výstrahy** panelu:

1. Otevřete soubor `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js`.

1. Definice sloupců změnit, jak je znázorněno v následující fragment kódu:

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

![Aktualizovat panely výstrahy](media/iot-suite-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Přizpůsobit graf telemetrie

Graf telemetrii na **řídicí panel** stránce je definován soubory v `src/components/pages/dashboard/panels/telemtry` složky. Uživatelské rozhraní načte telemetrii z back-end řešení v `src/services/telemetryService.js` souboru. Následující kroky vám ukážou, jak změnit časové období, zobrazí v grafu telemetrie z 15 minut až 5 minut:

1. V `src/services/telemetryService.js` souboru, vyhledejte volaná funkce **getTelemetryByDeviceIdP15M**. Vytvoření kopie této funkce a změnit kopii následujícím způsobem:

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

1. Chcete-li použít tuto novou funkci pro naplnění grafu telemetrická data, otevřete `src/components/pages/dashboard/dashboard.js` souboru. Vyhledejte řádek, který inicializuje datový proud telemetrie a upravit ho následujícím způsobem:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Graf telemetrie nyní zobrazuje za pět minut telemetrická data:

![Graf telemetrie jeden den](media/iot-suite-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Přidat nový klíčový ukazatel výkonu

**Řídicí panel** stránka zobrazuje klíčových ukazatelů výkonu v **klíčových ukazatelů výkonu systému** panelu. Probíhá výpočet těchto klíčových ukazatelů výkonu `src/components/pages/dashboard/dashboard.js` souboru. Klíčové ukazatele výkonu jsou vykreslovány pomocí `src/components/pages/dashboard/panels/kpis/kpisPanel.js` souboru. Následující kroky popisují postup výpočtu a vykreslení na novou hodnotu klíčového ukazatele výkonu **řídicí panel** stránky. Ukazuje příklad je přidání nové procento změny v nastavení upozornění upozornění klíčového ukazatele výkonu:

1. Otevřete soubor `src/components/pages/dashboard/dashboard.js`. Změnit **initialState** objekt, který chcete zahrnout **warningAlarmsChange** vlastnost následujícím způsobem:

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

1. Změnit **currentAlarmsStats** objekt, který chcete zahrnout **totalWarningCount** jako vlastnost:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Vypočítá nový klíčový ukazatel výkonu. Najít výpočtu pro počet kritické výstrahy. Duplicitní kód a změnit kopii následujícím způsobem:

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

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

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

1. Aktualizujte data předaná panelu klíčových ukazatelů výkonu:

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

Nyní dokončení změny v `src/components/pages/dashboard/dashboard.js` souboru. Následující kroky popisují změny udělejte `src/components/pages/dashboard/panels/kpis/kpisPanel.js` soubor zobrazíte nové klíčového ukazatele výkonu:

1. Upravte následující řádek kódu načíst nová hodnota klíčového ukazatele výkonu následujícím způsobem:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Upravte kód zobrazíte novou hodnotu klíčového ukazatele výkonu:

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

**Řídicí panel** stránky teď zobrazuje nová hodnota klíčového ukazatele výkonu:

![Upozornění klíčového ukazatele výkonu](media/iot-suite-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Přizpůsobení mapy

Najdete v článku [přizpůsobit mapy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) stránky na webu GitHub podrobnosti mapy součásti v řešení.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Další možnosti přizpůsobení

Další změnit prezentace a vizualizací vrstvu v řešení vzdáleného monitorování, můžete upravit kód. Příslušné úložiště Githubu jsou:

* [Mikroslužbu konfigurace pro Azure IoT řešení (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Mikroslužbu konfigurace pro řešení Azure IoT (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure IoT počítačů vzdálené monitorování webového uživatelského rozhraní](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o zdrojích, které vám umožní přizpůsobit webového uživatelského rozhraní v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektury vzdálené monitorování](iot-suite-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu [přizpůsobit a znovu ho zaveďte mikroslužbu](iot-suite-microservices-example.md)
<!-- Next tutorials in the sequence -->