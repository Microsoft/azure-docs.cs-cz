---
title: Azure Active Directory podmíněného přístupu k aplikacím | Dokumentace Microsoftu
description: Zjistěte, jak funguje podmíněný přístup na základě aplikace Azure Active Directory.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: f34fc4c41094292db9bed1294ee7b26ec04c96c6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630598"
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Azure Active Directory podmíněného přístupu k aplikacím  

Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. A ujistěte se, vaši zaměstnanci byli produktivní, můžete také chcete zabránit ztrátě dat. S Azure Active Directory (Azure AD) podmíněného přístupu k aplikacím můžete omezit přístup k vašim cloudovým aplikacím do klientských aplikací, které můžete chránit podniková data.  

Toto téma vysvětluje, jak nakonfigurovat podmíněný přístup na základě aplikace Azure AD.

## <a name="overview"></a>Přehled

S [podmíněný přístup Azure AD](overview.md), lze optimalizovat jak můžou Autorizovaní uživatelé přístup k vašim prostředkům. Například můžete omezit přístup k vašim cloudovým aplikacím pro důvěryhodného zařízení.

Můžete použít [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) k ochraně dat vaší společnosti. Zásady ochrany aplikací nevyžadují řešení správy mobilních zařízení (MDM), které umožňuje ochranu firemních dat i bez registrace zařízení do řešení správy zařízení.

V Azure Active Directory podmíněného přístupu na základě aplikace umožňuje omezit přístup k vašim cloudovým aplikacím do klientské aplikace, které podporují zásady ochrany aplikací Intune. Například můžete omezit přístup k Exchangi Online k aplikaci Outlook.

V terminologii podmíněný přístup, se nazývají tyto klientské aplikace **schválené klientské aplikace**.  


![Podmíněný přístup](./media/app-based-conditional-access/05.png)


Seznam klientem schválených aplikací najdete v tématu [schválené klientské aplikace požadavek](technical-reference.md#approved-client-app-requirement).


Zásady podmíněného přístupu na základě aplikace s jinými zásadami můžete kombinovat například [zásad podmíněného přístupu podle zařízení](require-managed-devices.md) flexibility při ochraně dat pro podnikové i osobní zařízení.

 


## <a name="before-you-begin"></a>Než začnete

Toto téma předpokládá, že máte zkušenosti s:

- [Schválené klientské aplikace požadavek](technical-reference.md#approved-client-app-requirement) technické reference.


- Základní koncepty [podmíněného přístupu v Azure Active Directory](overview.md).

- Jak [nakonfigurovat zásady podmíněného přístupu](app-based-mfa.md).

- [Migrace zásad podmíněného přístupu](best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Požadavky

Vytvoření zásady podmíněného přístupu na základě aplikace, musíte mít Enterprise Mobility + Security nebo předplatné Azure Active Directory premium, a uživatelé musí mít licenci pro EMS nebo Azure AD. 


## <a name="exchange-online-policy"></a>Zásady pro Exchange Online 

Tento scénář se skládá ze zásady podmíněného přístupu na základě aplikace pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:

- Nakonfiguruje e-mailu pro připojení k systému Exchange pomocí nativní e-mailová aplikace v Iosu nebo Androidu

- Obdrží e-mailu, která označuje, že přístup je k dispozici pouze pomocí aplikace Outlook

- Soubory ke stažení aplikace s odkazem

- Otevře se aplikace Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD

- Se zobrazí výzva k instalaci pokračujte Authenticator (iOS) nebo portál společnosti (Android)

- Instalace aplikací a může vrátit do aplikace Outlook a pokračovat

- Se zobrazí výzva k registraci zařízení

- Získat přístup k e-mailu

Žádné zásady ochrany aplikací Intune se aktivuje v době přístup k firemním datům a může vyzvat uživatele k restartování aplikace, použijte další atd kódu PIN (Pokud je nakonfigurovaná pro aplikace a platformy).

### <a name="configuration"></a>Konfigurace 

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/01.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/app-based-conditional-access/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**:

    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-based-conditional-access/03.png)

    b. Jako **klientské aplikace**vyberte **mobilních a desktopových aplikací**.

    ![Podmíněný přístup](./media/app-based-conditional-access/04.png)

5. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat klientem schválenou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/app-based-conditional-access/05.png)
 

**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/06.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.


3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/app-based-conditional-access/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    a. Jako **klientské aplikace**vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/app-based-conditional-access/08.png)

    b. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat klientem schválenou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/app-based-conditional-access/05.png)


**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Zásady Exchange Online a SharePoint Online

Tento scénář se skládá z podmíněného přístupu pomocí zásad správy mobilních aplikací pro přístup k Exchangi Online a SharePoint Online pomocí schválených aplikací.

### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:

- Se pokusí použít aplikaci SharePoint pro připojení a také k zobrazení svých podnikových webech

- Pokus o přihlášení pomocí stejných přihlašovacích údajů jako přihlašovací údaje aplikace Outlook

- Není potřeba znovu zaregistrovat a získat přístup k prostředkům


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/71.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.


3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online** a **Office 365 SharePoint Online**. 

    ![Podmíněný přístup](./media/app-based-conditional-access/02.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**:

    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-based-conditional-access/03.png)

    b. Jako **klientské aplikace**vyberte **mobilních a desktopových aplikací**.

    ![Podmíněný přístup](./media/app-based-conditional-access/04.png)

5. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat klientem schválenou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/app-based-conditional-access/05.png)




**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/06.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. Online 

    ![Podmíněný přístup](./media/app-based-conditional-access/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**:

    a. Jako **klientské aplikace**vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/app-based-conditional-access/08.png)

    b. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat klientem schválenou aplikaci (preview)** vybrané.

    ![Podmíněný přístup](./media/app-based-conditional-access/05.png)




**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Na základě aplikace nebo kompatibilní zařízení zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá z zásadu podmíněného přístupu založeného na aplikacích nebo kompatibilní zařízení pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že:
 
- Některé uživatele jsou už zaregistrovaná (s nebo bez firemní zařízení)

- Uživatelé, kteří nejsou zaregistrované a zaregistrované v Azure AD pomocí aplikace pro chráněné aplikaci potřeba k registraci zařízení pro přístup k prostředkům

- Zaregistrovaní uživatelé pomocí aplikace pro aplikaci chráněnou nemusíte znovu registrovat zařízení


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/62.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online** a **Office 365 SharePoint Online**. 

     ![Podmíněný přístup](./media/app-based-conditional-access/02.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-based-conditional-access/03.png)

    b. Jako **klientské aplikace**vyberte **mobilních a desktopových aplikací**.

    ![Podmíněný přístup](./media/app-based-conditional-access/04.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

    - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

    - **Vyžaduje se klientem schválená aplikace (preview)**

    - **Vyžadovat jeden z vybraných ovládacích prvků**   
 
    ![Podmíněný přístup](./media/app-based-conditional-access/11.png)



**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/61.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/app-based-conditional-access/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    Jako **klientské aplikace*vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/app-based-conditional-access/08.png)

5. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat klientem schválenou aplikaci (preview)** vybrané.
 
    ![Podmíněný přístup](./media/app-based-conditional-access/11.png)




**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Na základě aplikace a vyhovující zařízení zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá z zásadu podmíněného přístupu na základě aplikace a vyhovující zařízení pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:
 
-   Nakonfiguruje e-mailu pro připojení k systému Exchange pomocí nativní e-mailová aplikace v Iosu nebo Androidu
-   Obdrží e-mailu, která označuje, že přístup vyžaduje, aby se dají zaregistrovat zařízení
-   Soubory ke stažení aplikace portál společnosti a přihlásí k portálu společnosti
-   Ověří e-mailu a se zobrazí výzva, aby používali aplikaci Outlook
-   Soubory ke stažení aplikace Outlook
-   Otevře se aplikace Outlook a zadá přihlašovací údaje použité pro registraci
-   Uživatel je přístup k e-mailu

Žádné zásady ochrany aplikací Intune se aktivují v době přístup k firemním datům a může vyzvat uživatele k restartování aplikace, použijte další atd kódu PIN (Pokud je nakonfigurovaná pro aplikace a platformy)


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online a SharePoint Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/62.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online** a **Office 365 SharePoint Online**. 

     ![Podmíněný přístup](./media/app-based-conditional-access/02.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-based-conditional-access/03.png)

    b. Jako **klientské aplikace**vyberte **mobilních a desktopových aplikací**.

    ![Podmíněný přístup](./media/app-based-conditional-access/04.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

    - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

    - **Vyžaduje se klientem schválená aplikace (preview)**

    - **Vyžadovat všechny vybrané ovládací prvky**   
 
    ![Podmíněný přístup](./media/app-based-conditional-access/13.png)



**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-based-conditional-access/61.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/app-based-conditional-access/07.png)

4. **Podmínky:** jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    Jako **klientské aplikace**vyberte **protokolu Exchange Active Sync**.

    ![Podmíněný přístup](./media/app-based-conditional-access/08.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

    - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

    - **Vyžaduje se klientem schválená aplikace (preview)**

    - **Vyžadovat všechny vybrané ovládací prvky**   
 
    ![Podmíněný přístup](./media/app-based-conditional-access/64.png)




**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.






## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).

Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 
