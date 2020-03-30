---
title: 'Kurz: Konfigurace boxu pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a boxem .
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1397b4189a9c2c15e3878687ea8c67c1da7567f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058565"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Kurz: Konfigurace rámečku pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést v Box a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD do boxu.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s box, budete potřebovat následující položky:

- Klient Azure AD
- Box Obchodní plán nebo lepší

> [!NOTE]
> Při testování kroků v tomto kurzu doporučujeme *nepoužívat* produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Přiřazení uživatelů do pole 

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Box. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Box podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Přiřazení uživatelů a skupin
**Karta Box > Users and Groups** na webu Azure Portal umožňuje určit, kterým uživatelům a skupinám by měl být udělen přístup k boxu. Přiřazení uživatele nebo skupiny způsobí následující věci:

* Azure AD umožňuje přiřazenému uživateli (buď přímým přiřazením nebo členstvím ve skupině) k ověření do boxu. Pokud uživatel není přiřazen, pak Azure AD neumožňuje přihlášení k boxu a vrátí chybu na přihlašovací stránce Azure AD.
* Dlaždice aplikace pro box je přidána do [spouštěče aplikací](../manage-apps/end-user-experiences.md)uživatele .
* Pokud je povoleno automatické zřizování, pak přiřazené uživatele nebo skupiny jsou přidány do fronty zřizování, které mají být automaticky zřízeny.
  
  * Pokud byly nakonfigurovány pouze uživatelské objekty, které mají být zřízeny, pak jsou všichni přímo přiřazení uživatelé umístěni do fronty zřizování a všichni uživatelé, kteří jsou členy všech přiřazených skupin, jsou umístěni do fronty zřizování. 
  * Pokud byly objekty skupiny nakonfigurovány tak, aby byly zřízeny, pak jsou všechny přiřazené objekty skupiny zřízeny do pole a všichni uživatelé, kteří jsou členy těchto skupin. Členství ve skupině a uživateli se po zápisu do boxu zachová.

Pomocí karty **Atributy > jednotného přihlášení** můžete nakonfigurovat, které uživatelské atributy (nebo deklarace identity) se zobrazí do pole během ověřování na základě SAML, a na kartě **Atributy > zřizování** konfigurace způsobu toku atributů uživatelů a skupin z Azure AD do boxu během operací zřizování.

### <a name="important-tips-for-assigning-users-to-box"></a>Důležité tipy pro přiřazení uživatelů do boxu 

*   Doporučuje se, aby jeden uživatel Azure AD přiřazený box k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

*   Při přiřazování uživatele do pole je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolení automatického zřizování uživatelů

Tato část vás provede připojením vašeho Azure AD k rozhraní API pro zřizování uživatelských účtů boxu a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v poli na základě přiřazení uživatelů a skupin ve službě Azure AD.

Pokud je povoleno automatické zřizování, pak přiřazené uživatele nebo skupiny jsou přidány do fronty zřizování, které mají být automaticky zřízeny.
    
 * Pokud jsou konfigurovány pouze uživatelské objekty, které mají být zřízeny, pak jsou přímo přiřazení uživatelé umístěni do fronty zřizování a všichni uživatelé, kteří jsou členy všech přiřazených skupin, jsou umístěni do fronty zřizování. 
    
 * Pokud byly objekty skupiny nakonfigurovány tak, aby byly zřízeny, pak jsou všechny přiřazené objekty skupiny zřízeny do pole a všichni uživatelé, kteří jsou členy těchto skupin. Členství ve skupině a uživateli se po zápisu do boxu zachová.

> [!TIP] 
> Můžete se také rozhodnout povolit jednotné přihlašování pro box založené na SAML podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů:

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory do pole.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již nakonfigurovali pole pro jednotné přihlašování, vyhledejte svou instanci pole pomocí vyhledávacího pole. V opačném případě vgalerii aplikace vyberte **Přidat** a vyhledat **pole.** Ve výsledcích hledání vyberte Pole a přidejte ho do seznamu aplikací.

3. Vyberte svou instanci pole a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**. 

    ![Zajišťování](./media/box-userprovisioning-tutorial/provisioning.png)

5. V části **Pověření správce** klikněte na **Autorizovat** a otevřete dialogové okno pro přihlášení do nového okna prohlížeče.

6. Na stránce **Přihlásit se k udělení přístupu k poli** zadejte požadovaná pověření a klepněte na tlačítko **Autorizovat**. 
   
    ![Povolit automatické zřizování uživatelů](./media/box-userprovisioning-tutorial/IC769546.png "Povolit automatické zřizování uživatelů")

7. Kliknutím na **Udělit přístup boxu** autorizujte tuto operaci a vraťte se na portál Azure. 
   
    ![Povolit automatické zřizování uživatelů](./media/box-userprovisioning-tutorial/IC769549.png "Povolit automatické zřizování uživatelů")

8. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Box. Pokud se připojení nezdaří, ujistěte se, že váš účet Box má oprávnění správce týmu a zkuste krok **"Autorizovat"** znovu.

9. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

10. Klikněte na **Uložit.**

11. V části Mapování vyberte **synchronizovat pole Synchronizovat uživatele služby Azure Active Directory.**

12. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do boxu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v poli pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

13. Chcete-li povolit službu zřizování Azure AD pro box, změňte **stav zřizování** **na Zapnuto** v části Nastavení

14. Klikněte na **Uložit.**

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených k poli v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Box.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

V tenantovi Boxu jsou synchronizovaní uživatelé uvedeni v části **Spravní uživatelé** v **Konzole pro správu**.

![Stav integrace](./media/box-userprovisioning-tutorial/IC769556.png "Stav integrace")


## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](box-tutorial.md)
