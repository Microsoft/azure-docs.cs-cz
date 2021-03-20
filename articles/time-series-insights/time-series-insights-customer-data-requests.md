---
title: Funkce žádosti o zákaznická data – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o funkcích zákaznických údajů o zákaznících v Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5ecbabe4a5c3458c77d145fbce1281550470e00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019068"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí pro žádosti o data zákazníků

Azure Time Series Insights je spravovaná cloudová služba s komponentami pro úložiště, analýzu a vizualizace, která usnadňuje ingestování, ukládání, prozkoumávání a analýzu miliard událostí současně.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Pokud chcete zobrazit, exportovat a odstranit osobní údaje, které mohou podléhat požadavku subjektu dat, může správce klienta Azure Time Series Insights použít buď Azure Portal, nebo rozhraní REST API. Použití Azure Portal k poskytování požadavků na data subjektu poskytuje méně složitou metodu pro provádění těchto operací, které nejvíc uživatelé dávají přednost.

## <a name="identifying-customer-data"></a>Identifikace zákaznických dat

Azure Time Series Insights považují osobní údaje za data přidružená správcům a uživatelům Time Series Insights. Time Series Insights ukládá Azure Active Directory objektu – ID uživatelů s přístupem k prostředí. Azure Portal zobrazí e-mailové adresy uživatelů, ale tyto e-mailové adresy se v Time Series Insights neukládají, dynamicky se vyhledá pomocí Azure Active Directory objektu-ID v Azure Active Directory.

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data

Správce klienta může odstranit zákaznická data pomocí Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Než však odstraníte zákaznická data prostřednictvím portálu, měli byste odebrat zásady přístupu uživatele z Time Series Insightsho prostředí v rámci Azure Portal. Další informace najdete v článku [udělení přístupu k datům Time Series Insights prostředí pomocí Azure Portal](./concepts-access-policies.md).

Operace odstranění můžete také provádět v zásadách přístupu pomocí REST API. Další informace najdete v tématu [zásady přístupu pro čtení – odstranění](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete).

Time Series Insights je integrován do okna zásad v Azure Portal. Jak Time Series Insights, tak okno zásad vám umožní zobrazit, exportovat a odstranit uživatelská data uložená v rámci služby. Jakákoli akce odstranění provedené v okně zásad Azure Portal vede k odstranění uživatelských dat v rámci Time Series Insights. Pokud má uživatel například uložený osobní dotaz, tento dotaz je trvale odstraněn z Průzkumníka Time Series Insights. Pokud má uživatel uložený sdílený dotaz, dotaz bude trvat, ale informace o uživateli se trvale odstraní. Následující poznámka obsahuje pokyny k provedení těchto úloh.

## <a name="exporting-customer-data"></a>Export zákaznických dat

Podobně jako při odstraňování dat může správce klienta zobrazit a exportovat data uložená v Time Series Insights v okně zásady v Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Pokud jste správcem tenanta, můžete v Azure Portal zobrazit zásady přístupu k datům v prostředí Time Series Insights. Další informace najdete v článku [udělení přístupu k datům Time Series Insights prostředí pomocí Azure Portal](./concepts-access-policies.md).

Je také možné provádět operace exportu pro zásady přístupu pomocí operace "výpis podle prostředí" v zadaném REST API. Další informace najdete v tématu [zásady přístupu pro čtení – seznam podle prostředí](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Odstranění dat uložených v Time Series Insights

Osobní údaje se můžou přizpůsobovat do Time Series Insights úložiště, což je jiný scénář než z dat uživatelů a správců. Pokud považujete data uložená v Time Series Insights jako osobní údaje, můžete tato data exportovat a odstranit pomocí následujících kroků:

### <a name="view-and-export-data"></a>Zobrazení a export dat

Chcete-li zobrazit a exportovat data uložená v Time Series Insights, je nutné vyhledat tato data. K zobrazení a exportu dat můžete použít rozhraní API pro Time Series Insights nebo Time Series Insights dotazů. Chcete-li zobrazit a exportovat data pomocí Průzkumníka Time Series Insights, nejprve vyhledejte konkrétní uživatelská data. Po hledání klikněte pravým tlačítkem na graf a vyberte **prozkoumat události**. Zobrazí se mřížka události a prezentuje možnosti exportu dat ve formátu CSV a formátu JSON.

Další informace najdete v [Azure Time Series Insights Exploreru](time-series-insights-explorer.md).

### <a name="delete-data"></a>Odstranění dat

V současné době Time Series Insights nepodporuje přesnější mazání dat. Time Series Insights ale umožní odebrat zákaznická data uložená v Time Series Insights konfigurací zásad uchovávání informací. Dobu uchovávání celého Time Series Insights prostředí můžete upravit na libovolný počet dní, aby se podporovaly vaše požadavky na odstranění.

Další informace najdete v tématu [Konfigurace uchovávání v Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [udělení přístupu k datům do prostředí Azure Time Series Insights](./concepts-access-policies.md).

* Zobrazit [průzkumníka Azure Time Series Insights](time-series-insights-explorer.md).

* Přečtěte si o [konfiguraci uchovávání v Time Series Insights](time-series-insights-how-to-configure-retention.md).