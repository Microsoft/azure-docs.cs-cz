---
title: Funkce požadavků na zákaznická data – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Seznamte se s funkcemi požadavků na zákaznická data v Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/14/2020
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 64660e497bb1765b649e00b07fdb5db8c05910f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368697"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavků na zákaznická data

Azure Time Series Insights je spravovaná cloudová služba s komponentami úložiště, analýz a vizualizace, které usnadňují ingestování, ukládání, zkoumání a analýzu miliard událostí současně.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Chcete-li zobrazit, exportovat a odstranit osobní data, která mohou podléhat žádosti subjektu dat, správce tenanta Azure Time Series Insights můžete použít buď portál Azure nebo ROZHRANÍ API REST. Použití portálu Azure k požadavkům na služby subjektu dat poskytuje méně složitou metodu k provádění těchto operací, které většina uživatelů upřednostňuje.

## <a name="identifying-customer-data"></a>Identifikace údajů o zákaznících

Azure Time Series Insights považuje osobní data za data spojená se správci a uživateli Time Series Insights. Time Series Insights ukládá ID objektu Azure Active Directory uživatelů s přístupem k prostředí. Portál Azure zobrazuje e-mailové adresy uživatelů, ale tyto e-mailové adresy se neukládají v přehledech time series, jsou dynamicky vyhledávány pomocí ID objektu Azure Active Directory ve službě Azure Active Directory.

## <a name="deleting-customer-data"></a>Odstranění dat zákazníků

Správce klienta můžete odstranit zákaznická data pomocí portálu Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Než však odstraníte zákaznická data prostřednictvím portálu, měli byste odebrat zásady přístupu uživatele z prostředí Time Series Insights v rámci portálu Azure Portal. Další informace najdete v článek [Udělení přístupu k datům v prostředí Time Series Insights pomocí portálu Azure Portal](time-series-insights-data-access.md).

Můžete také provádět operace odstranění na zásady přístupu pomocí rozhraní REST API. Další informace naleznete [v zásadách přístupu – odstranění](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

Time Series Insights je integrovaná s okno zásad na portálu Azure. Přehledy časové řady i okno Zásad umožňují zobrazit, exportovat a odstranit uživatelská data uložená v rámci služby. Jakákoli akce odstranění provedená v okně Zásad y na portálu Azure vede k odstranění uživatelských dat v rámci přehledů časové řady. Například pokud má uživatel uložený osobní dotaz, tento dotaz je trvale odstraněn z průzkumníka Time Series Insights. Pokud má uživatel uložený sdílený dotaz, dotaz přetrvává, ale informace o uživateli jsou trvale odstraněny. Následující poznámka obsahuje pokyny k provedení těchto úkolů.

## <a name="exporting-customer-data"></a>Export dat zákazníků

Podobně jako při mazání dat může správce klienta zobrazit a exportovat data uložená v přehledech časové řady z okna Zásad na webu Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Pokud jste správce klienta, můžete zobrazit zásady přístupu k datům v prostředí Time Series Insights na webu Azure Portal. Další informace najdete v článek [Udělení přístupu k datům v prostředí Time Series Insights pomocí portálu Azure Portal](time-series-insights-data-access.md).

Je také možné provádět operace exportu na zásady přístupu pomocí operace "seznam podle prostředí" v poskytnutém rozhraní REST API. Další informace naleznete [v zásadách přístupu – Seznam podle prostředí](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Odstranění dat uložených v přehledech časových řad

Osobní údaje se mohou dostat do úložiště Time Series Insights, což je jiný scénář než data uživatelů a správců. Pokud data uložená v Time Series Insights považujete za osobní data, můžete tato data exportovat a odstranit pomocí následujících kroků:

**Zobrazení a export dat**

Chcete-li zobrazit a exportovat data uložená v přehledech časové řady, je třeba tato data vyhledat. K zobrazení a exportu dat můžete použít průzkumník analyty Time Series Insights nebo rozhraní API dotazu Time Series Insights. Chcete-li zobrazit a exportovat data pomocí průzkumníka Time Series Insights, nejprve vyhledejte dotyčná uživatelská data. Po prohledání klikněte pravým tlačítkem myši na graf a vyberte **prozkoumat události**. Zobrazí se mřížka událostí a zobrazí možnosti exportu dat jako CSV a JSON.

Další informace najdete v [průzkumníku Azure Time Series Insights](time-series-insights-explorer.md).

**Odstranění dat**

V současné době Time Series Insights nepodporuje podrobné odstranění dat. Time Series Insights však poskytuje možnost odebrat zákaznická data uložená v rámci Time Series Insights konfigurací zásad uchovávání informací. Dobu uchovávání celého prostředí Time Series Insights můžete upravit na libovolný počet dní, abyste podpořili vaše požadavky na odstranění.

Další informace naleznete [v článek Konfigurace uchovávání informací v přehledech časové řady .](time-series-insights-how-to-configure-retention.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [udělování přístupu k datům do prostředí Azure Time Series Insights](./time-series-insights-data-access.md).

* Prohlédněte si [průzkumníka Azure Time Series Insights](time-series-insights-explorer.md).

* Informace o [konfiguraci uchovávání informací v přehledech time series](time-series-insights-how-to-configure-retention.md).