---
title: 'Kurz: Konfigurace cloudu od společnosti Atlassian pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Cloud od společnosti Atlassian.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant
ms.openlocfilehash: ca9a569d28e42baafeabc15f49fb7f5206566730
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57572162"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Kurz: Konfigurace cloudu od společnosti Atlassian pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v cloudu od společnosti Atlassian a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny pro Cloud od společnosti Atlassian. 

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných Microsoft Azure podmínky použití pro funkce ve verzi Preview, najdete v části [doplňkovými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

*   Klient služby Azure AD
*   [An Atlassian Cloud tenant](https://www.atlassian.com/licensing/cloud)
*   Uživatelský účet v cloudu od společnosti Atlassian s oprávněními správce.

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na **API SCIM Cloud od společnosti Atlassian**, který je dostupný pro Cloud od společnosti Atlassian týmy.

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Přidání Cloud od společnosti Atlassian z Galerie
Před konfigurací Cloud od společnosti Atlassian pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Cloud od společnosti Atlassian z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cloud od společnosti Atlassian z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]
    
3. Chcete-li přidat Cloud od společnosti Atlassian, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cloud od společnosti Atlassian**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/app-search.png)

5. Na panelu výsledků vyberte **Cloud od společnosti Atlassian**a potom klikněte na tlačítko **přidat** tlačítko pro přidání do seznamu aplikací SaaS od společnosti Atlassian cloudu.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/app-create.png)

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/app-instance.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Přiřazování uživatelů do Cloud od společnosti Atlassian

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup pro Cloud od společnosti Atlassian. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny pro Cloud od společnosti Atlassian podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Důležité tipy pro přiřazování uživatelů do Cloud od společnosti Atlassian

*   Dále je doporučeno jednoho uživatele Azure AD je přiřadit ke cloudu od společnosti Atlassian otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazování uživatele pro Cloud od společnosti Atlassian, je nutné vybrat v dialogovém okně přiřazení žádné platné roli specifické pro aplikaci (Pokud je k dispozici). Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurace automatické zřizování uživatelů pro Cloud od společnosti Atlassian 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v cloudu od společnosti Atlassian podle uživatele a/nebo přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Cloud od společnosti Atlassian, postupujte podle pokynů uvedených v [Cloud od společnosti Atlassian jednotné přihlašování – kurz](atlassian-cloud-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Cloud od společnosti Atlassian ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte Cloud od společnosti Atlassian ze seznamu aplikací SaaS.
 
    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/application-instance-search.png)

3. Vyberte **zřizování** kartu.
    
    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta** a **tajný klíč tokenu** Cloud od společnosti Atlassian účtu. Mezi tyto hodnoty patří:

    *   V **adresy URL Tenanta** pole, zadejte koncový bod konkrétního tenanta, dostanete od od společnosti Atlassian, jak je popsáno v kroku 6. Příklad: **https://api.atlassian.com/scim/directory/{directoryId}**

    *   V **tajný klíč tokenu** pole, vyplňte token tajného kódu, jak je popsáno v kroku 6.

6. Přejděte do [správce organizace od společnosti Atlassian](https://admin.atlassian.com) **> zřizování uživatelů** a klikněte na **vytvořit Token**. Kopírovat **základní adresa URL adresáře** a **nosného tokenu** k **adresy URL Tenanta** a **tajný klíč tokenu** polí v uvedeném pořadí.

    ![Cloud od společnosti Atlassian zřizování](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Cloud od společnosti Atlassian zřizování](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    
    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Cloud od společnosti Atlassian. Pokud se nepovede, ujistěte se, že váš Cloud od společnosti Atlassian účet má oprávnění správce a zkuste to znovu.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)
    
8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users na Cloud od společnosti Atlassian**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD pro Cloud od společnosti Atlassian v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v cloudu od společnosti Atlassian pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory pro Cloud od společnosti Atlassian**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD pro Cloud od společnosti Atlassian v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v cloudu od společnosti Atlassian pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Služba pro Cloud od společnosti Atlassian zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení cloud od společnosti Atlassian výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/save.png)


Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba v cloudu od společnosti Atlassian zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Cloud od společnosti Atlassian umožňuje zřizovat uživatelům pouze z [ověřené domény](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Cloud od společnosti Atlassian nepodporuje přejmenuje skupinu ještě dnes. To znamená, že jakékoli změny zobrazovaným názvem skupiny ve službě Azure AD nebude aktualizuje a projeví v cloudu od společnosti Atlassian.
* Hodnota **e-mailu** atribut uživatele ve službě Azure AD je vyplněný pouze, když má uživatel poštovní schránky Microsoft Exchange. Pokud uživatel nemá jeden, se doporučuje pro mapování jinou požadovaný atribut pro **e-mailů** atribut v cloudu od společnosti Atlassian.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
