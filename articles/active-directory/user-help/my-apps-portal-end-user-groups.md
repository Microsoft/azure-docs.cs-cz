---
title: Aktualizace informací o skupinách na portálu Moje aplikace – Azure AD
description: Přečtěte si, jak zobrazit a aktualizovat informace týkající se skupin, včetně zobrazení skupin, které vlastníte, vytváření nových skupin, zobrazení skupin, do kterých jste již členem, a připojení ke skupinám, kterých ještě nejste součástí.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022292"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Aktualizace informací o skupinách na portálu Moje aplikace

Pomocí pracovního nebo školního účtu můžete pomocí webového portálu **Moje aplikace** zobrazit a spustit mnoho cloudových aplikací vaší organizace, aktualizovat některé informace o profilu a účtu, zobrazit informace o **skupinách** a provádět **kontroly přístupu** k aplikacím a skupinám. Pokud nemáte přístup k portálu **Moje aplikace,** musíte se obrátit na helpdesk s žádostí o povolení.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, další informace o nastavení a správě cloudových aplikací najdete v [dokumentaci ke správě aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Zobrazení informací o skupinách

Pokud vám správce udělil oprávnění k zobrazení dlaždice **Skupiny,** můžete:

- **Jako člen skupiny.** Zobrazit podrobnosti nebo opustit libovolnou skupinu.

- **Jako vlastník skupiny.** Zobrazení podrobností, vytvoření nové skupiny, přidání nebo odebrání členů nebo odstranění skupiny.

### <a name="to-view-your-groups-information"></a>Zobrazení informací o skupinách

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Otevřete webový prohlížeč https://myapps.microsoft.coma přejděte na stránku nebo použijte odkaz poskytnutý vaší organizací. Můžete být například přesměrováni na přizpůsobenou stránku https://myapps.microsoft.com/contoso.compro vaši organizaci, například .

    Zobrazí se stránka **Aplikace,** která zobrazuje všechny cloudové aplikace vlastněné vaší organizací a dostupné k použití.

    ![Stránka Aplikace na portálu Moje aplikace](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Výběrem dlaždice **Skupiny** zobrazíte informace týkající se skupiny.

    ![Stránka Skupiny s vlastněnými i člennými skupinami](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Na základě vašich oprávnění můžete na stránce **Skupiny:**

    - **Zkontrolujte skupiny, které vlastníte.** Zobrazení informací o všech skupinách, které vlastníte ve vaší organizaci, z oblasti **Skupiny, které vlastním.** Výběr konkrétního názvu skupiny poskytuje další podrobnosti o skupině, včetně typu skupiny, počtu členů, zásady připojení a seznamu aktivních členů.

    - **Vytvořte novou skupinu.** Vytvořte novou skupinu s vámi jako vlastníkem z oblasti **Skupiny, které vlastním.** Konkrétní kroky naleznete v části [Vytvoření nové skupiny](#create-a-new-group) v tomto článku.

    - **Úprava existující skupiny.** Upravte podrobnosti pro všechny své vlastní skupiny. Konkrétní kroky najdete v tomto článku v části [Úpravy existující skupiny.](#edit-an-existing-group)

    - **Přidejte nebo odeberte členy.** Přidejte nebo odeberte členy pro skupiny, které vlastníte. Konkrétní kroky najdete v části [Přidání nebo odebrání člena](#add-or-remove-a-member) tohoto článku.

    - **Obnovte skupinu Office 365.** Pokud to vaše organizace umožňuje, můžete obnovit skupiny Office 365. Konkrétní kroky najdete v tomto článku v článku [Obnovení skupiny Office 365.](#renew-an-office-365-group) 

    - **Odstraňte skupinu.** Odstraňte všechny skupiny, které vlastníte. Konkrétní kroky naleznete v části [Odstranění skupiny](#delete-a-group) v tomto článku.

    - **Zkontrolujte skupiny, kterých jste součástí.** Zobrazte názvy všech skupin, do kterých jste členem, ze skupin, ve **kterých jsem.** Výběr konkrétního názvu skupiny poskytuje další podrobnosti o skupině, včetně typu skupiny, počtu členů, zásady připojení a seznamu aktivních členů.

    - **Připojte se ke skupině.** Připojte se k existující skupině, jejíž členem ještě nejste, ze skupin, ve **kterých jsem.** Konkrétní kroky najdete v tématu [Připojení k existující skupině](#join-an-existing-group).

## <a name="create-a-new-group"></a>Vytvoření nové skupiny

1. Na stránce **Skupiny** vyberte **Vytvořit skupinu** z oblasti **Skupiny, které vlastním.**

    Zobrazí se pole **Vytvořit skupinu.**

    ![Vytvořit pole skupiny](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Zadejte požadované informace:

    - **Typ skupiny:**

        - **Zabezpečení.** Slouží ke správě členů a přístupu počítače ke sdíleným prostředkům u skupiny uživatelů. Můžete například vytvořit skupinu zabezpečení pro konkrétní zásady zabezpečení. Tímto způsobem můžete udělit sadu oprávnění všem členům najednou a nemusíte přidávat oprávnění členům jednotlivě.

        - **Office 365.** Poskytuje možnosti spolupráce tím, že umožňuje členům přístup ke sdílené poštovní schránce, kalendáři, souborům, sharepointovému webu a dalším prostředkům. Tato možnost vám také umožňuje udělit přístup ke skupině lidem mimo vaši organizaci.

    - **Název skupiny.** Přidejte název skupiny. Zvolte název, který si zapamatujete a který bude dávat smysl.

    - **Popis skupiny (nepovinné).** Volitelně můžete přidat také popis skupiny.

    - **Zásady skupiny.** Chcete-li povolit, aby se ke skupině připojili všichni uživatelé, nebo chcete povolit přidání členů pouze vlastníkovi skupiny.

3. Vyberte **Vytvořit**.

    Nová skupina je vytvořena s vámi jako vlastníkem a zobrazí se ve **vašich skupinách, které vlastním.** Vzhledem k tomu, že jste vlastníkem, tato skupina se také zobrazí v seznamu **Skupiny, ve kterých jsem.**

## <a name="edit-an-existing-group"></a>Úprava existující skupiny

Po vytvoření skupiny můžete upravit její podrobnosti, včetně aktualizace všech existujících informací.

1. Na stránce **Skupiny** vyberte skupinu, kterou chcete upravit, a pak na * &lt;stránce group_name&gt; * vyberte Upravit **podrobnosti.**

    Zobrazí se pole **Upravit podrobnosti** a můžete aktualizovat informace, které jste přidali při počátečním vytvoření skupiny.

2. Proveďte všechny změny a pak vyberte **Aktualizovat**.

## <a name="add-or-remove-a-member"></a>Přidání nebo odebrání člena

Členy můžete přidat nebo odebrat pro všechny skupiny, které vlastníte.

1. Vyberte skupinu, do které chcete **+** členy přidat, a pak vyberte na * &lt;stránce group_name.&gt; *

    ![Přidání člena skupiny se zvýrazněnou značkou +](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Vyhledejte člena, kterého chcete přidat, v poli **Přidat členy** a pak vyberte **Přidat**.

    ![Pole Přidat členy s novým členem, který chcete přidat](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Novému členovi je odeslána pozvánka, která začne přistupovat k aplikacím organizace.

3. Pokud jste přidali člena omylem nebo pokud člen opustil vaši organizaci, můžete ho odebrat výběrem **možnosti Odebrat člena** vedle jména člena na * &lt;&gt; stránce group_name.*

    ![Odebrání člena se zvýrazněným odkazem na odebrání](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Obnovení skupiny Office 365

Pokud to vaše organizace umožňuje, můžete obnovit skupinu Office 365 a prodloužit datum vypršení platnosti.

1. Vyberte skupinu Office 365, kterou chcete obnovit, a pak vyberte **Obnovit skupinu**.

    ![Obnovení skupiny Office 365 prodlužuje datum vypršení platnosti](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Klepnutím na **tlačítko OK** zavřete potvrzovací zprávu.

    Po aktualizaci stránky se zobrazí aktualizované datum vypršení platnosti **poslední ho roku a** vypršení **platnosti skupiny.**

## <a name="delete-a-group"></a>Odstranění skupiny

Libovolnou skupinu můžete kdykoli odstranit. Pokud však skupinu omylem odstraníte, budete ji muset vytvořit a znovu přidat členy.

1. Vyberte skupinu, kterou chcete trvale odstranit, a pak na * &lt;stránce group_name&gt; * vyberte Odstranit **skupinu.**

    ![<Group_name> stránka se zvýrazněným odkazem Odstranit skupinu](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. V potvrzovací zprávě vyberte **Ano.**

    Skupina je trvale odstraněna.

## <a name="join-an-existing-group"></a>Připojení k existující skupině

Na stránce **Skupiny** se můžete připojit nebo ji opustit.

1. Na stránce **Skupiny** vyberte **Připojit se ke skupině** v oblasti **Skupiny, ve kterých jsem.**

    Zobrazí se stránka **Skupiny spojení.**

    ![Stránka Připojit se ke skupinám se zvýrazněným tlačítkem Připojit se ke skupině](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na stránce **Připojit skupiny** vyberte název skupiny, ke které se chcete připojit, zobrazte podrobnosti přidružené skupiny a pak, pokud je skupina dostupná, vyberte **Připojit skupinu**.

    Pokud skupina vyžaduje, aby vlastník skupiny schválil členství, budete vyzváni k zadání obchodního odůvodnění, proč se musíte ke skupině připojit, a pak vyberte **Požádat**. Pokud skupina nevyžaduje schválení, budete okamžitě přidáni jako člen a skupina se zobrazí ve **vašich skupinách, které jsem v** seznamu.

3. Pokud jste se ke skupině připojili omylem nebo už nepotřebujete být její součástí, můžete vybrat název skupiny na stránce **Připojit se ke skupinám** a potom vybrat možnost Opustit **skupinu**.

    ![Stránka Připojit skupiny se zvýrazněným tlačítkem Opustit skupinu](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Další kroky

- [Přístup k aplikacím a jejich používání na portálu Moje aplikace](my-apps-portal-end-user-access.md).

- [Změňte informace o svém profilu](my-apps-portal-end-user-update-profile.md).

- [Proveďte vlastní kontroly přístupu](my-apps-portal-end-user-access-reviews.md).
