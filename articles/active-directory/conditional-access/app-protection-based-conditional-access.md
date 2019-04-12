---
title: Vyžadovat zásady ochrany aplikací pro přístup k aplikaci cloudu pomocí podmíněného přístupu v Azure Active Directory | Dokumentace Microsoftu
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
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496926"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Vyžadovat zásady ochrany aplikací pro přístup k aplikaci cloudu pomocí podmíněného přístupu (preview)

Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. A ujistěte se, vaši zaměstnanci byli produktivní, můžete také chcete zabránit ztrátě dat. S podmíněným přístupem Azure Active Directory (Azure AD) můžete chránit podniková data prostřednictvím omezení přístupu k vašim cloudovým aplikacím. Nejprve pomocí klientských aplikací pomocí zásad ochrany aplikací.

Tento článek vysvětluje, jak nakonfigurovat zásady podmíněného přístupu, které může vyžadovat zásady ochrany aplikací, než se udělí přístup k datům.

## <a name="overview"></a>Přehled

S [podmíněný přístup Azure AD](overview.md), lze optimalizovat jak můžou Autorizovaní uživatelé přístup k vašim prostředkům. Například můžete omezit přístup k vašim cloudovým aplikacím pro důvěryhodného zařízení.

Můžete použít [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) k ochraně dat vaší společnosti. Zásady ochrany aplikací nevyžadují řešení správy mobilních zařízení. Budete moci chránit data vaší společnosti s nebo bez registrace zařízení do řešení správy zařízení.

Podmíněný přístup služby Azure Active Directory omezuje přístup k vašim cloudovým aplikacím klientským aplikacím, které ohlásil do služby Azure AD jako příjem zásad ochrany aplikací Intune. Například můžete omezit přístup k Exchangi Online k aplikaci Outlook, která má zásady ochrany aplikací Intune.

V terminologii podmíněného přístupu jsou známé jako zásady, které jsou chráněné pomocí těchto klientských aplikací *zásady ochrany aplikací*.  

![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)

Seznam aplikací chráněných zásad klienta najdete v tématu [požadavek zásady ochrany aplikací](technical-reference.md#approved-client-app-requirement).

Zásady podmíněného přístupu na základě aplikace Ochrana dalších zásad, můžete kombinovat například [zásad podmíněného přístupu podle zařízení](require-managed-devices.md). Díky tomu mohou poskytnout flexibilitu v způsob ochrany dat pro podnikové i osobní zařízení.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Výhody požadavek podmíněný přístup založený na ochranu aplikací

Podobně jako na dodržování předpisů, která je ohlášena Intune pro iOS a Android pro spravovaná zařízení, Intune teď použít sestavy do služby Azure AD, pokud zásady ochrany aplikací. Podmíněného přístupu můžete použít tyto zásady jako kontroly přístupu. Toto nové zásady podmíněného přístupu, zásady ochrany aplikací, zvyšuje úroveň zabezpečení. Chrání proti chybám správce, jako například:

- Uživatelé, kteří nemají licenci Intune.
- Uživatelé, kteří nemůže přijímat zásady ochrany aplikací Intune.
- Intune zásad ochrany aplikací, které nejsou nakonfigurované pro příjem zásad.


## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že už znáte:

- [Požadavek zásady ochrany aplikací](technical-reference.md#app-protection-policy-requirement) technické reference.
- [Schválené klientské aplikace požadavek](technical-reference.md#approved-client-app-requirement) technické reference.
- Základní koncepty [podmíněného přístupu v Azure Active Directory](overview.md).
- Jak [nakonfigurovat zásady podmíněného přístupu](app-based-mfa.md).


## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit zásadu podmíněného přístupu na základě ochrany aplikací, musíte:

- Máte Enterprise Mobility + Security nebo předplatné Azure Active Directory premium + Intune.
- Ujistěte se, že uživatelé mají licenci k Enterprise Mobility + Security nebo Azure AD a Intune.
- Zajistěte, aby že klientská aplikace je nakonfigurovaná v Intune přijímat zásady ochrany aplikací.
- Zajistěte, aby že uživatelé jsou nakonfigurovaní v Intune přijímat zásady ochrany aplikací Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Zásady ochrany na základě aplikací pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě ochrany aplikací pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:

- Nastaví e-mailu pro připojení k systému Exchange pomocí nativní poštovní aplikace v Iosu nebo Androidu.
- Obdrží e-mailu, která označuje, že přístup je k dispozici pouze prostřednictvím aplikace Outlook.
- Soubory ke stažení aplikace s odkazem.
- Otevře se aplikace Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD.
- Se zobrazí výzva k instalaci buď Microsoft Authenticator pro iOS použití nebo portál společnosti Intune pro Android pomocí pokračujte.
- Nainstaluje aplikaci a vrátí do aplikace Outlook, abyste mohli pokračovat.
- Se zobrazí výzva k registraci zařízení.
- Můžou přijímat zásady ochrany aplikací Intune.
- Přístup k e-mailu.

Žádné zásady ochrany aplikací Intune musí být v aplikaci přístup k podnikovým datům. Zásady můžou vyzvat uživatele k restartování aplikace nebo použít další PIN kód. To platí, pokud jsou zásady nakonfigurované pro aplikace a platformy.

### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/01.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.

3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. V **podmínky**, nakonfigurujte **platformy zařízení** a **klientské aplikace (preview)**:

    a. V **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. V části **ovládací prvky přístupu**vyberte **vyžadovat zásady ochrany aplikací (preview)**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)
 

**Krok 2: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online s ActiveSync (EAS)**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.


3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. V **podmínky**, nakonfigurujte **klientské aplikace (preview)**. 

    a. V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klientů protokolu Exchange ActiveSync**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

    b. V části **ovládací prvky přístupu**vyberte **vyžadovat zásady ochrany aplikací (preview)**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)


**Krok 3: Nakonfigurujte zásady ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Aplikace založené na ochranu nebo kompatibilní zařízení zásady pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě ochrany nebo kompatibilní zařízení aplikací pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že:
 
- Uživatel je už zaregistrovaný, s nebo bez něj firemní zařízení.
- Uživatelé, kteří nejsou zaregistrované a zaregistrované v Azure AD pomocí aplikace chráněné aplikaci potřeba k registraci zařízení pro přístup k prostředkům.
- Zaregistrovaní uživatelé, kteří používají chráněný aplikaci nemusíte znovu registrovat zařízení.
- Uživatel může přijímat zásady ochrany aplikací Intune, pokud není zaregistrovaná.
- Uživatel přístup k e-mailu v Outlooku a zásady ochrany aplikací Intune, pokud není zaregistrovaná.
- Pokud je zařízení zaregistrované, má uživatel přístup e-mailu v Outlooku.


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/62.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.

3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**. 

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. V **podmínky**, nakonfigurujte **platformy zařízení** a **klientské aplikace (preview)**. 
 
    a. V **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. V části **ovládací prvky přístupu**, vyberte následující možnosti:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat jeden z vybraných ovládacích prvků**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/11.png)



**Krok 2: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online s ActiveSync**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.

3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. V **podmínky**, nakonfigurujte **klientské aplikace (preview)**. 

    V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klientů protokolu Exchange ActiveSync**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

5. V části **ovládací prvky přístupu**, vyberte následující možnosti:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat jeden z vybraných ovládacích prvků**

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/11.png)



**Krok 3: Nakonfigurujte zásady ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Aplikace založené na ochranu a vyhovující zařízení zásady pro Exchange Online

Tento scénář se skládá z zásadu podmíněného přístupu na základě aplikace ochrany a vyhovující zařízení pro přístup k Exchangi Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:
 
-   Nastaví e-mailu pro připojení k systému Exchange pomocí nativní poštovní aplikace v Iosu nebo Androidu.
-   Obdrží e-mailu, která označuje, že přístup vyžaduje registraci jejich zařízení.
-   Soubory ke stažení portálu společnosti Intune a přihlásí se k portálu.
-   Ověří e-mailu a se zobrazí výzva, aby používali aplikaci Outlook.
-   Soubory ke stažení aplikace Outlook.
-   Otevře se aplikace Outlook a zadá přihlašovací údaje použité pro registraci.
-   Můžou přijímat zásady ochrany aplikací Intune.
-   Přístup k e-mailu v Outlooku a zásady ochrany aplikací Intune.

Před udělením přístupu k firemním datům, aktivují se všechny zásady ochrany aplikací Intune. Zásady můžou vyzvat uživatele k restartování aplikace nebo použít další PIN kód. To platí, pokud jsou zásady nakonfigurované pro aplikace a platformy.


### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/01.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.

3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**. 

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. V **podmínky**, nakonfigurujte **platformy zařízení** a **klientské aplikace (preview)**. 
 
    a. V **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. V části **ovládací prvky přístupu**, vyberte následující možnosti:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat všechny vybrané ovládací prvky**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/13.png)



**Krok 2: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online s ActiveSync**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.

3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**. 

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

4. V **podmínky**, nakonfigurujte **klientské aplikace (preview)**. 

    V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klientů protokolu Exchange ActiveSync**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

5. V části **ovládací prvky přístupu**, vyberte následující možnosti:

   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat všechny vybrané ovládací prvky**   
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/13.png)




**Krok 3: Nakonfigurujte zásady ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Aplikace na základě ochrany nebo aplikací na zásady pro Exchange Online a SharePoint Online

Tento scénář se skládá ze zásad aplikací na základě ochrany nebo schválených aplikací pro přístup k Exchangi Online a SharePoint Online.


### <a name="scenario-playbook"></a>Scénář playbook

Tento scénář předpokládá, že uživatel:

- Nakonfiguruje klientské aplikace, které jsou buď v seznamu aplikací, které podporují požadavek zásady ochrany aplikací nebo požadavek na schválených aplikací.  
- Pomocí klientských aplikací, které splňují požadavky zásad ochrany aplikací a může přijímat zásady ochrany aplikací Intune.
- Použití klientské aplikace, které splňují zásady požadavek na schválené aplikace podporující zásady ochrany aplikací Intune.
- Otevře se aplikace pro přístup k e-mailu nebo dokumenty.
- Otevře se aplikace Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD.
- Se zobrazí výzva k instalaci buď Microsoft Authenticator pro iOS použití nebo portál společnosti Intune pro Android použijte, pokud jste ještě není nainstalovaný.
- Instalace aplikace a může vrátit do aplikace Outlook pokračujte.
- Se zobrazí výzva k registraci zařízení.
- Můžou přijímat zásady ochrany aplikací Intune.
- Přístup k e-mailu v Outlooku a zásady ochrany aplikací Intune.
- Můžete přístup k webům a dokumenty s aplikací není na požadavek zásady ochrany aplikací ale uvedený požadavek na schválenou aplikaci.

Žádné zásady ochrany aplikací Intune jsou požadovány, než se udělí přístup k podnikovým datům. Zásady můžou vyzvat uživatele k restartování aplikace nebo použít další PIN kód. To platí, pokud jsou zásady nakonfigurované pro aplikace a platformy.

**Poznámky**

- Tento scénář můžete použít, pokud chcete zajistit podporu zásady podmíněného přístupu na základě ochrany a na základě aplikace obě aplikace.
- V tomto *nebo* přístup před požadavek schválené klientské aplikace k se vyhodnocují zásady, aplikace se požadavek na zásady ochrany aplikací.

### <a name="configuration"></a>Konfigurace

**Krok 1: Konfigurovat zásadu podmíněného přístupu Azure AD pro Exchange Online**

Zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/62.png)

1. Zadejte název své zásady podmíněného přístupu.

2. V části **přiřazení**v **uživatelů a skupin**, vyberte aspoň jednoho uživatele nebo skupinu pro jednotlivé zásady podmíněného přístupu.

3. V **cloudové aplikace**vyberte **Office 365 Exchange Online**. 

     ![Podmíněný přístup](./media/app-protection-based-conditional-access/02.png)

4. V **podmínky**, nakonfigurujte **platformy zařízení** a **klientské aplikace (preview)**. 
 
    a. V **platformy zařízení**vyberte **Android** a **iOS**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

    b. V **klientské aplikace (preview)** vyberte **mobilní aplikace a desktopoví klienti** a **klienti moderního ověřování**.

    ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. V části **ovládací prvky přístupu**, vyberte následující možnosti:

   - **Vyžaduje se klientem schválená aplikace.**

   - **Vyžadovat zásady ochrany aplikací (Preview)**

   - **Vyžadovat jeden z vybraných ovládacích prvků**
 
     ![Podmíněný přístup](./media/app-protection-based-conditional-access/12.png)


**Krok 2: Nakonfigurujte zásady ochrany aplikací Intune pro iOS a Android klientské aplikace**


![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [ochrana dat a aplikací pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).
- Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si téma [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 