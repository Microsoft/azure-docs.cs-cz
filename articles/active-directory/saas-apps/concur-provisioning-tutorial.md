---
title: 'Kurz: Konfigurace Concur pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358207"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Kurz: Konfigurace Concur pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v Concur a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až Concur.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

*   Tenant Azure Active Directory.
*   Předplatné s povoleným Concurm jednotným přihlašováním.
*   Uživatelský účet v Concur s oprávněními správce týmu.

## <a name="assigning-users-to-concur"></a>Přiřazování uživatelů k Concur

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci Concur. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace Concur podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Důležité tipy pro přiřazení uživatelů k Concur

*   Doporučuje se, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD Concur. Další uživatele a skupiny můžete přiřadit později.

*   Při přiřazování uživatele k Concur je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v Concur na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip] 
> Můžete se také rozhodnout povolit pro Concur jednu Sign-On založenou na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je pořídit, jak povolit zřizování uživatelských účtů služby Active Directory pro Concur.

Aby bylo možné povolit aplikace ve službě výdaje, je nutné mít správné nastavení a použití profilu správce webové služby. Nepřidávejte roli WS admin do stávajícího profilu správce, který používáte pro funkce správy T&E.

Concur konzultanti nebo Správci klienta musí vytvořit odlišný profil správce webové služby a správce klienta musí použít tento profil pro funkce správce webových služeb (například povolení aplikací). Tyto profily musí být oddělené od správce klienta denní T&E (profil pro správu T&E by neměl mít přiřazenou roli WSAdmin).

Když vytvoříte profil, který se má použít k povolení aplikace, zadejte do polí profil uživatele název správce klienta. Tím se přiřadí vlastnictví profilu. Po vytvoření jednoho nebo více profilů se musí klient přihlásit pomocí tohoto profilu a kliknout na tlačítko *Povolit* u Partnerské aplikace v nabídce webové služby.

Z následujících důvodů by se tato akce neměla provádět s profilem, který používají pro správu normální T&E.

* Klient musí být ten, který klikne na *Ano* v dialogovém okně, které se zobrazí po povolení aplikace. Kliknutím na toto tlačítko potvrdí, že má Partnerská aplikace přístup k datům, takže vy nebo partner nemůže kliknout na tlačítko Ano.

* Pokud správce klienta, který povolil aplikaci pomocí profilu pro správu T&E odejde ze společnosti (výsledkem deaktivace profilu), nebudou všechny aplikace s tímto profilem fungovat, dokud se aplikace nepovolí s jiným aktivním profilem WS admin. Důvodem je, že byste měli vytvořit odlišné profily WS admin.

* Pokud správce odejde ze společnosti, název přidružený k profilu WS admin se dá změnit na správce nahrazení, pokud je to potřeba, aniž by to mělo vliv na povolenou aplikaci, protože tento profil nepotřebuje deaktivovaný.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Concur** .

2. V nabídce **Správa** vyberte možnost **webové služby**.
   
    ![Tenant Concur](./media/concur-provisioning-tutorial/IC721729.png "Tenant Concur")

3. Na levé straně v podokně **webové služby** vyberte **Povolit partnerských aplikací**.
   
    ![Povolit partnerských aplikací](./media/concur-provisioning-tutorial/ic721730.png "Povolit partnerských aplikací")

4. V seznamu **Povolit aplikaci** vyberte možnost **Azure Active Directory** a potom klikněte na tlačítko **Povolit**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Kliknutím na tlačítko **Ano** zavřete dialog **potvrdit akci** .
   
    ![Potvrdit akci](./media/concur-provisioning-tutorial/ic721732.png "Potvrdit akci")

6. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

7. Pokud jste už nakonfigurovali Concur pro jednotné přihlašování, vyhledejte vaši instanci Concur pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Concur** v galerii aplikací. Ve výsledcích hledání vyberte Concur a přidejte je do seznamu aplikací.

8. Vyberte svou instanci Concur a pak vyberte kartu **zřizování** .

9. Nastavte **Režim zřizování** na hodnotu **Automaticky**. 
 
    ![Snímek obrazovky s kartou zřizování pro Concur v Azure Portal. Režim zřizování je nastaven na automatické a zvýrazní se tlačítko Test připojení.](./media/concur-provisioning-tutorial/provisioning.png)

10. V části **přihlašovací údaje správce** zadejte **uživatelské jméno** a **heslo** správce Concur.

11. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci Concur. Pokud se připojení nepovede, ujistěte se, že váš účet Concur má oprávnění správce týmu.

12. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko.

13. Klikněte na **Uložit.**

14. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé Concur.**

15. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do Concur. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Concur pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

16. Pokud chcete povolit službu Azure AD Provisioning pro Concur, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

17. Klikněte na **Uložit.**

Nyní můžete vytvořit testovací účet. Počkejte až 20 minut, než ověříte, že byl účet synchronizovaný do Concur.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](concur-tutorial.md)