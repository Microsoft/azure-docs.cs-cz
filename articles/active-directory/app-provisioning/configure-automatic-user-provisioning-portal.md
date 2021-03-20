---
title: Správa zřizování uživatelů pro podnikové aplikace v Azure Active Directory
description: Naučte se spravovat zřizování uživatelských účtů pro podnikové aplikace pomocí Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5dceeb11ed9a4d6af88650a6146f58db412748d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579412"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Správa zřizování uživatelských účtů pro podnikové aplikace v Azure Portal

Tento článek popisuje obecné kroky pro správu automatického zřizování uživatelských účtů a zrušení zajišťování pro aplikace, které ho podporují. *Zřizování uživatelských účtů* je proces vytváření, aktualizace nebo zakázání záznamů uživatelských účtů v úložišti profilů místního uživatele aplikace. Většina cloudových a SaaS aplikací ukládá role uživatelů a oprávnění do vlastního místního úložiště profilů uživatele a k získání jednotného přihlašování se *vyžaduje* přítomnost takového záznamu uživatele v místním úložišti uživatele a přístup k práci. Další informace o automatickém zřizování uživatelských účtů najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů při SaaS aplikací pomocí Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) obsahuje galerii obsahující tisíce předem integrovaných aplikací, u kterých je povolené Automatické zřizování pomocí Azure AD. Měli byste začít tak, že v [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](../saas-apps/tutorial-list.md), provedete konkrétní kurz nastavení zřizování, který je specifický pro vaši aplikaci. Pravděpodobně najdete podrobné pokyny pro konfiguraci aplikace i služby Azure AD pro vytvoření zřizovacího připojení.

## <a name="finding-your-apps-in-the-portal"></a>Hledání aplikací na portálu

Pomocí portálu Azure Active Directory můžete zobrazit a spravovat všechny aplikace, které jsou nakonfigurované pro jednotné přihlašování v adresáři. Podnikové aplikace jsou aplikace, které jsou nasazené a používané v rámci vaší organizace. Pomocí těchto kroků můžete zobrazit a spravovat podnikové aplikace:

1. Otevřete [portál Azure Active Directory](https://aad.portal.azure.com).
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte libovolnou aplikaci a načtěte její podokno prostředků, kde můžete zobrazit sestavy a spravovat nastavení aplikace.
1. Vyberte **zřizování** pro správu nastavení zřizování uživatelských účtů pro vybranou aplikaci.

   ![Zřizování obrazovky pro správu nastavení zřizování uživatelských účtů](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Režimy zřizování

Podokno **zřizování** začíná nabídkou **režim** , která ukazuje režimy zřizování podporované pro podnikovou aplikaci a umožňuje jejich konfiguraci. K dispozici jsou tyto možnosti:

* **Automaticky** – Tato možnost se zobrazí, pokud Azure AD podporuje automatické zřizování a rušení uživatelských účtů na základě rozhraní API pro tuto aplikaci. Tento režim vyberte, pokud chcete zobrazit rozhraní, které pomáhá správcům:

  * Konfigurace Azure AD pro připojení k rozhraní API pro správu uživatelů aplikace
  * Vytvořte mapování účtů a pracovní postupy, které definují, jak by se data uživatelského účtu měla přesměrovat mezi Azure AD a aplikací.
  * Správa služby zřizování Azure AD

* **Ruční** – Tato možnost se zobrazí, pokud Azure AD nepodporuje automatické zřizování uživatelských účtů pro tuto aplikaci. V takovém případě musí být záznamy uživatelských účtů uložené v aplikaci spravovány pomocí externího procesu založeného na možnostech správy uživatelů a zřizování poskytovaných aplikací (které můžou zahrnovat zřizování za běhu za běhu).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

Pokud chcete zadat nastavení pro přihlašovací údaje správce, mapování, spouštění a zastavování a synchronizaci, vyberte možnost **automaticky** .

### <a name="admin-credentials"></a>Přihlašovací údaje správce

Rozbalte **přihlašovací údaje správce** a zadejte přihlašovací údaje požadované pro Azure AD pro připojení k rozhraní API pro správu uživatelů aplikace. Požadovaný vstup se liší v závislosti na aplikaci. Další informace o typech a požadavcích přihlašovacích údajů pro konkrétní aplikace najdete v [kurzu konfigurace této konkrétní aplikace](user-provisioning.md).

Vyberte **Test připojení** a otestujte přihlašovací údaje tím, že se Azure AD pokusí připojit k aplikaci zřizování aplikace pomocí zadaných přihlašovacích údajů.

### <a name="mappings"></a>Mapování

Rozbalením **mapování** můžete zobrazit a upravit atributy uživatele, které se při zřizování nebo aktualizaci uživatelských účtů flowují mezi službou Azure AD a cílovou aplikací.

Existuje předem nakonfigurovaná sada mapování mezi uživatelskými objekty Azure AD a všemi uživatelskými objekty aplikace v SaaS. Některé aplikace také spravují objekty skupiny. Vyberte mapování v tabulce pro otevření editoru mapování, kde je můžete zobrazit a přizpůsobit.

Mezi podporovaná přizpůsobení patří:

* Povolení a zakázání mapování pro konkrétní objekty, jako je objekt uživatele Azure AD, na objekt uživatele aplikace SaaS
* Úprava atributů, které se přesměrují z objektu uživatele Azure AD na objekt uživatele aplikace. Další informace o mapování atributů naleznete v tématu [porozumění typům mapování atributů](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrování akcí zřizování, které Azure AD spouští na cílové aplikaci. Místo toho, abyste mohli plně synchronizovat objekty služby Azure AD, můžete omezit spouštění akcí.

  Například jenom vyberte **aktualizovat** a Azure AD aktualizuje jenom existující uživatelské účty v aplikaci, ale nevytvoří nové. Jenom vyberte **vytvořit** a Azure jenom vytvoří nové uživatelské účty, ale neaktualizuje stávající. Tato funkce umožňuje správcům vytvářet různá mapování pro vytváření účtů a aktualizace pracovních postupů.

* Přidání nového mapování atributu. V dolní části podokna **mapování atributů** vyberte **Přidat nové mapování** . Vyplňte formulář **Upravit atribut** a výběrem **OK** přidejte nové mapování do seznamu.

### <a name="settings"></a>Nastavení

Rozbalte **Nastavení** a nastavte e-mailovou adresu pro příjem oznámení a to, jestli se mají zobrazovat výstrahy o chybách. Můžete také vybrat rozsah uživatelů, které se mají synchronizovat. Můžete si vybrat, jestli chcete synchronizovat všechny uživatele a skupiny, nebo jenom ty, které jsou přiřazené.

### <a name="provisioning-status"></a>Stav zřizování 

Pokud je zřizování pro aplikaci poprvé zapnuté, zapněte službu změnou **stavu zřizování** na **zapnuto**. Tato změna způsobí, že služba zřizování Azure AD spustí počáteční cyklus. Načte uživatele, kteří jsou přiřazeni v části **Uživatelé a skupiny** , zadá dotaz na cílovou aplikaci pro ně a potom spustí akce zřizování definované v oddílu **mapování** Azure AD. Během tohoto procesu služba zřizování ukládá data uložená v mezipaměti s informacemi o tom, jaké uživatelské účty spravuje, takže nespravované účty v cílových aplikacích, které nikdy nejsou v oboru pro přiřazení, nejsou ovlivněné operacemi zrušení zřízení. Po počátečním cyklu služba zřizování automaticky synchronizuje objekty uživatelů a skupin v intervalu 40 minut.

Změňte **stav zřizování** na **vypnuto**  , aby se služba zřizování zastavila. V tomto stavu Azure v aplikaci nevytvoří, neaktualizuje ani neodebere žádné objekty uživatelů ani skupin. Změňte stav zpět na **zapnuto** a služba se ponechá tam, kde skončila.
