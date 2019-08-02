---
title: Jak vyžadovat schválené klientské aplikace pro přístup k cloudovým aplikacím pomocí podmíněného přístupu v Azure Active Directory | Microsoft Docs
description: Naučte se, jak vyžadovat schválené klientské aplikace pro přístup k cloudovým aplikacím s podmíněným přístupem v Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45eeef7d96f194e224e5b44421e73eb5ee5d9c0d
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515138"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Jak: Vyžadovat pro cloudový přístup schválené klientské aplikace s podmíněným přístupem 

Vaši zaměstnanci používají mobilní zařízení pro osobní i pracovní úkoly. I když se rozhodnete, že vaše zaměstnanci můžou být produktivní, budete také chtít zabránit ztrátě dat. Pomocí podmíněného přístupu Azure Active Directory (Azure AD) můžete omezit přístup k vašim cloudovým aplikacím na schválené klientské aplikace, které můžou chránit vaše firemní data.  

Toto téma vysvětluje, jak nakonfigurovat zásady přístupu k podmínkám, které vyžadují schválené klientské aplikace.

## <a name="overview"></a>Přehled

Pomocí [podmíněného přístupu Azure AD](overview.md)můžete doladit, jak můžou autorizovaní uživatelé přistupovat k vašim prostředkům. Například můžete omezit přístup k vašim cloudovým aplikacím na důvěryhodná zařízení.

K ochraně firemních dat můžete použít [Zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) . Zásady ochrany aplikací Intune nevyžadují řešení správy mobilních zařízení (MDM), které umožňuje chránit podniková data pomocí nebo bez registrace zařízení do řešení pro správu zařízení.

Azure Active Directory podmíněný přístup umožňuje omezit přístup k vašim cloudovým aplikacím na klientské aplikace, které podporují zásady ochrany aplikací Intune. Můžete například omezit přístup k Exchangi Online do aplikace Outlook.

V terminologii podmíněného přístupu se tyto klientské aplikace označují jako **schválené klientské aplikace**.  

![Podmíněný přístup](./media/app-based-conditional-access/05.png)

Seznam schválených klientských aplikací najdete v tématu [schválený klient aplikace požadavky](technical-reference.md#approved-client-app-requirement).

Zásady podmíněného přístupu na základě aplikace můžete kombinovat s jinými zásadami, jako jsou [zásady podmíněného přístupu na základě zařízení](require-managed-devices.md) , které poskytují flexibilitu v tom, jak chránit data pro osobní i firemní zařízení.

## <a name="before-you-begin"></a>Před zahájením

V tomto tématu se předpokládá, že máte zkušenosti s nástrojem:

- Požadavek na technické informace o [schválené klientské aplikaci](technical-reference.md#approved-client-app-requirement) .
- Základní koncepty podmíněného [přístupu v Azure Active Directory](overview.md).
- Jak [nakonfigurovat zásadu podmíněného přístupu](app-based-mfa.md).
- [Migrace zásad podmíněného přístupu](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit zásadu podmíněného přístupu na základě aplikace, musíte mít Enterprise Mobility + Security nebo předplatné Azure Active Directory Premium a uživatelé musí mít licenci pro EMS nebo Azure AD. 

## <a name="exchange-online-policy"></a>Zásady pro Exchange Online 

Tento scénář se skládá ze zásad podmíněného přístupu na základě aplikace pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

V tomto scénáři se předpokládá, že uživatel:

- Nakonfiguruje e-maily pomocí nativní e-mailové aplikace v systému iOS nebo Android pro připojení k Exchangi.
- Obdrží e-mail s oznámením, že přístup je dostupný jenom v aplikaci Outlook.
- Stáhne aplikaci s odkazem.
- Otevře aplikaci Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD.
- Zobrazí se výzva k instalaci ověřovacího (iOS) nebo Portál společnosti (Android), aby bylo možné pokračovat.
- Nainstaluje aplikaci a vrátí se do aplikace Outlook, aby bylo možné pokračovat.
- Zobrazí se výzva k registraci zařízení.
- Je schopný získat přístup k e-mailu

V době, kdy se přistupují k firemním datům, se aktivují všechny zásady ochrany aplikací Intune a můžou uživatele vyzvat k restartování aplikace, použití dalšího kódu PIN atd. (Pokud je nakonfigurované pro aplikaci a platformu).

### <a name="configuration"></a>Konfiguraci 

**Krok 1 – Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **Office 365 Exchange Online**.
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **platformy zařízení** a **klientské aplikace**:
   1. Jako **platformy zařízení**vyberte **Android** a **iOS**.
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a aplikace klasické pracovní plochy** a **klienti moderních ověřování**.
1. Jako **řízení přístupu**musíte mít vybraný požadavek na **schválení klientské aplikace (Preview)** .

   ![Podmíněný přístup](./media/app-based-conditional-access/05.png)

**Krok 2 – konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s aktivní synchronizací (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **Office 365 Exchange Online**.
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **klientské aplikace (Preview)** . 
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.
   1. Jako **řízení přístupu**musíte mít vybraný požadavek na **schválení klientské aplikace (Preview)** .

      ![Podmíněný přístup](./media/app-based-conditional-access/05.png)

**Krok 3 – konfigurace zásad ochrany aplikací Intune pro klientské aplikace s iOS a Androidem**

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="exchange-online-and-sharepoint-online-policy"></a>Zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá z podmíněného přístupu pomocí zásad správy mobilních aplikací pro přístup k Exchangi Online a SharePointu Online se schválenými aplikacemi.

### <a name="scenario-playbook"></a>Scénář PlayBook

V tomto scénáři se předpokládá, že uživatel:

- Pokusí se použít aplikaci SharePoint pro připojení a také k zobrazení jejich podnikových webů.
- Pokusy o přihlášení se stejnými přihlašovacími údaji jako s přihlašovacími údaji aplikace Outlooku
- Nemusí znovu registrovat a získat přístup k prostředkům

### <a name="configuration"></a>Konfiguraci

**Krok 1 – Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **office 365 Exchange Online** a **Office 365 SharePoint Online**. 
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **platformy zařízení** a **klientské aplikace**:
   1. Jako **platformy zařízení**vyberte **Android** a **iOS**.
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti moderních ověřování**.
1. Jako **řízení přístupu**musíte mít vybraný požadavek na **schválení klientské aplikace (Preview)** .

   ![Podmíněný přístup](./media/app-based-conditional-access/05.png)

**Krok 2 – konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s aktivní synchronizací (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **Office 365 Exchange Online**. Online 
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **klientské aplikace**:
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.
   1. Jako **řízení přístupu**musíte mít vybraný požadavek na **schválení klientské aplikace (Preview)** .

      ![Podmíněný přístup](./media/app-based-conditional-access/05.png)

**Krok 3 – konfigurace zásad ochrany aplikací Intune pro klientské aplikace s iOS a Androidem**

![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zásady zařízení nebo dodržování předpisů v souladu s aplikacemi pro Exchange Online a SharePoint Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě aplikace nebo kompatibilního zařízení pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

Tento scénář předpokládá, že:
 
- Někteří uživatelé už jsou zaregistrovaní (s podnikovými zařízeními nebo bez nich).
- Uživatelé, kteří nejsou zaregistrovaní a zaregistrovaní ve službě Azure AD pomocí aplikace chráněné aplikací, musí zaregistrovat zařízení pro přístup k prostředkům.
- Zaregistrovaní uživatelé, kteří používají chráněnou aplikaci, nemusí zařízení znovu zaregistrovat.

### <a name="configuration"></a>Konfiguraci

**Krok 1 – Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **office 365 Exchange Online** a **Office 365 SharePoint Online**. 
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
   1. Jako **platformy zařízení**vyberte **Android** a **iOS**.
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti moderních ověřování**.
1. Jako **řízení přístupu**musíte mít vybrané následující:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat schválenou klientskou aplikaci (Preview)**
   - **Vyžadovat jeden z vybraných ovládacích prvků**   
 
      ![Podmíněný přístup](./media/app-based-conditional-access/11.png)

**Krok 2 – konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s aktivní synchronizací (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **Office 365 Exchange Online**. 
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **klientské aplikace**. 
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.
1. Jako **řízení přístupu**musíte mít vybraný požadavek na **schválení klientské aplikace (Preview)** .
 
   ![Podmíněný přístup](./media/app-based-conditional-access/11.png)

**Krok 3 – konfigurace zásad ochrany aplikací Intune pro klientské aplikace s iOS a Androidem**

![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Zásady zařízení a vyhovující předpisům na základě aplikací pro Exchange Online a SharePoint Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě aplikace a odpovídajících zařízení pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

V tomto scénáři se předpokládá, že uživatel:
 
- Nakonfiguruje e-maily pomocí nativní e-mailové aplikace v systému iOS nebo Android pro připojení k Exchangi.
- Obdrží e-mail s oznámením, že přístup vyžaduje, aby vaše zařízení bylo zaregistrované.
- Stáhne portál společnosti a přihlásí se k portálu společnosti.
- Zkontroluje poštu a zobrazí se výzva k použití aplikace Outlook.
- Stáhne aplikaci Outlook.
- Otevře aplikaci Outlook a zadá přihlašovací údaje použité při registraci.
- Uživatel může získat přístup k e-mailu.

Všechny zásady ochrany aplikací Intune se aktivují v době přístupu k podnikovým datům a můžou uživatele vyzvat k restartování aplikace, použití dalšího kódu PIN atd. (Pokud je nakonfigurované pro aplikaci a platformu)

### <a name="configuration"></a>Konfiguraci

**Krok 1 – Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **office 365 Exchange Online** a **Office 365 SharePoint Online**. 
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
   1. Jako **platformy zařízení**vyberte **Android** a **iOS**.
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a aplikace klasické pracovní plochy** a **klienti moderních ověřování**.
1. Jako **řízení přístupu**musíte mít vybrané následující:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat schválenou klientskou aplikaci (Preview)**
   - **Vyžadovat všechny vybrané ovládací prvky**   
 
      ![Podmíněný přístup](./media/app-based-conditional-access/13.png)

**Krok 2 – konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s aktivní synchronizací (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je potřeba nakonfigurovat následující komponenty:

1. **Název** zásad podmíněného přístupu.
1. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít vybrané aspoň jednoho uživatele nebo skupinu.
1. **Cloudové aplikace:** Jako cloudové aplikace musíte vybrat **Office 365 Exchange Online**. 
1. **Stavu** V případě **podmínek**je potřeba nakonfigurovat **klientské aplikace (Preview)** . 
   1. Jako **klientské aplikace (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.

   ![Podmíněný přístup](./media/app-based-conditional-access/92.png)

1. Jako **řízení přístupu**musíte mít vybrané následující:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat schválenou klientskou aplikaci (Preview)**
   - **Vyžadovat všechny vybrané ovládací prvky**   

**Krok 3 – konfigurace zásad ochrany aplikací Intune pro klientské aplikace s iOS a Androidem**

![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="next-steps"></a>Další postup

Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).

Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 
