---
title: 'Kurz: Konfigurace privátního přístupu Zscaler (ZPA) pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočatou uživatelské účty do soukromého přístupu Zscaler (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064139"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Kurz: Konfigurace soukromého přístupu Zscaler (ZPA) pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Zscaler Private Access (ZPA) a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-zřizování uživatelů a/nebo skupin z zzívaného privátního přístupu (ZPA).

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant zscaler soukromého přístupu (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uživatelský účet v zscaler soukromépřístup (ZPA) s oprávněními správce.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Přiřazení uživatelů k soukromému přístupu Zscaler (ZPA)

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k zscalerovému privátnímu přístupu (ZPA). Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Zscaler Private Access (ZPA) podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Důležité tipy pro přiřazení uživatelů k soukromému přístupu Zscaler (ZPA)

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Zscaler private access (ZPA) k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k soukromému přístupu Zscaler (ZPA) musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Nastavení soukromého přístupu Zscaler (ZPA) pro zřizování

1. Přihlaste se ke své [Konzole pro správu Soukromého přístupu Zscaler (ZPA).](https://admin.private.zscaler.com/) Přejděte na **nástroj Správa > konfiguraci protokolu IdP**.

    ![Konzola pro správu soukromého přístupu Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Ověřte, zda je nakonfigurován idp pro **jednotné přihlašování.** Pokud není nastaven žádný idp, přidejte ho kliknutím na ikonu plus v pravém horním rohu obrazovky.

    ![Zscaler soukromý přístup (ZPA) Přidat SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Chcete-li přidat idp, postupujte podle průvodce **přidáním konfigurace protokolu IdP.** Ponechte pole **jednotné přihlašování** nastavené na **uživatel .** Zadejte **název** a vrozené seznamu **vyberte domény.** Kliknutím na **Tlačítko Další** přejdete do dalšího okna.

    ![Zscaler soukromý přístup (ZPA) Přidat IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Stáhněte si **certifikát poskytovatele služeb**. Kliknutím na **Tlačítko Další** přejdete do dalšího okna.

    ![Certifikát SP pro soukromý přístup Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. V dalším okně nahrajte dříve stažený **certifikát poskytovatele služeb.**

    ![Zscaler Private Access (ZPA) upload certifikát](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Posunutím dolů zadejte **adresu URL jednotného přihlašování** a **ID entity idp**.

    ![Zscaler soukromé přístup (ZPA) IdP ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Posuňte se dolů **a povolte synchronizaci SCIM**. Klikněte na **tlačítko Generovat nový token.** Zkopírujte **žeton nosiče**. Tato hodnota se zadá do pole Tajný token na kartě Zanář aplikace Zscaler Private Access (ZPA) na webu Azure Portal.

    ![Zscaler soukromý přístup (ZPA) vytvořit token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Chcete-li vyhledat **adresu URL klienta** , přejděte **na položku Konfigurace protokolu IdP pro > správu**. Klikněte na název nově přidané konfigurace Protokolu IdP uvedené na stránce.

    ![Zscaler soukromý přístup (ZPA) Idp Název](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Posunutím dolů zobrazíte **koncový bod poskytovatele služeb SCIM** na konci stránky. Zkopírujte **koncový bod poskytovatele služeb SCIM**. Tato hodnota se zadá do pole ADRESA URL klienta na kartě Zanáč vaší aplikace Zscaler Private Access (ZPA) na webu Azure Portal.

    ![Zscaler soukromý přístup (ZPA) SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Přidání soukromého přístupu Zscaler (ZPA) z galerie

Před konfigurací Zscaler Private Access (ZPA) pro automatické zřizování uživatelů pomocí Azure AD, je třeba přidat Zscaler Private Access (ZPA) z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Zscaler Private Access (ZPA) z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler Private Access (ZPA),** vyberte v panelu výsledků **možnost Zscaler Private Access (ZPA)** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

    ![Soukromý přístup Zscaler (ZPA) v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurace automatického zřizování uživatelů pro soukromý přístup Zscaler (ZPA) 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Zscaler Private Access (ZPA) na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro Zscaler Private Access (ZPA) podle pokynů uvedených v [kurzu jednotného přihlášení Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

> [!NOTE]
> Další informace o koncovém bodu SCIM aplikace Zscaler Private Access naleznete na [tomto](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro soukromý přístup Zscaler (ZPA) ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Zscaler Private Access (ZPA).**

    ![Odkaz Zscaler Private Access (ZPA) v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** zadejte hodnotu **koncového bodu poskytovatele služeb SCIM** načtenou dříve v **adrese URL klienta**. Zadejte hodnotu **nosné tokenu** načtenou dříve v **tajném tokenu**. Klikněte na **test připojení** a ujistěte se, že Azure AD můžete připojit k Zscaler privátní přístup (ZPA). Pokud se připojení nezdaří, ujistěte se, že váš účet Zscaler Private Access (ZPA) má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **synchronizovat uživatele služby Azure Active Directory s privátním přístupem Zscaler (ZPA).**

    ![Mapování uživatelů soukromého přístupu Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Zscaler Private Access (ZPA) v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Zscaler Private Access (ZPA) pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy soukromého přístupu Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s privátním přístupem Zscaler (ZPA).**

    ![Mapování skupiny soukromého přístupu Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Zscaler Private Access (ZPA) v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Zscaler Private Access (ZPA) pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny soukromého přístupu Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro zišterový privátní přístup (ZPA), změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit zscaler private access (ZPA) výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v soukromém přístupu Zscaler (ZPA).

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

