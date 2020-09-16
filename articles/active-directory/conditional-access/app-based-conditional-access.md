---
title: Schválené klientské aplikace s podmíněným přístupem – Azure Active Directory
description: Naučte se, jak vyžadovat schválené klientské aplikace pro přístup k cloudovým aplikacím s podmíněným přístupem v Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a3cdb851ca00300d995bcf2075252a360242197
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601958"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Postupy: vyžadování schválených klientských aplikací pro cloudový přístup k aplikaci pomocí podmíněného přístupu

Lidé pravidelně používají svoje mobilní zařízení pro osobní i pracovní úkoly. I když se zaměříte na produktivitu zaměstnanců, organizace také chtějí zabránit ztrátě dat z potenciálně nezabezpečených aplikací. Díky podmíněnému přístupu můžou organizace omezovat přístup ke schváleným klientským aplikacím (podporujícím moderní ověřování).

Tento článek nabízí dva scénáře konfigurace zásad podmíněného přístupu pro prostředky, jako jsou Microsoft 365, Exchange Online a SharePoint Online.

- [Scénář 1: Microsoft 365 aplikace vyžadují schválenou klientskou aplikaci](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Scénář 2: Exchange Online a SharePoint Online vyžadují schválenou klientskou aplikaci.](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

V případě podmíněného přístupu se tato funkce označuje jako vyžadování schválené klientské aplikace. Seznam schválených klientských aplikací najdete v tématu [schválený klient aplikace požadavky](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Aby bylo možné vyžadovat schválené klientské aplikace pro zařízení s iOS a Androidem, musí se tato zařízení nejdřív zaregistrovat ve službě Azure AD.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Scénář 1: Microsoft 365 aplikace vyžadují schválenou klientskou aplikaci

V tomto scénáři společnost Contoso rozhodla, že uživatelé, kteří používají mobilní zařízení, mají přístup ke všem službám Microsoft 365 za předpokladu, že používají schválené klientské aplikace, jako je Outlook Mobile, OneDrive a Microsoft Teams. Všichni uživatelé už přihlásili pomocí přihlašovacích údajů Azure AD a mají přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Aby bylo možné vyžadovat použití schválené klientské aplikace na mobilních zařízeních, musí organizace provést následující tři kroky.

**Krok 1: zásady pro klienty moderních ověřování založené na Androidu a iOS vyžadující použití schválené klientské aplikace při přístupu k Exchangi Online.**

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **Office 365 (Preview)**.
1. V části **podmínky**vyberte **platformy zařízení**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Zahrňte **Android** a **iOS**.
1. V části **podmínky**vyberte **klientské aplikace (Preview)**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **mobilní aplikace a klienti pro stolní počítače** a **moderní ověřování**.
1. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online pomocí ActiveSync (EAS)**

1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **Office 365 Exchange Online**.
1. V části **podmínky**:
   1. **Klientské aplikace (Preview)**:
      1. Nastavte **Konfigurovat** na **Ano**.
      1. Vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.
1. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 3: Nakonfigurujte zásady ochrany aplikací Intune pro klientské aplikace iOS a Android.**

Přečtěte si článek [jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), abyste mohli vytvářet zásady ochrany aplikací pro Android a iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scénář 2: Exchange Online a SharePoint Online vyžadují schválenou klientskou aplikaci.

V tomto scénáři společnost Contoso rozhodla, že uživatelé budou mít přístup k datům e-mailu a SharePointu jenom na mobilních zařízeních, pokud používají schválenou klientskou aplikaci, jako je Outlook Mobile. Všichni uživatelé už přihlásili pomocí přihlašovacích údajů Azure AD a mají přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Aby organizace vyžadovala použití schválené klientské aplikace na mobilních zařízeních a klientech Exchange ActiveSync, musí provést následující tři kroky.

**Krok 1: zásady pro klienty moderních ověřování založené na Androidu a iOS vyžadující použití schválené klientské aplikace při přístupu k Exchangi Online a SharePointu Online.**

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **Office 365 Exchange Online** a **Office 365 SharePoint Online**.
1. V části **podmínky**vyberte **platformy zařízení**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Zahrňte **Android** a **iOS**.
1. V části **podmínky**vyberte **klientské aplikace (Preview)**.
   1. Nastavte **Konfigurovat** na **Ano**.
   1. Vyberte **mobilní aplikace a klienti pro stolní počítače** a **moderní ověřování**.
1. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 2: zásady pro klienty Exchange ActiveSync vyžadující použití schválené klientské aplikace**

1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **Office 365 Exchange Online**.
1. V části **podmínky**:
   1. **Klientské aplikace (Preview)**:
      1. Nastavte **Konfigurovat** na **Ano**.
      1. Vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.
1. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

**Krok 3: Nakonfigurujte zásady ochrany aplikací Intune pro klientské aplikace iOS a Android.**

Přečtěte si článek [jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), abyste mohli vytvářet zásady ochrany aplikací pro Android a iOS. 

## <a name="next-steps"></a>Další kroky

[Co je podmíněný přístup?](overview.md)

[Komponenty podmíněného přístupu](concept-conditional-access-policies.md)

[Běžné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
