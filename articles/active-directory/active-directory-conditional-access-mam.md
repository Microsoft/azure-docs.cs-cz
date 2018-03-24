---
title: Azure Active Directory, na základě aplikace podmíněného přístupu | Microsoft Docs
description: Zjistěte, jak funguje podmíněného přístupu na základě aplikace Azure Active Directory.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: b35aed3583307c34eadbed3dd13d1ffd51494927
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Azure Active Directory, na základě aplikace podmíněného přístupu  

Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. Vaši zaměstnanci byli produktivní, ale také chcete zabránit ztrátě dat. S Azure Active Directory (Azure AD) na základě aplikace podmíněný přístup můžete omezit přístup do vašich cloudových aplikací pro klientské aplikace, které může chránit firemní data.  

Toto téma vysvětluje postup konfigurace podmíněného přístupu na základě aplikace Azure AD.

## <a name="overview"></a>Přehled

S [podmíněného přístupu Azure AD](active-directory-conditional-access-azure-portal.md), můžete upřesnit jak oprávněným uživatelům můžete přístup k prostředkům. Například můžete omezit přístup do vašich cloudových aplikací pro důvěryhodná zařízení.

Můžete použít [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) k ochraně dat vaší společnosti. Zásady ochrany aplikací Intune nevyžadují řešení správy mobilních zařízení (MDM), což umožňuje ochranu firemních dat i bez registrace zařízení v řešení správy zařízení.

Azure Active Directory podmíněného přístupu na základě aplikace umožňuje omezit přístup do vašich cloudových aplikací pro klientské aplikace, které podporují zásady ochrany aplikací služby Intune. Například můžete omezit přístup na Exchange Online k aplikaci Outlook.

V terminologii podmíněný přístup se tyto klientské aplikace se označují jako **schválení klientských aplikací**.  


![Podmíněný přístup](./media/active-directory-conditional-access-mam/05.png)


Seznam schválených klientských aplikací, naleznete v části [schválení klienta aplikace požadavek](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Zkombinováním zásad podmíněného přístupu na základě aplikace pomocí jiných zásad, jako [zásady podmíněného přístupu na základě zařízení](active-directory-conditional-access-policy-connected-applications.md) zajistit flexibilitu v tom, jak chránit data pro osobní a podniková zařízení.

 


## <a name="before-you-begin"></a>Než začnete

Toto téma předpokládá, že jste obeznámeni s:

- [Schválení klienta aplikace požadavek](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) technické informace.


- Základní koncepty [podmíněného přístupu v Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- Postup [konfigurovat zásadu podmíněného přístupu](active-directory-conditional-access-azure-portal-get-started.md).

- [Migrace zásad podmíněného přístupu](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Požadavky

Vytvoření zásady podmíněného přístupu na základě aplikací, musí mít Enterprise Mobility + Security nebo předplatné Azure Active Directory premium a uživatelé musí mít licenci EMS nebo Azure AD. 


## <a name="exchange-online-policy"></a>Zásady pro Exchange Online 

Tento scénář se skládá z zásadu podmíněného přístupu na základě aplikaci pro přístup na Exchange Online.


### <a name="scenario-playbook"></a>Scénář scénářem

Tento scénář předpokládá, že uživatel:

- Nakonfiguruje e-mailu pomocí nativní e-mailová aplikace v iOS nebo Android pro připojení k systému Exchange

- Obdrží e-mailu, která určuje, že přístup je k dispozici pouze pomocí aplikace Outlook

- Soubory ke stažení aplikace s odkazem

- Otevře se v aplikaci Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD

- Výzva k instalaci pokračujte Authenticator (iOS) nebo portál společnosti (Android)

- Vrátí nainstaluje aplikace a můžete pokračovat v aplikaci Outlook

- Zobrazí výzva k registraci zařízení

- Dokáže získat přístup k e-mailu

Žádné zásady ochrany aplikací Intune se aktivuje v době přístup k podnikovým datům a může vyzvat uživatele k restartování aplikace, použijte další atd kódu PIN (Pokud je nakonfigurovaná pro platformu a aplikace).

### <a name="configuration"></a>Konfigurace 

**Krok 1 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/01.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.

3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**:

    a. Jako **platformy zařízení**, vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **klientské aplikace**, vyberte **mobilní aplikace a aplikace klasické pracovní plochy**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/04.png)

5. Jako **přístup k ovládacím prvkům**, musíte mít **vyžadují schválené klientskou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/05.png)
 

**Krok 2 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/06.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.


3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    a. Jako **klientské aplikace**, vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/08.png)

    b. Jako **přístup k ovládacím prvkům**, musíte mít **vyžadují schválené klientskou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/05.png)


**Krok 3: Konfigurace zásad ochrany Intune aplikace pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/active-directory-conditional-access-mam/09.png)

V tématu [ochranu dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Zásady Exchange Online a SharePoint Online

Tento scénář se skládá z podmíněný přístup pomocí zásad správy mobilních aplikací pro přístup k systému Exchange Online a SharePoint Online pomocí schválených aplikací.

### <a name="scenario-playbook"></a>Scénář scénářem

Tento scénář předpokládá, že uživatel:

- Pokusí se použít aplikace služby SharePoint pro připojení a také zobrazit svých podnikových webech

- Pokus o přihlášení se stejnými pověřeními, jako přihlašovací údaje aplikace Outlook

- Není nutné znovu registrovat a může získat přístup k prostředkům


### <a name="configuration"></a>Konfigurace

**Krok 1 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/71.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.


3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online** a **Office 365 SharePoint Online**. 

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/02.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**:

    a. Jako **platformy zařízení**, vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **klientské aplikace**, vyberte **mobilní aplikace a aplikace klasické pracovní plochy**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/04.png)

5. Jako **přístup k ovládacím prvkům**, musíte mít **vyžadují schválené klientskou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/05.png)




**Krok 2 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/06.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.

3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online**. Online 

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**:

    a. Jako **klientské aplikace**, vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/08.png)

    b. Jako **přístup k ovládacím prvkům**, musíte mít **vyžadují schválené klientskou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/05.png)




**Krok 3: Konfigurace zásad ochrany Intune aplikace pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/active-directory-conditional-access-mam/09.png)

V tématu [ochranu dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Na základě aplikace nebo kompatibilních zařízení zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá z zásadu podmíněného přístupu na základě aplikace nebo kompatibilních zařízení pro přístup na Exchange Online.


### <a name="scenario-playbook"></a>Scénář scénářem

Tento scénář předpokládá, že:
 
- Některé uživatele jsou už zaregistrovaná (s nebo bez firemní zařízení)

- Uživatelé, kteří nejsou zaregistrované a registraci do Azure AD pomocí aplikace pro chráněné aplikace je potřeba registrovat zařízení pro přístup k prostředkům

- Zaregistrovaná uživatele, kteří používají aplikace chráněné aplikace není nutné znovu registrovat zařízení


### <a name="configuration"></a>Konfigurace

**Krok 1 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/62.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.

3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online** a **Office 365 SharePoint Online**. 

     ![Podmíněný přístup](./media/active-directory-conditional-access-mam/02.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**, vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **klientské aplikace**, vyberte **mobilní aplikace a aplikace klasické pracovní plochy**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/04.png)

5. Jako **přístup k ovládacím prvkům**, musíte mít následující vybraná:

    - **Vyžaduje zařízení označit jako kompatibilní**

    - **Vyžadují aplikace schválené klienta (preview)**

    - **Vyžadovat vybrané ovládacích prvků**   
 
    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/11.png)



**Krok 2 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/61.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.

3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    Jako **klientské aplikace*, vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/08.png)

5. Jako **přístup k ovládacím prvkům**, musíte mít **vyžadují schválené klientskou aplikaci (preview)** vybrané.
 
    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/11.png)




**Krok 3: Konfigurace zásad ochrany Intune aplikace pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/active-directory-conditional-access-mam/09.png)

V tématu [ochranu dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Na základě aplikace a kompatibilní zařízení zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá z zásadu podmíněného přístupu na základě aplikace a kompatibilní zařízení pro přístup na Exchange Online.


### <a name="scenario-playbook"></a>Scénář scénářem

Tento scénář předpokládá, že uživatel:
 
-   Nakonfiguruje e-mailu pomocí nativní e-mailová aplikace v iOS nebo Android pro připojení k systému Exchange
-   Obdrží e-mail, který udává, že přístup vyžaduje vaše zařízení k registraci
-   Soubory ke stažení portálu společnosti a přihlášení k portálu společnosti
-   Ověří e-mailu a se zobrazí výzva, chcete-li používat aplikaci Outlook
-   Soubory ke stažení aplikace Outlook
-   Otevře se v případě aplikace Outlook a zadá přihlašovací údaje použité registrace
-   Uživatel je mít přístup k e-mailu

Žádné zásady ochrany aplikací Intune se aktivují v době přístup k firemním datům a může vyzvat uživatele k restartování aplikace, použijte atd. Další kód PIN (Pokud je nakonfigurovaná pro platformu a aplikace)


### <a name="configuration"></a>Konfigurace

**Krok 1 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/62.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.

3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online** a **Office 365 SharePoint Online**. 

     ![Podmíněný přístup](./media/active-directory-conditional-access-mam/02.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**, vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/03.png)

    b. Jako **klientské aplikace**, vyberte **mobilní aplikace a aplikace klasické pracovní plochy**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/04.png)

5. Jako **přístup k ovládacím prvkům**, musíte mít následující vybraná:

    - **Vyžaduje zařízení označit jako kompatibilní**

    - **Vyžadují aplikace schválené klienta (preview)**

    - **Vyžadovat všechny vybrané ovládací prvky**   
 
    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/13.png)



**Krok 2 – konfigurace Azure AD zásady podmíněného přístupu pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku je třeba konfigurovat následující součásti:

![Podmíněný přístup](./media/active-directory-conditional-access-mam/61.png)

1. **Název** zásad podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít alespoň jeden vybraný uživatel nebo skupina.

3. **Cloudových aplikací:** jako cloudové aplikace, budete muset vybrat možnost **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    Jako **klientské aplikace**, vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/08.png)

5. Jako **přístup k ovládacím prvkům**, musíte mít následující vybraná:

    - **Vyžaduje zařízení označit jako kompatibilní**

    - **Vyžadují aplikace schválené klienta (preview)**

    - **Vyžadovat všechny vybrané ovládací prvky**   
 
    ![Podmíněný přístup](./media/active-directory-conditional-access-mam/64.png)




**Krok 3: Konfigurace zásad ochrany Intune aplikace pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/active-directory-conditional-access-mam/09.png)

V tématu [ochranu dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.






## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [Začínáme s podmíněným přístupem v Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 
