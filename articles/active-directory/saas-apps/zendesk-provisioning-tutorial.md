---
title: 'Kurz: Konfigurace platformy Zendesk pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Zendesku.
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
ms.date: 01/31/2018
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e56cc5a893c5a88a5b64466d6feceb20ccd8cdc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167843"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Kurz: Konfigurace platformy Zendesk pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v systému Zendesk a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Zendesku. 

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

*   Klient služby Azure AD
*   Zendesk tenantovi se [Enterprise](https://www.zendesk.com/product/pricing/) plán nebo lépe povoleno 
*   Uživatelský účet v systému Zendesk s oprávněními správce 

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [rozhraní Rest API služby Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), což je k dispozici pro týmy Zendesku v plánu Enterprise nebo vyšší.

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesku z Galerie
Před konfigurací Zendesk pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Zendesku z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zendesku z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]
    
3. Chcete-li přidat Zendesku, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zendesku**.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. Na panelu výsledků vyberte **Zendesku**a potom klikněte na tlačítko **přidat** tlačítko pro přidání do seznamu aplikací SaaS Zendesku.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Přiřazování uživatelů k Zendesku.

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k službě Zendesk. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny k službě Zendesk podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Důležité tipy pro přiřazování uživatelů k Zendesku.

*    Role Zendesku se automaticky a dynamicky vyplní na webu Azure Portal uživatelského rozhraní ještě dnes. Před přiřazením Zendesku role pro uživatele, ujistěte se, že počáteční synchronizace je dokončena proti Zendesku načíst nejnovější role ve vašem tenantovi Zendesku.

*    Dále je doporučeno jednoho uživatele Azure AD je přiřazena k službě Zendesk k testování vašich počáteční automatické zřizování uživatelů konfigurace. Další uživatele a/nebo skupiny může být přiřazena vyšší Jakmile testy jsou úspěšné.
  
*   Dále je doporučeno jednoho uživatele Azure AD je přiřazena k službě Zendesk otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazování uživatele do Zendesku, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Konfigurace automatické zřizování uživatelů do Zendesku. 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v systému Zendesk podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Zendesk, postupujte podle pokynů uvedených v [Zendesku jednotné přihlašování – kurz](zendesk-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Zendesk ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS Zendesku.
 
    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. Vyberte **zřizování** kartu.
    
    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. V části **přihlašovacích údajů správce** části, zadejte **uživatelské jméno správce**, **tajný klíč tokenu**, a **domény** účtu vaší Zendesku. Mezi tyto hodnoty patří:

    *   V **uživatelské jméno správce** pole, naplnění uživatelské jméno účtu správce ve svém tenantovi Zendesku. Příklad: admin@contoso.com.

    *   V **tajný klíč tokenu** pole, vyplňte token tajného kódu, jak je popsáno v kroku 6.

    *   V **domény** pole, naplnění subdoménu tenanta Zendesku.
    Příklad: Pro účet s adresou URL tenanta https://my-tenant.zendesk.com, bude vaše subdoménu **Moje tenanta**.

6. **Tajný klíč tokenu** Zendesku. váš účet se nachází v **správce > rozhraní API > Nastavení**. 

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![zřizování Zendesku.](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k službě Zendesk. Pokud se nepovede, ujistěte se, že má oprávnění správce vašeho účtu Zendesku a zkuste to znovu.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure uživatelé služby Active Directory po Zendesk**.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Zendesku v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelských účtů v systému Zendesk pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory po ZenDesk**.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD k Zendesku v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v systému Zendesk pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Služba pro Zendesk zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení k službě Zendesk výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Zřizování platformy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba v Zendesku zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru
* Zendesk podporuje použití skupin pro uživatele s pouze role agenta. Další informace najdete [dokumentaci společnosti Zendesku](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).
* Při vlastní role je přiřazená uživatele a/nebo skupiny, Azure AD automatické zřizování uživatelů služby také přiřadí výchozí role **agenta**. Pouze **agentů** je možné přiřadit vlastní roli. Další informace najdete v této [dokumentace k rozhraní API platformy Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
