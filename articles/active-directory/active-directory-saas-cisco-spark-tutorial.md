---
title: 'Kurz: Azure Active Directory integrace s Cisco Spark | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 4307904e87bdf9a0361a7fb143e87ae3a662f18e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Kurz: Azure Active Directory integrace s Cisco Spark

V tomto kurzu zjistěte, jak integrovat Cisco Spark v Azure Active Directory (Azure AD).

Integrace Cisco Spark s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Cisco Spark
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Cisco Spark (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Cisco Spark, potřebujete následující položky:

- Předplatné služby Azure AD
- Cisco Spark jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cisco Spark z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cisco-spark-from-the-gallery"></a>Přidání Cisco Spark z Galerie
Při konfiguraci integrace Spark Cisco do služby Azure AD, potřebujete přidat Cisco Spark z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Cisco Spark z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Spark**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. Na panelu výsledků vyberte **Cisco Spark**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Spark podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Cisco Spark je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Cisco Spark je potřeba vytvořit.

V Cisco Spark přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Spark, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Cisco Spark](#creating-a-cisco-spark-test-user)**  – Pokud chcete mít protějšek Britta Simon v Cisco Spark, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Cisco Spark.

**Ke konfiguraci Azure AD jednotné přihlašování s Cisco Spark, proveďte následující kroky:**

1. Na portálu Azure na **Cisco Spark** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. Na **Cisco Spark domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://web.ciscospark.com/#/signin`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tato hodnota se skutečným identifikátorem. Obraťte se na [tým podpory Cisco Spark klienta](https://support.ciscospark.com/) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. Aplikací Spark Cisco očekává kontrolní výrazy SAML tak, aby obsahovala určité atributy. Nakonfigurujte následující atributy pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |    
    |   UID    | user.userprincipalname |   

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_400.png)

8. Přihlaste se k [správu spolupráce cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

9. Vyberte **nastavení** a v části **ověřování** klikněte na tlačítko **upravit**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. Vyberte **integrovat zprostředkovatele identity 3. stran. (Rozšířené)**  a přejděte na další obrazovce.

11. Na **importovat Idp Metadata** stránky, buď přetažení a odstraňte soubor metadat Azure AD na stránku nebo použijte možnost prohlížeče souboru najděte a nahrajte soubor metadat Azure AD. Pak vyberte **vyžadovat certifikát podepsaný certifikační autoritou v metadatech (bezpečnější)** a klikněte na tlačítko **Další**. 
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. Vyberte **Test jednotného přihlašování k připojení**a když se otevře novou kartu prohlížeče, ověřování s Azure AD po přihlášení.

13. Vraťte se na **správu spolupráce cloudu Cisco** kartu prohlížeče. Pokud byl test úspěšný, vyberte **tento test proběhl úspěšně. Možnost jednotného přihlašování** a klikněte na tlačítko **Další**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Vytvoření zkušebního uživatele Cisco Spark

V této části vytvoříte uživatele volal Britta Simon v Cisco Spark. V této části vytvoříte uživatele volal Britta Simon v Cisco Spark.

1. Přejděte na [správu spolupráce cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

2. Klikněte na tlačítko **uživatelé** a potom **Správa uživatelů**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. V **spravovat uživatelská** vyberte **ručně přidat nebo změnit uživatele** a klikněte na tlačítko **Další**.

4. Vyberte **názvy a e-mailová adresa**. Potom zadejte do textového pole následujícím způsobem:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. V **křestní jméno** textovému poli, typ **Britta**. 
    
    b. V **příjmení** textovému poli, typ **Simon**.
    
    c. V **e-mailová adresa** textovému poli, typ **britta.simon@contoso.com**.

5. Klikněte na symbol plus přidat Britta Simon. Pak klikněte na **Další**.

6. V **přidat služby pro uživatele** okně klikněte na tlačítko **Uložit** a potom **Dokončit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Cisco Spark.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Cisco Spark, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Cisco Spark**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici Cisco Spark na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Cisco Spark.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png

