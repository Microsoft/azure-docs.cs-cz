---
title: Diagnostika a řešení potíží s odpojením Azure IoT Hub DPS
description: Naučte se diagnostikovat a řešit běžné chyby pomocí připojení zařízení pro Azure IoT Hub Device Provisioning Service (DPS).
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: ba81be54592e591f734b05235bb3ce183a07a11c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709882"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Řešení potíží s Azure IoT Hub Device Provisioning Service

Problémy s připojením pro zařízení IoT můžou být obtížné řešit, protože existuje mnoho možných bodů selhání, například selhání ověření identity, selhání registrace atd. Tento článek poskytuje pokyny k detekci a řešení potíží s připojením zařízení prostřednictvím [Azure monitor](../azure-monitor/overview.md).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Použití Azure Monitor k zobrazení metrik a nastavení výstrah

Následující postup popisuje, jak zobrazit a nastavit výstrahu pro IoT Hub Device Provisioning Service metriky. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Přejděte na IoT Hub Device Provisioning Service.

3. Vyberte **Metriky**.

4. Vyberte požadovanou metriku. 
   <br />V současné době existují tři metriky pro DPS:

    | Název metriky | Popis |
    |-------|------------|
    | Pokusy o ověření identity | Počet zařízení, která se pokusila ověřit pomocí služby Device Provisioning Service|
    | Pokusy o registraci | Počet zařízení, která se pokusila o registraci IoT Hub po úspěšném ověření|
    | Přiřazené zařízení | Počet zařízení, která byla úspěšně přiřazena IoT Hub|

5. Vyberte požadovanou metodu agregace pro vytvoření vizuálního zobrazení metriky. 

6. Pokud chcete nastavit výstrahu metriky, vyberte **Nová pravidla upozornění** v pravém horním rohu okna metrika, podobně můžete přejít na okno **výstrah** a vybrat **Nová pravidla upozornění**.

7. Vyberte **Přidat podmínku** a pak podle následujících pokynů vyberte požadovanou metriku a prahovou hodnotu.

Další informace najdete v tématu [výstrahy v Azure monitor](../azure-monitor/platform/alerts-overview.md).

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Zobrazení a řešení chyb pomocí analytického protokolu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT Hub.

3. Vyberte **nastavení diagnostiky**.

4. Vyberte **zapnout diagnostiku**.

5. Povolte shromažďování požadovaných protokolů.

    | Název protokolu | Popis |
    |-------|------------|
    | DeviceOperations | Protokoly související s událostmi připojení zařízení |
    | ServiceOperations | Protokoly událostí související s používáním sady SDK služby (např. vytvoření nebo aktualizace skupin registrací)|

6. Zapnout **odesílání na Log Analytics** ([Viz ceny](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. V Azure Portal v části prostředek služby Device Provisioning Service přejít na kartu **protokoly** .

8. Kliknutím na tlačítko **Spustit** zobrazíte nedávné události.

9. Pokud jsou k dispozici výsledky, vyhledejte `OperationName` ,, `ResultType` `ResultSignature` a `ResultDescription` (chybová zpráva), abyste získali více podrobností o chybě.


## <a name="common-error-codes"></a>Běžné kódy chyb
Pomocí této tabulky můžete pochopit a řešit běžné chyby.

| Kód chyby| Popis | Stavový kód HTTP |
|-------|------------|------------|
| 400 | Tělo požadavku není platné. nelze jej například analyzovat nebo objekt nelze ověřit.| 400 Špatný formát |
| 401 | Autorizační token se nedá ověřit. například vypršela platnost nebo se nevztahuje na identifikátor URI žádosti. Tento kód chyby se také vrátí do zařízení jako součást toku ověření čipem TPM. | 401 – Neautorizováno|
| 404 | Instance služby Device Provisioning nebo prostředek (například registrace) neexistuje. |404 Nenalezeno |
| 412 | Značka ETag v žádosti se neshoduje s ETagm existujícího prostředku, jak na RFC7232. | Předběžná podmínka 412 se nezdařila |
| 429 | Služba omezuje operace. Omezení pro konkrétní služby najdete v tématu [omezení IoT Hub Device Provisioning Service](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | 429 příliš mnoho požadavků |
| 500 | Došlo k vnitřní chybě. | 500 – Vnitřní chyba serveru|