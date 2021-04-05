---
title: Zásady ochrany aplikací s podmíněným přístupem – Azure Active Directory
description: Naučte se vyžadovat zásady ochrany aplikací pro přístup ke cloudovým aplikacím s podmíněným přístupem v Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575767"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Postupy: vyžadování zásad ochrany aplikací a schválené klientské aplikace pro přístup k cloudovým aplikacím pomocí podmíněného přístupu

Lidé pravidelně používají svoje mobilní zařízení pro osobní i pracovní úkoly. I když se zaměříte na produktivitu zaměstnanců, organizace také chtějí zabránit ztrátě dat z potenciálně nezabezpečených aplikací. S podmíněným přístupem můžou organizace omezit přístup ke schváleným klientským aplikacím (podporujícím moderní ověřování) s použitými zásadami ochrany aplikací Intune.

Tento článek představuje tři scénáře konfigurace zásad podmíněného přístupu pro prostředky, jako jsou Microsoft 365, Exchange Online a SharePoint.

- [Scénář 1: Microsoft 365 aplikace vyžadují schválené aplikace se zásadami ochrany aplikací](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Scénář 2: aplikace prohlížeče vyžadují schválené aplikace se zásadami ochrany aplikací](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Scénář 3: Exchange Online a SharePoint vyžadují schválenou klientskou aplikaci a zásadu ochrany aplikací.](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

V podmíněném přístupu jsou tyto klientské aplikace chráněné zásadami ochrany aplikací. Další informace o zásadách ochrany aplikací najdete v článku [Přehled zásad ochrany aplikací](/intune/apps/app-protection-policy) .

> [!WARNING]
> Ne všechny aplikace jsou podporovány jako schválené aplikace nebo podporují zásady ochrany aplikací. Seznam oprávněných klientských aplikací najdete v tématu [požadavky na zásady ochrany aplikací](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> "Vyžadovat jeden z vybraných ovládacích prvků" v rámci grant Controls je LIKE klauzule OR. Tato možnost se používá v zásadách, aby uživatelé mohli využívat aplikace, které podporují **Zásady ochrany aplikací** , nebo vyžadují udělené ovládací prvky pro udělení **autorizovaných klientských aplikací** . Vyžadovat, aby se **Zásady ochrany aplikací** vynutily, když aplikace podporuje řízení udělení. Další informace o tom, které aplikace podporují řízení udělení **zásad ochrany aplikací** , najdete v tématu [požadavky na zásady ochrany aplikací](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Scénář 1: Microsoft 365 aplikace vyžadují schválené aplikace se zásadami ochrany aplikací

V tomto scénáři společnost Contoso rozhodla, že veškerý mobilní přístup k prostředkům Microsoft 365 musí používat schválené klientské aplikace, jako je Outlook Mobile a OneDrive, chráněné zásadami ochrany aplikací před přijetím přístupu. Všichni uživatelé už přihlásili pomocí přihlašovacích údajů Azure AD a mají přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Aby bylo možné vyžadovat použití schválené klientské aplikace na mobilních zařízeních, musí organizace provést následující kroky.

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Microsoft 365**

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **Office 365**.
1. V části **podmínky** vyberte **platformy zařízení**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Zahrňte **Android** a **iOS**.
1. V části **podmínky** vyberte **klientské aplikace**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **mobilní aplikace a klienti klasické pracovní plochy** a zrušte výběr všech ostatních.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte následující možnosti:
   - **Vyžadovat klientskou aplikaci schválenou**
   - **Vyžadování zásad ochrany aplikací**
   - **Vyžadovat jeden z vybraných ovládacích prvků**
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online pomocí ActiveSync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **Office 365 Exchange Online**.
1. V části **podmínky** vyberte **klientské aplikace**:
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **klienti Exchange ActiveSync** a zrušte výběr všech ostatních.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte **udělit přístup**, **vyžadovat zásadu ochrany aplikací** a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace s iOS a Androidem**

Přečtěte si článek [jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), abyste mohli vytvářet zásady ochrany aplikací pro Android a iOS. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Scénář 2: aplikace prohlížeče vyžadují schválené aplikace se zásadami ochrany aplikací

V tomto scénáři společnost Contoso rozhodla, že všechna přístupová oprávnění k mobilnímu webu pro Microsoft 365 prostředky musí používat schválenou klientskou aplikaci, jako je třeba Edge pro iOS a Android, chráněná před přijetím přístupu. Všichni uživatelé už přihlásili pomocí přihlašovacích údajů Azure AD a mají přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Aby bylo možné vyžadovat použití schválené klientské aplikace na mobilních zařízeních, musí organizace provést následující kroky.

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Microsoft 365**

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **Office 365**.
1. V části **podmínky** vyberte **platformy zařízení**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Zahrňte **Android** a **iOS**.
1. V části **podmínky** vyberte **klientské aplikace**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **prohlížeč** a zrušte výběr všech ostatních.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte následující možnosti:
   - **Vyžadovat klientskou aplikaci schválenou**
   - **Vyžadování zásad ochrany aplikací**
   - **Vyžadovat jeden z vybraných ovládacích prvků**
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 2: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace s iOS a Androidem**

Přečtěte si článek [jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), abyste mohli vytvářet zásady ochrany aplikací pro Android a iOS. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Scénář 3: Exchange Online a SharePoint vyžadují schválenou klientskou aplikaci a zásadu ochrany aplikací.

V tomto scénáři společnost Contoso rozhodla, že uživatelé budou mít přístup k e-mailu a datům SharePointu jenom na mobilních zařízeních, pokud používají schválenou klientskou aplikaci, jako je Outlook Mobile chráněná zásadami ochrany aplikací před přijetím přístupu. Všichni uživatelé už přihlásili pomocí přihlašovacích údajů Azure AD a mají přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Aby organizace vyžadovala použití schválené klientské aplikace na mobilních zařízeních a klientech Exchange ActiveSync, musí provést následující tři kroky.

**Krok 1: zásady pro klienty moderních ověřování založené na Androidu a iOS vyžadující použití schválené klientské aplikace a zásady ochrany aplikací při přístupu k Exchangi Online a SharePointu.**

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **Office 365 Exchange Online** a **Office 365 SharePoint Online**.
1. V části **podmínky** vyberte **platformy zařízení**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Zahrňte **Android** a **iOS**.
1. V části **podmínky** vyberte **klientské aplikace**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **mobilní aplikace a klienti klasické pracovní plochy** a zrušte výběr všech ostatních.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte následující možnosti:
   - **Vyžadovat klientskou aplikaci schválenou**
   - **Vyžadování zásad ochrany aplikací**
   - **Vyžadovat jeden z vybraných ovládacích prvků**
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 2: zásady pro klienty Exchange ActiveSync vyžadující použití schválené klientské aplikace**

1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **Office 365 Exchange Online**.
1. V části **podmínky** vyberte **klientské aplikace**:
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **klienti Exchange ActiveSync** a zrušte výběr všech ostatních.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte **udělit přístup**, **vyžadovat zásadu ochrany aplikací** a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 3: Nakonfigurujte zásady ochrany aplikací Intune pro klientské aplikace iOS a Android.**

Přečtěte si článek [jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), abyste mohli vytvářet zásady ochrany aplikací pro Android a iOS. 

## <a name="next-steps"></a>Další kroky

[Co je podmíněný přístup?](overview.md)

[Komponenty podmíněného přístupu](concept-conditional-access-policies.md)

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

