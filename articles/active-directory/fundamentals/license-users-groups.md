---
title: Přiřadit nebo odebrat licence – Azure Active Directory | Microsoft Docs
description: Pokyny k přiřazení nebo odebrání licencí Azure Active Directory uživatelům nebo skupinám.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50e0fe1cf3bb628c40e774423e45ea88d6ab78a5
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359709"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Přiřazení nebo odebrání licencí na portálu Azure Active Directory

Mnoho služeb Azure Active Directory (Azure AD) vyžaduje, abyste pro danou službu měli licenci na každého z vašich uživatelů nebo skupin (a přidružených členů). Pouze uživatelé s aktivními licencemi budou moci získat přístup k licencovaným službám Azure AD, pro které platí, a používat je. Licence se používají pro jednotlivé klienty a nemigrují se do jiných tenantů. 

## <a name="available-license-plans"></a>Dostupné plány licencí

Pro službu Azure AD je k dispozici několik plánů licencí, mezi které patří:

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

Konkrétní informace o jednotlivých licenčních plánech a související informace o licencování najdete v tématu [co je potřeba](https://azure.microsoft.com/pricing/details/active-directory/). Pokud si chcete zaregistrovat licenční plány Azure AD Premium, najdete je [tady](./active-directory-get-started-premium.md).

Ne všechny služby společnosti Microsoft jsou k dispozici ve všech umístěních. Předtím, než může být licence přiřazena ke skupině, je nutné zadat **umístění použití** pro všechny členy. Tuto hodnotu můžete nastavit v oblasti ** &gt; &gt; &gt; nastavení profilu Azure Active Directory uživatelů** v Azure AD. Žádný uživatel, jehož umístění pro použití není zadaný, zdědí umístění organizace Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Zobrazení plánů licencí a podrobností plánu

Můžete zobrazit dostupné plány služeb, včetně jednotlivých licencí, kontrolovat nevyřízená data vypršení platnosti a zobrazit počet dostupných přiřazení.

### <a name="to-find-your-service-plan-and-plan-details"></a>Postup vyhledání plánu služby a podrobností plánu

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce licencí ve vaší organizaci Azure AD.

1. Vyberte **Azure Active Directory**a pak vyberte **licence**.

    ![Stránka licence s počtem zakoupených služeb a přiřazených licencí](media/license-users-groups/license-details-blade.png)

1. Pokud si chcete zobrazit stránku **Products** , vyberte odkaz **koupeno** a zobrazí se **jim přiřazená**, **k dispozici**a **brzo vyprší** čísla vašich licenčních plánů.

    ![Stránka služby s licenčními plány služby a informacemi o přidružených licencích](media/license-users-groups/license-products-blade-with-products.png)

1. Pokud chcete zobrazit licencované uživatele a skupiny, vyberte název plánu.

## <a name="assign-licenses-to-users-or-groups"></a>Přiřazení licencí uživatelům nebo skupinám

Ujistěte se, že kdokoli, kdo potřebuje použít licencovanou službu Azure AD, má odpovídající licenci. Můžete přidat licenční práva uživatelům nebo do celé skupiny.

### <a name="to-assign-a-license-to-a-user"></a>Přiřazení licence uživateli

1. Na stránce **produkty** vyberte název licenčního plánu, který chcete uživateli přiřadit.

    ![Stránka služby se zvýrazněným plánem licencí služby](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stránce Přehled licenčního plánu vyberte **přiřadit**.

    ![Stránka služby se zvýrazněnou možností přiřadit](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stránce **přiřadit** vyberte **Uživatelé a skupiny**a pak vyhledejte a vyberte uživatele, kterému přiřadíte licenci.

    ![Stránka přiřadit licenci se zvýrazněným hledáním a vybrat možnosti](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Vyberte **možnosti přiřazení**, ujistěte se, že máte zapnuté správné možnosti licence, a pak vyberte **OK**.

    ![Stránka možností licence se všemi možnostmi dostupnými v plánu licencí](media/license-users-groups/license-option-blade-assignments.png)

    Stránka **přiřadit licenci** se aktualizuje a zobrazí se informace o tom, že je uživatel vybraný a že jsou nakonfigurované přiřazení.

    > [!NOTE]
    > Ne všechny služby společnosti Microsoft jsou k dispozici ve všech umístěních. Předtím, než může být licence přiřazena uživateli, je nutné zadat **umístění používání**. Tuto hodnotu můžete nastavit v oblasti ** &gt; &gt; &gt; nastavení profilu Azure Active Directory uživatelů** v Azure AD. Žádný uživatel, jehož umístění pro použití není zadaný, zdědí umístění organizace Azure AD.

1. Vyberte **Přiřadit**.

    Uživatel se přidá do seznamu licencovaných uživatelů a bude mít přístup k zahrnutým službám Azure AD.
    > [!NOTE]
    > Licence je také možné přiřadit uživateli přímo ze stránky **licence** uživatele. Pokud má uživatel přiřazenou licenci prostřednictvím členství ve skupině a chcete přiřadit stejnou licenci k uživateli přímo, můžete ji provést pouze ze stránky **produkty** uvedené v kroku 1.

### <a name="to-assign-a-license-to-a-group"></a>Přiřazení licence ke skupině

1. Na stránce **produkty** vyberte název licenčního plánu, který chcete uživateli přiřadit.

    ![Okno produkty s zvýrazněným plánem multilicenčních produktů](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stránce **Azure Active Directory Premium Plan 2** vyberte **přiřadit**.

    ![Stránka produkty s možností zvýraznění přiřazení](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stránce **přiřadit** vyberte **Uživatelé a skupiny**a pak vyhledejte a vyberte skupinu, které přiřazujete licenci.

    ![Stránka přiřadit licenci se zvýrazněným hledáním a vyberte možnosti 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Vyberte **možnosti přiřazení**, ujistěte se, že máte zapnuté správné možnosti licence, a pak vyberte **OK**.

    ![Stránka možností licence se všemi možnostmi dostupnými v rámci licenčního plánu 2](media/license-users-groups/license-option-blade-group-assignments.png)

    Stránka **přiřadit licenci** se aktualizuje a zobrazí se informace o tom, že je uživatel vybraný a že jsou nakonfigurované přiřazení.

1. Vyberte **Přiřadit**.

    Skupina se přidá do seznamu licencovaných skupin a všichni členové mají přístup k zahrnutým službám Azure AD.

## <a name="remove-a-license"></a>Odebrání licence

Licenci můžete odebrat na stránce uživatele Azure AD, ze stránky přehled skupiny pro přiřazení skupiny, nebo na stránce **licence** Azure AD zobrazit uživatele a skupiny pro licenci.

### <a name="to-remove-a-license-from-a-user"></a>Odebrání licence uživatele

1. Na stránce **licencované uživatele** pro plán služby vyberte uživatele, který už nemá mít licenci. Například _Alain Charon_.

1. Vyberte **odebrat licenci**.

    ![Stránka licencované uživatele s zvýrazněnou možností odebrat licenci](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licence, které uživatel dědí ze skupiny, se nedají odebrat přímo. Místo toho je nutné odebrat uživatele ze skupiny, ze které dědí licenci.

### <a name="to-remove-a-license-from-a-group"></a>Odebrání licence ze skupiny

1. Na stránce **licencované skupiny** pro licenční plán vyberte skupinu, která už nebude mít licenci.

1. Vyberte **odebrat licenci**.

    ![Stránka licencovaných skupin s zvýrazněnou možností odebrat licenci 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Pokud se místní uživatelský účet synchronizovaný do služby Azure AD nedostává do rozsahu pro synchronizaci nebo když se synchronizace odebere, uživatel se v Azure AD vymaže. V takovém případě budou licence přiřazené uživateli přímo nebo prostřednictvím licencování na základě skupin označeny jako **pozastavené** , nikoli jako **odstraněné**.

## <a name="next-steps"></a>Další kroky

Po přiřazení licencí můžete provádět následující procesy:

- [Identifikace a řešení problémů s přiřazením licencí](../enterprise-users/licensing-groups-resolve-problems.md)

- [Přidání licencovaných uživatelů do skupiny pro licencování](../enterprise-users/licensing-groups-migrate-users.md)

- [Scénáře, omezení a známé problémy, které používají skupiny pro správu licencování v Azure Active Directory](../enterprise-users/licensing-group-advanced.md)

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)
