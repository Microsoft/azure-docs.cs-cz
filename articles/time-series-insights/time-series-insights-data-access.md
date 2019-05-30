---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady pro zabezpečení Azure čas Series Insights ve verzi Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 53489ef9c1264fa31b06f4660e545e6efbd1f616
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237032"
---
# <a name="grant-data-access-to-an-environment"></a>Udělení přístupu k datům do prostředí

Tento článek popisuje dva typy zásad přístupu Azure čas Series Insights ve verzi Preview.

## <a name="sign-in-to-time-series-insights"></a>Přihlaste se k Time Series Insights

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte prostředí Time Series Insights. Zadejte `Time Series` v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání.
1. Ze seznamu vyberte vaše prostředí Time Series Insights.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu.

1. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Zvolte **vybrat uživatele**. Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Zvolte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:

    * Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.

    * V opačném případě vyberte **čtečky** umožní uživateli zadat dotaz na data v prostředí a ukládání osobních, ne sdílených dotazů v prostředí.

   Vyberte **OK** k potvrzení volby role.

    [![Data-access-three](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Vyberte **OK** na **vybrat roli uživatele** stránky.

    [![Data-access-four](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Ujistěte se, že **zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Poskytnutí přístupu hostů z jiného tenanta AAD

`Guest` není role správy. Je termín používaný pro účet, který je z jednoho tenanta pozvat. Jakmile účet guest pozvání do adresáře tenantovi, může mít stejné řízení přístupu u stejně jako jakýkoliv jiný účet. Můžete udělit přístup ke správě prostředí Time Series Insights pomocí okna řízení přístupu (IAM). Nebo můžete udělit přístup k datům v prostředí prostřednictvím okna zásady přístupu k datům. Další informace o přístupu hostů tenanta Azure Active Directory (Azure AD), přečtěte si [uživatele přidat Azure Active Directory s B2B spolupráce na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Postupujte podle těchto kroků k udělení přístupu hostů k prostředí Time Series Insights pro uživatele služby Azure AD z jiného tenanta.

1. Vyberte **zásady přístupu k datům**a pak vyberte **+ pozvat**.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Zadejte e-mailovou adresu uživatele, kterého chcete pozvat. Tato e-mailová adresa musí být službou Azure AD. Může volitelně zahrnovat e-mailové pozvánce osobní zprávu.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Vyhledejte bublin potvrzení, které se zobrazí na obrazovce.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Zvolte **vybrat uživatele**. Hledání e-mailová adresa uživatele typu Host, který je pozval vyhledejte uživatele, kterého chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Zvolte **vybrat roli**. Zvolte odpovídající přístup role pro uživatele typu Host:

    * Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.

    * V opačném případě vyberte **čtečky** umožní uživateli zadat dotaz na data v prostředí a ukládání osobních, ne sdílených dotazů v prostředí.

   Vyberte **OK** k potvrzení volby role.

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Vyberte **OK** na **vybrat roli uživatele** stránky.

1. Ujistěte se, že **zásady přístupu k datům** stránce uvedeny uživatele typu Host a rolí pro každého uživatele typu Host.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Uživatel typu Host nyní nutné postupovat podle kroků pro přístup k prostředí v tenantovi Azure, ke kterému je pozvaný. Nejdřív přijmout pozvánku, kterou jste jim poslali. Tato pozvánka se odeslala e-mailem na e-mailovou adresu, kterou jste použili v kroku 5. Vyberou **Začínáme** tak, aby přijímal.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. V dalším kroku uživatele typu Host přijímá oprávnění spojená s správce organizace.

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Poté, co uživatel typu Host je podepsán e-mailovou adresu, který jste použili a pozvat je a přijetí e-mailové pozvánce, přejdou na insights.azure.com. Jednou, vyberou avatara u své e-mailovou adresu v pravém horním rohu obrazovky.

    [![Data-access-fourteen](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Další, vybere uživatel typu Host z rozevírací nabídky adresáře tenanta Azure. Tento klient je ten, na který je pozval.

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Poté, co uživatel typu Host vybere vašeho tenanta, zobrazí se jim prostředí Time Series Insights, ke které jste zadali jejich přístupu. Tito uživatelé teď mají všechny požadované možnosti, které jsou přidružené k roli, kterou jste zadali, je v **kroku 5**.

## <a name="next-steps"></a>Další postup

* Přečtěte si [přidání zdroje událostí služby Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Time Series Insights.

* Odeslat [události ke zdroji událostí](./time-series-insights-send-events.md).

* Zobrazení [prostředí v Průzkumníku čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).
