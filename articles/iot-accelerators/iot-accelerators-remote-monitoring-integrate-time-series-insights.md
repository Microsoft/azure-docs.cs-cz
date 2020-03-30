---
title: Integrace přehledů časových řad se vzdáleným monitorováním – Azure | Dokumenty společnosti Microsoft
description: V tomto návodu se dozvíte, jak nakonfigurovat Přehledy časových řad pro existující řešení vzdáleného monitorování, které ještě neobsahuje přehledy časových řad.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564640"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrace služby Azure Time Series Insights se vzdáleným monitorováním

Azure Time Series Insights je plně spravovaná analytická, úložná a vizualizační služba pro správu dat časových řad v měřítku IoT v cloudu. Pomocí přehledů Time Series Insights můžete současně ukládat a spravovat data časových řad, zkoumat a vizualizovat události, provádět analýzu hlavních příčin a porovnávat více webů a prostředků.

Akcelerátor řešení vzdáleného monitorování nyní poskytuje automatické nasazení a integraci s time series insights. V tomto návodu se dozvíte, jak nakonfigurovat Přehledy časových řad pro existující řešení vzdáleného monitorování, které ještě neobsahuje přehledy časových řad.

> [!NOTE]
> Time Series Insights není momentálně dostupná v cloudu Azure China. Nová nasazení akcelerátoru řešení vzdáleného monitorování v cloudu Azure China používají Cosmos DB pro všechna úložiště.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento postup dokončit, musíte již nasadit řešení vzdáleného monitorování:

* [Nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Vytvoření skupiny spotřebitelů

Vytvořte vyhrazenou skupinu spotřebitelů ve vašem Centru IoT, která se použije pro streamování dat do Přehledů time series.

> [!NOTE]
> Skupiny spotřebitelů používají aplikace k vyprávění dat z Azure IoT Hub. Každá skupina spotřebitelů umožňuje až pět výstupních spotřebitelů. Měli byste vytvořit novou skupinu spotřebitelů pro každých pět výstupních propadů a můžete vytvořit až 32 skupin spotřebitelů.

1. Na webu Azure Portal klikněte na tlačítko Cloud Shell.

1. Chcete-li vytvořit novou skupinu spotřebitelů, proveďte následující příkaz. Použijte název služby IoT hub v nasazení vzdáleného monitorování a název nasazení vzdáleného monitorování jako název skupiny prostředků:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Nasazení přehledů časových řad

Dále nasaďte Time Series Insights jako další prostředek do vašeho řešení vzdáleného monitorování a připojte ho k centru IoT.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte **možnost Vytvořit zdroj** > **Internet of Things** > **Time Series Insights**.

    ![Nové přehledy časové řady](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Chcete-li vytvořit prostředí Time Series Insights, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Následující snímek obrazovky používá název **contorosrmtsi**. Po dokončení tohoto kroku si vyberte vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **Použijte existující**. Vyberte název existující skupiny prostředků vzdáleného monitorování. |
    | Umístění | Používáme **východní USA**. Pokud je to možné, vytvořte prostředí ve stejné oblasti jako řešení vzdáleného monitorování. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytváření přehledů časových řad](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klikněte na **Vytvořit**. Může chvíli trvat, než bude prostředí vytvořeno.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí pro připojení k centru IoT hub. Ujistěte se, že používáte skupinu spotřebitelů vytvořenou v předchozích krocích. Time Series Insights vyžaduje, aby každá služba měla vyhrazenou skupinu spotřebitelů, která není používána jinou službou.

1. Přejděte do nového prostředí Time Series Insights.

1. Vlevo vyberte **Zdroje událostí**.

    ![Zobrazit zdroje událostí](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na **Přidat**.

    ![Přidat zdroj událostí](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Chcete-li službu IoT hub nakonfigurovat jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název zdroje událostí | Následující snímek obrazovky používá název **contosorm-iot-hub**. Po dokončení tohoto kroku použijte svůj vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Použití služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IoT Hub | **contosorma57a6**. Použijte název služby IoT hub z vašeho řešení vzdáleného monitorování. |
    | Název zásady centra IoT Hub | **iothubmajitel** Ujistěte se, že použitá zásada je zásada vlastníka. |
    | Klíč zásad centra Iot | Toto pole je vyplněno automaticky. |
    | Skupina uživatelů centra IoT Hub | **přehled y timeseriesinsights** |
    | Formát serializace události | **JSON**     | 
    | Název vlastnosti časového razítka | Ponechte prázdné |

    ![Vytvořit zdroj událostí](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na **Vytvořit**.

## <a name="configure-the-data-access-policy"></a>Konfigurace zásad přístupu k datům

Chcete-li se ujistit, že všichni uživatelé, kteří mají přístup k vašemu řešení vzdáleného monitorování, budou moct zkoumat data v průzkumníku Time Series Insights, přidejte vaši aplikaci a uživatele v rámci zásad přístupu k datům na webu Azure Portal. 

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte skupinu prostředků **ContosoRM.**

1. V seznamu prostředků Azure zvolte **contosormtsi.**

1. Zvolte **Zásady přístupu k datům,** chcete-li zobrazit aktuální seznam přiřazení rolí.

1. Zvolte **Přidat,** chcete-li otevřít podokno **Vybrat uživatelské pravidlo.**

   Pokud nemáte oprávnění k přiřazování rolí, možnost **Přidat** se nezobrazí.

1. V rozevíracím seznamu **Role** vyberte roli, například **Čtečku** a **Přispěvatel**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** a vytvořte přiřazení role. Po několika okamžicích je objektu zabezpečení přiřazena role v zásadách přístupu k datům.

> [!NOTE]
> Pokud potřebujete udělit přístup dalším uživatelům průzkumníku Time Series Insights, můžete pomocí těchto kroků [udělit přístup k datům](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurace Azure Stream Analytics 

Dalším krokem je konfigurace mikroslužby Azure Stream Analytics Manager tak, aby přestala odesílat zprávy do Cosmos DB a ukláněla je jenom do přehledů Time Series. Tento krok přeskočte, pokud chcete duplikovat zprávy v Cosmos DB.

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte skupinu prostředků **ContosoRM.**

1. Najděte úlohu streamování Azure Stream Analytics (ASA) v seznamu prostředků. Název prostředku začíná **streamingjobs-**.

1. V horní části klikněte na tlačítko pro zastavení úloh y streamování ASA.

1. Upravte dotaz ASA a odeberte klauzule **SELECT**, **INTO**a **FROM,** které odkazují na datový proud zpráv v Cosmos DB. Tyto klauzule by měly být v dolní části dotazu a vypadat jako v následujícím příkladu:

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

7. Vytáhněte nejnovější změny mikroslužby správce Azure Stream Analytics zadáním následujícího příkazu na příkazovém řádku:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurace mikroslužby Telemetrie

Vytáhněte nejnovější mikroslužbu Telemetrie zadáním následujícího příkazu do příkazového řádku:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Nepovinné]* Konfigurace webového uživatelského rozhraní pro propojení s průzkumníkem Přehledy time series

Chcete-li snadno zobrazit data v průzkumníku Time Series Insights, doporučujeme přizpůsobit ui tak, aby bylo snadno propojeno s prostředím. Chcete-li tak učinit, vytáhněte nejnovější změny webového uživatelského uživatelského uživatelského času pomocí následujícího příkazu:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurace proměnných prostředí

Chcete-li dokončit integraci Time Series Insights, budete muset nakonfigurovat prostředí vašeho nasazení pro aktualizované mikroslužby.

### <a name="basic-deployments"></a>Základní nasazení

Nakonfigurujte prostředí `basic` nasazení pro aktualizované mikroslužby.

1. Na webu Azure Portal klikněte na kartě **Azure Active Directory** na levém panelu.

1. Klikněte na **registrace aplikací**.

1. Vyhledejte aplikaci **ContosoRM** a klikněte na ni.

1. Přejděte na**položku Klávesy** **nastavení** > a vytvořte nový klíč pro aplikaci. Ujistěte se, že zkopírujete hodnotu klíče do bezpečného umístění.

1. Vytáhněte [nejnovější docker compose yaml soubor](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) z úložiště GitHub pomocí nejnovější značky. 

1. SSH do virtuálního počítači podle kroků popsaných o [tom, jak vytvořit a používat SSH keys](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Po připojení `cd /app`zadejte .

1. Přidejte následující proměnné prostředí ke každé mikroslužbě v souboru yaml dockeru a skriptu `env-setup` ve virtuálním počítači:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Přejděte do **služby telemetrie** a také upravte soubor compose dockeru přidáním stejných proměnných prostředí výše.

1. Přejděte na **službu správce ASA** a upravte `PCS_TELEMETRY_STORAGE_TYPE`soubor docker compose přidáním .

1. Restartujte kontejnery `sudo ./start.sh` dockeru pomocí z virtuálního počítače.

> [!NOTE]
> Výše uvedená konfigurace proměnných prostředí je platná pro verze vzdáleného monitorování před 1.0.2

### <a name="standard-deployments"></a>Standardní nasazení

Konfigurace prostředí `standard` nasazení pro výše uvedené aktualizované mikroslužby

1. Na příkazovém `kubectl proxy`řádku spusťte . Další informace naleznete [v tématu přístup k rozhraní API Kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Otevřete konzolu pro správu Kubernetes.

1. Vyhledejte konfigurační mapu a přidejte pro TSI následující nové proměnné prostředí:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Upravte soubor šablony yaml pro pod služby telemetrie:

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

5. Upravte soubor šablony yaml pro pod služby správce ASA:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak prozkoumat data a diagnostikovat výstrahu v průzkumníku Time Series Insights, najdete v našem kurzu o [provádění analýzy hlavních příčin](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Informace o tom, jak zkoumat a dotazovat data v průzkumníku Time Series Insights, najdete v dokumentaci k [průzkumníku Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
