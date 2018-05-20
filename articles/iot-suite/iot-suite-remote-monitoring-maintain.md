---
title: Řešení potíží s zařízení v řešení vzdáleného monitorování - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak k řešení potíží a opravám problémů zařízení v řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 99cf9e341edf0acee434e2d01f6346291cbcea5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Řešení potíží a opravám problémů zařízení

V tomto kurzu se dozvíte, jak používat **údržby** stránky v řešení k řešení potíží a opravám problémů zařízení. Zavádět tyto možnosti, tento kurz používá scénáři v aplikaci Contoso IoT.

Contoso je testování novou **prototypu** zařízení v poli. Jako operátor Contoso, si všimnete během testování, které **prototypu** zařízení je aktivován neočekávaně teploty výstrahy na řídicím panelu. Teď musíte prozkoumat chování tento vadný **prototypu** zařízení.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Použití **údržby** stránky k prozkoumání výstrahy
> * Volání metody zařízení k nápravě problému

## <a name="prerequisites"></a>Požadavky

Chcete-li v tomto kurzu, je třeba nasazené instanci řešení vzdáleného monitorování ve vašem předplatném Azure.

Pokud jste nenasadili řešení vzdáleného monitorování ještě by se měla Dokončit [nasazení vzdálené monitorování akcelerátoru řešení](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) kurzu.

## <a name="use-the-maintenance-dashboard"></a>Řídicí panel údržby

Na **řídicí panel** stránky zjistíte, že existují neočekávané teploty výstrahy, které pocházejí z přidruženo pravidlo **prototypu** zařízení:

![Výstrahy zobrazuje na řídicím panelu](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Chcete-li prozkoumat další potíže, zvolte **prozkoumat upozornění** možnost vedle výstrahy:

![Prozkoumat upozornění z řídicího panelu](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Zobrazení podrobností výstrahy se zobrazuje:

* Kdy byla výstraha
* Informace o zařízení spojených s výstrahou stavu
* Telemetrická data ze zařízení spojených s výstrahou

![Podrobnosti upozornění](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Aby vzali na vědomí výstrahu, vyberte **výstrahy výskytů** a zvolte **potvrzení**. Tato akce umožní dalšími operátory viděli jste, výstrahy a jsou na něm pracovat.

![Potvrdit výstrahy](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

Po potvrzení výstrahy, stav výskytu změní na **potvrzeno**.

V seznamu se zobrazí **prototypu** zodpovědná za aktivuje výstraha teploty zařízení:

![Vytvořte seznam zařízení, která způsobila výstrahu](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Opravte problém

Oprava problému s **prototypu** zařízení, je třeba volat **DecreaseTemperature** metoda v zařízení.

Tak, aby fungoval na zařízení, vyberte ho v seznamu zařízení a potom zvolte **úlohy**. **Prototypu** model zařízení určuje šesti metody musí podporovat zařízení:

![Zobrazení metod, které podporuje zařízení](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Zvolte **DecreaseTemperature** a nastavte název úlohy na **DecreaseTemperature**. Zvolte **použít**:

![Vytvoření úlohy snížení teplota](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Chcete-li sledovat stav úlohy na **údržby** vyberte **úlohy**. Použití **úlohy** zobrazení sledovat všechny úlohy a metoda volá v řešení:

![Monitorování úlohy snížení teplota](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Chcete-li zobrazit podrobnosti o konkrétní úlohy nebo volání metody, vyberte v seznamu **úlohy** zobrazení:

![Zobrazení podrobností o úloze](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste viděli, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Použití **údržby** stránky k prozkoumání výstrahy
> * Volání metody zařízení k nápravě problému

Nyní jste se naučili jak spravovat zařízení problémy, navrhované dalším krokem je další postup [testování řešení s Simulovaná zařízení](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->