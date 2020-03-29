---
title: 'Kurz: Konfigurace concur pro automatické zřizování uživatelů pomocí služby Azure Active Directory| Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60280409"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Kurz: Konfigurace concur pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v Concur a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD do Concur.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

*   Tenant advitus azure active directory.
*   Předplatné s povolenou jedním přihlášením.
*   Uživatelský účet v Concur s oprávněními správce týmu.

## <a name="assigning-users-to-concur"></a>Přiřazení uživatelů ke společnosti Concur

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Concur. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Concur podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Důležité tipy pro přiřazení uživatelů do Concur

*   Doporučuje se, aby jeden uživatel Azure AD přiřazena Concur k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

*   Při přiřazování uživatele společnosti Concur je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolení zřizování uživatelů

Tato část vás provede propojením azure ad s rozhraním API pro zřizování uživatelských účtů společnosti Concur a konfigurací služby zřizování tak, aby vytvářela, aktualizovala a zakazovala přiřazené uživatelské účty v Concur na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip] 
> Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Concur podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory společnosti Concur.

Chcete-li povolit aplikace v výdajové službě, musí být správné nastavení a použití profilu správce webové služby. Nepřidávejte roli správce WS do stávajícího profilu správce, který používáte pro funkce správy T&E.

Konzultanti concur nebo správce klienta musí vytvořit odlišný profil správce webové služby a správce klienta musí tento profil použít pro funkce správce webových služeb (například povolení aplikací). Tyto profily musí být odděleny od denního profilu správce klienta T&E (profil správce T&E by neměl být přiřazen roli WSAdmin).

Když vytvoříte profil, který se bude používat pro povolení aplikace, zadejte název správce klienta do polí profilu uživatele. Tím přiřadíte vlastnictví profilu. Po vytvoření jednoho nebo více profilů se klient musí přihlásit pomocí tohoto profilu a kliknout na tlačítko *"Povolit*" pro partnerskou aplikaci v nabídce Webové služby.

Z následujících důvodů by tato akce neměla být prováděna s profilem, který používají pro normální podávání T&E.

* Klient musí být ten, který klikne na tlačítko *"Ano*" v dialogovém okně, které se zobrazí po povolení aplikace. Toto kliknutí potvrzuje, že klient je ochoten pro partnerskou aplikaci získat přístup ke svým datům, takže vy nebo partner nemůžete klepnout na tlačítko Ano.

* Pokud správce klienta, který povolil aplikaci pomocí profilu správce T&E, opustí společnost (výsledkem je deaktivace profilu), všechny aplikace povolené pomocí tohoto profilu nefungují, dokud není aplikace povolena s jiným aktivním profilem správce WS. To je důvod, proč jste měl vytvořit odlišné WS Admin profily.

* Pokud správce opustí společnost, název přidružený k profilu správce WS lze v případě potřeby změnit na náhradního správce, aniž by to mělo vliv na povolenou aplikaci, protože tento profil není nutné inaktivovat.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Concur.**

2. V nabídce **Správa** vyberte **možnost Webové služby**.
   
    ![Concur tenant](./media/concur-provisioning-tutorial/IC721729.png "Concur tenant")

3. Na levé straně vyberte v podokně **Webové služby** **možnost Povolit partnerskou aplikaci**.
   
    ![Povolit partnerskou aplikaci](./media/concur-provisioning-tutorial/ic721730.png "Povolit partnerskou aplikaci")

4. V seznamu **Povolit aplikaci** vyberte **Azure Active Directory**a klikněte na **Povolit**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klepnutím na tlačítko **Ano** zavřete dialogové okno **Potvrdit akci.**
   
    ![Potvrdit akci](./media/concur-provisioning-tutorial/ic721732.png "Potvrdit akci")

6. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

7. Pokud jste již nakonfigurovali Concur pro jednotné přihlašování, vyhledejte svou instanci Concur pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Concur** v galerii aplikací. Ve výsledcích hledání vyberte Concur a přidejte je do seznamu aplikací.

8. Vyberte svou instanci Concur a pak vyberte kartu **Zřizování.**

9. Nastavte **režim zřizování** na **automatické**. 
 
    ![Zajišťování](./media/concur-provisioning-tutorial/provisioning.png)

10. V části **Pověření správce** zadejte **uživatelské jméno** a **heslo** správce concur.

11. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Concur. Pokud se připojení nezdaří, ujistěte se, že váš účet Concur má oprávnění správce týmu.

12. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

13. Klikněte na **Uložit.**

14. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory na Concur.**

15. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Concur. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v aplikaci Concur pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

16. Chcete-li povolit službu zřizování Azure AD pro Concur, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

17. Klikněte na **Uložit.**

Nyní můžete vytvořit testovací účet. Počkejte až 20 minut a ověřte, zda byl účet synchronizován se společností Concur.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](concur-tutorial.md)

