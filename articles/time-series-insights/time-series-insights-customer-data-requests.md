---
title: Podat žádost Azure zákaznická data služby Time Series Insights – zákaznická data o funkce v Azure Time Series Insights | Dokumentace Microsoftu
description: Souhrn funkcí žádosti o data zákazníků Azure Time Series Insights.
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: c749a61fa26b13ffe67c5913d1a11b6c1f0e186b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276056"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce

Azure Time Series Insights je spravovaná Cloudová služba s komponentami úložiště, analýzy a vizualizace, které usnadňují ingestování, ukládání, zkoumání a analýzy miliard událostí současně.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

K zobrazení, exportovat a odstranit osobní údaje, které se můžou stát terčem žádost subjektu dat, můžete použít správce klienta Azure Time Series Insights na webu Azure portal nebo rozhraní REST API. Pomocí webu Azure portal k žádosti o službu data subjektu, poskytuje méně složitý metodu k provedení těchto operací, které dáváte přednost většina uživatelů.

## <a name="identifying-customer-data"></a>Identifikace zákaznická data

Azure Time Series Insights bude považovat za osobní údaje, aby data přidružená k správci a uživatelé služby Time Series Insights. Time Series Insights jsou uloženy Azure Active Directory – ID objektu uživatelů s přístupem k prostředí. Na webu Azure portal zobrazuje e-mailové adresy uživatele, ale tyto e-mailové adresy se neukládají v Time Series Insights, jejich dynamicky vyhledávat pomocí ID objektu Azure Active Directory v Azure Active Directory.

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data

Správce tenanta může odstranit data zákazníků pomocí webu Azure portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Nicméně před odstraněním zákaznická data prostřednictvím portálu byste měli odebrat zásady přístupu uživatele z prostředí Time Series Insights na webu Azure portal. Další informace najdete v tématu [udělení přístupu k datům do prostředí Time Series Insights pomocí webu Azure portal](time-series-insights-data-access.md).

Můžete také provádět operace delete na zásady přístupu pomocí rozhraní REST API. Další informace najdete v tématu [odstranit zásady přístupu –](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

Time Series Insights jsou součástí zásad okna na webu Azure Portal. Time Series Insights a okno zásad umožňují zobrazit, exportovat a odstranit data uživatele uložená v rámci služby. Odstranit akci prováděné v okně zásady Azure portal výsledků k odstranění dat uživatele v rámci služby Time Series Insights. Například pokud má uživatel osobní uloženého dotazu, tento dotaz je trvale odstraněn z Průzkumníka služby Time Series Insights. Pokud má uživatel uložené sdílený dotaz, opakuje dotazu, ale informace o uživateli se trvale odstraní. Následující poznámka obsahuje pokyny k provedení následujících úkolů.

## <a name="exporting-customer-data"></a>Export zákaznických dat

Podobně k odstranění dat, jako správce tenanta můžete zobrazit a exportovat data uložená v Time Series Insights v okně zásady na portálu Azure portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Pokud jste správcem tenanta, zobrazí se zásady přístupu k datům prostředí Time Series Insights na webu Azure Portal. Další informace najdete v tématu [udělení přístupu k datům do prostředí Time Series Insights pomocí webu Azure portal](time-series-insights-data-access.md).

Je také možné provést operacích exportu proveďte na zásady přístupu pomocí operace "seznam prostředí" v zadané rozhraní REST API. Další informace najdete v tématu [zásady přístupu – prostředí seznamu](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Odstranit data uložená v Time Series Insights

Osobní údaje činí cestě do úložiště služby Time Series Insights, od uživatelů a správu dat na jiný scénář. Pokud považujete za data uložená v Time Series Insights jako osobní údaje, můžete exportovat a odstranění dat pomocí následujících kroků:

**Zobrazení a export dat**

Můžete zobrazit a exportovat data uložená v rámci služby Time Series Insights, budete muset vyhledat tato data. K zobrazení a export dat můžete použít Průzkumníka služby Time Series Insights nebo Time Series Insights dotaz rozhraní API. Můžete zobrazit a exportovat data pomocí Průzkumníka služby Time Series Insights, první vyhledávání k nalezení dat uživatele dotyčný. Po vyhledání, klikněte pravým tlačítkem na graf a vyberte **zkoumat události**. Mřížka událostí se zobrazí a nabízí možnosti pro export dat jako CSV a JSON.

Další informace najdete v tématu [Průzkumníka služby Azure Time Series Insights](time-series-insights-explorer.md).

**Odstranit data**

V současné době Time Series Insights detailní odstranění dat nepodporuje. Ale Time Series Insights poskytuje možnost odebrat zákaznická data uložená v Time Series Insights tím, že nakonfigurujete zásady uchovávání informací. Můžete upravit dobu uchovávání celé prostředí Time Series Insights do libovolného počtu dnů pro podporu vašich požadavků na odstranění.

Další informace najdete v tématu [konfigurace uchovávání naleznete v Time Series Insights](time-series-insights-how-to-configure-retention.md).