---
title: 'Kurz: Integrace Azure Active Directory se Sparkem Cisco | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: bebc8d674d7448ea0ce6a1f11b7ae80335df9cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431694"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Kurz: Integrace Azure Active Directory se Sparkem Cisco

V tomto kurzu se dozvíte, jak integrovat Cisco Spark se službou Azure Active Directory (Azure AD).

Integrace Cisco Sparku s využitím Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Cisco Spark
- Uživatele, aby automaticky získat přihlášení k Cisco Spark (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Sparkem Cisco, potřebujete následující položky:

- S předplatným služby Azure AD
- Cisco Spark jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cisco Spark z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cisco-spark-from-the-gallery"></a>Přidání Cisco Spark z Galerie
Konfigurace integrace Cisco Sparku do služby Azure AD, budete muset přidat Cisco Spark na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání Cisco Spark z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Cisco Spark**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/cisco-spark-tutorial/tutorial_ciscospark_search.png)

1. Na panelu výsledků vyberte **Cisco Spark**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování se Sparkem Cisco podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Cisco Spark je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve Sparku Cisco.

Ve Cisco Spark, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Sparku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Sparku Cisco](#creating-a-cisco-spark-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco Spark, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Cisco Spark.

**Ke konfiguraci Azure AD jednotného přihlašování se Sparkem Cisco, proveďte následující kroky:**

1. Na webu Azure Portal na **Cisco Spark** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

1. Na **Cisco Spark domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://web.ciscospark.com/#/signin`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné identifikátor. Kontakt [tým podpory klienta Cisco Spark](https://support.ciscospark.com/) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

1. V aplikaci Spark došlo Cisco očekává, že kontrolní výrazy SAML obsahuje konkrétní atributy. Nakonfigurujte následující atributy pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_ciscospark_07.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |    
    |   UID    | user.userprincipalname |   

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_general_400.png)

1. Přihlaste se k [správu spolupráci cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

1. Vyberte **nastavení** a v části **ověřování** klikněte na tlačítko **změnit**.
   
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
1. Vyberte **integrovat zprostředkovatele identity 3. stran. (Rozšířené)**  a přejít na další obrazovce.

1. Na **Import metadat zprostředkovatele identity** stránka, buď přetažením a soubor metadat služby Azure AD na stránku nebo pomocí možnosti prohlížeč souborů najděte a nahrajte soubor metadat služby Azure AD. Vyberte **vyžadovat certifikát podepsaný certifikační autoritou v metadatech (s vyšším zabezpečením)** a klikněte na tlačítko **Další**. 
    
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

1. Vyberte **Test jednotného přihlašování k připojení**a když se otevře na nové kartě prohlížeče, proveďte ověření pomocí služby Azure AD přihlášením.

1. Zpět **správu spolupráci cloudu Cisco** záložce prohlížeče. Pokud byl test úspěšný, vyberte **tento test proběhl úspěšně. Možnost jednotného přihlašování** a klikněte na tlačítko **Další**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/cisco-spark-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/cisco-spark-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/cisco-spark-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/cisco-spark-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Vytvoření zkušebního uživatele Cisco Spark

V této části vytvořte uživatele Britta Simon ve Sparku Cisco. V této části vytvořte uživatele Britta Simon ve Sparku Cisco.

1. Přejděte [správu spolupráci cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

1. Klikněte na tlačítko **uživatelé** a potom **spravovat uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

1. V **spravovat uživatele** okně **ručně přidávat nebo upravovat uživatele** a klikněte na tlačítko **Další**.

1. Vyberte **jména a e-mailová adresa**. Potom zadejte textového pole následujícím způsobem:
   
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. V **křestní jméno** textové pole, typ **Britta**. 
    
    b. V **příjmení** textové pole, typ **Simon**.
    
    c. V **e-mailová adresa** textové pole, typ **britta.simon@contoso.com**.

1. Klikněte symbol plus přidejte Britta Simon. Pak klikněte na **Další**.

1. V **přidat služby pro uživatele** okna, klikněte na tlačítko **Uložit** a potom **Dokončit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Cisco Sparku.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon Cisco Spark, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Cisco Spark**.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_ciscospark_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Cisco Spark na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Cisco Spark.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/cisco-spark-tutorial/tutorial_general_203.png

