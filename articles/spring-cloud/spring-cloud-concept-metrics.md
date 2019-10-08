---
title: Principy metrik pro jarní cloud Azure
description: Naučte se kontrolovat metriky v Azure jaře cloudu.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039123"
---
# <a name="metrics-for-azure-spring-cloud"></a>Metriky pro jarní cloud Azure

Azure Monitor Průzkumník metrik je součástí portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a prozkoumat špičky a rozmítají metriky v metrikách. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašich prostředků. V Azure jaře Cloud nabízíme dvě možnosti, jak zobrazit metriky: grafy na stránce **Přehled aplikace** a na stránce metriky úrovně služeb.

## <a name="application-overview-page"></a>Stránka s přehledem aplikace

Na stránce **Přehled aplikací** každé aplikace se zobrazuje graf metrik, který vám umožní provést rychlou kontrolu stavu aplikace.  Přejít na stránku jarní cloudová služba Azure a vybrat **řídicí panel aplikace**a pak vyberte aplikaci ze seznamu.  

Poskytujeme 5 grafů s metrikami aktualizovanými každou minutu pro následující:

* **Chyby serveru http**: počet chyb pro požadavky HTTP na vaši aplikaci.
* **Data v**: bajtů přijatých vaší aplikací.
* **Výstupní data**: bajtů odeslaných do vaší aplikace.
* **Požadavky**: žádosti přijaté vaší aplikací.
* **Průměrná doba odezvy**: Průměrná doba odezvy z vaší aplikace

Můžete vybrat časový rozsah pro graf v rozmezí 1 hodiny až 7 dní.

## <a name="service-level-metric-queries"></a>Dotazy na metriky na úrovni služby

Azure jaře Cloud umožňuje monitorovat nejrůznější metriky aplikací. Další informace o této službě najdete v [Průvodci pro zahájení práce](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) s Azure monitor metrikami.

Chcete-li zkontrolovat data metriky, vyberte metriku, **agregaci**a časový rozsah.  Tyto koncepty jsou vysvětleny níže.

### <a name="aggregation"></a>Agregace 

Azure se dotazuje a aktualizuje metriky každou minutu. Azure poskytuje tři způsoby, jak agregovat data pro zvolené časové období:

* **Total**: součet všech metrik jako cílový výstup.
* **Average**: jako cílový výstup použijte průměrnou hodnotu v periodě.
* **Max/min**: jako cílový výstup použijte hodnotu Max/min v periodě.

### <a name="time-range"></a>Časové rozmezí

Vyberte výchozí časový rozsah nebo definujte vlastní.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Úprava členitosti dotazu metriky

Ve výchozím nastavení Azure agreguje metriky pro všechny aplikace jarní cloudové služby Azure. Chcete-li zkontrolovat metriky na úrovni aplikace nebo instance, použijte funkci Filter.  
Vyberte **Přidat filtr**, nastavte vlastnost na **aplikace** a vyberte cílovou aplikaci, kterou chcete monitorovat. Volitelně můžete pomocí možnosti **použít rozdělení** nakreslit samostatné čáry pro každou aplikaci v grafu.

>[!TIP]
> Na stránce metriky můžete vytvořit vlastní grafy a připnout je na **řídicí panel**. Začněte pojmenováním grafu.  Potom **v pravém horním rohu vyberte Připnout na řídicí panel**. Nyní se můžete podívat na svou aplikaci na **řídicím panelu**portálu.