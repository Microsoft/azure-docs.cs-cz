---
title: Vyžadování zásad ochrany aplikací pro přístup k aplikaci cloudu pomocí podmíněného přístupu v Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak vyžadování zásad ochrany aplikací pro přístup k aplikaci cloudu pomocí podmíněného přístupu v Azure Active Directory.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288498"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Jak: Vyžadovat zásady ochrany aplikací pro přístup k aplikaci cloudu pomocí podmíněného přístupu (Preview)

Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. A ujistěte se, vaši zaměstnanci byli produktivní, můžete také chcete zabránit ztrátě dat. S podmíněným přístupem Azure Active Directory (Azure AD) můžete chránit podniková data omezením přístupu k vašim cloudovým aplikacím do klientské aplikace, které mají zásady ochrany aplikací nejprve.

Toto téma vysvětluje, jak nakonfigurovat zásady podmíněného přístupu, které můžou vyžadovat, aby zásady ochrany aplikací před přístup k datům.

## <a name="overview"></a>Přehled

S [podmíněný přístup Azure AD](overview.md), lze optimalizovat jak můžou Autorizovaní uživatelé přístup k vašim prostředkům. Například můžete omezit přístup k vašim cloudovým aplikacím pro důvěryhodného zařízení.

Můžete použít [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) k ochraně dat vaší společnosti. Zásady ochrany aplikací nevyžadují řešení správy mobilních zařízení (MDM), které umožňuje ochranu firemních dat i bez registrace zařízení do řešení správy zařízení.

Podmíněný přístup služby Azure Active Directory omezuje přístup k vašim cloudovým aplikacím klientským aplikacím, které ohlásil do služby Azure AD jako příjem zásad ochrany aplikací Intune. Například můžete omezit přístup k Exchangi Online k aplikaci Outlook, která má zásady ochrany aplikací Intune.

V terminologii podmíněného přístupu jsou známé jako zásady, které jsou chráněné pomocí těchto klientských aplikací **zásady ochrany aplikací**.  

![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)

Seznam zásad chráněných klientských aplikací, najdete v části [požadavek zásady ochrany aplikací](technical-reference.md#approved-client-app-requirement).

Zásady podmíněného přístupu na základě aplikace ochrany pomocí jiných zásad můžete kombinovat například [zásad podmíněného přístupu podle zařízení](require-managed-devices.md) flexibility při ochraně dat pro podnikové i osobní zařízení.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Výhody požadavek podmíněný přístup založený na ochranu aplikací

Podobný dodržování předpisů hlášených Intune pro iOS a Android pro spravované zařízení, Intune nyní sestavy do služby Azure AD Pokud jsou použité zásady ochrany aplikací, tak, aby podmíněného přístupu můžete použít jako kontroly přístupu. Tyto nové zásady podmíněného přístupu **zásady ochrany aplikací** zvyšuje úroveň zabezpečení, jako ochrana proti chybám správce:

- Uživatelé, kteří nemají licenci pro Intune
- Uživatelé, kteří nemůže přijímat zásady ochrany aplikací Intune
- Intune zásad ochrany aplikací, které nebyly nakonfigurovány pro příjem zásad


## <a name="before-you-begin"></a>Před zahájením

Toto téma předpokládá, že máte zkušenosti s:

- [Požadavek zásady ochrany aplikací](technical-reference.md#app-protection-policy-requirement) technické reference.

- [Schválené klientské aplikace požadavek](technical-reference.md#approved-client-app-requirement) technické reference.

- Základní koncepty [podmíněného přístupu v Azure Active Directory](overview.md).

- Jak [nakonfigurovat zásady podmíněného přístupu](app-based-mfa.md).


## <a name="prerequisites"></a>Požadavky

K vytvoření zásady podmíněného přístupu na základě ochrany aplikací, musíte
- Máte Enterprise Mobility + Security nebo předplatné Azure Active Directory premium + Intune
- Ujistěte se uživatelé mají licenci pro EMS nebo Azure AD a Intune
- Ujistěte se, že klientská aplikace není nakonfigurovaná v Intune přijímat zásady ochrany aplikací
- Ujistěte se, že že uživatelé jsou nakonfigurovaní v Intune přijímat zásady ochrany aplikací Intune

## <a name="app-protection-based-policy-for-exchange-online"></a>Zásady ochrany na základě aplikací pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě ochrany aplikací pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:

- Nakonfiguruje e-mailu pro připojení k systému Exchange pomocí nativní e-mailová aplikace v Iosu nebo Androidu

- Obdrží e-mailu, která označuje, že přístup je k dispozici pouze pomocí aplikace Outlook

- Soubory ke stažení aplikace s odkazem

- Otevře se aplikace Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD

- Se zobrazí výzva k instalaci pokračujte Authenticator (iOS) nebo portál společnosti (Android)

- Instalace aplikací a může vrátit do aplikace Outlook a pokračovat

- Se zobrazí výzva k registraci zařízení

- Může přijímat zásady ochrany aplikací Intune

- Získat přístup k e-mailu

Žádné zásady ochrany aplikací Intune musí být v aplikaci pro přístup k firemním datům a může vyzvat uživatele k restartování aplikace, použijte další atd kódu PIN (Pokud je nakonfigurovaná pro aplikace a platformy).

### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/01.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**:

    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. Jako **klientské aplikace (preview)** vyberte **mobilních a desktopových aplikací** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat zásady ochrany aplikací (preview)** vybrané.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)
 

**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.


3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **klientské aplikace (preview)**. 

    a. Jako **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klientů protokolu Exchange ActiveSync**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

    b. Jako **ovládací prvky přístupu**, musíte mít **vyžadovat zásady ochrany aplikací (preview)** vybrané.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)


**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Aplikace založené na ochranu nebo kompatibilní zařízení zásady pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě ochrany nebo kompatibilní zařízení aplikací pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že:
 
- Některé uživatele je už zaregistrovaný (s nebo bez firemní zařízení)

- Uživatelé, kteří nejsou zaregistrované a zaregistrované v Azure AD pomocí aplikace pro chráněné aplikaci potřeba k registraci zařízení pro přístup k prostředkům

- Zaregistrovaní uživatelé pomocí aplikace pro aplikaci chráněnou nemusíte znovu registrovat zařízení

- Uživatel může přijímat zásady ochrany aplikací Intune Pokud není zaregistrovaná

- Uživatel přístup k e-mailu v Outlooku a zásady ochrany aplikací Intune Pokud není zaregistrovaná

- Uživatel získat přístup k e-mailu v Outlooku, když je zařízení zaregistrované


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/62.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. Jako **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat jeden z vybraných ovládacích prvků**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/11.png)



**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **klientské aplikace**. 

    Jako **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klientů protokolu Exchange ActiveSync**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat jeden z vybraných ovládacích prvků**   
    ![Podmíněný přístup](./media/app-protection-based-conditional-access/11.png)



**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Aplikace založené na ochranu a vyhovující zařízení zásady pro Exchange Online

Tento scénář se skládá z zásadu podmíněného přístupu na základě aplikace ochrany a vyhovující zařízení pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:
 
-   Nakonfiguruje e-mailu pro připojení k systému Exchange pomocí nativní e-mailová aplikace v Iosu nebo Androidu
-   Obdrží e-mailu, která označuje, že přístup vyžaduje, aby se dají zaregistrovat zařízení
-   Soubory ke stažení aplikace portál společnosti a přihlásí k portálu společnosti
-   Ověří e-mailu a se zobrazí výzva, aby používali aplikaci Outlook
-   Soubory ke stažení aplikace Outlook
-   Otevře se aplikace Outlook a zadá přihlašovací údaje použité pro registraci
-   Může přijímat přijímat zásady ochrany aplikací Intune
-   Je možné pro přístup k e-mailu v Outlooku a zásady ochrany aplikací Intune

Žádné zásady ochrany aplikací Intune se aktivují před přístup k firemním datům a může vyzvat uživatele k restartování aplikace, použijte další atd kódu PIN (Pokud je nakonfigurovaná pro aplikace a platformy)


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/01.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. Jako **klientské aplikace (preview)** vyberte **mobilních a desktopových aplikací** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat všechny vybrané ovládací prvky**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/13.png)



**Krok 2: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online s Active Sync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **klientské aplikace (preview)**. 

    Jako **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klientů protokolu Exchange ActiveSync**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžaduje se klientem schválená aplikace (preview)**

   - **Vyžadovat všechny vybrané ovládací prvky**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/13.png)




**Krok 3: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Aplikace na základě ochrany nebo aplikací na zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá ze zásad aplikací na základě ochrany nebo schválených aplikací pro přístup k Exchangi Online a SharePoint Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:

- Konfiguruje buď klientské aplikace, které jsou buď v seznamu aplikací, které podporují požadavek zásady ochrany aplikací nebo požadavek na schválených aplikací.  
- Uživatel používá klientské aplikace, které splňují požadavky zásad ochrany aplikace můžou přijímat zásady ochrany aplikací Intune
- Uživatel používá klientské aplikace, které splňují zásady požadavek na schválené aplikace podporující zásady ochrany aplikací Intune
- Otevře se aplikace pro přístup k e-mailu nebo dokumenty
- Otevře se aplikace Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD
- Se zobrazí výzva k instalaci pokračujte Authenticator (iOS) nebo portál společnosti (Android) (Pokud není nainstalovaná)
- Instalace aplikací a může vrátit do aplikace Outlook a pokračovat
- Se zobrazí výzva k registraci zařízení
- Může přijímat zásady ochrany aplikací Intune
- Je možné pro přístup k e-mailu v Outlooku a zásady ochrany aplikací Intune
- Je moci přistupovat k lokality nebo dokumenty s aplikací není na požadavek zásady ochrany aplikací ale uvedené požadavek na schválenou aplikaci.

Žádné zásady ochrany aplikací Intune se vyžaduje před přístup k firemním datům a může vyzvat uživatele k restartování aplikace, použijte další atd kódu PIN (Pokud je nakonfigurovaná pro aplikace a platformy)

**Poznámky**

- Tento scénář můžete použít, pokud chcete zajistit podporu zásady podmíněného přístupu na základě ochrany a na základě aplikace obě aplikace.

- V tomto *nebo* zásady, aplikace se **zásady ochrany aplikací** požadavek na přístup k se vyhodnocují před **schválené klientské aplikace** požadavek.

### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurace zásady podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku budete muset nakonfigurovat následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/62.png)

1. **Název** zásady podmíněného přístupu.

2. **Uživatelé a skupiny**: Každá zásada podmíněného přístupu musí mít aspoň jeden vybraný uživatel nebo skupina.

3. **Cloudové aplikace:** Jako cloudové aplikace, budete muset vybrat **Office 365 Exchange Online**. 

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/02.png)

4. **Podmínky:** Jako **podmínky**, budete muset nakonfigurovat **platformy zařízení** a **klientské aplikace**. 
 
    a. Jako **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. Jako **klientské aplikace (preview)** vyberte **mobilních a desktopových aplikací** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. Jako **ovládací prvky přístupu**, musíte mít následující vybrané:

   - **Vyžaduje se klientem schválená aplikace.**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat jeden z vybraných ovládacích prvků**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/12.png)


**Krok 2: Konfigurace zásad ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Zobrazit [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) Další informace.




## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).

Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 