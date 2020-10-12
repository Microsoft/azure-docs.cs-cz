---
title: Podmíněný přístup – vyžaduje zařízení vyhovující předpisům – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, která bude vyžadovat vyhovující zařízení
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049124"
---
# <a name="conditional-access-require-compliant-devices"></a>Podmíněný přístup: vyžadovat vyhovující zařízení

Organizace, které nasadily Microsoft Intune, můžou použít informace vrácené ze svých zařízení k identifikaci zařízení, která splňují požadavky na dodržování předpisů, jako jsou:

* Vyžadování PIN kódu k odemknutí
* Vyžadování šifrování zařízení
* Vyžadování minimální nebo maximální verze operačního systému
* Požadavek na zařízení není jailbreakem nebo rootem.

Tyto informace o dodržování zásad se předají do služby Azure AD, kde podmíněný přístup může učinit rozhodnutí udělit nebo blokovat přístup k prostředkům. Další informace o zásadách dodržování předpisů pro zařízení najdete v článku [Nastavení pravidel na zařízeních pro povolení přístupu k prostředkům ve vaší organizaci pomocí Intune](/intune/protect/device-compliance-get-started) .

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby zařízení přistupující k prostředkům byla označená jako vyhovující zásadám dodržování předpisů Intune vaší organizace.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **všechny cloudové aplikace**.
   1. Pokud musíte z vaší zásady vyloučit konkrétní aplikace, můžete je vybrat z karty **vyloučit** v části **Vybrat vyloučené cloudové aplikace** a zvolit **Vybrat**.
   1. Vyberte **Hotovo**.
1. V části **podmínky**  >  **klientské aplikace (Preview)**  >  **Vyberte klientské aplikace, na které se bude tato zásada vztahovat**, ponechte vybrané všechny výchozí hodnoty a vyberte **Hotovo**.
1. V části **řízení přístupu**  >  **udělení**přístupu vyberte **vyžadovat, aby zařízení bylo označené jako vyhovující**.
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

> [!NOTE]
> Můžete zaregistrovat nová zařízení do Intune i v případě, že vyberete možnost **vyžadovat, aby zařízení byla označena jako kompatibilní** pro **všechny uživatele** a **všechny cloudové aplikace** , a to pomocí výše uvedeného postupu. **Vyžadovat, aby zařízení bylo označené jako vyhovující,** neblokuje registraci v Intune. 

### <a name="known-behavior"></a>Známé chování

Ve Windows 7, iOS, Androidu, macOS a některých webových prohlížečích třetích stran služba Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí při registraci zařízení ve službě Azure AD. Když se uživatel poprvé přihlásí prostřednictvím prohlížeče, zobrazí se uživateli výzva k výběru certifikátu. Koncový uživatel musí tento certifikát vybrat předtím, než bude moci pokračovat v používání prohlížeče.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Zásady dodržování předpisů pro zařízení fungují s Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
