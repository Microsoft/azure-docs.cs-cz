---
title: Dynamics CRM | Azure Marketplace
description: Konfigurace správy zájemců pro Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 4ccc8b85e72a4da3b0e640abcc70d24b7cdc54af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825258"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurace správy zájemců pro Dynamics CRM Online

Tento článek popisuje, jak nastavit Dynamics CRM Online pro zpracování potenciálních zákazníků prodeje.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku jsou potřeba následující oprávnění uživatele:
- Aby bylo možné nainstalovat řešení, musíte být správcem vaší instance Dynamics CRM Online.
- Abyste mohli vytvořit nový účet služby pro službu zájemce, musíte být správcem tenanta.

<a name="install-the-solution"></a>Instalace řešení
--------------------

1.  Stáhněte si [řešení Microsoft Marketplace pro zápis zájemců](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) a uložte ho místně.

2.  Otevřete Dynamics CRM Online a pokračujte na nastavení.
    >[!NOTE]
    >Pokud nevidíte možnosti na snímku další obrazovky, nemáte potřebná oprávnění.
 
       ![Zobrazení nastavení Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Vyberte **importovat**a pak vyberte řešení, které jste stáhli v kroku 1.
 
    ![Možnost importu Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Dokončete instalaci řešení.

## <a name="configure-user-permissions"></a>Konfigurace uživatelských oprávnění

Pokud chcete psát zájemce do vaší instance Dynamics CRM, musíte s námi sdílet účet služby a nakonfigurovat oprávnění pro tento účet.

Pomocí následujícího postupu vytvořte účet služby a přiřaďte oprávnění. Můžete použít **Azure Active Directory** nebo **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Tuto možnost doporučujeme, protože vám přineseme výhodu, že nikdy nepotřebujete aktualizovat uživatelské jméno nebo heslo, aby bylo možné dál získávat zájemce. Pokud chcete použít možnost Azure Active Directory, zadejte ID aplikace, klíč aplikace a ID adresáře z aplikace Active Directory.

Pro konfiguraci Azure Active Directory pro Dynamics CRM použijte následující postup.

1.  Přihlaste se k [Azure Portal](https://portal.azure.com/) a pak vyberte službu Azure Active Directory.

2.  Vyberte **vlastnosti** a pak zkopírujte **ID adresáře**. Toto je vaše identifikace účtu tenanta, kterou potřebujete použít v portál partnerů cloudu.

    ![Získat ID adresáře](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Vyberte **Registrace aplikací**a pak vyberte **Nová registrace aplikace**.
4.  Zadejte název aplikace.
5.  Jako typ vyberte **Webová aplikace/rozhraní API**.
6.  Zadejte adresu URL. Toto pole není pro zájemce potřeba, ale vyžaduje se k vytvoření aplikace.
7. Vyberte **Vytvořit**.
8.  Teď, když je vaše aplikace zaregistrovaná, vyberte **vlastnosti** a pak vyberte **Kopírovat ID aplikace**. Tyto informace o připojení použijete v portál partnerů cloudu.
9.  V okně Vlastnosti nastavte aplikaci jako víceklientské klienty a pak vyberte **Uložit**.

10. Vyberte **klíče** a vytvořte nový klíč s dobou trvání nastavenou na hodnotu *nikdy nevyprší*. Vyberte **Save (Uložit** ) a vytvořte klíč. 
11. V nabídce klíče vyberte možnost **zkopírovat hodnotu klíče.** Uložte kopii této hodnoty, protože ji budete potřebovat pro portál partnerů cloudu.
    
    ![Registrace klíče v aplikaci Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Vyberte **požadovaná oprávnění** a pak vyberte **Přidat**. 
13. Vyberte **Dynamics CRM Online** jako nové rozhraní API a ověřte oprávnění pro *přístup k CRM Online jako uživatelé organizace*.

14. V Dynamics CRM přejděte na uživatelé a vyberte rozevírací seznam povolených uživatelů, abyste mohli přepnout na **uživatele aplikace**.
    
    ![Uživatelé aplikace](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Pokud chcete vytvořit nového uživatele, vyberte **Nový** . Vyberte položku **Uživatel: rozevírací seznam uživatel aplikace** .
    
    ![Přidat nového uživatele aplikace](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Do pole **Nový uživatel**zadejte jméno a e-mail, které chcete s tímto připojením použít. Vložte do **ID aplikace** aplikaci, kterou jste vytvořili v Azure Portal.

     ![Konfigurovat nového uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. V části nastavení zabezpečení v tomto článku můžete dokončit konfiguraci připojení pro tohoto uživatele.

### <a name="office-365"></a>Office 365

Pokud nechcete používat Azure Active Directory, můžete zaregistrovat nového uživatele v *centru pro správu Microsoft 365*. Aby bylo možné pokračovat v získávání zájemců, budete muset aktualizovat uživatelské jméno nebo heslo každých 90 dní.

Pro konfiguraci Office 365 pro Dynamics CRM použijte následující postup.

1. Přihlaste se k [centru pro správu Microsoft 365](https://admin.microsoft.com).

2. Vyberte dlaždici **správce** .

    ![Správce Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Vyberte **Přidat uživatele**.

    ![Přidání uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Vytvořte nového uživatele pro službu zapisovače zájemců. Nakonfigurujte následující nastavení:

    -   Zadejte heslo a zrušte u možnosti nastavit, aby uživatel změnil heslo při prvním přihlášení.
    -   Jako roli pro uživatele vyberte uživatel (bez přístupu správce).
    -   Vyberte licenci na produkt zobrazenou v následujícím snímku obrazovky. Bude se vám účtovat licence, kterou si zvolíte. Řešení bude fungovat i v aplikaci Dynamics CRM Online Basic License.
    
    ![Konfigurace uživatelských oprávnění a licencí](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Nastavení zabezpečení

Posledním krokem je povolit uživatele, kterého jste vytvořili pro zápis zájemců.

1.  Přihlaste se k Dynamics CRM Online.
2.  V **Nastavení**vyberte **zabezpečení**.
    
    ![Nastavení zabezpečení](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Vyberte uživatele, kterého jste vytvořili v **oprávněních uživatele**, a pak vyberte **Spravovat role uživatelů**. Chcete-li přiřadit roli, ověřte **Microsoft Marketplace zapisovače potenciálního zákazníka** .

    ![Přiřadit roli uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Tato role je vytvořena řešením, které jste importovali, a má pouze oprávnění k zápisu zájemců a ke sledování verze řešení za účelem zajištění kompatibility.

4.  V části zabezpečení vyberte **role zabezpečení** a najděte roli pro Microsoft Marketplace zapisovače potenciálních zákazníků.
    
    ![Konfigurace zapisovače vedoucího zabezpečení](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Vyberte kartu **Základní záznamy** . pro uživatelské rozhraní entity uživatele povolte možnost vytvořit, číst a zapsat.

    ![Povolit vytvoření, čtení a zápis pro uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Zabalit

Dokončete konfiguraci služby Dynamics CRM pro správu potenciálních zákazníků přidáním informací o vygenerovaném účtu do portál partnerů cloudu. Příklad:

-   **Azure Active Directory** - **ID aplikace** (příklad: *23456052-AAAA-bbbb-8662-1234df56788f*), **ID adresáře** (příklad: *12345678-8Af1-4asf-1234-12234d01db47*) a **klíč aplikace** ( Příklad: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =* ).
-   **Office 365** - **URL** (příklad: *https://contoso.crm4.dynamics.com* ), **uživatelské jméno** (příklad: *Contoso\@contoso.onmicrosoft.com*) a **heslo** (příklad: *P\@ssw0rd*).
