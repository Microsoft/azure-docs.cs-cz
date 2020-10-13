---
title: Integrace Time Series Insights se vzdáleným monitorováním – Azure | Microsoft Docs
description: V tomto postupu se naučíte, jak nakonfigurovat Time Series Insights pro stávající řešení vzdáleného monitorování, které ještě nezahrnuje Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: b0ab1e2e43210ecd7a61b7dffcb12d2c7c5c1c46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86536602"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrace služby Azure Time Series Insights se vzdáleným monitorováním

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která slouží ke správě dat časových řad ve službě IoT-Scale v cloudu. Pomocí Time Series Insights můžete ukládat a spravovat data časových řad, zkoumat a vizualizovat události souběžně, provádět analýzu původních příčin a porovnat několik webů a prostředků.

Akcelerátor řešení vzdáleného monitorování teď poskytuje automatické nasazení a integraci s Time Series Insights. V tomto postupu se naučíte, jak nakonfigurovat Time Series Insights pro stávající řešení vzdáleného monitorování, které ještě nezahrnuje Time Series Insights.

> [!NOTE]
> Time Series Insights není aktuálně k dispozici v cloudu Azure Čína. Nové nasazení akcelerátoru řešení vzdáleného monitorování v cloudu Azure Čína používá Cosmos DB pro všechna úložiště.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento postup, musíte již nasadit řešení vzdáleného monitorování:

* [Nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Vytvoření skupiny uživatelů

Vytvořte ve svém IoT Hub vyhrazenou skupinu příjemců, která se použije pro streamování dat do Time Series Insights.

> [!NOTE]
> Skupiny uživatelů používají aplikace k vyžádání dat z Azure IoT Hub. Každá skupina uživatelů umožňuje až pět výstupních spotřebitelů. Měli byste vytvořit novou skupinu uživatelů pro každých pět výstupních umyvadel a můžete vytvořit až 32 skupin uživatelů.

1. V Azure Portal klikněte na tlačítko Cloud Shell.

1. Spuštěním následujícího příkazu vytvořte novou skupinu uživatelů. V nasazení vzdáleného monitorování použijte název centra IoT a jako název skupiny prostředků zadejte název svého nasazení vzdáleného monitorování:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Nasazení Time Series Insights

V dalším kroku nasaďte Time Series Insights jako další prostředek do řešení vzdáleného monitorování a připojte ho ke službě IoT Hub.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **Time Series Insights**.

    ![Nový Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Chcete-li vytvořit prostředí Time Series Insights, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Následující snímek obrazovky používá název **contorosrmtsi**. Po dokončení tohoto kroku vyberte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **Použijte existující**. Vyberte název existující skupiny prostředků vzdáleného monitorování. |
    | Umístění | Používáme **východní USA**. Pokud je to možné, vytvořte prostředí ve stejné oblasti jako řešení vzdáleného monitorování. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytvoření instance Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klikněte na **Vytvořit**. Vytvoření prostředí může chvíli trvat.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí pro připojení ke službě IoT Hub. Ujistěte se, že používáte skupinu uživatelů vytvořenou v předchozích krocích. Time Series Insights vyžaduje, aby každá služba měla vyhrazenou skupinu uživatelů, kterou nepoužívá jiná služba.

1. Přejděte do nového Time Series Insights prostředí.

1. Na levé straně vyberte **zdroje událostí**.

    ![Zobrazení zdrojů událostí](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na **Přidat**.

    ![Přidat zdroj události](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Ke konfiguraci služby IoT Hub jako nového zdroje událostí použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název zdroje událostí | Následující snímek obrazovky používá název **contosorm-IoT-Hub**. Po dokončení tohoto kroku použijte vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Použít IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IoT Hub | **contosorma57a6**. Použijte název centra IoT z řešení vzdáleného monitorování. |
    | Název zásady centra IoT Hub | **iothubowner** Ujistěte se, že použitá zásada je zásada vlastníka. |
    | Klíč zásad centra IoT Hub | Toto pole je vyplněno automaticky. |
    | Skupina uživatelů centra IoT Hub | **timeseriesinsights** |
    | Formát serializace události | **JSON**     | 
    | Název vlastnosti časového razítka | Ponechte prázdné. |

    ![Vytvořit zdroj události](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na **Vytvořit**.

## <a name="configure-the-data-access-policy"></a>Konfigurace zásad přístupu k datům

Abyste měli jistotu, že všichni uživatelé, kteří mají přístup k řešení vzdáleného monitorování, budou moci prozkoumat data v Průzkumníkovi Time Series Insights, přidejte svoji aplikaci a uživatele v části zásady přístupu k datům v Azure Portal. 

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Vyberte skupinu prostředků **ContosoRM** .

1. V seznamu prostředků Azure vyberte **contosormtsi** .

1. Pokud chcete zobrazit aktuální seznam přiřazení rolí, vyberte **zásady přístupu k datům** .

1. Zvolte **Přidat** a otevřete tak podokno **vybrat pravidlo uživatele** .

   Pokud nemáte oprávnění k přiřazování rolí, neuvidíte možnost **Přidat** .

1. V rozevíracím seznamu **role** vyberte roli **Čtenář** a **Přispěvatel**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** a vytvořte přiřazení role. Po chvíli se objektu zabezpečení přiřadí role v zásadách přístupu k datům.

> [!NOTE]
> Pokud potřebujete udělit dalším uživatelům přístup k aplikaci Time Series Insights Explorer, můžete k [udělení přístupu k datům](../time-series-insights/time-series-insights-data-access.md#grant-data-access)použít tento postup.

## <a name="configure-azure-stream-analytics"></a>Konfigurace Azure Stream Analytics 

Dalším krokem je konfigurace mikroslužby Azure Stream Analytics Manageru na odesílání zpráv Cosmos DB a jejich ukládání do Time Series Insights. Tento krok přeskočte, pokud byste chtěli duplikovat zprávy v Cosmos DB.

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Vyberte skupinu prostředků **ContosoRM** .

1. V seznamu prostředků Najděte úlohu streamování Azure Stream Analytics (ASA). Název prostředku začíná na **streamingjobs-**.

1. V horní části klikněte na tlačítko a zastavte úlohy streamování ASA.

1. Upravte dotaz ASA a odeberte klauzule **Select**, **into**a **from** , které odkazují na datový proud zpráv v Cosmos DB. Tyto klauzule by měly být na konci dotazu a měly by vypadat jako v následujícím příkladu:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Restartujte úlohy streamování Azure Stream Analytics.

7. Do příkazového řádku zadejte následující příkaz a Stáhněte si nejnovější změny mikroslužby Azure Stream Analytics Manageru:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurace mikroslužby telemetrie

Stáhněte si nejnovější mikroslužbu telemetrie zadáním následujícího příkazu do příkazového řádku:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Nepovinné]* Konfigurace webového uživatelského rozhraní pro odkazování na Time Series Insights Explorer

Chcete-li snadno zobrazit data v Průzkumníkovi Time Series Insights, doporučujeme přizpůsobovat uživatelské rozhraní pro snadné připojení k prostředí. Provedete to tak, že pomocí následujícího příkazu načtete do webového uživatelského rozhraní nejnovější změny:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurace proměnných prostředí

Pro dokončení integrace Time Series Insights budete muset nakonfigurovat prostředí nasazení pro aktualizované mikroslužby.

### <a name="basic-deployments"></a>Základní nasazení

Nakonfigurujte prostředí `basic` nasazení pro aktualizované mikroslužby.

1. V Azure Portal klikněte na panelu na levé straně na kartu **Azure Active Directory** .

1. Klikněte na **Registrace aplikací**.

1. Vyhledejte a klikněte na aplikaci **ContosoRM** .

1. Přejděte na **Nastavení**  >  **klíče** a pak vytvořte nový klíč pro svou aplikaci. Nezapomeňte zkopírovat hodnotu klíče do bezpečného umístění.

1. Pomocí nejnovější značky si z úložiště GitHubu [Stáhněte nejnovější soubor YAML Docker](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) . 

1. Připojte se k virtuálnímu počítači přes SSH podle postupu popsaného v tématu [jak vytvářet a používat klíče SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Po připojení zadejte `cd /app` .

1. Přidejte následující proměnné prostředí pro každou mikroslužbu do souboru Docker YAML a `env-setup` ve skriptu ve virtuálním počítači:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Přejděte do **služby telemetrie** a také upravte soubor s doplňováním Docker přidáním stejných proměnných prostředí výše.

1. Přejděte do **služby správce ASA** a úpravou souboru Docker vytvořte soubor přidáním `PCS_TELEMETRY_STORAGE_TYPE` .

1. Restartujte kontejnery Docker pomocí `sudo ./start.sh` z virtuálního počítače.

> [!NOTE]
> Výše uvedená konfigurace proměnných prostředí je platná pro vzdálené monitorování verzí před 1.0.2

### <a name="standard-deployments"></a>Standardní nasazení

Konfigurace prostředí `standard` nasazení pro aktualizované mikroslužby výše

1. Na příkazovém řádku spusťte příkaz `kubectl proxy` . Další informace najdete v tématu [přístup k rozhraní Kubernetes API](https://kubernetes.io/docs/reference/access-authn-authz/#using-kubectl-to-start-a-proxy-server).

1. Otevřete konzolu pro správu Kubernetes.

1. Vyhledejte mapu konfigurace a přidejte následující nové proměnné prostředí pro TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Upravte soubor YAML šablony pro službu telemetrie pod:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Upravte soubor YAML šablony pro službu správce ASA pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak prozkoumat data a diagnostikovat upozornění v Průzkumníkovi Time Series Insights, najdete v našem kurzu o [provedení analýzy hlavní příčiny](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Informace o tom, jak prozkoumat a dotazovat data v Průzkumníkovi Time Series Insights, najdete v dokumentaci v [průzkumníkovi Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
