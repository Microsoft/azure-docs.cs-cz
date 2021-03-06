---
title: 'Kurz: Konfigurace OpenText adresářových služeb pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po OpenText adresářových služeb.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 2f31eddab1070d073d3fd5a4761dad597e42a2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181879"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Kurz: Konfigurace adresářových služeb OpenText pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést v rámci služby OpenText Directory Services, a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny s OpenText adresářovými službami pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v OpenText adresářových službách
> * Odebrat uživatele v OpenText adresářových služeb, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a OpenText adresářovými službami
> * Zřizování skupin a členství ve skupinách v OpenText adresářových službách
> * [Jednotné přihlašování](./opentext-directory-services-tutorial.md) k adresářovým službám OpenText (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Instalace OTDS přístupná službou Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a OpenText adresářovými službami](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace adresářových služeb OpenText pro podporu zřizování s Azure AD

> [!NOTE]
> Níže uvedené kroky platí pro instalaci OpenTextch adresářových služeb. Nevztahují se na klienty OpenText CoreShare nebo OpenText OT2.

1. Vytvořte vyhrazeného důvěrného **klienta OAuth**.
2. Nastavte životnost dlouhého **přístupového tokenu**.

      ![Doba platnosti přístupového tokenu](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Nezadávejte žádné adresy URL pro přesměrování. Nejsou požadovány. 
4. OTDS vygeneruje a zobrazí **tajný klíč klienta**. Uložte **ID klienta** a **tajný klíč klienta** do zabezpečeného umístění.

      ![Tajný klíč klienta](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Vytvořte oddíl pro uživatele a skupiny, které se budou synchronizovat ze služby Azure AD.

      ![Stránka oddílu](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Udělte klientovi OAuth oprávnění správce, které jste vytvořili v oddílu, který budete používat pro synchronizaci uživatelů a skupin Azure AD.    
      * > akce oddílu – > upravit správce

      ![Stránka správce](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. V Azure AD se musí načíst a nakonfigurovat tajný token. Pro tuto aplikaci se dá použít kterákoli klientská aplikace HTTP. Níže jsou uvedené kroky pro načtení pomocí aplikace API Swagger, která je součástí OTDS.
      * Ve webovém prohlížeči, přejít na adresu {OTDS URL}/otdsws/OAuth2
      * Přejděte na/token a klikněte na ikonu zámku v pravém horním rohu. Do pole uživatelské jméno a heslo zadejte ID klienta OAuth a tajný kód, který jste získali dříve. Klikněte na autorizovat.

      ![Tlačítko autorizace](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Pro grant_type vyberte **client_credentials** a klikněte na **Spustit**.

      ![Spustit Buton](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. Přístupový token v odpovědi by měl být použit v poli **tajného tokenu** ve službě Azure AD.

      ![Token přístupu](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání adresářových služeb OpenText z Galerie aplikací Azure AD

Přidejte adresářové služby OpenText z Galerie aplikací Azure AD a začněte spravovat zřizování pro adresářové služby OpenText. Pokud jste dříve nastavili OpenText Directory Services pro jednotné přihlašování, můžete použít stejnou aplikaci. Pro účely počátečního testování integrace však doporučujeme vytvořit samostatnou aplikaci. Další informace o přidání aplikace z galerie najdete [tady](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definování uživatelů, kteří budou v rozsahu zřizování 

Služba zřizování Azure AD umožňuje nastavit rozsah uživatelů, kteří se zřídí, na základě přiřazení k aplikaci nebo atributů jednotlivých uživatelů nebo skupin. Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí pro vaši aplikaci, na základě přiřazení, můžete k aplikaci přiřadit uživatele a skupiny pomocí následujících [kroků](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete nastavit rozsah uživatelů, kteří se zřídí, pouze na základě atributů jednotlivých uživatelů nebo skupin, můžete použít filtr rozsahu, jak je popsáno [tady](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k OpenText adresářovým službám musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je v aplikaci k dispozici pouze role Výchozí přístup, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Pokud je rozsah zřizování nastavený na přiřazené uživatele a skupiny, můžete testování provést tak, že k aplikaci přiřadíte jednoho nebo dva uživatele nebo skupiny. Pokud je rozsah nastavený na všechny uživatele a skupiny, můžete určit [filtr rozsahu na základě atributů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Krok 5. Konfigurace automatického zřizování uživatelů pro adresářové služby OpenText 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TestApp na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro adresářové služby OpenText ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **OpenText Directory Services**.

    ![Odkaz na adresářové služby OpenText v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatická karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL tenanta OpenText adresářových služeb.
   * Adresa URL tenanta bez specifc: {OTDS URL}/scim/{partitionName}
   * Adresa URL konkrétního tenanta: {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Zadejte tajný token načtený z kroku 2. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k OpenText adresářovým službám. Pokud se připojení nepovede, ujistěte se, že váš účet OpenText adresářových služeb má oprávnění správce, a zkuste to znovu.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **Oznamovací e-mail** zadejte e-mailovou adresu osoby nebo skupiny, na kterou by se měla odesílat oznámení o chybách zřizování, a zaškrtněte políčko **Když dojde k selhání, poslat oznámení e-mailem**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele, aby se OpenTexty adresářové služby**.

9. Zkontrolujte atributy uživatelů synchronizované z Azure AD až po OpenText adresářových služeb v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v OpenText Directory Services pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), budete muset zajistit, aby rozhraní API OpenText Directory Services podporovalo filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |displayName|Řetězec|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |preferredLanguage|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |externalId|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Reference| 

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s OpenText adresářovými službami**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD až po OpenText adresářových služeb v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v OpenText Directory Services pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

      |Atribut|Typ|
      |---|---|
      |displayName|Řetězec|
      |externalId|Řetězec|
      |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro adresářové služby OpenText, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Určete uživatele nebo skupiny, které chcete zřídit pro OpenText adresářové služby, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

1. S využitím [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
2. Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
3. Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).