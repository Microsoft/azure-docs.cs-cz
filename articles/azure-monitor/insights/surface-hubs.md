---
title: Monitorování Center Surface pomocí Azure Monitor | Microsoft Docs
description: Pomocí Surface Hub řešení můžete sledovat stav rozbočovačů Surface a pochopit, jak se používají.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7e3b0f92770b48ef5163846e67940efe80fb669a
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085344"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitorujte rozbočovače Surface Azure Monitor, abyste mohli sledovat jejich stav.

![Symbol Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

Tento článek popisuje, jak můžete pomocí řešení Surface Hub v Azure Monitor monitorovat zařízení Microsoft Surface Hub. Řešení vám pomůže sledovat stav vašich Surface Hub a pochopit, jak se používají.

Každý Surface Hub má Microsoft Monitoring Agent nainstalován. Prostřednictvím agenta můžete odesílat data z Surface Hub do pracovního prostoru Log Analytics v Azure Monitor. Soubory protokolu se čtou z vašich Surface Hub a pak se odešlou do Azure Monitor. Problémy jako servery, které jsou offline, se kalendář nesynchronizuje, nebo pokud se účet zařízení nemůže přihlásit ke Skypu, zobrazuje se na řídicím panelu Surface Hub v Azure Monitor. Pomocí dat na řídicím panelu můžete identifikovat zařízení, která neběží nebo která mají jiné problémy, a potenciálně použít opravy zjištěných problémů.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace. Aby bylo možné spravovat centra Surface v Azure Monitor, budete potřebovat následující:

* Úroveň [předplatného Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) , která bude podporovat počet zařízení, která chcete monitorovat. Ceny Log Analytics se liší v závislosti na tom, kolik zařízení je zaregistrované a kolik dat zpracovává. Tento postup je potřeba vzít v úvahu při plánování nasazení Surface Hub.

V dalším kroku přidáte existující Log Analytics pracovní prostor nebo vytvoříte nový. Podrobné pokyny pro použití obou metod najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../learn/quick-create-workspace.md). Po nakonfigurování Log Analytics pracovního prostoru existují dva způsoby, jak zaregistrovat Surface Hub zařízení:

* Automaticky prostřednictvím Intune
* Ručně prostřednictvím **Nastavení** na zařízení Surface Hub.

## <a name="set-up-monitoring"></a>Nastavení monitorování
Pomocí Azure Monitor můžete monitorovat stav a aktivitu Surface Hub. Surface Hub můžete zaregistrovat pomocí Intune nebo lokálně pomocí **Nastavení** v Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Propojení rozbočovačů Surface a Azure Monitor přes Intune
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro Log Analytics pracovní prostor, který bude spravovat vaše Surface Hub. Můžete je získat z nastavení pracovního prostoru v Azure Portal.

Intune je produkt společnosti Microsoft, který umožňuje centrálně spravovat nastavení konfigurace Log Analytics pracovního prostoru, která se vztahují na jedno nebo více vašich zařízení. Pomocí těchto kroků nakonfigurujete zařízení přes Intune:

1. Přihlaste se k Intune.
2. Přejděte do **Nastavení**  >  **připojené zdroje**.
3. Vytvoří nebo upraví zásadu na základě šablony Surface Hub.
4. Přejděte do části zásady Azure Operational Insights a přidejte do této zásady *ID pracovního prostoru* Log Analytics a *klíč pracovního prostoru* .
5. Uložte zásady.
6. Přidružte zásadu k příslušné skupině zařízení.

   ![Zásady Intune](./media/surface-hubs/intune.png)

Intune pak synchronizuje nastavení Log Analytics se zařízeními v cílové skupině a zaregistruje je do pracovního prostoru Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Připojení Center Surface k Azure Monitor pomocí aplikace nastavení
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro Log Analytics pracovní prostor, který bude spravovat vaše Surface Hub. Můžete je získat z nastavení pracovního prostoru Log Analytics v Azure Portal.

Pokud ke správě svého prostředí nepoužíváte Intune, můžete zařízení zaregistrovat ručně pomocí **Nastavení** v každém Surface Hub:

1. Z Surface Hub otevřete **Nastavení**.
2. Po zobrazení výzvy zadejte přihlašovací údaje Správce zařízení.
3. Klikněte na **Toto zařízení**a v části **monitorování**klikněte na **Konfigurovat Log Analytics nastavení**.
4. Vyberte **Povolit monitorování**.
5. V dialogovém okně nastavení Log Analytics zadejte **ID pracovního prostoru** Log Analytics a zadejte **klíč pracovního prostoru**.  
   ![Snímek obrazovky zobrazuje nastavení sady Microsoft Operations Manager Suite s vybraným možnostmi Povolit monitorování a textová pole pro ID pracovního prostoru a klíč pracovního prostoru.](./media/surface-hubs/settings.png)
6. Kliknutím na tlačítko **OK** dokončete konfiguraci.

Zobrazí se potvrzení o tom, jestli se konfigurace na zařízení úspěšně nastavila. Pokud k tomu došlo, zobrazí se zpráva oznamující, že se agent úspěšně připojil k Azure Monitor. Zařízení pak začne odesílat data do Azure Monitor, kde je můžete zobrazit a pracovat s nimi.

## <a name="monitor-surface-hubs"></a>Monitorovat centra Surface
Monitorování vašich Surface Hub pomocí Azure Monitor je podobně jako monitorování všech ostatních zaregistrovaných zařízení.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Když kliknete na dlaždici Surface Hub, zobrazí se stav vašeho zařízení.

   ![Řídicí panel Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Můžete vytvářet [výstrahy](../platform/alerts-overview.md) na základě existujících nebo vlastních prohledávání protokolu. Pomocí dat Azure Monitor shromažďuje z vašich Surface Hub, můžete vyhledat problémy a upozornit na podmínky, které pro svoje zařízení definujete.

## <a name="next-steps"></a>Další kroky
* Použijte [dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) k zobrazení podrobných dat Surface Hub.
* Můžete vytvářet [výstrahy](../platform/alerts-overview.md) , které vás upozorní, když dojde k problémům s rozbočovači Surface Hub.
