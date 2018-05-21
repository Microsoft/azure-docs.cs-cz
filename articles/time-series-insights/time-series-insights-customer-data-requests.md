---
title: Zákaznická data žádosti o funkce v Azure časové řady přehledy
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
ms.openlocfilehash: d99febdf88fdbf66c45c2ba9b0ab274db1fde185
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavek data zákazníků

Statistiky Azure řady čas je spravovaná Cloudová služba se úložiště, analýzy a vizualizace součásti, které usnadňují ingestování, ukládat, zkoumat a analyzovat až miliardy událostí současně.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Pokud chcete zobrazit, exportovat a odstranit osobní data, která mohou podléhat subjektu požadavku na data, můžete použít správce klienta Azure časové řady přehled portálu Azure nebo rozhraní REST API. Pomocí portálu Azure ke zpracování dat subjektu požadavků, poskytuje méně složitých metodu, jak provádět tyto operace, které dávají přednost většina uživatelů.

## <a name="identifying-customer-data"></a>Identifikace data zákazníků

Statistiky Azure řady čas zvažuje data přidružená k správcům a uživatelům časové řady Statistika osobních údajů. Statistika časové řady ukládá objekt ID Azure Active Directory uživatelům s přístupem do prostředí. Portál Azure zobrazí e-mailové adresy uživatele, ale tyto e-mailové adresy nejsou uložené v rámci časové řady statistiky, se dynamicky prohledávat pomocí ID objektu služby Azure Active Directory v Azure Active Directory.

## <a name="deleting-customer-data"></a>Odstraňování dat zákazníka

Správce klienta můžete odstranit zákaznická data pomocí portálu Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Ale předtím, než odstraníte zákaznická data prostřednictvím portálu, byste měli odebrat zásady přístupu uživatele z prostředí časové řady Insights na portálu Azure. Další informace najdete v tématu [udělit přístup k datům časové řady Statistika prostředí pomocí portálu Azure](time-series-insights-data-access.md).

Můžete také provést operace odstranění na zásady přístupu pomocí rozhraní REST API. Další informace najdete v tématu [odstranit zásady přístupu -](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

Statistika řady čas je integrovaná do okna zásady na portálu Azure. Statistika řady čas a v okně zásady umožňují zobrazit, exportovat a odstranit data uživatele uložená v rámci služby. Odstranit všechny akci prováděné v okně zásady Azure portálu výsledků k odstranění dat uživatele v rámci Statistika časové řady. Například pokud má uživatel uložený osobní dotaz, tento dotaz je trvale odstraněn z Průzkumníka Statistika časové řady. Pokud má uživatel uložený dotaz sdílené, potrvají dotazu, ale informace o uživateli je trvale odstraněn. Následující poznámka obsahuje pokyny k provedení těchto úloh.

## <a name="exporting-customer-data"></a>Export dat zákazníka

Stejně tak k odstranění dat, správce klienta můžete zobrazit a exportovat data uložená v časové řady přehledy v okně zásady na portálu Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Pokud jste správce klienta, můžete zobrazit zásady přístupu k datům v rámci prostředí časové řady Insights na portálu Azure. Další informace najdete v tématu [udělit přístup k datům časové řady Statistika prostředí pomocí portálu Azure](time-series-insights-data-access.md).

Také je možné provádět operace exportu na zásady přístupu pomocí operace "seznam prostředí" v zadané REST API. Další informace najdete v tématu [zásady přístupu - prostředí seznamu](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Chcete-li odstranit data uložená v rámci Statistika časové řady

Osobní údaje provést jeho způsob do úložiště časové řady statistiky, jiný scénář z uživatele a správce data. Pokud uvažujete o data uložená v časové řady přehledy jako osobní údaje, můžete exportovat a k odstranění dat pomocí následujících kroků:

**Zobrazení a export dat**

K zobrazení a export dat uložených v rámci časové řady Insights, budete muset vyhledat data. K zobrazení a exportu dat můžete použít Průzkumníka časové řady statistiky nebo časové řady Statistika dotazu rozhraní API. K zobrazení a export dat pomocí Průzkumníka časové řady statistiky, nejprve hledat data uživatele nejistá. Po hledání, klikněte pravým tlačítkem na graf a vyberte **prozkoumat události**. Mřížky události se zobrazí a uvede možnosti pro export dat jako sdílený svazek clusteru a JSON.

Další informace najdete v tématu [Statistika řady čas Azure explorer](time-series-insights-explorer.md).

**Odstranit data**

V současné době časové řady Statistika granulární odstranění dat nepodporuje. Statistika časové řady však poskytuje možnost odebrání zákaznická data uložená v časové řady statistiky tak, že nakonfigurujete zásady uchovávání informací. Můžete upravit dobu uchování celé prostředí Statistika časové řady na libovolný počet dní pro podporu požadavků odstranění.

Další informace najdete v tématu [konfigurace uchování v časové řady přehledy](time-series-insights-how-to-configure-retention.md).