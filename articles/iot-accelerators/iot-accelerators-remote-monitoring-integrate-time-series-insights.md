---
title: Integrace služby Azure Time Series Insights pomocí vzdáleného monitorování | Dokumentace Microsoftu
description: V tomto návodu se dozvíte, jak nakonfigurovat služby Time Series Insights pro existující řešení vzdáleného monitorování, které již neobsahuje Time Series Insights.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 3ae41ff4cf501a58668d25b16027029f6bae4749
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317480"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrace služby Azure Time Series Insights se vzdáleným monitorováním

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která umožňuje správu dat časových řad v IoT měřítku v cloudu. Time Series Insights můžete použít k ukládání a správu dat časových řad, prozkoumat a vizualizovat událostí současně, provádět analýzy původních příčin a porovnávat několik webů a prostředků.

Akcelerátor řešení vzdálené monitorování nyní poskytuje automatického nasazení a integraci s Time Series Insights. V tomto návodu se dozvíte, jak nakonfigurovat služby Time Series Insights pro existující řešení vzdáleného monitorování, které již neobsahuje Time Series Insights.

> [!NOTE]
> Time Series Insights není aktuálně k dispozici v cloud Azure China. Nové vzdálené monitorování akcelerátoru nasazení řešení v cloudu Azure China pomocí služby Cosmos DB pro všechna úložiště.

## <a name="prerequisites"></a>Požadavky

Pokud chcete dokončit tento návod, potřebujete jste už nasadili řešení vzdáleného monitorování:

* [Nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Vytvořit skupinu uživatelů

Vytvořte vyhrazenou skupinu spotřebitelů ve službě IoT Hub má být použit pro streamování dat do služby Time Series Insights.

> [!NOTE]
> Skupiny uživatelů se aplikace používají k načítání dat ze služby Azure IoT Hub. Každé skupině příjemců umožní až pěti příjemců výstup. Pro každých pět výstupní jímky a můžete vytvořit až 32 skupin konzumentů, by měl vytvořit novou skupinu uživatelů.

1. Na webu Azure Portal klikněte na tlačítko Cloud Shell.

1. Spusťte následující příkaz k vytvoření nové skupiny příjemců. Jako název skupiny prostředků použijte název centra IoT ve vašem nasazení vzdáleného monitorování a název vašeho nasazení vzdálené monitorování:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Nasazení služby Time Series Insights

V dalším kroku nasaďte Time Series Insights jako zdroj dalších do vašeho řešení vzdáleného monitorování a připojení ke službě IoT hub.

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com/).

1. Vyberte **vytvořit prostředek** > **Internet of Things** > **Time Series Insights**.

    ![Nové Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Pokud chcete vytvořit prostředí Time Series Insights, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Na následujícím snímku obrazovky používá název **contorosrmtsi**. Po dokončení tohoto kroku, zvolte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **Použít existující**. Vyberte název existující skupiny prostředků vzdáleného monitorování. |
    | Umístění | Používáme **USA – východ**. Pokud je to možné vytvořte ve stejné oblasti jako vaše řešení vzdálené monitorování vašeho prostředí. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytvoření služby Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klikněte na možnost **Vytvořit**. Může trvat chvíli, než se prostředí, který se má vytvořit.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí k připojení ke službě IoT hub. Ujistěte se, že použijete se skupina uživatelů vytvořené v předchozích krocích. Time Series Insights vyžaduje každá služba má vyhrazenou skupinu spotřebitelů není používán jinou službou.

1. Přejděte do nového prostředí Time Series Insights.

1. Na levé straně vyberte **zdroje událostí**.

    ![Zobrazit události zdroje](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na tlačítko **Add** (Přidat).

    ![Přidání zdroje událostí](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Pokud chcete nakonfigurovat službu IoT hub jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název zdroje událostí | Na následujícím snímku obrazovky používá název **contosorm-iot-hub**. Po dokončení tohoto kroku, použijte vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Pomocí služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IOT | **contosorma57a6**. Použijte název vašeho centra IoT z vašeho řešení vzdáleného monitorování. |
    | Název zásad centra IOT | **iothubowner** zkontrolujte zásady používané zásady vlastníka. |
    | Klíč zásad centra IOT | Toto pole se vyplní automaticky. |
    | Skupina uživatelů centra IOT | **timeseriesinsights** |
    | Formát serializace události | **JSON**     | Název vlastnosti časového razítka | Ponechte prázdné |

    ![Vytvoření zdroje událostí](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na možnost **Vytvořit**.

## <a name="configure-the-data-access-policy"></a>Konfigurace zásad přístupu dat

Pokud chcete mít jistotu, že se všichni uživatelé, kteří mají přístup k řešení vzdáleného monitorování ke zkoumání dat v Průzkumníku Time Series Insights, přidejte svoji aplikaci a uživatele v části zásady přístupu k datům na webu Azure Portal. 

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte **ContosoRM** skupinu prostředků.

1. Zvolte **contosormtsi** v seznamu prostředků Azure.

1. Zvolte **zásady přístupu k datům** zobrazíte aktuální seznam přiřazení rolí.

1. Zvolte **přidat** otevřít **vyberte pravidlo uživatele** podokně.

   Pokud nemáte oprávnění k přiřazování rolí, se nezobrazí **přidat** možnost.

1. V **Role** rozevíracího seznamu, vyberte roli, jako **čtečky** a **Přispěvatel**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** a vytvořte přiřazení role. Po chvíli se objekt zabezpečení je přiřazena role v zásady přístupu k datům.

> [!NOTE]
> Pokud potřebujete další uživatelům udělit přístup k Průzkumníka služby Time Series Insights, můžete použít následující postup [udělení přístupu k datům](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurace Azure Stream Analytics 

Dalším krokem je konfigurace mikroslužeb Azure Stream Analytics Manageru přestat odesílání zpráv do služby Cosmos DB a uložit je pouze v Time Series Insights. Tento krok přeskočte, pokud chcete duplikovat zpráv ve službě Cosmos DB.

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte **ContosoRM** skupinu prostředků.

1. Najdete Azure Stream Analytics (ASA) datové proudy úlohy v seznamu prostředků. Název prostředku začíná **streamingjobs -**.

1. V horní části stránky klikněte na tlačítko Zastavit ASA streamovacích úloh.

1. Umožňuje upravit dotaz Azure Stream Analytics a odebrat **vyberte**, **INTO**, a **FROM** streamování klauzulí, které odkazují na zprávy ve službě Cosmos DB. Těchto klauzulí by měla být v dolní části dotazu a vypadat jako v následujícím příkladu:

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

6. Restartujte streamovacích úloh Azure Stream Analytics.

7. Stáhněte si nejnovější změny do mikroslužeb správce Azure Stream Analytics tak, že zadáte následující příkaz na příkazovém řádku:

.NET: 

```
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:
```
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurace Telemetrie mikroslužeb

Stáhněte si nejnovější mikroslužeb Telemetrie zadáním následujícího příkazu do příkazového řádku:

.NET:
```
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Volitelné]*  Konfigurace webového uživatelského rozhraní pro propojení Průzkumníka služby Time Series Insights

Data snadno zobrazit v Průzkumníku Time Series Insights, doporučujeme přizpůsobením uživatelského rozhraní snadno propojit do prostředí. Uděláte to tak, stáhněte si nejnovější změny do webového uživatelského rozhraní pomocí následujícího příkazu:

```
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurace proměnných prostředí

K dokončení integrace služby Time Series Insights, musíte nakonfigurovat prostředí, které vaše nasazení aktualizované mikroslužeb.

### <a name="basic-deployments"></a>Základní nasazení

Konfigurace prostředí, které `basic` nasazení aktualizované mikroslužeb.

1. Na webu Azure Portal, klikněte na **Azure Active Directory** kartu na panelu vlevo.

1. Klikněte na **registrace aplikací**.

1. Vyhledejte a klikněte na vaše **ContosoRM** aplikace.

1. Přejděte do **nastavení** > **klíče** a vytvořte nový klíč pro vaši aplikaci. Ujistěte se, že jste hodnotu klíče zkopírovat do bezpečného umístění.

1. O přijetí změn [nejnovější docker compose soubor yaml](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) z úložiště Github pomocí nejnovější značky. 

1. SSH k virtuálnímu počítači podle kroků uvedených v [postupy vytváření a používání klíčů SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Jakmile budete připojeni, zadejte `cd /app`.

1. Přidejte následující proměnné prostředí na jednotlivých mikroslužeb v dockeru compose soubor yaml a `env-setup` skriptu ve virtuálním počítači:

    ```
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Přejděte **telemetrické službě** a také upravit docker compose soubor přidáním stejné výše uvedených proměnných prostředí.

1. Přejděte **Správce služby Azure Stream Analytics** a úpravy docker compose souboru tak, že přidáte `PCS_TELEMETRY_STORAGE_TYPE`.

1. Restartujte kontejnerů dockeru pomocí `sudo ./start.sh` z virtuálního počítače.

### <a name="standard-deployments"></a>Standardní nasazení

Konfigurace prostředí, které `standard` nasazení pro aktualizace mikroslužby výše

1. Na příkazovém řádku spusťte `kubectl proxy`. Další informace najdete v tématu [přístup k rozhraní API Kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Otevřete konzolu pro správu Kubernetes.

1. Najdete mapování konfigurace k přidání následující nové proměnné prostředí pro TSI:

    ```
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Upravte soubor yaml šablony pod služba telemetrie:

    ```
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

5. Upravte soubor yaml šablony pro Azure Stream Analytics pod service manager:

    ```
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak zkoumat data a Diagnostika výstrahu v Průzkumníku Time Series Insights, najdete v našem kurzu na [analýzu provádění kořenové příčiny](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Zjistěte, jak zkoumat a dotazování dat v Průzkumníku Time Series Insights, najdete v článku dokumentace na [Průzkumníka služby Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
