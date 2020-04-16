---
title: Dynamics CRM | Azure Marketplace
description: Konfigurace správy zájemců pro aplikaci Dynamics CRM
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416307"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurace správy potenciálních zákazníků pro aplikaci Dynamics CRM online

Tento článek popisuje, jak nastavit aplikaci Dynamics CRM Online pro zpracování prodejních zájemců.

## <a name="prerequisites"></a>Požadavky

Následující uživatelská oprávnění jsou potřeba pro dokončení kroků v tomto článku:
- Chcete-li nainstalovat řešení, musíte být správcem instance aplikace Dynamics CRM Online.
- Chcete-li vytvořit nový účet služby pro službu pro potenciální zákazníky, musíte být správcem klienta.

<a name="install-the-solution"></a>Instalace řešení
--------------------

1.  Stáhněte si [řešení Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) a uložte ho místně.

2.  Otevřete Dynamics CRM Online a přejděte do nastavení.
    >[!NOTE]
    >Pokud možnosti v dalším snímání obrazovky nevidíte, nemáte potřebná oprávnění.
 
       ![Zobrazení nastavení dynamiky](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Vyberte **Importovat**a pak vyberte řešení, které jste stáhli v kroku 1.
 
    ![Možnost importu dynamiky](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Dokončete instalaci řešení.

## <a name="configure-user-permissions"></a>Konfigurace uživatelských oprávnění

Chcete-li zapsat zájemce do instance aplikace Dynamics CRM, musíte s námi sdílet účet služby a konfigurovat oprávnění pro účet.

Pomocí následujících kroků vytvořte účet služby a přiřaďte oprávnění. Můžete použít **Azure Active Directory** nebo Office **365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Tuto možnost doporučujeme, protože získáte další výhodu, že nikdy nebudete muset aktualizovat své uživatelské jméno / heslo, abyste získali potenciální zákazníky. Chcete-li použít možnost Služby Azure Active Directory, zadejte ID aplikace, klíč aplikace a ID adresáře z aplikace služby Active Directory.

Pomocí následujících kroků nakonfigurujte službu Azure Active Directory for Dynamics CRM.

1.  Přihlaste se na [portál Azure portal](https://portal.azure.com/) a pak vyberte službu Azure Active Directory.

2.  Vyberte **vlastnosti** a zkopírujte **ID adresáře**. Toto je identifikace vašeho účtu klienta, kterou potřebujete použít na portálu partnerů cloudu.

    ![Získat ID adresáře](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Vyberte **Registrace aplikací**a potom vyberte **Nová registrace aplikace**.
4.  Zadejte název aplikace.
5.  V pole Typ vyberte **možnost Webová aplikace / ROZHRANÍ API**.
6.  Zadejte adresu URL. Toto pole není potřeba pro zájemce, ale je nutné k vytvoření aplikace.
7. Vyberte **Vytvořit**.
8.  Teď, když je vaše aplikace zaregistrována, vyberte **Vlastnosti** a pak **zvolte zkopírovat ID aplikace**. Tyto informace o připojení použijete na portálu partnerů cloudu.
9.  V části Vlastnosti nastavte aplikaci jako víceklientské a pak vyberte **Uložit**.

10. Vyberte **Klávesy** a vytvořte nový klíč s nastavenou dobou trvání na *Nikdy nevyprší*. Chcete-li vytvořit klíč, vyberte **Uložit.** 
11. V nabídce Klávesy vyberte **Kopírovat hodnotu klíče.** Uložte kopii této hodnoty, protože ji budete potřebovat pro portál partnerů cloudu.
    
    ![Dynamics získat registrovaný klíč](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Vyberte **Požadovaná oprávnění** a pak vyberte **Přidat**. 
13. Vyberte **Dynamics CRM Online** jako nové rozhraní API a zkontrolujte oprávnění pro *aplikaci Access CRM Online jako uživatele organizace*.

14. V aplikaci Dynamics CRM přejděte na položku Uživatelé a vyberte rozevírací seznam Povoleno uživatelé, chcete-li přepnout na **položku Uživatelé aplikací**.
    
    ![Uživatelé aplikací](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Chcete-li vytvořit nového uživatele, vyberte **možnost Nový.** Vyberte rozevírací obsah **UŽIVATEL APLIKACE.**
    
    ![Přidání nového uživatele aplikace](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. V **části Nový uživatel**zadejte jméno a e-mail, který chcete s tímto připojením použít. Vložte **id aplikace** pro aplikaci, kterou jste vytvořili na webu Azure Portal.

     ![Konfigurace nového uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Přejděte na "Nastavení zabezpečení" v tomto článku dokončit konfiguraci připojení pro tohoto uživatele.

### <a name="office-365"></a>Office 365

Pokud službu Azure Active Directory používat nechcete, můžete nového uživatele zaregistrovat v *Centru pro správu Microsoftu 365*. Budete muset aktualizovat své uživatelské jméno / heslo každých 90 dní, abyste pokračovali v získávání potenciálních zákazníků.

Pomocí následujících kroků nakonfigurujte Office 365 pro Dynamics CRM.

1. Přihlaste se k [Centru pro správu Microsoftu 365](https://admin.microsoft.com).

2. Vyberte dlaždici **Správce.**

    ![Správce Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Vyberte **Přidat uživatele**.

    ![Přidání uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Vytvořte nového uživatele pro službu autora zájemce. Nakonfigurujte tahle nastavení:

    -   Zadejte heslo a odškrtněte možnost "Make this user change their password when they first sign in" možnost.
    -   Jako roli uživatele vyberte možnost "Uživatel (bez přístupu správce)".
    -   Vyberte licenci produktu zobrazenou v dalším zachycení obrazovky. Licence, kterou si vyberete, vám bude účtována. Řešení bude fungovat také s licencí Dynamics CRM Online Basic.
    
    ![Konfigurace uživatelských oprávnění a licencí](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Nastavení zabezpečení

Posledním krokem je povolit uživateli, který jste vytvořili, zapsat zájemce.

1.  Přihlaste se k aplikaci Dynamics CRM online.
2.  V **části Nastavení**vyberte možnost **Zabezpečení**.
    
    ![Nastavení zabezpečení](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Vyberte uživatele, kterého jste vytvořili v **části Uživatelská oprávnění**, a pak vyberte **Spravovat role uživatelů**. Chcete-li roli přiřadit, zaškrtněte autora **webu Microsoft Marketplace.**

    ![Přiřazení role uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Tato role je vytvořena řešením, které jste importovali, a má oprávnění pouze k zápisu zájemců a ke sledování verze řešení, aby byla zajištěna kompatibilita.

4.  V části Zabezpečení vyberte **role zabezpečení** a vyhledejte roli pro autora hlavního autora webu Microsoft Marketplace.
    
    ![Konfigurace autora zájemců o zabezpečení](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Vyberte kartu **Základní záznamy.**

    ![Povolit vytváření/čtení/zápis pro uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Zabalit

Dokončete konfiguraci aplikace Dynamics CRM pro správu zájemců přidáním generovaných informací o účtu na portál partnerů cloudu. Příklad:

-   **Id aplikace** **služby Azure Active Directory** - (příklad: *23456052-aaaa-bbbb-8662-1234df56788f),* **ID adresáře** (příklad: *12345678-8af1-4 asf-1234-12234d01db47*) a **aplikační klíč** (příklad: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Adresa URL Office 365** - **Url** **`https://contoso.crm4.dynamics.com`**(příklad: **`contoso\@contoso.onmicrosoft.com`**), Uživatelské **jméno** (příklad: ) a **Heslo** (příklad: *\@P ssw0rd*).
