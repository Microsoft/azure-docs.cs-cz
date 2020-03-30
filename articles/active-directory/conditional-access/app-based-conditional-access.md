---
title: Schválené klientské aplikace s podmíněným přístupem – Azure Active Directory
description: Zjistěte, jak vyžadovat schválené klientské aplikace pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu ve službě Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480891"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Postup: Vyžadovat schválené klientské aplikace pro přístup ke cloudovým aplikacím s podmíněným přístupem

Lidé pravidelně používají svá mobilní zařízení pro osobní i pracovní úkoly. Při zajištění, že zaměstnanci mohou být produktivní, organizace také chtějí zabránit ztrátě dat z potenciálně nezabezpečených aplikací. Pomocí podmíněného přístupu mohou organizace omezit přístup ke schváleným klientským aplikacím (s podporou moderního ověřování).

Tento článek představuje dva scénáře konfigurace zásad podmíněného přístupu pro prostředky, jako je Office 365, Exchange Online a SharePoint Online.

- [Scénář 1: Aplikace Office 365 vyžadují schválenou klientskou aplikaci](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Scénář 2: Exchange Online a SharePoint Online vyžadují schválenou klientskou aplikaci](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

V podmíněném přístupu se tato funkce označuje jako vyžadování schválené klientské aplikace. Seznam schválených klientských aplikací naleznete v tématu [požadavek na schválené klientské aplikace](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Aby bylo možné vyžadovat schválené klientské aplikace pro zařízení se systémem iOS a Android, musí se tato zařízení nejprve zaregistrovat ve službě Azure AD.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Scénář 1: Aplikace Office 365 vyžadují schválenou klientskou aplikaci

V tomto scénáři společnost Contoso rozhodla, že uživatelé, kteří používají mobilní zařízení, mají přístup ke všem službám Office 365, pokud používají schválené klientské aplikace, jako je Outlook mobile, OneDrive a Microsoft Teams. Všichni jejich uživatelé se už přihlašují pomocí přihlašovacích údajů Azure AD a mají k nim přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Organizace musí provést následující tři kroky, aby bylo nutné vyžadovat použití schválené klientské aplikace na mobilních zařízeních.

**Krok 1: Zásady pro klienty s moderním ověřováním na androida a iOS, které vyžadují použití schválené klientské aplikace při přístupu k Exchange Online.**

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé** nebo konkrétní **uživatelé a skupiny,** na které chcete tuto zásadu použít. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Office 365 (preview).**
1. V **části Podmínky**vyberte **platformy zařízení**.
   1. Nastavit **konfigurovat** na **ano**.
   1. Zahrnout **Android** a **iOS**.
1. V **části Podmínky**vyberte **Klientské aplikace (preview).**
   1. Nastavit **konfigurovat** na **ano**.
   1. Vyberte **Možnost Mobilní aplikace a klienti pro stolní počítače** a klienti **moderního ověřování**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online s ActiveSync (EAS)**

1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé** nebo konkrétní **uživatelé a skupiny,** na které chcete tuto zásadu použít. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Office 365 Exchange Online**.
1. Za **podmínek**:
   1. **Klientské aplikace (náhled)**:
      1. Nastavit **konfigurovat** na **ano**.
      1. Vyberte **Mobilní aplikace a desktopoví klienti** a klienti Exchange **ActiveSync**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android.**

V článku [Jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), kde najděte postup k vytvoření zásad ochrany aplikací pro Android a iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scénář 2: Exchange Online a SharePoint Online vyžadují schválenou klientskou aplikaci

V tomto scénáři společnost Contoso rozhodla, že uživatelé mohou přistupovat pouze k e-mailu a datům služby SharePoint na mobilních zařízeních, pokud používají schválenou klientskou aplikaci, jako je Outlook mobile. Všichni jejich uživatelé se už přihlašují pomocí přihlašovacích údajů Azure AD a mají k nim přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Organizace musí provést následující tři kroky, aby bylo nutné vyžadovat použití schválené klientské aplikace na mobilních zařízeních a klientech Exchange ActiveSync.

**Krok 1: Zásady pro klienty s moderním ověřováním na androidu a iOS, které vyžadují použití schválené klientské aplikace při přístupu k Exchange Online a SharePointu Online.**

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé** nebo konkrétní **uživatelé a skupiny,** na které chcete tuto zásadu použít. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Office 365 Exchange Online** a Office **365 SharePoint Online**.
1. V **části Podmínky**vyberte **platformy zařízení**.
   1. Nastavit **konfigurovat** na **ano**.
   1. Zahrnout **Android** a **iOS**.
1. V **části Podmínky**vyberte **Klientské aplikace (preview).**
   1. Nastavit **konfigurovat** na **ano**.
   1. Vyberte **Možnost Mobilní aplikace a klienti pro stolní počítače** a klienti **moderního ověřování**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 2: Zásady pro klienty Exchange ActiveSync, které vyžadují použití schválené klientské aplikace.**

1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé** nebo konkrétní **uživatelé a skupiny,** na které chcete tuto zásadu použít. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Office 365 Exchange Online**.
1. Za **podmínek**:
   1. **Klientské aplikace (náhled)**:
      1. Nastavit **konfigurovat** na **ano**.
      1. Vyberte **Mobilní aplikace a desktopoví klienti** a klienti Exchange **ActiveSync**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **schválenou klientskou aplikaci**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android.**

V článku [Jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), kde najděte postup k vytvoření zásad ochrany aplikací pro Android a iOS. 

## <a name="next-steps"></a>Další kroky

[Co je podmíněný přístup?](overview.md)

[Součásti podmíněného přístupu](concept-conditional-access-policies.md)

[Běžné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
