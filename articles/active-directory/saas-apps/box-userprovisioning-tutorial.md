---
title: 'Kurz: Konfigurace boxu pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: df4031a590eb4547d4327cebe96ccbe63d21785a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437804"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Kurz: Konfigurace boxu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v poli a Azure AD pro automatické zřízení a zrušení zřizování uživatelských účtů ze služby Azure AD do boxu.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s polem budete potřebovat následující položky:

- Tenant Azure AD
- Krabicový obchodní plán nebo lepší

> [!NOTE]
> Když testujete kroky v tomto kurzu, doporučujeme *Nepoužívat produkční* prostředí.

> [!NOTE]
> Aplikace musí být nejprve povolené v aplikaci box.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

K otestování kroků v tomto kurzu použijte tato doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Přiřazování uživatelů do boxu 

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny v Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci box. Po rozhodnutí můžete tyto uživatele přiřadit k aplikaci box podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="assign-users-and-groups"></a>Přiřazení uživatelů a skupin
Karta **> uživatelé a skupiny** v Azure Portal vám umožní určit, kteří uživatelé a skupiny by se měli udělit přístup k boxu. Přiřazení uživatele nebo skupiny způsobí, že dojde k následujícím akcím:

* Služba Azure AD povoluje ověřování přiřazeného uživatele (buď přímým přiřazením, nebo členstvím ve skupině). Pokud uživatel není přiřazený, služba Azure AD jim nepovoluje přihlašovat se k boxu a na přihlašovací stránce Azure AD vrátí chybu.
* Dlaždice aplikace pro box se přidá do [spouštěče aplikace](../manage-apps/end-user-experiences.md)uživatele.
* Pokud je povolené Automatické zřizování, přidaní uživatelé nebo skupiny se přidají do fronty zřizování, aby se automaticky zřídily.
  
  * Pokud byly nakonfigurovány pouze uživatelské objekty, jsou všechny přímo přiřazené uživatele umístěny do fronty zřizování a všichni uživatelé, kteří jsou členy všech přiřazených skupin, budou umístěni do fronty zřizování. 
  * Pokud byly objekty skupiny nakonfigurované tak, aby se zřídily, jsou všechny přiřazené objekty skupiny zřízené do pole a všichni uživatelé, kteří jsou členy těchto skupin. Členství ve skupinách a uživatelích se při zápisu do boxu zachová.

Pomocí **atributů > karta jednotného přihlašování** můžete nakonfigurovat, které atributy uživatele (nebo deklarace identity) se mají uvést do pole během ověřování založeného na SAML, a **> kartu zřizování** a nakonfigurovat, jak budou atributy uživatelů a skupin v rámci služby Azure AD k dispozici během operací zřizování.

### <a name="important-tips-for-assigning-users-to-box"></a>Důležité tipy pro přiřazování uživatelů do boxu 

*   Doporučuje se k otestování konfigurace zřizování používat jeden uživatel Azure AD přiřazený k tomuto boxu. Další uživatele a skupiny můžete přiřadit později.

*   Při přiřazování uživatele k poli musíte vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit automatizované zřizování uživatelů

Tato část vás provede připojením rozhraní API pro zřizování uživatelského účtu Azure AD k poli a konfigurací zřizovací služby k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v poli podle přiřazení uživatelů a skupin ve službě Azure AD.

Pokud je povolené Automatické zřizování, přidaní uživatelé nebo skupiny se přidají do fronty zřizování, aby se automaticky zřídily.
    
 * Jsou-li pro zřizování nastaveny pouze uživatelské objekty, jsou přímo přiřazení uživatelé umístěni do fronty zřizování a všichni uživatelé, kteří jsou členy všech přiřazených skupin, budou umístěni do fronty zřizování. 
    
 * Pokud byly objekty skupiny nakonfigurované tak, aby se zřídily, jsou všechny přiřazené objekty skupiny zřízené do pole a všichni uživatelé, kteří jsou členy těchto skupin. Členství ve skupinách a uživatelích se při zápisu do boxu zachová.

> [!TIP] 
> Můžete se také rozhodnout povolit pro box jednu Sign-On založenou na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů:

Cílem této části je obrysy, jak povolit zřizování uživatelských účtů služby Active Directory.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

2. Pokud jste již nakonfigurovali pole pro jednotné přihlašování, vyhledejte instanci pole pomocí vyhledávacího pole. V opačném případě **vyberte v galerii** aplikací možnost **Přidat** a vyhledat. Ve výsledcích hledání vyberte políčko a přidejte ho do seznamu aplikací.

3. Vyberte instanci pole a pak vyberte kartu **zřizování** .

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**. 

    ![Snímek obrazovky s kartou zřizování pro box v Azure Portal. Režim zřizování je nastaven na automatické a v přihlašovacích údajích správce je zvýrazněná možnost autorizovat.](./media/box-userprovisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** klikněte na **autorizovat** . otevře se dialogové okno přihlášení k boxu v novém okně prohlížeče.

6. Na stránce **přihlášení pro udělení přístupu k poli** zadejte požadované přihlašovací údaje a klikněte na **autorizovat**. 
   
    ![Snímek obrazovky přihlášení, který uděluje přístup k boxu, zobrazuje položku pro E-mail a heslo a tlačítko autorizovat.](./media/box-userprovisioning-tutorial/IC769546.png "Povolit automatické zřizování uživatelů")

7. Kliknutím na **udělit přístup k tomuto poli** autorizujte tuto operaci a vraťte se k Azure Portal. 
   
    ![Snímek obrazovky s přístupem pro autorizaci v krabici, která zobrazuje vysvětlující zprávu a tlačítko pro udělení přístupu k boxu.](./media/box-userprovisioning-tutorial/IC769549.png "Povolit automatické zřizování uživatelů")

8. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k aplikaci box. Pokud se připojení nepovede, ujistěte se, že má váš účet box oprávnění správce týmu, a zkuste znovu provést krok **autorizovat** .

9. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko.

10. Klikněte na **Uložit.**

11. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé do boxu.**

12. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do boxu. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v poli pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

13. Pokud chcete povolit službu Azure AD Provisioning pro box, změňte **stav zřizování** na **zapnuto** v části nastavení.

14. Klikněte na **Uložit.**

Tím se spustí počáteční synchronizace všech uživatelů a skupin přiřazených do pole v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci box.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

V tenantovi vašeho tenanta jsou synchronizující uživatelé uvedeni v části **spravované uživatele** v **konzole pro správu**.

![Stav integrace](./media/box-userprovisioning-tutorial/IC769556.png "Stav integrace")


## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](box-tutorial.md)