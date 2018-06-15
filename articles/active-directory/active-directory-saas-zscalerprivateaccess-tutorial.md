---
title: 'Kurz: Azure Active Directory integrace s Zscaler privátní přístup (ZPA) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler privátní přístup (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 1d5434c57a95e53e4fe37c33f3fe11585fc3177f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354329"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Kurz: Azure Active Directory integrace s Zscaler privátní přístup (ZPA)

V tomto kurzu zjistěte, jak integrovat Zscaler privátní přístup (ZPA) s Azure Active Directory (Azure AD).

Integrace Zscaler privátní přístup (ZPA) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscaler privátní přístup (ZPA)
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k Zscaler privátní přístup (ZPA) (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Zscaler privátní přístup (ZPA), potřebujete následující položky:

- Předplatné služby Azure AD
- Zscaler privátní přístup (ZPA) jednotného přihlašování povolené předplatné


> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.


Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscaler privátní přístup (ZPA) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Přidání Zscaler privátní přístup (ZPA) z Galerie
Při konfiguraci integrace služby Zscaler privátní přístup (ZPA) do služby Azure AD, potřebujete přidat Zscaler privátní přístup (ZPA) z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Zscaler privátní přístup (ZPA) z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Zscaler privátní přístup (ZPA)**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. Na panelu výsledků vyberte **Zscaler privátní přístup (ZPA)** a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler privátní přístup (ZPA) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Zscaler privátní přístup (ZPA) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Zscaler privátní přístup (ZPA).

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Zscaler privátní přístup (ZPA).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler privátní přístup (ZPA), je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zscaler privátní přístup (ZPA)](#creating-a-zscaler-private-access-(zpa)-test-user)**  – Pokud chcete mít v Zscaler privátní přístup (ZPA) propojeném s Azure AD reprezentace jí protějšek Britta Simon.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci Zscaler privátní přístup (ZPA).

**Pokud chcete konfigurovat Azure AD jednotné přihlašování s Zscaler privátní přístup (ZPA), proveďte následující kroky:**

1. Na portálu Azure Management portal na **Zscaler privátní přístup (ZPA)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. Na **Zscaler privátní přístup (ZPA) domény a adresy URL** část, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. V **identifikátor** textovému poli, typ: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresa URL a identifikátor. Zde, doporučujeme vám použít jedinečnou hodnotu adresy URL v identifikátoru. Obraťte se na [tým podpory Zscaler privátní přístup (ZPA)](https://help.zscaler.com/zpa-submit-ticket) získat tyto hodnoty.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)   

5. Na **vytvořit nový certifikát** dialogové okno, klikněte na ikonu kalendáři a vyberte **datum vypršení platnosti**. Pak klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. Na **SAML podpisový certifikát** vyberte **aktivujte nový certifikát** a klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. V místní nabídce **certifikát výměny** okně klikněte na tlačítko **OK**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Zscaler privátní přístup (ZPA).

10. Přejděte na **správce** a pak klikněte na **Idp konfigurace**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. V **Idp konfigurace** klikněte na tlačítko **přidat novou konfiguraci IDP**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. V **novou konfiguraci IDP** část, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Klikněte na tlačítko **vyberte soubor** a nahrát soubor stažený metadat.

    b. Klikněte na tlačítko **Uložit** tlačítko.
    


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Vytvoření zkušebního uživatele Zscaler privátní přístup (ZPA)

V této části vytvoříte uživatele názvem Britta Simon v Zscaler privátní přístup (ZPA). Spojte se s [tým podpory Zscaler privátní přístup (ZPA)](https://help.zscaler.com/zpa-submit-ticket) přidat uživatele do platformy Zscaler privátní přístup (ZPA).


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k Zscaler privátní přístup (ZPA).

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon k Zscaler privátní přístup (ZPA), proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zscaler privátní přístup (ZPA)**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    


### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zscaler privátní přístup (ZPA) na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Zscaler privátní přístup (ZPA).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png