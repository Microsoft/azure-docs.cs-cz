---
title: Konfigurace zabezpečení pro udělení přístupu k datům – Azure Time Series Insights | Microsoft Docs
description: Naučte se konfigurovat zabezpečení, oprávnění a správu zásad přístupu k datům v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780854"
---
# <a name="grant-data-access-to-an-environment"></a>Udělení přístupu k datům do prostředí

Tento článek pojednává o dvou typech zásad přístupu Azure Time Series Insights.

## <a name="sign-in-to-azure-time-series-insights"></a>Přihlášení k Azure Time Series Insights

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Zadejte `Time Series Insights environments` do **vyhledávacího** pole své prostředí Azure Time Series Insights. `Time Series Insights environments`Ve výsledcích hledání vyberte.
1. Ze seznamu vyberte prostředí Azure Time Series Insights.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Pomocí těchto kroků udělíte přístup k datům pro objekt zabezpečení uživatele.

1. Vyberte **zásady přístupu k datům** a pak vybrat **+ Přidat**.

    [![Výběr a přidání zásad přístupu k datům](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Zvolte **Vybrat uživatele**. Vyhledejte uživatelské jméno nebo e-mailovou adresu a vyhledejte uživatele, kterého chcete přidat. Vyberte **možnost vybrat** a potvrďte výběr.

    [![Vyberte uživatele, kterého chcete přidat.](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Zvolte **Vybrat roli**. Vyberte příslušnou roli přístupu pro uživatele:

    * Vyberte **Přispěvatel** , pokud chcete, aby uživatel mohl měnit referenční data a sdílet uložené dotazy a perspektivy s ostatními uživateli tohoto prostředí.

    * V opačném případě vyberte **Čtenář** , který uživateli umožní dotazovat se na data v prostředí a ukládat osobní, nesdílené a dotazy v prostředí.

   Výběrem **OK** potvrďte volbu role.

    [![Potvrďte zvolenou roli.](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Na stránce **Vybrat roli uživatele** vyberte **OK** .

    [![Na stránce Vybrat roli uživatele vyberte OK.](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Potvrďte, že na stránce **zásady přístupu k datům** jsou uvedeni uživatelé a role pro každého uživatele.

    [![Ověření správných uživatelů a rolí](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Poskytnutí přístupu hosta z jiného tenanta Azure AD

Tato `Guest` role není rolí správy. Jedná se o termín, který se používá pro účet, který je pozván z jednoho klienta na jiný. Po pozvání účtu hosta do adresáře klienta může být pro něj použito stejné řízení přístupu jako u jakéhokoli jiného účtu. Přístup pro správu můžete Azure Time Series Insights prostředí udělit pomocí okna Access Control (IAM). Nebo můžete přístup k datům v prostředí udělit prostřednictvím okna zásady přístupu k datům. Pokud chcete získat další informace o přístupu klienta Azure Active Directory (Azure AD) k hostům, přečtěte si téma [přidání Azure Active Directory uživatelů pro spolupráci B2B v Azure Portal](../active-directory/external-identities/add-users-administrator.md).

Pomocí těchto kroků udělíte uživateli služby Azure AD přístup hosta k Azure Time Series Insights prostředí z jiného tenanta.

1. Přejděte na Azure Portal, klikněte na  **Azure Active Directory**, přejděte dolů na kartu **Přehled** a pak vyberte **uživatel typu Host**.

    [![Vyberte zásady přístupu k datům, potom + pozvání.](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Zadejte e-mailovou adresu uživatele, kterého chcete pozvat. Tato e-mailová adresa musí být přidružená k Azure AD. Volitelně můžete přidat osobní zprávu s pozvánkou.

    [![Zadejte e-mailovou adresu pro vyhledání vybraného uživatele.](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Vyhledejte bublinu s potvrzením, která se zobrazí na obrazovce. Můžete také kliknout na **oznámení** a potvrdit, že uživatel typu Host byl přidán.

    [![Vyhledejte bublinu s potvrzením, které se má zobrazit.](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Vraťte se do prostředí Time Series Insights a přidejte nově vytvořeného uživatele typu Host. Klikněte na **zásady přístupu k datům** , jak je popsáno v části **udělení přístupu k datům**. **Vyberte možnost uživatel**. Vyhledejte e-mailovou adresu uživatele typu Host, kterého jste pozvali, a vyhledejte uživatele, kterého chcete přidat. Pak **výběrem** potvrďte výběr.

    [![Vyberte uživatele a potvrďte výběr.](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Zvolte **Vybrat roli**. Vyberte příslušnou roli přístupu pro uživatele typu Host:

    * Vyberte **Přispěvatel** , pokud chcete, aby uživatel mohl měnit referenční data a sdílet uložené dotazy a perspektivy s ostatními uživateli tohoto prostředí.

    * V opačném případě vyberte **Čtenář** , který uživateli umožní dotazovat se na data v prostředí a ukládat osobní, nesdílené a dotazy v prostředí.

   Výběrem **OK** potvrďte volbu role.

    [![Potvrďte volbu role](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Na stránce **Vybrat roli uživatele** vyberte **OK** .

1. Potvrďte, že na stránce **zásady přístupu k datům** se zobrazuje uživatel typu Host a role pro každého uživatele typu Host.

    [![Ověření správného přiřazení uživatelů a rolí](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Nyní uživatel typu Host dostane e-mail s pozvánkou na e-mailovou adresu uvedenou výše. Uživatel typu host vybere možnost **Začínáme, aby** potvrdil přijetí a připojení ke cloudu Azure.

    [![Host vybere začátek pro přijetí.](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Po výběru **možnosti Začínáme se** uživateli typu Host zobrazí pole oprávnění přidružené k organizaci správce. Po udělení oprávnění výběrem možnosti **přijmout** se přihlásí.

    [![Host kontroluje oprávnění a přijímá](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Správce [nasdílí adresu URL prostředí](time-series-insights-parameterized-urls.md) s jejich hostem.

1. Jakmile je uživatel typu Host přihlášený k e-mailové adrese, kterou jste použili k jejich pozvání, a pozvánku přijme, budou přesměrováni na Azure Portal.

1. Host teď může přistupovat ke sdílenému prostředí pomocí adresy URL prostředí poskytnuté správcem. Můžou zadat tuto adresu URL do svého webového prohlížeče pro okamžitý přístup.

1. Po výběru ikony profilu v pravém horním rohu Průzkumníka časových řad se tenant správce zobrazí uživateli typu Host.

    [![Výběr avataru na insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Jakmile uživatel typu host vybere tenanta správce, bude mít možnost vybrat sdílené prostředí Azure Time Series Insights.

    Nyní mají všechny možnosti spojené s rolí, které jste jim poskytli v **kroku 5**.

    [![Uživatel typu host vybere z rozevíracího seznamu klienta Azure.](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [ověřování a autorizace](time-series-insights-authentication-and-authorization.md) pro Azure Active Directory kroky registrace aplikace.

* Zobrazte [si prostředí v průzkumníkovi Azure Time Series Insights](./concepts-ux-panels.md).
