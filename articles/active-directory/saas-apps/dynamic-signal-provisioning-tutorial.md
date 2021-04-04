---
title: 'Kurz: Konfigurace dynamického signálu pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů na dynamický signál.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 263a67fd8fba2c336d1ed4d91475386a8ae175dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005788"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Kurz: Konfigurace dynamického signálu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v dynamickém signálu a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a skupin na dynamický signál.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant s dynamickým signálem](https://dynamicsignal.com/)
* Uživatelský účet v dynamickém signálu s oprávněními správce.

## <a name="add-dynamic-signal-from-the-gallery"></a>Přidat dynamický signál z Galerie

Než začnete konfigurovat dynamický signál pro Automatické zřizování uživatelů pomocí Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat dynamický signál z Galerie aplikací Azure AD.

**Pokud chcete přidat dynamický signál z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **dynamický signál**, v panelu výsledků vyberte možnost **dynamický signál** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Dynamický signál v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Přiřazování uživatelů k dynamickému signálu

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k dynamickému signálu. Po rozhodnutí můžete tyto uživatele a skupiny přiřadit k dynamickému signálu podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Důležité tipy pro přiřazování uživatelů k dynamickému signálu

* Doporučujeme, aby se k dynamickému signálu přiřadí jeden uživatel Azure AD, který bude testovat automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k dynamickému signálu musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (je-li k dispozici). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Konfigurace automatického zřizování uživatelů na dynamický signál 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v dynamickém signálu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro dynamické signály jednotné přihlašování založené na SAML, a to podle pokynů uvedených v kurzu pro [jednotné přihlašování pomocí dynamického signálu](dynamicsignal-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro dynamický signál v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **dynamický signál**.

    ![Odkaz na dynamický signál v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** účtu dynamického signálu, jak je popsáno v kroku 6.

6. V konzole pro správu dynamického signálu přejděte na **správce > rozšířené rozhraní > API**.

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-1.png" alt-text="Snímek obrazovky s konzolou správce dynamického signálu V nabídce správce je zvýrazněna položka Upřesnit. Zobrazí se také nabídka Upřesnit se zvýrazněnou možností P." border="false":::

    Zkopírujte **adresu URL rozhraní API SCIM** na **adresu URL tenanta**. Kliknutím na **vygenerovat nový token** vygenerujte **nosný token** a zkopírujte hodnotu do **tajného tokenu**.

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-2.png" alt-text="Snímek obrazovky se stránkou s tokeny s C I M A P I U R L, vygenerujte nový token a zvýrazněný token nosiče a zástupný text v poli nosných tokenů." border="false":::

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k dynamickému signálu. Pokud se připojení nepovede, ujistěte se, že váš účet dynamického signálu má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele do dynamického signálu**.

    ![Mapování uživatele dynamického signálu](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do dynamického signálu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v dynamickém signálu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele dynamického signálu](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro dynamický signál, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit s dynamickým signálem, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na dynamickém signálu.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Dynamický signál nepodporuje trvalé odstranění uživatelů ze služby Azure AD. Chcete-li trvale odstranit uživatele v dynamickém signálu, je nutné operaci provést prostřednictvím uživatelského rozhraní konzoly správce dynamického signálu. 
* Dynamický signál v současné době nepodporuje skupiny.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

