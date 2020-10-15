---
title: Nasazení řešení vzdáleného monitorování pomocí CLI – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak zřídit akcelerátor řešení vzdáleného monitorování pomocí rozhraní příkazového řádku.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: f9dcf19f5318021df5d9fdde777b8786942e33d8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072250"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Nasazení akcelerátoru řešení vzdáleného monitorování pomocí rozhraní příkazového řádku

V této příručce se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování. Řešení nasadíte pomocí rozhraní příkazového řádku. Řešení můžete nasadit také pomocí webového uživatelského rozhraní na adrese azureiotsolutions.com. Další informace o této možnosti najdete v tématu [nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md) .

## <a name="prerequisites"></a>Požadavky

K nasazení akcelerátoru řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

Chcete-li spustit rozhraní příkazového řádku, potřebujete [Node.js](https://nodejs.org/) nainstalovat na místním počítači.

## <a name="install-the-cli"></a>Instalace rozhraní příkazového řádku

Rozhraní CLI nainstalujete spuštěním následujícího příkazu v prostředí příkazového řádku:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Přihlášení k rozhraní příkazového řádku

Než budete moct nasadit akcelerátor řešení, musíte se přihlásit ke svému předplatnému Azure pomocí rozhraní příkazového řádku:

```cmd/sh
pcs login
```

Dokončete proces přihlašování podle pokynů na obrazovce.

## <a name="deployment-options"></a>Možnosti nasazení

Při nasazení akcelerátoru řešení je k dispozici několik možností konfigurace procesu nasazení:

| Možnost | Hodnoty | Popis |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | _Základní_ nasazení je určené pro testování a ukázky, nasadí všechny mikroslužby na jeden virtuální počítač. _Standardní_ nasazení je určené pro produkční prostředí, nasadí mikroslužby na několik virtuálních počítačů. _Místní_ nasazení nakonfiguruje kontejner Docker tak, aby spouštěl mikroslužby na místním počítači a používal Azure Cloud Services, jako je například storage a Cosmos DB. |
| Modul runtime | `dotnet`, `java` | Vybere jazykovou implementaci mikroslužeb. |

Informace o tom, jak používat místní možnost nasazení, najdete v části [spuštění řešení vzdáleného monitorování místně](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Základní a standardní nasazení

V této části najdete přehled klíčových rozdílů mezi základním a standardním nasazením.

### <a name="basic"></a>Základní

Můžete provést základní nasazení z [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) nebo pomocí rozhraní příkazového řádku.

Základní nasazení je zaměřené na předvádí řešení. Za účelem snížení nákladů jsou všechny mikroslužby nasazeny na jednom virtuálním počítači. Toto nasazení nepoužívá architekturu připravenou pro produkční prostředí.

Základní nasazení ve vašem předplatném Azure vytvoří následující služby:

| Počet | Prostředek                       | Typ         | Používá se pro |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuální počítač se systémem Linux](https://azure.microsoft.com/services/virtual-machines/) | D1 v2 úrovně Standard  | Hostování mikroslužeb |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – úroveň Standard | Správa zařízení a komunikace |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Ukládání konfiguračních dat, pravidel, upozornění a dalších chladírenských úložišť |  
| 1     | [Účet služby Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)  | Standard        | Úložiště pro kontrolní body virtuálního počítače a streamování |
| 1     | [Webová aplikace](https://azure.microsoft.com/services/app-service/web/)        |                 | Hostování front-endové webové aplikace |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit a zabezpečení uživatelů |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení umístění prostředků |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýz v reálném čase |
| 1     | [Služba Azure Device Provisioning](../iot-dps/index.yml)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro data zpráv a umožnění hloubkové analýzy telemetrie podrobně |

### <a name="standard"></a>Standard

Standardní nasazení můžete provést pouze pomocí rozhraní příkazového řádku.

Standardní nasazení je nasazení připravené pro produkční prostředí, které může vývojář přizpůsobit a zvětšit. Možnost standardního nasazení použijte, když jste připraveni přizpůsobit architekturu připravenou pro produkční prostředí vytvořenou pro škálování a rozšiřitelnost. Mikroslužby aplikací jsou sestavené jako kontejnery Docker a nasazeny pomocí služby Azure Kubernetes. Kubernetes Orchestrator nasazuje, škáluje a spravuje mikroslužby.

Standardní nasazení vytvoří v předplatném Azure následující služby:

| Počet | Prostředek                                     | SKU/velikost      | Používá se pro |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Použití plně spravované služby orchestrace kontejnerů Kubernetes, výchozí nastavení je 3 agenti|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – úroveň Standard | Správa zařízení, příkaz a řízení |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Ukládání konfiguračních dat a telemetrie zařízení, jako jsou pravidla, výstrahy a zprávy |
| 5     | [Účty Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)    | Standard        | 4 pro úložiště virtuálních počítačů a 1 pro kontrolní body streamování |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Application Gateway přes TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit a zabezpečení uživatelů |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení umístění prostředků |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýz v reálném čase |
| 1     | [Služba Azure Device Provisioning](../iot-dps/index.yml)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro data zpráv a umožnění hloubkové analýzy telemetrie podrobně |

> [!NOTE]
> Informace o cenách pro tyto služby najdete na adrese [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing) . Podrobnosti o využití a fakturaci pro vaše předplatné najdete na webu [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Nasazení akcelerátoru řešení

Příklady nasazení:

### <a name="example-deploy-net-version"></a>Příklad: nasazení verze .NET

Následující příklad ukazuje, jak nasadit základní verzi rozhraní .NET akcelerátoru řešení vzdáleného monitorování:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Příklad: nasazení verze Java

Následující příklad ukazuje, jak nasadit standardní verzi Java akcelerátoru řešení vzdáleného monitorování:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>možnosti příkazů pro počítače

Po spuštění `pcs` příkazu k nasazení řešení budete požádáni o:

- Název vašeho řešení. Tento název musí být jedinečný.
- Předplatné Azure, které se má použít.
- Umístění.
- Přihlašovací údaje pro virtuální počítače, které jsou hostiteli mikroslužeb. Tyto přihlašovací údaje můžete použít pro přístup k virtuálním počítačům pro řešení potíží.

Až se `pcs` příkaz dokončí, zobrazí se adresa URL nového akcelerátoru řešení. `pcs`Příkaz také vytvoří soubor `{deployment-name}-output.json` , který obsahuje informace, jako je název IoT Hub, který vytvořil.

Další informace o parametrech příkazového řádku získáte spuštěním příkazu:

```cmd/sh
pcs -h
```

Další informace o rozhraní příkazového řádku najdete v tématu [Jak používat rozhraní](https://github.com/Azure/pcs-cli/blob/master/README.md)příkazového řádku.

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte:

> [!div class="checklist"]
> * Konfigurace akcelerátoru řešení
> * Nasazení akcelerátoru řešení
> * Přihlaste se k akcelerátoru řešení.

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumat možnosti řídicího panelu řešení](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->