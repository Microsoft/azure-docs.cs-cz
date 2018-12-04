---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady pro zabezpečení služby Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 3694ff0db63d685cb63f586062158b4f8acac5cf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847605"
---
# <a name="grant-data-access-to-an-environment"></a>Udělení přístupu k datům do prostředí

Tento článek popisuje dva typy zásad přístupu v Azure Time Series Insights (preview).

## <a name="grant-data-access"></a>Udělení přístupu k datům

Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu:

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).
1. Vyhledejte prostředí Time Series Insights (TSI). Typ `Time Series` v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání.
1. Ze seznamu vyberte vaše prostředí TSI.
1. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.

    ![data. přístup k jednomu][1]

1. Vyberte **vybrat uživatele**. Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.

    ![data access dva][2]

1. Vyberte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:

    * Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.

    * V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.

    Vyberte **Ok** k potvrzení volby role.

    ![data-access tři][3]

1. Vyberte **Ok** v **vybrat roli uživatele** stránky.

    ![data access čtyři][4]

1. **Zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

    ! [data access pět[5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Poskytnout přístup hosta pro uživatele z jiného tenanta Azure Active Directory

`Guest` není role správy; To je termín používaný pro účet, který je pozval z jednoho tenanta do jiného. Jakmile účet hosta má pozvánku do adresáře tenantovi, může mít stejné řízení přístupu u stejně jako jakýkoli jiný účet, udělit přístup ke správě služby TSI prostředí pomocí okna řízení přístupu (IAM) nebo udělit přístup k datům v prostředí prostřednictvím okna zásady přístupu k datům. Další informace o přístupu hostů tenanta Azure Active Directory (AAD) najdete v článku [uživatele přidat Azure Active Directory s B2B spolupráce na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Postupujte podle těchto kroků k udělení přístupu hostů k TSI prostředí pro uživatele služby AAD z jiného tenanta:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte prostředí TSI. Typ **časové řady** do vyhledávacího pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání.
1. Ze seznamu vyberte vaše prostředí TSI.
1. Vyberte **zásady přístupu k datům**a pak vyberte **+ pozvat**.

    ![data access šest][6]

1. Zadejte e-mail uživatele, který chcete pozvat. Poznámka: mělo že by to být e-mailu přidružené k AAD. Může volitelně zahrnovat e-mailové pozvánce osobní zprávu.

    ![data-access sedm][7]

1. Měli byste vidět bublinu potvrzení na obrazovce.

    ![data access osm][8]

1. Vyberte **vybrat uživatele**. Hledání e-mailová adresa uživatele typu Host, jenom pozval vyhledejte uživatele, kterého chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.

    ![data-access devět][9]

1. Vyberte **vyberte** role. Zvolte odpovídající přístup role pro uživatele typu Host:

    1. Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.
    1. V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.
    1. Vyberte **Ok** k potvrzení volby role.

    ![data-access deset][10]

1. Vyberte **Ok** v **vybrat roli uživatele** stránky.

1. **Zásady přístupu k datům** stránka nyní obsahuje uživatele typu Host a rolím pro každého uživatele typu Host.

    ![data-access jedenáct][11]

1. Teď uživatel typu Host bude potřeba provést určité pouze kroky pro přístup k prostředí v Azure můžete tenanta pozvat jim. Nejprve je třeba přijmout pozvánku, kterou jste právě odeslali. Tato pozvánka dala přijde e-mailem na e-mailovou adresu, které vás pozvat v kroku 5. Měli byste kliknout na **Začínáme**, tak, aby přijímal.

    ![data-access dvanácti][12]

1. V dalším kroku uživatele typu Host muset přijmout oprávnění spojená s správce organizace.

    ![data-access třináct][13]

1. Když uživatel typu Host se zaznamená do e-mailovou adresu zveme a nepřijme pozvání, bude přejít k insights.azure.com. Jednou, se musí kliknout na avatara u e-mailu v pravém horním rohu obrazovky.

    ![data-access čtrnáct][14]

1. V dalším kroku bude uživatel typu Host vyberte Azure tenanta z rozevírací nabídky adresáře. Toto je tenant, který je pozval.

    ![data-access patnáct][15]

1. A konečně když uživatel typu Host vybere vašeho tenanta, uvidí TSI prostředí, které jste právě zadali jim přístup k. By teď měly mít všechny možnosti, které jsou přidružené k roli, kterou jste zadali v kroku 8.

## <a name="next-steps"></a>Další postup

* Přečtěte si [přidání zdroje událostí centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Azure TSI.
* Odeslat [události ke zdroji událostí](./time-series-insights-send-events.md).
* Zobrazení [prostředí v Průzkumníku Time Series Insights](./time-series-insights-update-explorer.md).

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