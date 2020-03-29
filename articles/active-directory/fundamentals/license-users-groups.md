---
title: Přiřazení nebo odebrání licencí – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přiřadit nebo odebrat licence služby Azure Active Directory uživatelům nebo skupinám.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128830"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Přiřazení nebo odebrání licencí na portálu Azure Active Directory

Mnoho služeb Azure Active Directory (Azure AD) vyžaduje, abyste pro tuto službu licencovali každého uživatele nebo skupiny (a přidružené členy). K licencovaným službám Azure AD, pro které je to pravda, budou mít přístup a používat licencované služby Azure AD, pro které budou mít přístup a používat jet. Licence se použijí na klienta a nepřevádí se na jiné klienty. 

## <a name="available-license-plans"></a>Dostupné licenční plány

Pro službu Azure AD je k dispozici několik licenčních plánů, včetně:

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

Konkrétní informace o jednotlivých licenčních plánech a souvisejících licenčních podrobnostech naleznete v tématu [Jaká licence potřebuji?](https://azure.microsoft.com/pricing/details/active-directory/).

Ne všechny služby společnosti Microsoft jsou k dispozici ve všech umístěních. Před přiřazením licence ke skupině je nutné zadat **umístění využití** pro všechny členy. Tuto hodnotu můžete nastavit v oblasti **Nastavení profilu &gt; uživatelů služby &gt; &gt; Azure Active Directory** ve službě Azure AD. Každý uživatel, jehož umístění využití není zadán dědí umístění organizace Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Zobrazení licenčních plánů a podrobností plánu

Můžete zobrazit dostupné plány služeb, včetně jednotlivých licencí, zkontrolovat data vypršení platnosti a zobrazit počet dostupných přiřazení.

### <a name="to-find-your-service-plan-and-plan-details"></a>Vyhledání servisního plánu a podrobnosti plánu

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu správce licencí ve vaší organizaci Azure AD.

1. Vyberte **Službu Azure Active Directory**a pak vyberte **Licence**.

    ![Stránka Licence s počtem zakoupených služeb a přiřazenými licencemi](media/license-users-groups/license-details-blade.png)

1. Výběrem odkazu **Zakoupené** zobrazíte stránku **Produkty** a zobrazí tenová čísla **Přiřazené**, **Dostupné**a **Vypršení platnosti pro** vaše licenční plány.

    ![stránka služby s plány servisních licencí a souvisejícími informacemi o licencích](media/license-users-groups/license-products-blade-with-products.png)

1. Vyberte název plánu, chcete-li zobrazit licencované uživatele a skupiny.

## <a name="assign-licenses-to-users-or-groups"></a>Přiřazení licencí uživatelům nebo skupinám

Ujistěte se, že každý, kdo potřebuje používat licencovanou službu Azure AD, má příslušnou licenci. Licenční práva můžete přidat uživatelům nebo celé skupině.

### <a name="to-assign-a-license-to-a-user"></a>Přiřazení licence uživateli

1. Na stránce **Produkty** vyberte název licenčního plánu, který chcete uživateli přiřadit.

    ![stránka služby se zvýrazněným plánem licence na služby](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stránce přehled plánu licence vyberte **Přiřadit**.

    ![stránka služby se zvýrazněnou možností Přiřadit](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stránce **Přiřadit** vyberte **Uživatelé a skupiny a**pak vyhledejte a vyberte uživatele, které přiřazujete licenci.

    ![Přiřazení licenční stránky se zvýrazněným hledáním a možnostmi výběru](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Vyberte **možnosti přiřazení**, zkontrolujte, zda máte zapnuté příslušné možnosti licence, a pak vyberte **OK**.

    ![Stránka s možností licence se všemi možnostmi dostupnými v licenčním plánu](media/license-users-groups/license-option-blade-assignments.png)

    Aktualizace licenční stránky **Přiřazení,** které ukazují, že je vybrán uživatel a že jsou nakonfigurována přiřazení.

    > [!NOTE]
    > Ne všechny služby společnosti Microsoft jsou k dispozici ve všech umístěních. Před přiřazením licence uživateli je nutné zadat **umístění použití**. Tuto hodnotu můžete nastavit v oblasti **Nastavení profilu &gt; uživatelů služby &gt; &gt; Azure Active Directory** ve službě Azure AD. Každý uživatel, jehož umístění využití není zadán dědí umístění organizace Azure AD.

1. Vyberte **Přiřadit**.

    Uživatel je přidán do seznamu licencovaných uživatelů a má přístup k zahrnutým službám Azure AD.
    > [!NOTE]
    > Licence lze také přiřadit přímo uživateli ze stránky **Licence** uživatele. Pokud má uživatel licenci přiřazenou prostřednictvím členství ve skupině a chcete mu přiřadit stejnou licenci přímo, lze ji provést pouze na stránce **Produkty** uvedené v kroku 1.

### <a name="to-assign-a-license-to-a-group"></a>Přiřazení licence skupině

1. Na stránce **Produkty** vyberte název licenčního plánu, který chcete uživateli přiřadit.

    ![Čepel produktů se zvýrazněným licenčním plánem produktu](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stránce **Azure Active Directory Premium Plan 2** vyberte **Přiřadit**.

    ![Stránka Produkty se zvýrazněnou možností Přiřadit](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stránce **Přiřadit** vyberte **Uživatelé a skupiny a**pak vyhledejte a vyberte skupinu, které přiřazujete licenci.

    ![Přiřazení licenční stránky se zvýrazněným hledáním a možnostmi výběru](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Vyberte **možnosti přiřazení**, zkontrolujte, zda máte zapnuté příslušné možnosti licence, a pak vyberte **OK**.

    ![Stránka s možností licence se všemi možnostmi dostupnými v licenčním plánu](media/license-users-groups/license-option-blade-group-assignments.png)

    Aktualizace licenční stránky **Přiřazení,** které ukazují, že je vybrán uživatel a že jsou nakonfigurována přiřazení.

1. Vyberte **Přiřadit**.

    Skupina se přidá do seznamu licencovaných skupin a všichni členové mají přístup k zahrnutým službám Azure AD.

## <a name="remove-a-license"></a>Odebrání licence

Licenci můžete odebrat ze stránky uživatele Azure AD uživatele, ze stránky přehledu skupiny pro přiřazení **skupiny** nebo počínaje stránkou Licence Azure AD, abyste viděli uživatele a skupiny pro licenci.

### <a name="to-remove-a-license-from-a-user"></a>Odebrání licence uživateli

1. Na stránce **Licencovaní uživatelé** pro plán služeb vyberte uživatele, který by již neměl mít licenci. Například _Alain Charon_.

1. Vyberte **Odebrat licenci**.

    ![Stránka Licencovaní uživatelé se zvýrazněnou možností Odebrat licenci](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licence, které uživatel dědí ze skupiny, nelze odebrat přímo. Místo toho je třeba odebrat uživatele ze skupiny, ze které dědí licenci.

### <a name="to-remove-a-license-from-a-group"></a>Odebrání licence ze skupiny

1. Na stránce **Licencované skupiny** pro licenční plán vyberte skupinu, která by již neměla mít licenci.

1. Vyberte **Odebrat licenci**.

    ![Stránka Licencované skupiny se zvýrazněnou možností Odebrat licenci](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Když místní uživatelský účet synchronizovaný s Azure AD vypadne z oboru pro synchronizaci nebo při odebrání synchronizace, uživatel je obnovitelné odstranění ve službě Azure AD. V takovém případě budou licence přiřazené uživateli přímo nebo prostřednictvím licencí založených na skupinách označeny jako **pozastavené,** nikoli **odstraněny**.

## <a name="next-steps"></a>Další kroky

Po přiřazení licencí můžete provádět následující procesy:

- [Identifikace a řešení problémů s přiřazením licencí](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Přidání licencovaných uživatelů do skupiny pro licencování](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scénáře, omezení a známé problémy pomocí skupin ke správě licencí ve službě Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Přidání nebo změna informací o profilu](active-directory-users-profile-azure-portal.md)
