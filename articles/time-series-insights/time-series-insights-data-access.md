---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady pro zabezpečení Azure čas Series Insights ve verzi Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: 9aea7a9c9dd96bf30ebb3def9354df9e4bd30114
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558512"
---
# <a name="grant-data-access-to-an-environment"></a>Udělení přístupu k datům do prostředí

Tento článek popisuje dva typy zásad přístupu Azure čas Series Insights ve verzi Preview.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte prostředí Time Series Insights. Zadejte `Time Series` v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání.
1. Ze seznamu vyberte vaše prostředí Time Series Insights.
1. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.

    ![data. přístup k jednomu][1]

1. Zvolte **vybrat uživatele**. Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.

    ![data access dva][2]

1. Zvolte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:

    * Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.

    * V opačném případě vyberte **čtečky** umožní uživateli zadat dotaz na data v prostředí a ukládání osobních, ne sdílených dotazů v prostředí.

   Vyberte **OK** k potvrzení volby role.

    ![data-access tři][3]

1. Vyberte **OK** na **vybrat roli uživatele** stránky.

    ![data access čtyři][4]

1. Ujistěte se, že **zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

    ![Data access pět][5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Poskytnout přístup hosta pro uživatele z jiného tenanta Azure Active Directory

`Guest` není role správy. Je termín používaný pro účet, který je z jednoho tenanta pozvat. Jakmile účet guest pozvání do adresáře tenantovi, může mít stejné řízení přístupu u stejně jako jakýkoliv jiný účet. Můžete udělit přístup ke správě prostředí Time Series Insights pomocí okna řízení přístupu (IAM). Nebo můžete udělit přístup k datům v prostředí prostřednictvím okna zásady přístupu k datům. Další informace o přístupu hostů tenanta Azure Active Directory (Azure AD), přečtěte si [uživatele přidat Azure Active Directory s B2B spolupráce na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Postupujte podle těchto kroků k udělení přístupu hostů k prostředí Time Series Insights pro uživatele služby Azure AD z jiného tenanta.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte prostředí Time Series Insights. Zadejte **časové řady** v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání.
1. Ze seznamu vyberte vaše prostředí Time Series Insights.
1. Vyberte **zásady přístupu k datům**a pak vyberte **+ pozvat**.

    ![data access šest][6]

1. Zadejte e-mailovou adresu uživatele, kterého chcete pozvat. Tato e-mailová adresa musí být službou Azure AD. Může volitelně zahrnovat e-mailové pozvánce osobní zprávu.

    ![data-access sedm][7]

1. Vyhledejte bublin potvrzení, které se zobrazí na obrazovce.

    ![data access osm][8]

1. Zvolte **vybrat uživatele**. Hledání e-mailová adresa uživatele typu Host, který je pozval vyhledejte uživatele, kterého chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.

    ![data-access devět][9]

1. Zvolte **vybrat roli**. Zvolte odpovídající přístup role pro uživatele typu Host:

    * Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.

    * V opačném případě vyberte **čtečky** umožní uživateli zadat dotaz na data v prostředí a ukládání osobních, ne sdílených dotazů v prostředí.

   Vyberte **OK** k potvrzení volby role.

    ![data-access deset][10]

1. Vyberte **OK** na **vybrat roli uživatele** stránky.

1. Ujistěte se, že **zásady přístupu k datům** stránce uvedeny uživatele typu Host a rolí pro každého uživatele typu Host.

    ![data-access jedenáct][11]

1. Uživatel typu Host nyní nutné postupovat podle kroků pro přístup k prostředí v tenantovi Azure, ke kterému je pozvaný. Nejdřív přijmout pozvánku, kterou jste jim poslali. Tato pozvánka se odeslala e-mailem na e-mailovou adresu, kterou jste použili v kroku 5. Vyberou **Začínáme** tak, aby přijímal.

    ![data-access dvanácti][12]

1. V dalším kroku uživatele typu Host přijímá oprávnění spojená s správce organizace.

    ![data-access třináct][13]

1. Poté, co uživatel typu Host je podepsán e-mailovou adresu, který jste použili a pozvat je a přijetí e-mailové pozvánce, přejdou na insights.azure.com. Jednou, vyberou avatara u své e-mailovou adresu v pravém horním rohu obrazovky.

    ![data-access čtrnáct][14]

1. Další, vybere uživatel typu Host z rozevírací nabídky adresáře tenanta Azure. Tento klient je ten, na který je pozval.

    ![data-access patnáct][15]

Poté, co uživatel typu Host vybere vašeho tenanta, zobrazí se jim prostředí Time Series Insights, ke které jste zadali jejich přístupu. Tito uživatelé teď mají všechny požadované možnosti, které jsou přidružené k roli, které jste je zadali v kroku 8.

## <a name="next-steps"></a>Další postup

* Přečtěte si [přidání zdroje událostí služby Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Time Series Insights.
* Odeslat [události ke zdroji událostí](./time-series-insights-send-events.md).
* Zobrazení [prostředí v Průzkumníku čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png