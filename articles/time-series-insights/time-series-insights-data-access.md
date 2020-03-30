---
title: Konfigurace zabezpečení pro udělení přístupu k datům – Azure Time Series Insights Preview | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat zabezpečení, oprávnění a spravovat zásady přístupu k datům v prostředí Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254349"
---
# <a name="grant-data-access-to-an-environment"></a>Udělení přístupu k datům do prostředí

Tento článek popisuje dva typy zásad přístupu Azure Time Series Insights Preview.

> [!TIP]
> Přečtěte si kroky registrace aplikací Azure Active Directory ověřování [a autorizace.](time-series-insights-authentication-and-authorization.md)

## <a name="sign-in-to-time-series-insights"></a>Přihlaste se k přehledům time series

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Vyhledejte prostředí Time Series Insights. Zadejte `Time Series` **do** vyhledávacího pole. Ve výsledcích hledání vyberte **prostředí časové řady.**
1. Ze seznamu vyberte vaše prostředí Time Series Insights.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Chcete-li udělit přístup k datům pro objekt zabezpečení uživatele, postupujte takto.

1. Vyberte **Zásady přístupu k datům**a pak vyberte **+ Přidat**.

    [![Výběr a přidání zásad přístupu k datům](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Zvolte **Vybrat uživatele**. Vyhledejte uživatele, kterého chcete přidat, vyhledejte uživatelské jméno nebo e-mailovou adresu. Výběrem **možnosti Výběr** výběru potvrďte.

    [![Vyberte uživatele, který má být](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Zvolte **Vybrat roli**. Zvolte příslušnou přístupovou roli pro uživatele:

    * Pokud chcete uživateli povolit změnu referenčních dat a sdílet uložené dotazy a perspektivy s ostatními uživateli prostředí, vyberte **přispěvatele.**

    * V opačném případě vyberte **Reader,** aby uživatel mohl dotazovat data v prostředí a ukládat osobní, nesdílené dotazy v prostředí.

   Výběrem **možnosti OK** potvrďte volbu role.

    [![Potvrzení vybrané role](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Na stránce **Vybrat roli uživatele** vyberte **OK.**

    [![Výběr OK na stránce Vybrat roli uživatele](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Zkontrolujte, zda je na stránce **Zásady přístupu k datům** uvedeno seznam uživatelů a rolí jednotlivých uživatelů.

    [![Ověření správných uživatelů a rolí](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Poskytnutí přístupu hosta z jiného klienta Azure AD

Role `Guest` není role řízení. Je to termín používaný pro účet, který je pozván z jednoho klienta do druhého. Po pozvání účtu hosta do adresáře klienta může mít stejný ovládací prvek přístupu, který se na něj použije jako u jiných účtů. Přístup ke správě prostředí Time Series Insights můžete udělit pomocí okna Řízení přístupu (IAM). Nebo můžete udělit přístup k datům v prostředí prostřednictvím okna Zásady přístupu k datům. Další informace o přístupu hosta hosta klienta Azure Active Directory (Azure AD) nabloguy [o službě Azure Active Directory nawebu: Přidání uživatelů spolupráce Služby Azure AD B2B na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Podle těchto kroků udělit hostpřístup k prostředí Time Series Insights pro uživatele Azure AD z jiného klienta.

1. Vyberte **Zásady přístupu k datům**a pak vyberte **+ Invite**.

    [![Vyberte zásady přístupu k datům a pak + Pozvat](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Zadejte e-mailovou adresu uživatele, kterého chcete pozvat. Tato e-mailová adresa musí být přidružená k Azure AD. Volitelně můžete k pozvánce zahrnout osobní zprávu.

    [![Zadáním e-mailové adresy vyhledejte vybraného uživatele.](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Vyhledejte potvrzovací bublinu, která se zobrazí na obrazovce.

    [![Vyhledejte potvrzovací bublinu, která se zobrazí](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Zvolte **Vybrat uživatele**. Vyhledejte e-mailovou adresu uživatele typu Host, kterého jste pozvali k vyhledání uživatele, kterého chcete přidat. Potom **vyberte,** chcete-li výběr potvrdit.

    [![Vyberte uživatele a potvrďte výběr](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Zvolte **Vybrat roli**. Zvolte příslušnou přístupovou roli pro uživatele typu Host:

    * Pokud chcete uživateli povolit změnu referenčních dat a sdílet uložené dotazy a perspektivy s ostatními uživateli prostředí, vyberte **přispěvatele.**

    * V opačném případě vyberte **Reader,** aby uživatel mohl dotazovat data v prostředí a ukládat osobní, nesdílené dotazy v prostředí.

   Výběrem **možnosti OK** potvrďte volbu role.

    [![Potvrzení volby role](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Na stránce **Vybrat roli uživatele** vyberte **OK.**

1. Zkontrolujte, zda je na stránce **Zásady přístupu k datům** uvedeno uložit uživatele typu Host a role každého uživatele typu Host.

    [![Ověření správného přiřazení uživatelů a rolí](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Nyní uživatel hostobdrží e-mail s pozvánkou na výše uvedenou e-mailovou adresu. Uživatel typu Host vybere **Možnost Začínáme,** aby potvrdil jejich přijetí a připojil se k Azure Cloudu.

    [![Host vybere Možnost Začít přijímat.](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Po výběru **možnosti Začínáme**se uživateli typu Host zobrazí pole oprávnění přidružené k organizaci správce. Po udělení oprávnění výběrem možnosti **Přijmout**budou přihlášeni.

    [![Host kontroluje oprávnění a přijímá](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Správce [sdílí adresu URL prostředí](time-series-insights-parameterized-urls.md) se svým hostem.

1. Poté, co je uživatel typu Host přihlášen k e-mailové adrese, kterou jste použili k pozvání, a on i přijmout pozvánku, bude přesměrován na portál Azure. 

1. Host má nyní přístup ke sdílenému prostředí pomocí adresy URL prostředí poskytnuté správcem. Mohou zadat tuto adresu URL do svého webového prohlížeče pro okamžitý přístup.

1. Klient správce se zobrazí uživateli hosta po výběru ikony profilu v pravém horním rohu průzkumníka časové řady.

    [![Avatar výběr na insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Poté, co uživatel typu Host vybere klienta správce, bude mít možnost vybrat sdílené prostředí Time Series Insights. 
    
    Nyní mají všechny funkce spojené s rolí, kterou jste jim poskytli v **kroku 5**.

    [![Uživatel Typu Host vybere vašeho klienta Azure z rozbalovacího seznamu.](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak přidat zdroj událostí centra Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Time Series Insights.

* Odešle [události do zdroje událostí](./time-series-insights-send-events.md).

* Zobrazení [prostředí v průzkumníku Náhled přehledů časové řady](./time-series-insights-update-explorer.md).
