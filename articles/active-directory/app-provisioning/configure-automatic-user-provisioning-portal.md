---
title: Správa zřizování uživatelů pro podnikové aplikace ve službě Azure AD
description: Zjistěte, jak spravovat zřizování uživatelských účtů pro podnikové aplikace pomocí Služby Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264125"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Správa zřizování uživatelských účtů pro podnikové aplikace na webu Azure Portal

Tento článek popisuje obecné kroky pro správu automatického zřizování uživatelských účtů a zrušení zřizování aplikací, které jej podporují. *Zřizování uživatelských účtů* je akt vytváření, aktualizace nebo zakázání záznamů uživatelských účtů v úložišti místních uživatelských profilů aplikace. Většina cloudových a SaaS aplikací ukládá roli uživatelů a oprávnění v úložišti vlastního místního uživatelského profilu uživatele a pro jednotné přihlašování a přístup k práci je *vyžadován* a přítomnost takového uživatelského záznamu v místním úložišti uživatele. Další informace o automatickém zřizování uživatelských účtů najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) má galerii, která obsahuje tisíce předem integrovaných aplikací, které jsou povolené pro automatické zřizování pomocí Azure AD. Měli byste začít tím, že najdete zřizování instalační kurz specifický pro vaši aplikaci v [seznamu kurzů o tom, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Pravděpodobně najdete podrobné pokyny pro konfiguraci aplikace a Azure AD k vytvoření připojení zřizování.

## <a name="finding-your-apps-in-the-portal"></a>Hledání aplikací na portálu

Portál Azure Active Directory slouží k zobrazení a správě všech aplikací, které jsou nakonfigurované pro jednotné přihlašování v adresáři. Podnikové aplikace jsou aplikace, které jsou nasazené a používané ve vaší organizaci. Chcete-li zobrazit a spravovat podnikové aplikace, postupujte takto:

1. Otevřete [portál Azure Active Directory](https://aad.portal.azure.com).
1. V levém podokně vyberte **podnikové aplikace.** Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací, které byly přidány z galerie.
1. Vyberte libovolnou aplikaci, ve které chcete načíst podokno prostředků, ve kterém můžete zobrazit sestavy a spravovat nastavení aplikace.
1. Vyberte **Zřizování,** chcete-li spravovat nastavení zřizování uživatelského účtu pro vybranou aplikaci.

   ![Obrazovka zřizování pro správu nastavení zřizování uživatelských účtů](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Režimy zřizování

Podokno **Zřizování** začíná nabídkou **Režim,** která zobrazuje režimy zřizování podporované pro podnikovou aplikaci a umožňuje jejich konfiguraci. Dostupné možnosti zahrnují:

* **Automatické** – Tato možnost se zobrazí, pokud Azure AD podporuje automatické zřizování na základě rozhraní API nebo zrušení zřizování uživatelských účtů do této aplikace. Výběrem tohoto režimu zobrazíte rozhraní, které pomáhá správcům:

  * Konfigurace služby Azure AD pro připojení k rozhraní API pro správu uživatelů aplikace
  * Vytvoření mapování účtů a pracovních postupů, které definují, jak by měla data uživatelských účtů natékat mezi Azure AD a aplikací
  * Správa zřizovací služby Azure AD

* **Ruční** – Tato možnost se zobrazí, pokud Azure AD nepodporuje automatické zřizování uživatelských účtů do této aplikace. V takovém případě musí být záznamy uživatelských účtů uložené v aplikaci spravovány pomocí externího procesu na základě možností správy a zřizování uživatelů poskytovaných touto aplikací (které mohou zahrnovat zřizování SAML Just-In-Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

Vyberte možnost **Automaticky,** chcete-li určit nastavení pověření správce, mapování, spuštění a zastavení a synchronizace.

### <a name="admin-credentials"></a>Pověření správce

Rozbalte **pověření správce** a zadejte přihlašovací údaje, které je vyžadováno pro připojení Azure AD k rozhraní API pro správu uživatelů aplikace. Požadovaný vstup se liší v závislosti na aplikaci. Další informace o typech pověření a požadavcích pro konkrétní aplikace naleznete [v kurzu konfigurace pro danou konkrétní aplikaci](user-provisioning.md).

Vyberte **Testovat připojení** k testování přihlašovacích údajů tím, že se Azure AD pokusí připojit k aplikaci pro zřizování aplikace pomocí zadaných přihlašovacích údajů.

### <a name="mappings"></a>Mapování

Rozbalte **mapování** a zobrazte a upravte atributy uživatele, které točí mezi Azure AD a cílovou aplikací při zřizování nebo aktualizaci uživatelských účtů.

Je předkonfigurovaná sada mapování mezi objekty uživatelů Azure AD a objekty uživatele každé aplikace SaaS. Některé aplikace spravují jiné typy objektů, například Skupiny nebo Kontakty. Vyberte mapování v tabulce a otevřete editor mapování vpravo, kde je můžete zobrazit a přizpůsobit.

![Zobrazí obrazovku Mapování atributů.](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Mezi podporovaná vlastní nastavení patří:

* Povolení a zakázání mapování pro konkrétní objekty, jako je například objekt uživatele Azure AD k objektu uživatele aplikace SaaS.
* Úpravy atributů, které proudí z objektu uživatele Azure AD do objektu uživatele aplikace. Další informace o mapování atributů naleznete v [tématu Principy typů mapování atributů](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrování zřizování akce, které Azure AD běží na cílové aplikace. Místo toho, aby Azure AD plně synchronizovat objekty, můžete omezit spuštění akce.

  Například vyberte jenom **aktualizace** a Azure AD pouze aktualizuje existující uživatelské účty v aplikaci, ale nevytváří nové. Vyberte jenom **vytvořit** a Azure pouze vytvoří nové uživatelské účty, ale neaktualizuje stávající. Tato funkce umožňuje správcům vytvářet různá mapování pro vytváření účtů a aktualizaci pracovních postupů.

* Přidání nového mapování atributů. V dolní části podokna **Mapování atributů** vyberte **Přidat nové mapování.** Vyplňte formulář **Upravit atribut** a výběrem **ok** přidejte nové mapování do seznamu.

### <a name="settings"></a>Nastavení

Můžete spustit a zastavit službu zřizování Azure AD pro vybranou aplikaci v oblasti **Nastavení** na obrazovce **Zřizování.** Můžete také vymazat zřizovací mezipaměti a restartovat službu.

Pokud je zřizování poprvé povoleno pro aplikaci, zapněte službu změnou **stavu zřizování** **na Zapnuto**. Tato změna způsobí, že služba zřizování Azure AD spustit počáteční cyklus. Přečte uživatele přiřazené v části **Uživatelé a skupiny,** dotazuje se na cílovou aplikaci a pak spustí akce zřizování definované v části **Mapování** Azure AD. Během tohoto procesu zřizovací služba ukládá data uložená v mezipaměti o tom, jaké uživatelské účty spravuje, takže nespravované účty uvnitř cílových aplikací, které nikdy nebyly v oboru pro přiřazení, nejsou ovlivněny operacemi zrušení zřizování. Po počátečním cyklu služba zřizování automaticky synchronizuje objekty uživatelů a skupin ve čtyřicetiminutovém intervalu.

Změňte **stav zřizování** na **Vypnuto** pozastavit zřizování služby. V tomto stavu Azure nevytváří, neaktualizuje ani neodebere žádné objekty uživatelů nebo skupin v aplikaci. Změňte stav zpět **na Zapnuto** a služba se naváže tam, kde skončila.

**Vymazání aktuálního stavu a restartování synchronizace** spustí počáteční cyklus. Služba pak znovu vyhodnotí všechny uživatele ve zdrojovém systému a určí, zda jsou v oboru pro zřizování. To může být užitečné, když je aplikace aktuálně v karanténě nebo je třeba provést změnu mapování atributů. Všimněte si, že počáteční cyklus trvá déle než typické přírůstkové cyklu z důvodu počtu objektů, které je třeba vyhodnotit. Další informace o výkonu počátečních a přírůstkových cyklů naleznete [zde](application-provisioning-when-will-provisioning-finish-specific-user.md). 
