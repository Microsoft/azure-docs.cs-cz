---
title: Monitorování rozbočovačů Surface s Azure Monitorem | Dokumenty společnosti Microsoft
description: Pomocí řešení Surface Hub můžete sledovat stav rozbočovačů Surface Hubs a pochopit, jak se používají.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7999735bf2d182b2811d01172adcfc89cba27dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662497"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Sledování surface hubů pomocí Azure Monitoru za účelem sledování jejich stavu

![Symbol rozboje povrchu](./media/surface-hubs/surface-hub-symbol.png)

Tento článek popisuje, jak můžete použít řešení Surface Hub v Azure Monitoru ke sledování zařízení Microsoft Surface Hub. Řešení vám pomůže sledovat stav vašich surface hubů a také pochopit, jak se používají.

Každý Surface Hub má nainstalovanou Microsoft Monitoring Agent. Jeho prostřednictvím agenta, který můžete odesílat data z surface hubu do pracovního prostoru Log Analytics v Azure Monitoru. Soubory protokolu se načtou z vašich surface hubů a pak se odesílají do Azure Monitoru. Problémy, jako jsou servery offline, kalendář není synchronizován, nebo pokud účet zařízení není schopen se přihlásit do Skype se zobrazí v řídicím panelu Surface Hub v Azure Monitoru. Pomocí dat v řídicím panelu můžete identifikovat zařízení, která nejsou spuštěna nebo která mají jiné problémy, a potenciálně použít opravy zjištěných problémů.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace. Abyste mohli spravovat Surface Huby ve Službě Azure Monitor, budete potřebovat následující:

* Úroveň [předplatného Log Analytics,](https://azure.microsoft.com/pricing/details/log-analytics/) která bude podporovat počet zařízení, která chcete sledovat. Ceny Log Analytics se liší v závislosti na tom, kolik zařízení je zaregistrovaných a kolik dat zpracovává. Budete to chtít vzít v úvahu při plánování zavádění Surface Hubu.

Dále přidáte existující pracovní prostor Log Analytics nebo vytvoříte nový. Podrobné pokyny pro použití obou metod je na [vytvoření pracovního prostoru Log Analytics na webu Azure Portal](../learn/quick-create-workspace.md). Jakmile je pracovní prostor Log Analytics nakonfigurován, existují dva způsoby, jak zaregistrovat zařízení Surface Hub:

* Automaticky přes Intune
* Ručně prostřednictvím **nastavení** na zařízení Surface Hub.

## <a name="set-up-monitoring"></a>Nastavení monitorování
Můžete sledovat stav a aktivitu vašeho Surface Hub pomocí Azure Monitor. Surface Hub můžete zaregistrovat pomocí Intune nebo místně pomocí **Nastavení** na Surface Hubu.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Připojení surface hubů k Azure Monitoru přes Intune
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro pracovní prostor Log Analytics, který bude spravovat vaše Surface Hubs. Můžete je získat z nastavení pracovního prostoru na webu Azure Portal.

Intune je produkt společnosti Microsoft, který umožňuje centrální správu nastavení konfigurace pracovního prostoru Analýzy protokolů, která se použijí na jedno nebo více vašich zařízení. Podle těchto kroků nakonfigurujte zařízení prostřednictvím Intune:

1. Přihlaste se k Intune.
2. Přejděte do **nastavení** > **připojených zdrojů**.
3. Vytvořte nebo upravte zásadu na základě šablony Surface Hub.
4. Přejděte do části zásady Azure Operational Insights a přidejte do zásad *ID pracovního prostoru* analýzy protokolů a *klíč pracovního prostoru.*
5. Uložte zásady.
6. Přidružte zásadu k příslušné skupině zařízení.

   ![Zásady Intune](./media/surface-hubs/intune.png)

Intune pak synchronizuje nastavení Log Analytics se zařízeními v cílové skupině a zaregistruje je do pracovního prostoru Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Připojení surface hubů k Azure Monitoru pomocí aplikace Nastavení
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro pracovní prostor Log Analytics, který bude spravovat vaše Surface Hubs. Můžete je získat z nastavení pracovního prostoru Log Analytics na webu Azure Portal.

Pokud ke správě prostředí nepoužíváte Intune, můžete zařízení zaregistrovat ručně prostřednictvím **nastavení** na každém Surface Hubu:

1. V surface hubu otevřete **Nastavení**.
2. Po zobrazení výzvy zadejte přihlašovací údaje správce zařízení.
3. Klepněte na **toto zařízení**a v části **Sledování**klepněte na **tlačítko Konfigurovat nastavení analýzy protokolů**.
4. Vyberte **možnost Povolit monitorování**.
5. V dialogovém okně Nastavení analýzy protokolů zadejte **ID pracovního prostoru** Analýzy protokolů a **zadejte klíč pracovního prostoru**.  
   ![Nastavení](./media/surface-hubs/settings.png)
6. Chcete-li dokončit konfiguraci, klepněte na tlačítko **OK.**

Zobrazí se potvrzení, které vás oznamuje, zda byla konfigurace v zařízení úspěšně použita. Pokud tomu tak bylo, zobrazí se zpráva, že agent úspěšně připojen k Azure Monitor. Zařízení pak začne odesílat data do Azure Monitoru, kde můžete zobrazit a jednat na něj.

## <a name="monitor-surface-hubs"></a>Monitorovat rozbočovače povrchů
Sledování surface hubů pomocí Azure Monitoru je podobné sledování všech ostatních zaregistrovaných zařízení.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Když kliknete na dlaždici Surface Hub, zobrazí se stav vašeho zařízení.

   ![Řídicí panel Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Výstrahy [alerts](../platform/alerts-overview.md) můžete vytvářet na základě existujících nebo vlastních vyhledávání v protokolu. Pomocí dat, která Azure Monitor shromažďuje z vašich Surface Hubs, můžete vyhledávat problémy a upozorňovat na podmínky, které definujete pro vaše zařízení.

## <a name="next-steps"></a>Další kroky
* Pomocí [dotazů protokolu v Azure Monitoru](../log-query/log-query-overview.md) zobrazte podrobná data Surface Hubu.
* Vytvořte [upozornění,](../platform/alerts-overview.md) která vás upozorní na problémy s povrchovými rozbočovači.
