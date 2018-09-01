---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8c114ed137089e70899e601ebdc1d4d39f562601
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383131"
---
## <a name="create-a-consumer-group"></a>Vytvořit skupinu uživatelů

Je potřeba vytvořit vyhrazenou skupinu spotřebitelů ve službě IoT hub do datového proudu telemetrických dat do služby Time Series Insights. Zdroje událostí v Time Series Insights by měl mít výhradní použití skupinu příjemců IoT Hub.

Následující kroky pomocí rozhraní příkazového řádku Azure ve službě Azure Cloud Shell vytvořte skupinu příjemců:

1. IoT hub je jedním z několika prostředků vygenerován při nasazení akcelerátoru řešení simulaci zařízení. Spusťte následující příkaz Najít název služby IoT hub – nezapomeňte použít název akcelerátor řešení:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT hub je prostředek typu **Microsoft.Devices/IotHubs**.

1. Přidat skupinu uživatelů s názvem **devicesimulationtsi** k rozbočovači. V následujícím příkazu použijte název centra a řešení akcelerátoru:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Teď můžete zavřít Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Vytvoření nového prostředí Time Series Insights

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) je plně spravovaná služba analýzy, ukládání a vizualizace pro správu dat časových řad IoT měřítku v cloudu. Chcete-li vytvořit nové prostředí Time Series Insights:

1. Přihlaste se k [portálu Azure](http://portal.azure.com/).

1. Vyberte **vytvořit prostředek** > **Internet of Things** > **Time Series Insights**:

    ![Nové Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. Chcete-li vytvořit prostředí Time Series Insights ve stejné skupině prostředků jako akcelerátor řešení, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Na následujícím snímku obrazovky používá název **Contoso-TSI**. Po dokončení tohoto kroku, zvolte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **simulace contoso**. Použijte název akcelerátor řešení. |
    | Umístění | Tento příklad používá **USA – východ**. Vytvořte prostředí ve stejné oblasti jako váš akcelerátoru simulace zařízení. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytvoření služby Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Přidání Time Series Insights prostředí do stejné skupiny prostředků jako akcelerátor řešení znamená, že se odstraní při odstranění akcelerátor řešení.

1. Klikněte na možnost **Vytvořit**. Může trvat několik minut, než prostředí, který se má vytvořit.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí k připojení ke službě IoT hub. Použijte skupinu příjemců, který jste vytvořili v předchozích krocích. Zdroje událostí Time Series Insights vyžaduje vyhrazenou skupinu spotřebitelů není používán jinou službou.

1. Na webu Azure Portal přejděte do nového prostředí Time Series Insights.

1. Na levé straně klikněte na tlačítko **zdroje událostí**:

    ![Zobrazit události zdroje](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Klikněte na tlačítko **přidat**:

    ![Přidání zdroje událostí](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Pokud chcete nakonfigurovat službu IoT hub jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zdroj událostí název | Na následujícím snímku obrazovky používá název **contoso-iot-hub**. Po dokončení tohoto kroku, použijte vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Pomocí služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IOT | **Contoso-simulation7d894**. Použijte název vašeho centra IoT z akcelerátor řešení simulace zařízení. |
    | Název zásad centra IOT | **iothubowner** |
    | Klíč zásad centra IOT | Toto pole se vyplní automaticky. |
    | Skupina uživatelů centra IOT | **devicesimulationtsi** |
    | Formát serializace události | **JSON** |
    | Název vlastnosti časového razítka | Ponechte prázdné |

    ![Vytvoření zdroje událostí](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Je možné [udělení dodatečného přístupu uživatelům](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) do Průzkumníka služby Time Series Insights.