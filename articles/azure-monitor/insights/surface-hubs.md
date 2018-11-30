---
title: Monitorování zařízení Surface hub s využitím Azure Log Analytics | Dokumentace Microsoftu
description: Zařízení Surface Hub řešení použijte ke sledování stavu Surface Huby a pochopit, jak se používají.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 761140b1022f5a1c1cd523c6e0e52193ff2a9700
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428561"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorování pomocí Log Analytics ke sledování jejich stavu Surface Huby

![Zařízení Surface Hub symbol](./media/surface-hubs/surface-hub-symbol.png)

Tento článek popisuje, jak můžete zařízení Surface Hub řešení v Log Analytics k monitorování zařízení Microsoft Surface Hub. Log Analytics umožňuje sledovat stav Surface Huby dobře porozumět, jak se používají.

Každé zařízení Surface Hub je nainstalovaný Agent sledování Microsoft. Jeho prostřednictvím agenta, že můžete data odesílat do Log Analytics z Surface Hub. Soubory protokolu se načítají z Surface hub a jsou pak odesílají do Log Analytics. Problémy, jako jsou servery, je offline, kalendář nesynchronizuje, nebo pokud účet zařízení se nemůže přihlásit do Skypu jsou zobrazené v řídicím panelu zařízení Surface Hub ve službě Log Analytics. Pomocí dat na řídicím panelu můžete identifikovat zařízení, která neběží, nebo jsou potíže a potenciálně použít opravy pro zjištěné problémy.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace. Pokud chcete spravovat vaše Surface Huby v Log Analytics, budete potřebovat následující:

* A [předplatné Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) úroveň, která bude podporovat víc zařízení, které chcete monitorovat. Log Analytics ceny se liší v závislosti na tom, kolik zařízení jsou zaregistrovaná a kolik dat se procesy. Bude potřeba vzít v úvahu při plánování zavádění řešení Surface Hub.

V dalším kroku vám bude přidat existující pracovní prostor Log Analytics nebo vytvořte novou. Podrobný návod, jak pomocí některé z metod je na [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../../log-analytics/log-analytics-quick-create-workspace.md). Po nakonfigurování pracovní prostor Log Analytics existují dva způsoby, jak zaregistrovat svoje zařízení Surface Hub:

* Automaticky pomocí Intune
* Ručně pomocí **nastavení** na vašem zařízení Surface Hub.

## <a name="set-up-monitoring"></a>Nastavte monitorování
Můžete monitorovat stav a aktivitu Surface hubu pomocí Log Analytics. Surface Hubu můžete registrovat pomocí Intune nebo místně s použitím **nastavení** na Surface Hubu.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Připojit zařízení Surface hub ke službě Log Analytics prostřednictvím služby Intune
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro pracovní prostor Log Analytics, která bude spravovat Surface Huby. Můžete dosáhnout z nastavení pracovního prostoru na webu Azure Portal.

Intune je produkt Microsoftu, která umožňuje centrálně spravovat nastavení konfigurace Log Analytics, která se použijí pro jeden nebo více zařízení. Postupujte podle těchto kroků a nakonfigurujte zařízení přes Intune:

1. Přihlášení k Intune.
2. Přejděte do **nastavení** > **připojené zdroje**.
3. Vytvořte nebo upravte zásady na základě šablony Surface Hub.
4. Přejděte do části Azure Operational Insights zásad a přidejte Log Analytics *ID pracovního prostoru* a *klíč pracovního prostoru* k zásadám.
5. Uložte zásadu.
6. Přidružení zásad příslušné skupiny zařízení.

   ![Zásady Intune](./media/surface-hubs/intune.png)

Intune pak synchronizuje nastavení Log Analytics se zařízeními v cílové skupině, registrace ve vašem pracovním prostoru Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Surface Huby se připojit ke službě Log Analytics pomocí nastavení aplikace
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro pracovní prostor Log Analytics, která bude spravovat Surface Huby. Můžete dosáhnout na základě nastavení pracovního prostoru Log Analytics na portálu Azure portal.

Pokud nepoužíváte Intune ke správě prostředí, abyste mohli registrovat zařízení ručně přes **nastavení** na každé zařízení Surface Hub:

1. Na Surface Hubu, otevřete **nastavení**.
2. Zadejte pověření správce zařízení po zobrazení výzvy.
3. Klikněte na tlačítko **toto zařízení**a v části **monitorování**, klikněte na tlačítko **nakonfigurovat nastavení analýzy protokolů**.
4. Vyberte **povolit monitorování**.
5. V dialogovém okně Nastavení Log Analytics, zadejte Log Analytics **ID pracovního prostoru** a zadejte **klíč pracovního prostoru**.  
   ![Nastavení](./media/surface-hubs/settings.png)
6. Klikněte na tlačítko **OK** a dokončete tak konfiguraci.

Zobrazí se potvrzení se o tom, jestli konfigurace úspěšně použila na zařízení. Pokud ano, zobrazí se zpráva s informacemi o tom, že agent se úspěšně připojil ke službě Log Analytics. Zařízení spustí odesílání dat do služby Log Analytics, kde můžete zobrazit a reagovat na něj.

## <a name="monitor-surface-hubs"></a>Monitorování zařízení Surface hub
Surface hub monitorování pomocí Log Analytics je podobné jako u monitorování jiných zaregistrovaných zařízení.

1. Přihlaste se k portálu Azure.
2. Přejděte do pracovního prostoru Log Analytics a vyberte **přehled**.
2. Kliknutím na dlaždici zařízení Surface Hub.
3. Zobrazí se stav vašeho zařízení.

   ![Řídicí panel zařízení Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Můžete vytvořit [výstrahy](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) založené na existující nebo vlastní prohledávání protokolů. Pomocí data, která Log Analytics shromažďuje z Surface hub, můžete vyhledat problémy a upozornění na podmínky, které definujete pro vaše zařízení.

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů v Log Analytics](../../log-analytics/log-analytics-queries.md) k zobrazení podrobných dat o Surface Hub.
* Vytvoření [výstrahy](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) které vás upozorní, když dojde k problémům s Surface Huby.
