---
title: Diagnostika a řešení potíží s odpojením s Azure IoT Hub DPS
description: Naučte se diagnostikovat a řešit běžné chyby pomocí připojení zařízení pro službu Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646468"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Řešení potíží se službou Azure IoT Hub Deviceprovisioning Service

Problémy s připojením pro zařízení IoT může být obtížné řešit, protože existuje mnoho možných bodů selhání, jako jsou selhání atestace, selhání registrace atd. Tento článek obsahuje pokyny, jak zjistit a řešit problémy s připojením zařízení prostřednictvím [služby Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Použití Azure Monitoru k zobrazení metrik a nastavení výstrah

Následující postup popisuje, jak zobrazit a nastavit výstrahu na metriku služby zřizování zařízení služby IoT Hub. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Přejděte na službu zřizování zařízení služby IoT Hub.

3. Vyberte **Metriky**.

4. Vyberte požadovanou metriku. 
   <br />V současné době existují tři metriky pro DPS:

    | Název metriky | Popis |
    |-------|------------|
    | Pokusy o atestaci | Počet zařízení, která se pokusila ověřit pomocí služby Device Provisioning Service|
    | Pokusy o registraci | Počet zařízení, která se pokusila zaregistrovat do služby IoT Hub po úspěšném ověření|
    | Přiřazené zařízení | Počet zařízení, která byla úspěšně přiřazena k centru IoT Hub|

5. Vyberte požadovanou metodu agregace a vytvořte vizuální zobrazení metriky. 

6. Chcete-li nastavit upozornění na metriku, vyberte **nová pravidla výstrah** v pravém horním rohu okna metriky, podobně můžete přejít na okno **Výstrahy** a vybrat **nová pravidla výstrah**.

7. Vyberte **Přidat podmínku**, pak vyberte požadovanou metriku a prahovou hodnotu pomocí následujících výzev.

Další informace najdete v tématu [Co jsou klasické výstrahy v Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Použití analýzy protokolů k zobrazení a vyřešení chyb

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Přejděte do svého centra IoT hub.

3. Vyberte **Nastavení diagnostiky**.

4. Vyberte **možnost Zapnout diagnostiku**.

5. Povolte požadované protokoly, které mají být shromažďovány.

    | Název protokolu | Popis |
    |-------|------------|
    | Provoz zařízení | Protokoly související s událostmi připojení zařízení |
    | ServiceOperations | Protokoly událostí související s používáním sady SDK služby (např. vytváření nebo aktualizace skupin zápisu)|

6. Zapněte **odeslat do analýzy protokolů** [(viz ceny).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Přejděte na kartu **Protokoly** na webu Azure Portal v části Prostředek služby zřizování zařízení.

8. Chcete-li zobrazit nedávné události, klepněte na tlačítko **Spustit.**

9. Pokud existují výsledky, `OperationName`vyhledejte `ResultSignature`, `ResultDescription` `ResultType`, a (chybová zpráva) získat další podrobnosti o chybě.


## <a name="common-error-codes"></a>Běžné kódy chyb
V této tabulce můžete porozumět běžným chybám a vyřešit je.

| Kód chyby| Popis | Stavový kód HTTP |
|-------|------------|------------|
| 400 | Tělo požadavku není platné. například nelze analyzovat nebo objekt nelze ověřit.| 400 Chybný formát |
| 401 | Autorizační token nelze ověřit. například vypršela nebo se nevztahuje na identifikátor URI požadavku požadavku. Tento kód chyby je také vrácen a tok emitačního systému Čipu TPM. | 401 Neautorizováno|
| 404 | Instance device provisioning service nebo prostředek (např. registrace) neexistuje. |404 Nenalezeno |
| 412 | ETag v požadavku neodpovídá ETag existujícího prostředku podle RFC7232. | 412 Předběžná podmínka se nezdařila. |
| 429 | Operace jsou omezeny službou. Konkrétní omezení služeb najdete v tématu [Limity služby Zřizování zařízení služby IoT Hub](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Příliš mnoho požadavků |
| 500 | Došlo k vnitřní chybě. | 500 – Interní chyba serveru|
