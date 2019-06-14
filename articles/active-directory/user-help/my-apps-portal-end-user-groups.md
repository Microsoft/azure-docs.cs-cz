---
title: Aktualizovat údaje skupiny z portálu Moje aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit a aktualizovat skupiny související informace, včetně zobrazení skupin, které vlastníte, vytváření nových skupin, zobrazení skupin, ke kterým jste již členem a připojení k žádné skupiny ještě nejsou součástí.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578ca5fa1de338a92280e2dc7cc252fb616e2111
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706695"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Aktualizovat údaje skupiny z portálu Moje aplikace
Můžete použít pracovní nebo školní účet s webovou **Moje aplikace** portál, zobrazit a spustit řadu aplikace vaší organizace založené na cloudu, chcete-li aktualizovat některé z vašeho profilu a účtu informace zobrazíte vaše **skupiny** informace a k provádění **kontrol přístupu** vašich aplikací a skupin. Pokud nemáte přístup k **Moje aplikace** portálu, musíte požádat o Helpdesk oprávnění.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o tom, jak nastavit a spravovat vaše cloudové aplikace v [aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Zobrazit informace o skupinách
Pokud vám správce udělil oprávnění k zobrazení **skupiny** dlaždice, můžete:

- **Jako člena skupiny.** Zobrazit podrobnosti nebo nechte libovolnou skupinu.

- **Jako vlastníka skupiny.** Zobrazit podrobnosti, vytvořte novou skupinu, přidat nebo odebrat členy nebo odstraňte vaši skupinu.

### <a name="to-view-your-groups-information"></a>Chcete-li zobrazit informace o skupinách

1.  Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2.  Otevřete webový prohlížeč a přejděte na https://myapps.microsoft.com, nebo použijte odkaz, poskytnuté vaší organizací. Například je může přesměrováni na upravené stránky pro vaši organizaci, jako https://myapps.microsoft.com/contoso.com.

    **Aplikace** vlastněné vaší organizací a k dispozici pro použití se zobrazí stránka zobrazuje všechny cloudové aplikace.

    ![Stránky aplikací na portálu Moje aplikace](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Vyberte **skupiny** dlaždici zobrazíte informace týkající se skupiny.

    ![Stránce skupiny v rámci vlastní a členské skupiny](media/my-apps-portal/my-apps-portal-groups-page.png)

4. V závislosti na vašem oprávnění, můžete využít **skupiny** stránce:

    - **Zkontrolujte skupiny, které vlastníte.** Zobrazit informace o všech skupin, které vlastní v rámci vaší organizace z **skupiny moje** oblasti. Vyberte název určité skupiny poskytuje další podrobnosti o skupiny, včetně typ skupiny, počet členů, zásady připojení a seznam aktivních členů.

    - **Vytvořte novou skupinu.** Vytvořte novou skupinu jako vlastníka z s vámi **skupiny moje** oblasti. Konkrétní pokyny najdete v článku [vytvořte novou skupinu](#create-a-new-group) části tohoto článku.

    - **Upravte existující skupinu.** Upravte podrobnosti pro všechny vlastní skupiny. Konkrétní pokyny najdete v článku [upravit existující skupinu](#edit-an-existing-group) části tohoto článku.

    - **Přidat nebo odebrat členy.** Přidat nebo odebrat členy skupin, které vlastníte. Konkrétní pokyny najdete v článku [přidat nebo odebrat člena](#add-or-remove-a-member) části tohoto článku.

    - **Prodlužte platnost skupiny Office 365.** Pokud vaše organizace umožňuje, můžete obnovit vaše skupiny Office 365. Konkrétní pokyny najdete v článku [obnovit skupinu Office 365](#renew-an-office-365-group) části tohoto článku. 

    - **Odstraňte skupinu.** Odstraňte všechny skupiny, které vlastníte. Konkrétní pokyny najdete v článku [odstranit skupinu](#delete-a-group) části tohoto článku.

    - **Zkontrolujte, že jste součástí skupiny.** Zobrazit názvy skupin, které jste členem z **skupiny ve** oblasti. Vyberte název určité skupiny poskytuje další podrobnosti o skupiny, včetně typ skupiny, počet členů, zásady připojení a seznam aktivních členů.

    - **Připojte se k skupinu.** Připojte se k existující skupinu, pro který ještě nejste členem, z **skupiny ve** oblasti. Konkrétní pokyny najdete v článku [připojit k existující skupině](#join-an-existing-group).

## <a name="create-a-new-group"></a>Vytvoření nové skupiny
1. Na **skupiny** stránce **vytvořte skupinu** z **skupiny moje** oblasti.

    **Vytvořit skupinu** se zobrazí okno.

    ![Vytvořit skupinový rámeček](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Zadejte požadované informace:

    - **Typ skupiny:**
        
        - **Zabezpečení.** Slouží ke správě členů a přístupu počítače ke sdíleným prostředkům u skupiny uživatelů. Můžete například vytvořit skupinu zabezpečení pro konkrétní zásady zabezpečení. Tímto způsobem můžete udělit sadu oprávnění všem členům najednou a nemusíte přidávat oprávnění členům jednotlivě.

        - **Office 365.** Poskytuje možnosti spolupráce tím, že umožňuje členům přístup ke sdílené poštovní schránce, kalendáři, souborům, sharepointovému webu a dalším prostředkům. Tato možnost vám také umožňuje udělit přístup ke skupině lidem mimo vaši organizaci.

    - **Název skupiny.** Přidejte název skupiny. Zvolte název, který si zapamatujete a který bude dávat smysl.

    - **Popis skupiny (nepovinné).** Volitelně můžete přidat také popis skupiny.

    - **Zásady skupiny.** Zvolte, aby umožnit všem uživatelům připojit ke skupině nebo Povolit jenom vlastník skupiny pro přidání členů.

3. Vyberte **Vytvořit**.

    Bude vytvořena nová skupina s vámi jako vlastník a zobrazí se ve vaší **skupiny moje** seznamu. Protože jste vlastníkem, tato skupina se zobrazí také v **skupiny ve** seznamu.

## <a name="edit-an-existing-group"></a>Upravit existující skupinu
Po vytvoření skupiny můžete upravit jeho podrobnosti, včetně všechny stávající informace o aktualizaci.

### <a name="to-edit-your-details"></a>Chcete-li upravit podrobnosti o vašem
1. Vyberte skupinu, kterou chcete upravit z **skupiny** stránce a pak vyberte **upravit podrobnosti** na *&lt;group_name&gt;* stránky.

    **Upravit podrobnosti** se zobrazí okno a aktualizujete informace, které jste přidali, když jste původně vytvořili skupinu.

2. Proveďte všechny změny a pak vyberte **aktualizace**.

## <a name="add-or-remove-a-member"></a>Přidat nebo odebrat člena
Můžete přidat nebo odebrat členy pro všechny vlastní skupiny.

### <a name="to-add-or-remove-a-member"></a>Chcete-li přidat nebo odebrat člena
1. Vyberte skupinu, kterou chcete přidat členy do a pak vyberte **+** na *&lt;group_name&gt;* stránky.

    ![Přidat člena skupiny s + zvýrazněnou přihlašování](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Hledání pro člena, které chcete přidat, ze **přidat členy** a potom vyberte **přidat**.

    ![Přidat členy pole s přidáte nového člena](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Pozvánku posílá do nového člena, abyste mohli začít, kteří přistupují k aplikacím organizace.

3. Pokud jste přidali člen omylem nebo pokud Člen opustil organizaci, můžete odebrat člena výběrem **člena odebrat** u názvu člena *&lt;group_name&gt;* stránky.

    ![Odebrat člena, se zvýrazněným odkazem odebrání](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Obnovit skupinu Office 365
Pokud vaše organizace umožňuje, můžete obnovit skupinu Office 365, rozšíří vaše datum vypršení platnosti.

### <a name="to-renew-a-group"></a>Chcete-li obnovit skupinu
1. Vyberte skupinu Office 365, kterou chcete obnovit a pak vyberte **obnovit skupinu**.

    ![Obnovit skupinu Office 365, rozšíření datum vypršení platnosti](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Klikněte na tlačítko **OK** potvrzovací zprávu zavřete.

    Po aktualizaci stránky uvidíte aktualizovaný **poslední obnovit** a **skupině vypršení platnosti** kalendářní data.

## <a name="delete-a-group"></a>Odstranění skupiny
Kdykoli můžete odstranit jakékoli vlastní skupiny. Nicméně pokud omylem odstraníte skupinu budete mít a vytvořte ho znovu přidat členy.

### <a name="to-delete-the-group"></a>Pokud chcete odstranit skupinu
1. Vyberte skupinu, kterou chcete trvale odstranit a pak vyberte **odstranit skupinu** na *&lt;group_name&gt;* stránky.

    ![< Group_name > stránka se zvýrazněným odkazem skupiny Delete](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Vyberte **Ano** v potvrzovací zprávě.

    Skupiny je trvale odstraněn. 
    
## <a name="join-an-existing-group"></a>Připojte se k existující skupinu
Již existující skupiny z se můžete zapojit **skupiny** stránky.

### <a name="to-join-or-leave-a-group"></a>Připojit nebo opuštění skupiny

1. Na **skupiny** stránce **připojit se ke skupině** z **skupiny ve** oblasti.

    **Připojte skupiny** se zobrazí stránka.

    ![Připojte se k stránce skupiny, se zvýrazněným tlačítkem skupiny spojení](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na **připojte skupiny** stránky, vyberte název skupiny, které chcete připojit, zobrazit podrobnosti o přidruženou skupinu a vyberte Pokud je k dispozici, skupina **připojit se ke skupině**.

    Pokud skupina vyžaduje vlastníka skupiny členství schválit, budete požádáni o zadání obchodní odůvodnění, proč je potřeba připojit ke skupině a vyberte **požádat o**. Pokud skupinu nevyžaduje schválení, můžete ihned přidané jako člen a skupina se zobrazí v vaše **skupiny ve** seznamu.

3. Pokud jste se zapojili skupiny omylem nebo pokud už nepotřebujete mít jeho součástí, můžete vybrat název skupiny z **připojte skupiny** stránce a pak vyberte **opustit skupinu**.

    ![Připojte se k stránce skupiny, se zvýrazněným tlačítkem skupiny ponechejte tuto položku](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Další postup

- [Přístup a používání aplikací na portálu Moje aplikace](my-apps-portal-end-user-access.md).

- [Změnit informace z vašeho profilu](my-apps-portal-end-user-update-profile.md).

- [Provést vlastní kontroly přístupu](my-apps-portal-end-user-access-reviews.md).