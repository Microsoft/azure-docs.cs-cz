---
title: Dynamics CRM | Dokumentace Microsoftu
description: Konfigurace správy zájemce Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 8c432146d33db992a0ae612dfc56ace9460ade17
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870855"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurace Správa potenciálních zákazníků pro Dynamics CRM online

Tento článek popisuje, jak nastavit Dynamics CRM Online zpracování potenciálních zákazníků.

## <a name="prerequisites"></a>Požadavky

Tato oprávnění uživatele jsou nutné pro dokončení kroků v tomto článku:
- Musíte být správce na instanci aplikace Dynamics CRM Online k instalaci řešení.
- Musíte být správce tenanta, chcete-li vytvořit nový účet služby pro službu potenciálních zákazníků.

<a name="install-the-solution"></a>Instalace řešení
--------------------

1.  Stáhněte si [zapisovače vést Microsoft Marketplace řešení](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) a uloží do místního prostředí.

2.  Otevřete Dynamics CRM Online a přejděte na nastavení.
    >[!NOTE]
    >Pokud se nezobrazí možnosti v další snímek obrazovky, nemáte oprávnění, která potřebujete.
 
       ![Zobrazit nastavení Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Vyberte **Import**a pak vyberte řešení, které jste si stáhli v kroku 1.
 
    ![Možnost importu Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Dokončete instalaci řešení.

## <a name="configure-user-permissions"></a>Konfigurovat uživatelská oprávnění

Zápis vede do vaší instance aplikace Dynamics CRM, které jste s námi sdílet účet služby a konfigurovat oprávnění pro účet.

Pomocí následujících kroků vytvořte účet služby a přiřaďte oprávnění. Můžete použít **Azure Active Directory** nebo **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Doporučujeme tuto možnost, protože se zobrazí její Další výhodou nikdy by bylo potřeba aktualizovat vaše uživatelské jméno a heslo, aby bylo možné zachovat získávání potenciálních zákazníků. Možnost pro Azure Active Directory zadejte Id aplikace, klíč aplikace a Id adresáře z vaší aplikace služby Active Directory.

Použijte následující postup ke konfiguraci služby Azure Active Directory pro Dynamics CRM.

1.  Přihlaste se k [webu Azure portal](https://portal.azure.com/) a potom vyberte službu Azure Active Directory.

2.  Vyberte **vlastnosti** a zkopírujte **Id adresáře**. Toto je vaše identifikace účtu tenanta, které potřebujete, použijte v portál partnerů cloudu.
    ![Získejte ID adresáře](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Vyberte **registrace aplikací**a pak vyberte **registrace nové aplikace**.
4.  Zadejte název aplikace.
5.  Jako typ **webovou aplikaci nebo API**.
6.  Zadejte adresu URL. Toto pole není potřeba pro potenciální zákazníky, ale je potřeba k vytvoření aplikace.
7. Vyberte **Vytvořit**.
8.  Teď, když vaše aplikace bude zaregistrovaná, vyberte **vlastnosti** a pak vyberte **zkopírujte Id aplikace**. Tyto informace o připojení použijete v portál partnerů cloudu.
9.  Ve vlastnostech nastavení aplikace s více klienty a pak vyberte **Uložit**.

10. Vyberte **klíče** a vytvořte nový klíč s dobou trvání nastavenou *nikdy nevyprší platnost*. Vyberte **Uložit** vytvoření klíče. 
11. V nabídce klíče, vyberte **hodnotu klíče si zkopírujte.** Uložte kopii této hodnoty, protože ho budete potřebovat pro portál partnerů cloudu.
    
    ![Získat registrovaný klíč Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
12. Vyberte **požadovaná oprávnění** a pak vyberte **přidat**. 
13. Vyberte **Dynamics CRM Online** jako nové rozhraní API a zaškrtněte políčko pro oprávnění *přístup CRM Online jako uživatelé organizace*.

14. Na Dynamics CRM, přejděte na uživatelé a vyberte "Uživatelé povoleno" rozevírací seznam pro přepnutí na **uživatelů aplikace**.
    
    ![Uživatelé aplikace](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Vyberte **nový** k vytvoření nového uživatele. Vyberte **uživatelů: uživatel aplikace** rozevíracího seznamu.
    
    ![Přidání nového uživatele aplikace](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. V **nového uživatele**, zadejte název a e-mailu, kterou chcete použít k tomuto připojení. Vložte **Id aplikace** pro aplikace, které jste vytvořili na webu Azure Portal.
     ![Konfigurace nového uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Přejdete na "Nastavení zabezpečení" v tomto článku dokončete konfiguraci připojení pro tohoto uživatele.

### <a name="office-365"></a>Office 365

Pokud už nechcete používat Azure Active Directory, můžete se zaregistrovat nového uživatele na portálu pro správu Office 365. Bude třeba k aktualizaci vašeho uživatelského jména a hesla každých 90 dnech pokračovat v uplatňování potenciálních zákazníků.

Následujícím postupem nakonfigurujte Office 365 pro Dynamics CRM.

1. Přihlaste se k [portálu pro správu Microsoft Office 365](https://go.microsoft.com/fwlink/?LinkId=225975).

2. Vyberte **správce** dlaždici ![Office Online správce](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Vyberte **přidání uživatele**.

    ![Přidání uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Vytvoření nového uživatele pro službu zapisovač potenciálních zákazníků. Nakonfigurujte následující nastavení:

    -   Zadejte heslo a zrušte zaškrtnutí možnosti "Vytvořit tento uživatel heslo změnit při prvním přihlášení".
    -   Vyberte roli pro uživatele "Uživatel (bez přístupových práv správce)".
    -   Vyberte licenci na produkt je znázorněno v následující snímek obrazovky. Bude se účtovat licenci, kterou zvolíte. Řešení budou fungovat i pro Dynamics CRM Online licence Basic.
    ![Konfigurovat uživatelská oprávnění a licence](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Nastavení zabezpečení

Posledním krokem je povolit uživateli, který jste vytvořili pro zápis si potenciální zákazníci.

1.  Přihlaste se k Dynamics CRM online.
2.  Na **nastavení**vyberte **zabezpečení**.
    
    ![Nastavení zabezpečení](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Vyberte uživatele, který jste vytvořili v **uživatelská oprávnění**a pak vyberte **spravovat role uživatelů**. Zkontrolujte **zapisovače vést Microsoft Marketplace** přiřazení role.
    ![Přiřazení role uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Tato role se vytvoří řešení, že jste importovali a má pouze oprávnění k zápisu potenciální zákazníky a ke sledování verze řešení pro zajištění kompatibility.

4.  V oblasti zabezpečení, vyberte **role zabezpečení** a najít roli pro zapisovač vést Microsoft Marketplace.
    ![Konfigurace zabezpečení zájemce zapisovače](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Vyberte **základní záznamy** kartu. Povolte vytvoření, čtení a zápisu pro entitu uživatele uživatelského rozhraní.

    ![Povolit vytvoření a čtení/zápisu pro uživatele](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Zabalit

Dokončení konfigurace Dynamics CRM pro zájemce management tak, že přidáte informace generované účtu na portál Cloud Partner. Příklad:

-   **Azure Active Directory** - **Id aplikace** (Příklad: *23456052-aaaa-bbbb-8662-1234df56788f*), **Id adresáře** (Příklad: *12345678-8af1-4asf-1234-12234d01db47*), a **klíč aplikace** (Příklad: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =*).
-   **Office 365** - **Url** (Příklad: *https://contoso.crm4.dynamics.com*), **uživatelské jméno** (Příklad: *contoso\@ contoso.onmicrosoft.com*), a **heslo** (Příklad: *P\@ssw0rd*).
