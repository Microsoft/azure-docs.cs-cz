---
title: Azure DDoS Protection testování simulace
description: Informace o tom, jak testovat pomocí simulací
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905262"
---
# <a name="test-through-simulations"></a>Testování prostřednictvím simulací

Je dobrým zvykem otestovat vaše předpoklady, jak budou vaše služby reagovat na útok díky provádění pravidelných simulací. Během testování ověřte, že vaše služby nebo aplikace nadále fungují podle očekávání a že nedochází k přerušení činnosti koncového uživatele. Identifikujte mezery z hlediska technologie a procesu a zahrňte je do strategie DDoS Response. Tyto testy doporučujeme provádět v přípravném prostředí nebo během období mimo špičku, abyste minimalizovali dopad na produkční prostředí.

Spolupracujeme s [cloudem BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud), samoobslužným generátorem provozu, který vytvoří rozhraní, ve kterém můžou zákazníci Azure vygenerovat provoz z veřejných koncových bodů s povoleným DDoS Protection pro simulace. Simulaci můžete použít k těmto akcím:

- Ověřte, jak Azure DDoS Protection pomáhá chránit vaše prostředky Azure před útoky DDoS.
- Optimalizujte proces reakce na incidenty při útoku DDoS.
- DDoS dodržování předpisů v dokumentu.
- Vyškolte své týmy zabezpečení sítě.

## <a name="prerequisites"></a>Předpoklady

- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [Plán Standard Protection pro Azure DDoS](manage-ddos-protection.md) s chráněnými veřejnými IP adresami.
- Nejprve musíte vytvořit účet s [BreakingPoint cloudem](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Konfigurace útoku DDoS test

1. Zadejte nebo vyberte následující hodnoty a pak vyberte **Spustit test** :

    |Nastavení        |Hodnota                                              |
    |---------      |---------                                          |
    |Cílová IP adresa           | Zadejte jednu z vaší veřejné IP adresy, kterou chcete otestovat.                     |
    |Číslo portu   | Zadejte _443_ .                       |
    |Profil DDoS | Vyberte možnost **zaplavení TCP syn** .|
    |Velikost testu       | Vyberte **200 tisíc PPS, 100 MB/s a 8 zdrojovou IP adresou.**                                  |
    |Doba trvání testu | Vyberte **10 minut** .|

Teď by měl vypadat takto:

![Příklad simulace útoku DDoS: Cloud BreakingPoint](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorovat a ověřit

1. Přihlaste se ke https://portal.azure.com svému předplatnému a přejít na něj.
1. Vyberte veřejnou IP adresu, na které jste otestovali útok.
1. V části **Sledování** klikněte na **Metriky** .
1. V části **metrika** vyberte možnost _útok DDoS nebo ne_ .

Jakmile je prostředek napadený, měli byste vidět, že se hodnota mění z **0** na **1** , podobně jako na následujícím obrázku:

![Příklad simulace útoků DDoS: portál](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Zobrazit a nakonfigurovat telemetrii DDoS Protection](telemetry-monitoring-alerting.md).
- Přečtěte si, jak [nakonfigurovat sestavy zmírnění útoků DDoS a protokoly toků](reports-and-flow-logs.md).
- Naučte se, jak [zahájit DDoS rychlou odezvu](ddos-rapid-response.md).