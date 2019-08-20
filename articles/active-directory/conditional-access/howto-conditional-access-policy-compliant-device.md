---
title: Podmíněný přístup – vyžaduje zařízení vyhovující předpisům – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, která bude vyžadovat vyhovující zařízení
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 255d1be1c8cfaec366aee6964e41537f6b6c50d8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576612"
---
# <a name="conditional-access-require-compliant-devices"></a>Podmíněný přístup: Vyžadovat vyhovující zařízení

Organizace, které nasadily Microsoft Intune, můžou použít informace vrácené ze svých zařízení k identifikaci zařízení, která splňují požadavky na dodržování předpisů, jako jsou:

* Vyžadování PIN kódu k odemknutí
* Vyžadování šifrování zařízení
* Vyžadování minimální nebo maximální verze operačního systému
* Požadavek na zařízení není jailbreakem nebo rootem.

Tyto informace o dodržování zásad se předají do služby Azure AD, kde podmíněný přístup může učinit rozhodnutí udělit nebo blokovat přístup k prostředkům.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby zařízení přistupující k prostředkům byla označená jako vyhovující zásadám dodržování předpisů Intune vaší organizace.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce** > vyberte **všechny cloudové aplikace**.
   1. Pokud musíte z vaší zásady vyloučit konkrétní aplikace, můžete je vybrat z karty **vyloučit** v části **Vybrat vyloučené cloudové aplikace** a zvolit **Vybrat**.
   1. Vyberte **Done** (Hotovo).
1. V části **řízení** > přístupu**udělení**přístupu vyberte **vyžadovat, aby zařízení bylo označené jako vyhovující**.
   1. Vyberte **vyberte**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Zásady dodržování předpisů pro zařízení fungují s Azure AD](/intune/device-compliance-get-started.md#device-compliance-policies-work-with-azure-ad)
