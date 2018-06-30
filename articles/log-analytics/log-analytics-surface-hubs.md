---
title: Monitorování Surface Hubů s Azure Log Analytics | Microsoft Docs
description: Použijte Surface Hub řešení ke sledování stavu Surface Huby a pochopit, jak se právě používají.
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
ms.component: na
ms.openlocfilehash: dfbcdce293d6d47267892487d0760410665af94a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130766"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorování Surface Huby se analýzy protokolů sledovat jejich stav

![Surface Hub – symbol](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Tento článek popisuje, jak použít řešení Surface Hub v analýzy protokolů pro monitorování zařízení Microsoft Surface Hub. Analýzy protokolů vám pomůže sledovat stav Surface Huby stejně jako pochopit, jak se právě používají.

Každý Surface Hub má Microsoft Monitoring Agent nainstalována. Jeho prostřednictvím, může odesílat data z Surface Hub k analýze protokolů agenta. Soubory protokolu se načítají z Surface Huby a jsou pak odešlou k analýze protokolů. Problémy, jako je offline, servery kalendáře nesynchronizuje, nebo pokud účet zařízení se nemůže přihlásit do Skype se zobrazují na řídicím panelu Surface Hub v analýzy protokolů. Pomocí dat v řídicím panelu můžete identifikovat zařízení, která neběží nebo které jsou s jiné problémy a potenciálně použít opravy zjištěných problémů.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace. Abyste mohli spravovat vaše Surface Huby v analýzy protokolů, budete potřebovat následující:

* A [analýzy protokolů předplatné](https://azure.microsoft.com/pricing/details/log-analytics/) úroveň, která bude podporovat počet zařízení, které chcete monitorovat. Log Analytics ceny se liší v závislosti na tom, kolik zařízení jsou zaregistrovaná a kolik dat se procesy. Je potřeba vzít v úvahu při plánování zavádění řešení Surface Hub.

Dále můžete se přidat existujícímu pracovnímu prostoru analýzy protokolů nebo vytvořte novou. Podrobné pokyny pro použití metody je v [začít pracovat s analýzy protokolů](log-analytics-get-started.md). Po nakonfigurování pracovní prostor analýzy protokolů existují dva způsoby, jak zaregistrovat svoje zařízení Surface Hub:

* Automaticky prostřednictvím služby Intune
* Ručně pomocí **nastavení** na vašem zařízení Surface Hub.

## <a name="set-up-monitoring"></a>Nastavte monitorování
Můžete sledovat stav a aktivitu vaší Surface Hub pomocí analýzy protokolů. Surface Hub můžete zaregistrovat pomocí Intune nebo místně pomocí **nastavení** na Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Připojení Surface Hubů k analýze protokolů prostřednictvím služby Intune
Pro pracovní prostor analýzy protokolů, která bude spravovat Surface Huby budete potřebovat ID pracovního prostoru a klíč pracovního prostoru. Ty z nastavení pracovního prostoru můžete získat na portálu Azure.

Intune je produkt společnosti Microsoft, která umožňuje centrálně spravovat nastavení konfigurace analýzy protokolů, které se použijí na jeden nebo více zařízení. Postupujte podle těchto kroků pro konfiguraci zařízení prostřednictvím služby Intune:

1. Přihlášení k Intune.
2. Přejděte na **nastavení** > **připojené zdroje**.
3. Vytvořte nebo upravte zásadu na základě šablony Surface Hub.
4. Přejděte do části OMS (Statistika provozu Azure) zásad a přidejte analýzy protokolů *ID pracovního prostoru* a *klíč pracovního prostoru* zásad.
5. Zásadu uložte.
6. Přidružení zásad k příslušné skupiny zařízení.

   ![Zásady Intune](./media/log-analytics-surface-hubs/intune.png)

Intune pak synchronizuje nastavení analýzy protokolů zařízení v cílové skupině, je zaregistrujete v pracovním prostoru analýzy protokolů.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Surface Huby se připojit k analýze protokolů pomocí nastavení aplikace
Pro pracovní prostor analýzy protokolů, která bude spravovat Surface Huby budete potřebovat ID pracovního prostoru a klíč pracovního prostoru. Můžete získat z nastavení pro pracovní prostor analýzy protokolů na portálu Azure.

Pokud nepoužíváte Intune ke správě prostředí, můžete zaregistrovat zařízení ručně pomocí **nastavení** na každý Surface Hub:

1. Surface Hub, otevřete **nastavení**.
2. Zadejte pověření správce zařízení po zobrazení výzvy.
3. Klikněte na tlačítko **toto zařízení**a v části **monitorování**, klikněte na tlačítko **konfigurovat nastavení OMS**.
4. Vyberte **povolit sledování**.
5. V dialogovém okně Nastavení OMS zadejte analýzy protokolů **ID pracovního prostoru** a zadejte **klíč pracovního prostoru**.  
   ![Nastavení](./media/log-analytics-surface-hubs/settings.png)
6. Klikněte na tlačítko **OK** k dokončení konfigurace.

Zobrazení potvrzení o tom, zda bylo úspěšně aplikováno konfigurace do zařízení. Pokud byl, zobrazí se zpráva s oznámením, že agent úspěšně připojen k analýze protokolů. Zařízení pak spustí odesílání dat k analýze protokolů, kde můžete zobrazit a pracovat.

## <a name="monitor-surface-hubs"></a>Monitorování Surface Hubů
Monitorování Surface Huby pomocí analýzy protokolů je velmi podobný monitorování jiných zaregistrovaných zařízení.

1. Přihlaste se k portálu Azure.
2. Přejděte do pracovního prostoru analýzy protokolů a vyberte **přehled**.
2. Klikněte na dlaždici Surface Hub.
3. Zobrazí se stav vašeho zařízení.

   ![Surface Hub řídicí panel](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Můžete vytvořit [výstrahy](log-analytics-alerts.md) založené na protokolu hledání existujících nebo vlastních. Pomocí dat, která shromažďuje analýzy protokolů z Surface Huby, můžete vyhledat problémy a upozornění na podmínky, které definujete pro vaše zařízení.

## <a name="next-steps"></a>Další postup
* Použití [hledání přihlásit analýzy protokolů](log-analytics-log-searches.md) na podrobnější data Surface Hub.
* Vytvoření [výstrahy](log-analytics-alerts.md) upozornění v případě problémů, ke kterým dochází u Surface Huby.
