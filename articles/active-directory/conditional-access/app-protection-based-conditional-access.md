---
title: Vyžadovat zásady ochrany aplikací pro přístup ke cloudovým aplikacím s podmíněným přístupem v Azure Active Directory | Microsoft Docs
description: Naučte se vyžadovat zásady ochrany aplikací pro přístup ke cloudovým aplikacím s podmíněným přístupem v Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 4/4/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8b7cc1f3a8431986ffbaac604ec5863236f112
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357102"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Vyžadovat zásady ochrany aplikací pro cloudový přístup k aplikaci pomocí podmíněného přístupu (Preview)

Vaši zaměstnanci používají mobilní zařízení pro osobní i pracovní úkoly. I když se rozhodnete, že vaše zaměstnanci můžou být produktivní, budete také chtít zabránit ztrátě dat. Pomocí podmíněného přístupu Azure Active Directory (Azure AD) můžete chránit podniková data tím, že omezíte přístup k vašim cloudovým aplikacím. Nejdřív použijte klientské aplikace se zásadami ochrany aplikací.

Tento článek vysvětluje, jak nakonfigurovat zásady podmíněného přístupu, které můžou vyžadovat zásady ochrany aplikací před tím, než se udělí přístup k datům.

## <a name="overview"></a>Přehled

Pomocí [podmíněného přístupu Azure AD](overview.md)můžete doladit, jak můžou autorizovaní uživatelé přistupovat k vašim prostředkům. Například můžete omezit přístup k vašim cloudovým aplikacím na důvěryhodná zařízení.

K ochraně firemních dat můžete použít [Zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) . Zásady ochrany aplikací Intune nevyžadují řešení správy mobilních zařízení (MDM). Firemní data můžete chránit pomocí nebo bez registrace zařízení do řešení pro správu zařízení.

Azure Active Directory podmíněný přístup omezuje přístup k vašim cloudovým aplikacím klientským aplikacím, které Intune oznámilo službě Azure AD jako příjem zásad ochrany aplikací. Můžete například omezit přístup k Exchangi Online na aplikaci Outlook, která má zásady ochrany aplikací Intune.

V terminologii podmíněného přístupu se tyto klientské aplikace označují jako zásady chráněné *zásadami ochrany aplikací*.  

![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)

Seznam klientských aplikací chráněných zásadami najdete v tématu požadavky na [Zásady ochrany aplikací](technical-reference.md#approved-client-app-requirement).

Zásady podmíněného přístupu na základě ochrany aplikací můžete kombinovat s jinými zásadami, jako jsou [zásady podmíněného přístupu podle zařízení](require-managed-devices.md). Tímto způsobem můžete zajistit flexibilitu v tom, jak chránit data pro osobní i firemní zařízení.

> [!NOTE]
> Zásady ochrany aplikací podmíněného přístupu nelze použít na uživatele B2B, protože subjekt pro pozvání k organizaci neobsahují žádné informace v domovské organizaci uživatele B2B.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Výhody požadavku na podmíněný přístup na základě ochrany aplikací

Podobně jako u dodržování předpisů, které Intune oznamuje pro iOS a Android pro spravované zařízení, Intune teď hlásí do Azure AD, pokud se uplatní zásady ochrany aplikací. Podmíněný přístup může používat tuto zásadu jako kontrolu přístupu. Tato nová zásada podmíněného přístupu, zásady ochrany aplikací, zvyšuje zabezpečení. Chrání před chybami správců, například:

- Uživatelé, kteří nemají licenci Intune
- Uživatelé, kteří nemůžou přijímat zásady ochrany aplikací Intune
- Aplikace zásad ochrany aplikací Intune, které nejsou nakonfigurované pro příjem zásad

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že jste obeznámeni s:

- Technické informace o [požadavcích zásad ochrany aplikací](technical-reference.md#app-protection-policy-requirement) .
- Požadavek na technické informace o [schválené klientské aplikaci](technical-reference.md#approved-client-app-requirement) .
- Základní koncepty podmíněného [přístupu v Azure Active Directory](overview.md).
- Jak [nakonfigurovat zásadu podmíněného přístupu](app-based-mfa.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit zásadu podmíněného přístupu na základě ochrany aplikací, musíte:

- Mít Enterprise Mobility + Security nebo předplatné služby Azure Active Directory Premium + Intune.
- Ujistěte se, že uživatelé mají licenci pro Enterprise Mobility + Security nebo Azure AD + Intune.
- Ujistěte se, že je klientská aplikace nakonfigurovaná v Intune, aby získala zásady ochrany aplikací.
- Zajistěte, aby se uživatelé nakonfigurovali v Intune, aby získali zásady ochrany aplikací Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Zásady na základě ochrany aplikací pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě ochrany aplikací pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

V tomto scénáři se předpokládá, že uživatel:

- Nakonfiguruje e-maily pomocí nativní e-mailové aplikace v systému iOS nebo Android pro připojení k Exchangi.
- Obdrží e-mail s oznámením, že přístup je k dispozici pouze pomocí aplikace Outlook.
- Stáhne aplikaci s odkazem.
- Otevře aplikaci Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD.
- Zobrazí se výzva k instalaci **aplikace Microsoft Authenticator** nebo **portál společnosti Intune** , aby bylo možné pokračovat.
- Nainstaluje aplikaci a vrátí se do aplikace Outlook, aby bylo možné pokračovat.
- Zobrazí se výzva k registraci zařízení.
- Může přijmout zásady ochrany aplikací Intune.
- Má přístup k e-mailu.

Aby bylo možné přistupovat k podnikovým datům, musí být všechny zásady ochrany aplikací Intune v aplikaci. Zásady mohou uživateli vyzvat k restartování aplikace nebo použití dalšího kódu PIN. Jedná se o případ, kdy jsou zásady nakonfigurované pro aplikaci a platformu.

### <a name="configuration"></a>Konfiguraci

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/01.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**.

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

1. V **podmínkách**nakonfigurujte **platformy zařízení** a **klientské aplikace (Preview)** :
   1. Na **platformách zařízení**vyberte **Android** a **iOS**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

   1. V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti pro stolní počítače** a **moderní ověřování**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

1. V části **řízení přístupu**vyberte **vyžadovat zásadu ochrany aplikací (Preview)** .

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online pomocí ActiveSync (EAS)**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**.

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

1. V **podmínkách**nakonfigurujte **klientské aplikace (Preview)** . 

   1. V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

   1. V části **řízení přístupu**vyberte **vyžadovat zásadu ochrany aplikací (Preview)** .

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/05.png)

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android**

![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Zásady zařízení na základě ochrany aplikací nebo dodržování předpisů pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě aplikace nebo kompatibilního zařízení pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

Tento scénář předpokládá, že:
 
- Uživatel je už zaregistrovaný s podnikovým zařízením nebo bez něj.
- Uživatelé, kteří nejsou zaregistrovaní a zaregistrovaní ve službě Azure AD pomocí aplikace chráněné aplikací, musí zaregistrovat zařízení pro přístup k prostředkům.
- Zaregistrovaní uživatelé, kteří používají aplikaci chráněnou aplikací, nemusí zařízení znovu zaregistrovat.
- Pokud není zaregistrovaný, může uživatel obdržet zásady ochrany aplikací Intune.
- Uživatel má přístup k e-mailu pomocí Outlooku a zásadám ochrany aplikací Intune, pokud není zaregistrovaný.
- Uživatel má přístup k e-mailu v Outlooku, pokud je zařízení zaregistrované.

### <a name="configuration"></a>Konfiguraci

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/62.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**. 

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

1. V **podmínkách**konfigurujte **platformy zařízení** a **klientské aplikace (Preview)** . 
 
   1. Na **platformách zařízení**vyberte **Android** a **iOS**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

   1. V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti pro stolní počítače** a **moderní ověřování**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

5. V části **řízení přístupu**vyberte následující možnosti:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat zásady ochrany aplikací (Preview)**
   - **Vyžadovat jeden z vybraných ovládacích prvků**   
 
      ![Podmíněný přístup](./media/app-protection-based-conditional-access/11.png)

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online pomocí ActiveSync**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**. 

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

1. V **podmínkách**nakonfigurujte **klientské aplikace (Preview)** . 

   V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

1. V části **řízení přístupu**vyberte následující možnosti:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat zásady ochrany aplikací (Preview)**
   - **Vyžadovat jeden z vybraných ovládacích prvků**

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/11.png)

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android**

![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Zásady zařízení na základě ochrany aplikací a dodržování předpisů pro Exchange Online

Tento scénář se skládá ze zásad podmíněného přístupu na základě ochrany aplikací a zařízení odpovídajících předpisům pro přístup k Exchangi Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

V tomto scénáři se předpokládá, že uživatel:
 
- Nakonfiguruje e-maily pomocí nativní e-mailové aplikace v systému iOS nebo Android pro připojení k Exchangi.
- Obdrží e-mail s oznámením, že přístup vyžaduje, aby zařízení bylo zaregistrované.
- Stáhne Portál společnosti Intune a přihlásí se k portálu.
- Zkontroluje poštu a zobrazí se výzva k používání aplikace Outlook.
- Stáhne aplikaci Outlook.
- Otevře aplikaci Outlook a zadá přihlašovací údaje použité při registraci.
- Může přijmout zásady ochrany aplikací Intune.
- Má přístup k e-mailu s Outlookem a zásadami ochrany aplikací Intune.

Všechny zásady ochrany aplikací Intune se aktivují předtím, než se udělí přístup k podnikovým datům. Zásady mohou uživateli vyzvat k restartování aplikace nebo použití dalšího kódu PIN. Jedná se o případ, kdy jsou zásady nakonfigurované pro aplikaci a platformu.

### <a name="configuration"></a>Konfiguraci

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/01.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**. 

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

1. V **podmínkách**konfigurujte **platformy zařízení** a **klientské aplikace (Preview)** . 
   1. Na **platformách zařízení**vyberte **Android** a **iOS**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

   1. V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti pro stolní počítače** a **moderní ověřování**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

1. V části **řízení přístupu**vyberte následující možnosti:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat zásady ochrany aplikací (Preview)**
   - **Vyžadovat všechny vybrané ovládací prvky**   
 
      ![Podmíněný přístup](./media/app-protection-based-conditional-access/13.png)

**Krok 2: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online pomocí ActiveSync**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/06.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**. 

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/07.png)

1. V **podmínkách**nakonfigurujte **klientské aplikace (Preview)** . 

   V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti klasické pracovní plochy** a **klienti Exchange ActiveSync**.

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/92.png)

1. V části **řízení přístupu**vyberte následující možnosti:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
   - **Vyžadovat zásady ochrany aplikací (Preview)**
   - **Vyžadovat všechny vybrané ovládací prvky**   
 
      ![Podmíněný přístup](./media/app-protection-based-conditional-access/13.png)

**Krok 3: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android**

![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Zásada založená na ochraně aplikací nebo na základě aplikace pro Exchange Online a SharePoint Online

Tento scénář se skládá z zásad pro aplikace založené na ochraně aplikací nebo schválených aplikací pro přístup k Exchangi Online a SharePointu Online.

### <a name="scenario-playbook"></a>Scénář PlayBook

V tomto scénáři se předpokládá, že uživatel:

- Nakonfiguruje klientské aplikace, které jsou v seznamu aplikací, které podporují požadavek zásad ochrany aplikací nebo požadavky na schválené aplikace.  
- Používá klientské aplikace, které splňují požadavky zásad ochrany aplikací a můžou přijímat zásady ochrany aplikací Intune.
- Používá klientské aplikace, které splňují požadavky zásad schválené aplikace, které podporují zásady ochrany aplikací Intune.
- Otevře aplikaci pro přístup k e-mailu nebo dokumentům.
- Otevře aplikaci Outlook a přihlásí se pomocí přihlašovacích údajů Azure AD.
- Zobrazí se výzva k instalaci Microsoft Authenticator pro použití iOS nebo pro použití Portál společnosti Intune pro Android, pokud už nejsou nainstalovaná.
- Nainstaluje aplikaci a vrátí se do aplikace Outlook, aby bylo možné pokračovat.
- Zobrazí se výzva k registraci zařízení.
- Může přijmout zásady ochrany aplikací Intune.
- Má přístup k e-mailu s Outlookem a zásadami ochrany aplikací Intune.
- Má přístup k webům a dokumentům aplikace, která není v požadavku zásad ochrany aplikací, ale uvedená v požadavku na schválenou aplikaci.

Předtím, než se udělí přístup k podnikovým datům, jsou vyžadovány všechny zásady ochrany aplikací Intune. Zásady mohou uživateli vyzvat k restartování aplikace nebo použití dalšího kódu PIN. Jedná se o případ, kdy jsou zásady nakonfigurované pro aplikaci a platformu.

**Poznámky**

- Tento scénář můžete použít, pokud chcete podporovat jak zásady podmíněného přístupu na základě ochrany aplikace, tak i aplikace.
- V tomto *případě* se u aplikací s požadavkem na zásadu ochrany aplikací před schválením požadavků klientů vyhodnotí pro přístup nejdřív.

### <a name="configuration"></a>Konfiguraci

**Krok 1: Konfigurace zásad podmíněného přístupu Azure AD pro Exchange Online**

Pro zásady podmíněného přístupu v tomto kroku nakonfigurujte následující komponenty:

![Podmíněný přístup](./media/app-protection-based-conditional-access/62.png)

1. Zadejte název zásady podmíněného přístupu.
1. V části **přiřazení**v části **Uživatelé a skupiny**vyberte alespoň jednoho uživatele nebo skupinu pro každou zásadu podmíněného přístupu.
1. V **cloudových aplikacích**vyberte **Office 365 Exchange Online**. 

   ![Podmíněný přístup](./media/app-protection-based-conditional-access/02.png)

1. V **podmínkách**konfigurujte **platformy zařízení** a **klientské aplikace (Preview)** . 
   1. Na **platformách zařízení**vyberte **Android** a **iOS**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/03.png)

   1. V **klientských aplikacích (Preview)** vyberte **mobilní aplikace a klienti pro stolní počítače** a **moderní ověřování**.

      ![Podmíněný přístup](./media/app-protection-based-conditional-access/91.png)

1. V části **řízení přístupu**vyberte následující možnosti:
   - **Vyžadovat klientskou aplikaci schválenou**
   - **Vyžadovat zásady ochrany aplikací (Preview)**
   - **Vyžadovat jeden z vybraných ovládacích prvků**
 
      ![Podmíněný přístup](./media/app-protection-based-conditional-access/12.png)

**Krok 2: Konfigurace zásad ochrany aplikací Intune pro klientské aplikace pro iOS a Android**

![Podmíněný přístup](./media/app-protection-based-conditional-access/09.png)

Další informace najdete v tématu [Ochrana aplikací a dat pomocí Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si téma [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).
