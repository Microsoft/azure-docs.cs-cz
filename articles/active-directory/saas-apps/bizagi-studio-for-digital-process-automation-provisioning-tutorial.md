---
title: 'Kurz: Konfigurace bizagi studia pro automatizaci digitálních procesů pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po bizagi Studio pro automatizaci digitálních procesů.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 4eaac716d06b102a07872059af28da4986889caa
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673432"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Kurz: Konfigurace bizagi studia pro automatizaci digitálních procesů pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v bizagi studiu pro automatizaci digitálního procesu a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Když to uděláte, Azure AD automaticky zřídí a odsouhlasí uživatele a skupiny s [bizagi Studio pro automatizaci digitálních procesů](https://www.bizagi.com/) pomocí služby zřizování Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v bizagi studiu pro automatizaci digitálních procesů
> * Odebrat uživatele v nástroji bizagi Studio pro automatizaci digitálních procesů, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a bizagi Studio pro automatizaci digitálních procesů
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) do bizagi studia pro automatizaci digitálního procesu (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující:

* [Tenanta Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování. Mezi příklady patří správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce. 
* Bizagi Studio pro automatizaci digitálního procesu verze 11.2.4.2 X nebo novější.

## <a name="plan-your-provisioning-deployment"></a>Plánování nasazení zřizování
Pro plánování použijte následující postup:

1. Seznamte se s [fungováním služby zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude [v oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a bizagi Studio pro automatizaci digitálních procesů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Konfigurace pro podporu zřizování s Azure AD
Pokud chcete nakonfigurovat bizagi Studio pro automatizaci digitálních procesů tak, aby podporovala zřizování s Azure AD, postupujte takto:

1. Přihlaste se k pracovnímu portálu jako uživatel s **oprávněními správce**.

2. Přejít na **správce**  >  **zabezpečení**  >  **OAuth 2 aplikace**.

   ![Snímek obrazovky s bizagi se zvýrazněnými aplikacemi OAuth 2.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Vyberte **Přidat**.
4. Jako **typ udělení** vyberte **token nosiče**. V případě **povoleného oboru** vyberte **rozhraní API** a **synchronizaci uživatelů**. Pak vyberte **Uložit**.

   ![Snímek aplikace s registrací a zvýrazněným typem udělení a povoleným oborem](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Zkopírujte a uložte **tajný klíč klienta**. V Azure Portal v rámci aplikace bizagi Studio pro automatizaci digitálního procesu se na kartě **zřizování** v poli **tajný token** zadá hodnota tajného klíče klienta.

   ![Snímek žádosti OAuth s tajným klíčem klienta highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete začít spravovat zřizování pro bizagi Studio pro automatizaci digitálních procesů, přidejte aplikaci z Galerie aplikací Azure AD. Pokud jste již dříve nastavili bizagi Studio pro automatizaci digitálního procesu jednotného přihlašování, můžete použít stejnou aplikaci. Při počátečním testování integrace byste ale měli vytvořit samostatnou aplikaci. Další informace najdete v tématu [rychlý Start: Přidání aplikace do tenanta Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definujte, kdo je v oboru pro zřizování. 

Pomocí služby Azure AD Provisioning můžete nastavit obor, který se zřídí v závislosti na přiřazení aplikace, a to na základě atributů uživatele a skupiny nebo obou. Pokud rozsah zadáte na základě přiřazení, přečtěte si postup přiřazení [nebo zrušení přiřazení uživatelů a skupin pro aplikaci, která používá Graph API](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud je obor založený výhradně na atributech uživatele nebo skupiny, můžete použít filtr oborů. Další informace najdete v tématu [zřizování aplikace na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

Všimněte si následujících bodů o oboru:

* Když přiřazujete uživatele a skupiny do bizagi studia pro automatizaci digitálních procesů, musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu jsou vyloučení z zřizování a jsou označeni v protokolech zřizování, které budou označeny jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat tak další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Když je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="configure-automatic-user-provisioning"></a>Konfigurace automatického zřizování uživatelů 

Tato část vás provede postupem konfigurace služby zřizování Azure AD pro vytváření, aktualizaci a zakázání uživatelů a skupin. Provádíte to v testovací aplikaci na základě přiřazení uživatelů a skupin ve službě Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro bizagi Studio pro automatizaci digitálních procesů v Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Snímek obrazovky Azure Portal, u podnikových aplikací a všech aplikací se zvýrazní.](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **bizagi Studio pro automatizaci digitálních procesů**.

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy s zvýrazněným zřizováním](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Ovládací prvek režimu zřizování Screenshotof s automatickým zvýrazněním](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL tenanta a tajný token pro bizagi Studio pro automatizaci digitálního procesu. 

      * **Adresa URL klienta:** Zadejte koncový bod bizagi SCIM s následující strukturou:  `<Your_Bizagi_Project>/scim/v2/` .
         Například: `https://my-company.bizagi.com/scim/v2/`.

      * **Tajný token:** Tato hodnota se načte z kroku popsaného výše v tomto článku.

      Pokud chcete zajistit, aby se služba Azure AD mohla připojit k bizagi studiu pro automatizaci digitálních procesů, vyberte **Test připojení**. Pokud se připojení nepovede, zajistěte, aby měl účet bizagi Studio pro digitální proces Automation oprávnění správce, a zkuste to znovu.

   ![Snímek přihlašovacích údajů správce se zvýrazněným testovým připojením.](common/provisioning-testconnection-tenanturltoken.png)

6. V části **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Vyberte možnost k **odeslání e-mailového oznámení, když dojde k selhání**.

    ![Snímek obrazovky s možnostmi e-mailu s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele a bizagi Studio pro automatizaci digitálních procesů**.

9. V sekci **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do bizagi studia pro automatizaci digitálních procesů. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v nástroji bizagi Studio pro automatizaci digitálních procesů pro operace aktualizace. Pokud změníte [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), je nutné zajistit, aby rozhraní API pro automatizaci bizagi Studio pro digitální procesy podporovalo filtrování uživatelů na základě tohoto atributu. Vyberte **Uložit** a potvrďte všechny změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |emails[type eq "work"].value|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|

   Vlastní atributy rozšíření se dají přidat tak, že přejdete na **Zobrazit pokročilé možnosti > upravit seznam atributů pro bizagi**. Vlastní atributy rozšíření musí obsahovat předponu **urn: IETF: params: SCIM: schemas: bizagi: 2.0: UserProperties:**. Například pokud je atribut vlastního rozšíření **IdentificationNumber**, musí být atribut přidán jako **urn: IETF: param: SCIM: schemas: Extension: bizagi: 2.0: UserProperties: IdentificationNumber**. Vyberte **Uložit** a potvrďte všechny změny.
   
    ![Upravit seznam atributů](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Další informace o tom, jak přidat vlastní atributy, najdete v tématu [přizpůsobení atributů aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

> [!NOTE]
> Jsou podporovány pouze základní vlastnosti typu (například řetězec, celé číslo, logická hodnota, datum a čas atd.). Vlastnosti propojené s tabulkami ukazatelů nebo více typy se ještě nepodporují.

10. Postup konfigurace filtrů oborů najdete v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro bizagi Studio pro automatizaci digitálních procesů, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

    ![Snímek obrazovky s přepínačem stavu zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele a skupiny, které chcete zřídit pro bizagi Studio pro automatizaci digitálních procesů. V části **Nastavení** vyberte požadované hodnoty v **oboru**.

    ![Snímek obrazovky s možnostmi oboru](common/provisioning-scope.png)

13. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Snímek obrazovky ovládacího prvku pro uložení](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="monitor-your-deployment"></a>Monitorování nasazení
Po dokončení konfigurace zřizování použijte následující prostředky k monitorování nasazení:

- Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
- Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
- Pokud je konfigurace zřizování v nesprávném stavu, bude aplikace přejít do karantény. Další informace najdete v tématu [zřizování aplikací ve stavu karantény](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).
