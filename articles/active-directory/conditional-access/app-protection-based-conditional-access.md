---
title: Zásady ochrany aplikací s podmíněným přístupem – Azure Active Directory
description: Zjistěte, jak vyžadovat zásady ochrany aplikací pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu ve službě Azure Active Directory.
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
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080068"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Postup: Vyžadovat zásady ochrany aplikací a schválenou klientskou aplikaci pro přístup ke cloudovým aplikacím s podmíněným přístupem

Lidé pravidelně používají svá mobilní zařízení pro osobní i pracovní úkoly. Při zajištění, že zaměstnanci mohou být produktivní, organizace také chtějí zabránit ztrátě dat z potenciálně nezabezpečených aplikací. Díky podmíněnému přístupu můžou organizace omezit přístup ke schváleným klientským aplikacím (s podporou moderního ověřování) s tím, že se na ně aplikují zásady ochrany aplikací Intune.

Tento článek představuje dva scénáře konfigurace zásad podmíněného přístupu pro prostředky, jako je Office 365, Exchange Online a SharePoint Online.

- [Scénář 1: Aplikace Office 365 vyžadují schválené aplikace se zásadami ochrany aplikací](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scénář 2: Exchange Online a SharePoint Online vyžadují schválené zásady ochrany klientských aplikací a aplikací](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

V podmíněném přístupu je známo, že tyto klientské aplikace jsou chráněné pomocí zásad ochrany aplikací. Další informace o zásadách ochrany aplikací najdete v článku [Přehled zásad ochrany aplikací.](/intune/apps/app-protection-policy)

Seznam způsobilých klientských aplikací najdete v [tématu Požadavek na zásady ochrany aplikací](concept-conditional-access-grant.md).

> [!NOTE]
>    Klauzule or se používá v rámci zásad, aby uživatelé mohli využívat aplikace, které podporují **zásady ochrany aplikací vyžadovat ochranu aplikací** nebo vyžadovat schválené ovládací prvky udělení **klientské aplikace.** Další informace o tom, které aplikace podporují ovládací **prvek Vyžadovat udělení zásad ochrany aplikací,** najdete v [tématu Požadavek na zásady ochrany aplikací](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scénář 1: Aplikace Office 365 vyžadují schválené aplikace se zásadami ochrany aplikací

V tomto scénáři společnost Contoso rozhodla, že veškerý mobilní přístup k prostředkům Office 365 musí před získáním přístupu používat schválené klientské aplikace, jako je Outlook mobile, OneDrive a Microsoft Teams chráněné zásadami ochrany aplikací. Všichni jejich uživatelé se už přihlašují pomocí přihlašovacích údajů Azure AD a mají k nim přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Organizace musí provést následující kroky, aby bylo nutné vyžadovat použití schválené klientské aplikace na mobilních zařízeních.

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Office 365**

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
1. V části **Access controls** > **Grant**vyberte následující možnosti:
   - **Vyžadovat schválenou klientskou aplikaci**
   - **Vyžadovat zásady ochrany aplikací (preview)**
   - **Vyžadovat jeden z vybraných ovládacích prvků**
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online s ActiveSync (EAS)**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující součásti:

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
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **zásady ochrany aplikací**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android**

V článku [Jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), kde najděte postup k vytvoření zásad ochrany aplikací pro Android a iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scénář 2: Exchange Online a SharePoint Online vyžadují schválené zásady ochrany klientských aplikací a aplikací

V tomto scénáři společnost Contoso rozhodla, že uživatelé mohou přistupovat pouze k e-mailům a datům služby SharePoint na mobilních zařízeních, pokud používají schválenou klientskou aplikaci, jako je Outlook mobile, chráněnou zásadami ochrany aplikací před získáním přístupu. Všichni jejich uživatelé se už přihlašují pomocí přihlašovacích údajů Azure AD a mají k nim přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Organizace musí provést následující tři kroky, aby bylo nutné vyžadovat použití schválené klientské aplikace na mobilních zařízeních a klientech Exchange ActiveSync.

**Krok 1: Zásady pro klienty s moderním ověřováním na Androidu a iOS, které vyžadují použití schválené klientské aplikace a zásad ochrany aplikací při přístupu k Exchange Online a SharePointu Online.**

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
1. V části **Access controls** > **Grant**vyberte následující možnosti:
   - **Vyžadovat schválenou klientskou aplikaci**
   - **Vyžadovat zásady ochrany aplikací (preview)**
   - **Vyžadovat jeden z vybraných ovládacích prvků**
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
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **zásady ochrany aplikací**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit a povolit zásady, vyberte **vytvořit.**

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android.**

V článku [Jak vytvořit a přiřadit zásady ochrany aplikací](/intune/apps/app-protection-policies), kde najděte postup k vytvoření zásad ochrany aplikací pro Android a iOS. 

## <a name="next-steps"></a>Další kroky

[Co je podmíněný přístup?](overview.md)

[Součásti podmíněného přístupu](concept-conditional-access-policies.md)

[Běžné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

