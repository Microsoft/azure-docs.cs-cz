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
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383058"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Nasazení akcelerátoru řešení vzdáleného monitorování pomocí rozhraní příkazového řádku

V této příručce se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování. Řešení nasadíte pomocí rozhraní příkazového řádku. Řešení můžete nasadit také pomocí webového uživatelského rozhraní na adrese azureiotsolutions.com. Další informace o této možnosti najdete v tématu [nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md) .

## <a name="prerequisites"></a>Předpoklady

K nasazení akcelerátoru řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

Chcete-li spustit rozhraní příkazového řádku, musíte mít v místním počítači nainstalovaný [Node. js](https://nodejs.org/) .

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
| Skladová jednotka (SKU)    | `basic`, `standard`, `local` | _Základní_ nasazení je určené pro testování a ukázky, nasadí všechny mikroslužby na jeden virtuální počítač. _Standardní_ nasazení je určené pro produkční prostředí, nasadí mikroslužby na několik virtuálních počítačů. _Místní_ nasazení nakonfiguruje kontejner Docker tak, aby spouštěl mikroslužby na místním počítači a používal Azure Cloud Services, jako je například storage a Cosmos DB. |
| Modul runtime | `dotnet`, `java` | Vybere jazykovou implementaci mikroslužeb. |

Informace o tom, jak používat místní možnost nasazení, najdete v části [spuštění řešení vzdáleného monitorování místně](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Základní a standardní nasazení

V této části najdete přehled klíčových rozdílů mezi základním a standardním nasazením.

### <a name="basic"></a>Basic

Můžete provést základní nasazení z [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) nebo pomocí rozhraní příkazového řádku.

Nasazení Basic slouží k předvádění řešení. Za účelem snížení nákladů jsou všechny mikroslužby nasazeny na jednom virtuálním počítači. Toto nasazení nepoužívá architekturu připravenou pro produkční prostředí.

Základní nasazení ve vašem předplatném Azure vytvoří následující služby:

| Počet | Prostředek                       | Typ         | Používá se pro |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuální počítač se systémem Linux](https://azure.microsoft.com/services/virtual-machines/) | D1 v2 úrovně Standard  | Hostování mikroslužeb |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – úroveň Standard | Správa zařízení a komunikace |
| 1     | [Databáze Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Ukládání konfiguračních dat, pravidel, upozornění a dalších chladírenských úložišť |  
| 1     | [Účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Úložiště pro kontrolní body virtuálního počítače a streamování |
| 1     | [Webová aplikace](https://azure.microsoft.com/services/app-service/web/)        |                 | Hostování front-endové webové aplikace |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit a zabezpečení uživatelů |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení umístění prostředků |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýz v reálném čase |
| 1     | [Služba Azure Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro data zpráv a umožnění hloubkové analýzy telemetrie podrobně |

### <a name="standard"></a>Standard

Standardní nasazení můžete provést pouze pomocí rozhraní příkazového řádku.

Standardní nasazení je nasazení připravené pro produkční prostředí, které může vývojář přizpůsobit a zvětšit. Možnost standardního nasazení použijte, když jste připraveni přizpůsobit architekturu připravenou pro produkční prostředí vytvořenou pro škálování a rozšiřitelnost. Mikroslužby aplikací jsou sestavené jako kontejnery Docker a nasazeny pomocí služby Azure Kubernetes. Kubernetes Orchestrator nasazuje, škáluje a spravuje mikroslužby.

Standardní nasazení vytvoří v předplatném Azure následující služby:

| Počet | Prostředek                                     | SKU/velikost      | Používá se pro |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Použití plně spravované služby orchestrace kontejnerů Kubernetes, výchozí nastavení je 3 agenti|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – úroveň Standard | Správa zařízení, příkaz a řízení |
| 1     | [Databáze Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Ukládání konfiguračních dat a telemetrie zařízení, jako jsou pravidla, výstrahy a zprávy |
| 5     | [Účty Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 pro úložiště virtuálních počítačů a 1 pro kontrolní body streamování |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Application Gateway přes SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit a zabezpečení uživatelů |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení umístění prostředků |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýz v reálném čase |
| 1     | [Služba Azure Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro data zpráv a umožnění hloubkové analýzy telemetrie podrobně |

> [!NOTE]
> Informace o cenách pro tyto služby najdete na [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). Podrobnosti o využití a fakturaci pro vaše předplatné najdete na webu [Azure Portal](https://portal.azure.com/).

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

Když spustíte příkaz `pcs` k nasazení řešení, budete požádáni o:

- Název vašeho řešení. Tento název musí být jedinečný.
- Předplatné Azure, které se má použít.
- Umístění.
- Přihlašovací údaje pro virtuální počítače, které jsou hostiteli mikroslužeb. Tyto přihlašovací údaje můžete použít pro přístup k virtuálním počítačům pro řešení potíží.

Až se příkaz `pcs` dokončí, zobrazí se adresa URL nového akcelerátoru řešení. Příkaz `pcs` také vytvoří soubor `{deployment-name}-output.json` obsahující informace, jako je název IoT Hub, který byl vytvořen.

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
