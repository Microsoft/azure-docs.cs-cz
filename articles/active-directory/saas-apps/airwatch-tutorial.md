---
title: 'Kurz: Azure Active Directory integrace s AirWatch | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 4073fbb534ae712f8a5cad6c2e4aa750cec365b7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213280"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Kurz: Azure Active Directory integrace s AirWatch

V tomto kurzu zjistěte, jak integrovat AirWatch s Azure Active Directory (Azure AD).

Integrace AirWatch s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AirWatch
- Můžete povolit uživatelům, aby automaticky získat přihlášení k AirWatch (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AirWatch, potřebujete následující položky:

- Předplatné služby Azure AD
- AirWatch jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání AirWatch z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-airwatch-from-the-gallery"></a>Přidání AirWatch z Galerie
Při konfiguraci integrace AirWatch do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS AirWatch z galerie.

**Pokud chcete přidat AirWatch z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **AirWatch**.

    ![Vytváření testovacího uživatele Azure AD](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. Na panelu výsledků vyberte **AirWatch**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s AirWatch podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v AirWatch je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v AirWatch musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v AirWatch.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AirWatch, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele AirWatch](#creating-a-airwatch-test-user)**  – Pokud chcete mít protějšek Britta Simon v AirWatch propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci AirWatch.

**Ke konfiguraci Azure AD jednotné přihlašování s AirWatch, proveďte následující kroky:**

1. Na portálu Azure na **AirWatch** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Na **AirWatch domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. V **identifikátor** textovému poli, zadejte hodnotu jako `AirWatch`

    > [!NOTE] 
    > Tato hodnota není reálné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory AirWatch klienta](http://www.air-watch.com/company/contact-us/) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Na **AirWatch konfigurace** klikněte na tlačítko **konfigurace AirWatch** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti AirWatch jako správce.

8. V levém navigačním podokně klikněte na tlačítko **účty**a potom klikněte na **správci**.
   
   ![Správci](./media/airwatch-tutorial/ic791920.png "správci")

9. Rozbalte **nastavení** nabídce a pak klikněte na tlačítko **Directory Services**.
   
   ![Nastavení](./media/airwatch-tutorial/ic791921.png "nastavení")

10. Klikněte na tlačítko **uživatele** ve **Base DN** textovému poli, zadejte název domény a pak klikněte na tlačítko **Uložit**.
   
   ![Uživatel](./media/airwatch-tutorial/ic791922.png "uživatele")

11. Klikněte **Server** kartě.
   
   ![Server](./media/airwatch-tutorial/ic791923.png "serveru")

12. Proveďte následující kroky:
    
    ![Nahrát](./media/airwatch-tutorial/ic791924.png "nahrát")   
    
    a. Jako **typu adresáře**, vyberte **žádné**.

    b. Vyberte **pomocí SAML pro ověřování**.

    c. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **nahrát**.

13. V **požadavku** část, proveďte následující kroky:
    
    ![Žádosti o](./media/airwatch-tutorial/ic791925.png "požadavku")  

    a. Jako **typ vazby žádosti**, vyberte **POST**.

    b. Na portálu Azure na **nakonfigurovat jednotné přihlašování v Airwatch** dialogové okno stránky, kopírování **SAML jeden přihlašování adresa URL služby** hodnotu a vložte ji do **Identity zprostředkovatele jednotné přihlašování Adresa URL** textové pole.

    c. Jako **NameID formátu**, vyberte **e-mailovou adresu**.

    d. Klikněte na **Uložit**.

14. Klikněte **uživatele** kartě znovu.
    
    ![Uživatel](./media/airwatch-tutorial/ic791926.png "uživatele")

15. V **atribut** část, proveďte následující kroky:
    
    ![Atribut](./media/airwatch-tutorial/ic791927.png "atribut")

    a. V **identifikátor objektu** textovému poli, typ **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. V **uživatelské jméno** textovému poli, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. V **zobrazovaný název** textovému poli, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. V **křestní jméno** textovému poli, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. V **příjmení** textovému poli, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. V **e-mailu** textovému poli, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Klikněte na **Uložit**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/airwatch-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/airwatch-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/airwatch-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/airwatch-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z Britta Simon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-airwatch-test-user"></a>Vytvoření zkušebního uživatele AirWatch

Pokud chcete povolit uživatelům Azure AD přihlášení k AirWatch, se musí být zřízená v k AirWatch.

* Při zřizování AirWatch, je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **AirWatch** společnosti lokality jako správce.
2. V navigačním podokně na levé straně klikněte na **účty**a potom klikněte na **uživatelé**.
   
   ![Uživatelé](./media/airwatch-tutorial/ic791929.png "uživatelů")
3. V **uživatelé** nabídky, klikněte na tlačítko **zobrazení seznamu**a potom klikněte na **přidat \> přidat uživatele**.
   
   ![Přidat uživatele](./media/airwatch-tutorial/ic791930.png "přidat uživatele")
4. Na **přidat nebo upravit uživatele** dialogové okno, proveďte následující kroky:

   ![Přidat uživatele](./media/airwatch-tutorial/ic791931.png "přidat uživatele")   
   1. Typ **uživatelské jméno**, **heslo**, **potvrzení hesla**, **křestní jméno**, **příjmení**,  **E-mailová adresa** platný účtu Azure Active Directory má mají být zahrnuty do související textových polí.
   2. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít všechny ostatní AirWatch uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované AirWatch zřídit AAD uživatelské účty.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu AirWatch.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon AirWatch, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **AirWatch**.

    ![Konfigurovat jednotné přihlašování](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Pokud chcete testovat vaše nastavení jednotného přihlašování, otevřete Panel přístupu. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/airwatch-tutorial/tutorial_general_01.png
[2]: ./media/airwatch-tutorial/tutorial_general_02.png
[3]: ./media/airwatch-tutorial/tutorial_general_03.png
[4]: ./media/airwatch-tutorial/tutorial_general_04.png

[100]: ./media/airwatch-tutorial/tutorial_general_100.png

[200]: ./media/airwatch-tutorial/tutorial_general_200.png
[201]: ./media/airwatch-tutorial/tutorial_general_201.png
[202]: ./media/airwatch-tutorial/tutorial_general_202.png
[203]: ./media/airwatch-tutorial/tutorial_general_203.png

