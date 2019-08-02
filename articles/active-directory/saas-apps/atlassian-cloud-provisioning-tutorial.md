---
title: 'Kurz: Konfigurace Atlassian cloudu pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k Atlassian cloudu.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0c3173841de25a30b84870332c7334a81773e84d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561587"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace Atlassian cloudu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Atlassian cloudu a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro Atlassian Cloud.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Cloudový tenant Atlassian](https://www.atlassian.com/licensing/cloud)
* Uživatelský účet v cloudu Atlassian s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na **rozhraní Atlassian Cloud SCIM API**, které je dostupné pro Atlassian cloudové týmy.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Přidání cloudu Atlassian z Galerie

Před konfigurací Atlassian cloudu pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat Cloud Atlassian z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Atlassian Cloud z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Atlassian Cloud**, na panelu výsledků vyberte **Cloud Atlassian** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Atlassian Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Přiřazení uživatelů k Atlassian cloudu

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup ke cloudu Atlassian. Jakmile se rozhodnete, můžete tyto uživatele nebo skupiny přiřadit k Atlassian cloudu podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Důležité tipy pro přiřazení uživatelů ke Atlassian cloudu

* Doporučuje se, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k Atlassian cloudu. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k Atlassian cloudu, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurace automatického zřizování uživatelů pro Atlassian Cloud 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v cloudu Atlassian na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Atlassian Cloud, a to podle pokynů uvedených v [kurzu Atlassian Cloud Single Signing](atlassian-cloud-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Atlassian Cloud v Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Cloud Atlassian**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cloud Atlassian**.

    ![Odkaz Atlassian Cloud v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** účtu Atlassian vašeho cloudu. Příklady těchto hodnot:

   * Do pole **Adresa URL tenanta** zadejte konkrétní koncový bod tenanta, který obdržíte od Atlassian, jak je popsáno v kroku 6. Například: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * V poli **token tajného klíče** vyplňte tajný token, jak je popsáno v kroku 6.

6. Přejděte na [Atlassian Organization Manager](https://admin.atlassian.com) **> zřizování uživatelů** a klikněte na **vytvořit token**. Zkopírujte **základní adresu URL adresáře** a **nosný token** do polí **Adresa URL tenanta** a **tajného tokenu** v uvedeném pořadí.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) Cloud ![Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k Atlassian cloudu. Pokud se připojení nepovede, zajistěte, aby měl váš cloudový účet Atlassian oprávnění správce, a zkuste to znovu.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. V poli **e-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé a Atlassian Cloud**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Atlassian cloudu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Atlassian cloudu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do cloudu Atlassian**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do Atlassian cloudu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v cloudu Atlassian pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Cloud Atlassian, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro Atlassian Cloud, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Zřizování cloudu Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v cloudu Atlassian.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Atlassian Cloud umožňuje zřizování uživatelů jenom z ověřených [domén](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud nepodporuje přejmenování skupin ještě dnes. To znamená, že jakékoli změny DisplayName ve skupině ve službě Azure AD nebudou aktualizovány a projeví se v Atlassian cloudu.
* Hodnota atributu uživatel **pošty** ve službě Azure AD se naplní jenom v případě, že má uživatel poštovní schránku Microsoft Exchange. Pokud uživatel ho nemá, doporučuje se namapovat jiný požadovaný atribut na atribut **Emails** v Atlassian cloudu.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
