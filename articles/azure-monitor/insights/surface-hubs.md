---
title: Monitorování zařízení Surface hub s Azure Azure Monitor | Dokumentace Microsoftu
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
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 902cf62e53581785caf2730f63af3633d8e1e498
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005433"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Díky Azure monitoru ke sledování jejich stavu monitorování Surface Huby

![Zařízení Surface Hub symbol](./media/surface-hubs/surface-hub-symbol.png)

Tento článek popisuje, jak použít řešení Surface Hub ve službě Azure Monitor monitorovat zařízení Microsoft Surface Hub. Toto řešení umožňuje sledovat stav Surface Huby dobře porozumět, jak se používají.

Každé zařízení Surface Hub je nainstalovaný Agent sledování Microsoft. Jeho prostřednictvím agenta, můžete posílat data z Surface Hub k pracovnímu prostoru Log Analytics ve službě Azure Monitor. Soubory protokolu se načítají z Surface hub a jsou pak odesílají do Azure monitoru. Problémy, jako jsou servery, je offline, kalendář nesynchronizuje, nebo pokud účet zařízení se nemůže přihlásit do Skypu jsou zobrazené v řídicím panelu zařízení Surface Hub ve službě Azure Monitor. Pomocí dat na řídicím panelu můžete identifikovat zařízení, která neběží, nebo jsou potíže a potenciálně použít opravy pro zjištěné problémy.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace. Pokud chcete spravovat vaše Surface hub ve službě Azure Monitor, budete potřebovat následující:

* A [předplatné Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) úroveň, která bude podporovat víc zařízení, které chcete monitorovat. Log Analytics ceny se liší v závislosti na tom, kolik zařízení jsou zaregistrovaná a kolik dat se procesy. Bude potřeba vzít v úvahu při plánování zavádění řešení Surface Hub.

V dalším kroku vám bude přidat existující pracovní prostor Log Analytics nebo vytvořte novou. Podrobný návod, jak pomocí některé z metod je na [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../learn/quick-create-workspace.md). Po nakonfigurování pracovní prostor Log Analytics existují dva způsoby, jak zaregistrovat svoje zařízení Surface Hub:

* Automaticky pomocí Intune
* Ručně pomocí **nastavení** na vašem zařízení Surface Hub.

## <a name="set-up-monitoring"></a>Nastavte monitorování
Můžete monitorovat stav a aktivitu Surface hubu pomocí Azure monitoru. Surface Hubu můžete registrovat pomocí Intune nebo místně s použitím **nastavení** na Surface Hubu.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Připojit zařízení Surface hub ke sledování Azure prostřednictvím služby Intune
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro pracovní prostor Log Analytics, která bude spravovat Surface Huby. Můžete dosáhnout z nastavení pracovního prostoru na webu Azure Portal.

Intune je produkt Microsoftu, která umožňuje centrálně spravovat nastavení konfigurace pracovního prostoru Log Analytics, která platí pro jeden nebo více zařízení. Postupujte podle těchto kroků a nakonfigurujte zařízení přes Intune:

1. Přihlášení k Intune.
2. Přejděte do **nastavení** > **připojené zdroje**.
3. Vytvořte nebo upravte zásady na základě šablony Surface Hub.
4. Přejděte do části Azure Operational Insights zásad a přidejte Log Analytics *ID pracovního prostoru* a *klíč pracovního prostoru* k zásadám.
5. Uložte zásadu.
6. Přidružení zásad příslušné skupiny zařízení.

   ![Zásady Intune](./media/surface-hubs/intune.png)

Intune pak synchronizuje nastavení Log Analytics se zařízeními v cílové skupině, registrace ve vašem pracovním prostoru Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Připojení k Azure Monitor, pomocí nastavení aplikace Surface Huby
Budete potřebovat ID pracovního prostoru a klíč pracovního prostoru pro pracovní prostor Log Analytics, která bude spravovat Surface Huby. Můžete dosáhnout na základě nastavení pracovního prostoru Log Analytics na portálu Azure portal.

Pokud nepoužíváte Intune ke správě prostředí, abyste mohli registrovat zařízení ručně přes **nastavení** na každé zařízení Surface Hub:

1. Na Surface Hubu, otevřete **nastavení**.
2. Zadejte pověření správce zařízení po zobrazení výzvy.
3. Klikněte na tlačítko **toto zařízení**a v části **monitorování**, klikněte na tlačítko **nakonfigurovat nastavení analýzy protokolů**.
4. Vyberte **povolit monitorování**.
5. V dialogovém okně Nastavení Log Analytics, zadejte Log Analytics **ID pracovního prostoru** a zadejte **klíč pracovního prostoru**.  
   ![settings](./media/surface-hubs/settings.png)
6. Klikněte na tlačítko **OK** a dokončete tak konfiguraci.

Zobrazí se potvrzení se o tom, jestli konfigurace úspěšně použila na zařízení. Pokud ano, zobrazí se zpráva s informacemi o tom, že agent se úspěšně připojil do Azure monitoru. Zařízení spustí odesílání dat do Azure monitoru, kde můžete zobrazit a reagovat na něj.

## <a name="monitor-surface-hubs"></a>Monitor Surface Hubs
Monitorování Surface Huby používat Azure Monitor je podobné jako u monitorování jiných zaregistrovaných zařízení.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Když kliknete na dlaždici Surface Hubu, zobrazí se stav vašeho zařízení.

   ![Řídicí panel zařízení Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Můžete vytvořit [výstrahy](../platform/alerts-overview.md) založené na existující nebo vlastní prohledávání protokolů. Pomocí data, která Azure Monitor shromažďuje z Surface hub, můžete vyhledat problémy a upozornění na podmínky, které definujete pro vaše zařízení.

## <a name="next-steps"></a>Další postup
* Použití [protokolu dotazů ve službě Azure Monitor](../log-query/log-query-overview.md) k zobrazení podrobných dat o Surface Hub.
* Vytvoření [výstrahy](../platform/alerts-overview.md) které vás upozorní, když dojde k problémům s Surface Huby.
