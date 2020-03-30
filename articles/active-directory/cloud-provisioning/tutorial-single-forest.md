---
title: Kurz – integrace jedné doménové struktury s jedním klientem Azure AD
description: Toto téma popisuje předpoklady a hardwarové požadavky cloud zřizování.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dab553a93db4650a5d7126d7f1a0c3ca5f808f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332240"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Kurz: Integrace jedné doménové struktury s jedním klientem Azure AD

Tento kurz vás provede vytvořením prostředí hybridní identity pomocí Azure Active Directory (Azure AD) Connect zřizování cloudu.

![Vytvořit](media/tutorial-single-forest/diagram1.png)

Prostředí, které vytvoříte v tomto kurzu, můžete použít pro testování nebo pro seznámení s zřizováním cloudu.

## <a name="prerequisites"></a>Požadavky
### <a name="in-the-azure-active-directory-admin-center"></a>V Centru pro správu Služby Azure Active Directory

1. Vytvořte účet globálního správce pouze pro cloud v tenantovi Azure AD. Tímto způsobem můžete spravovat konfiguraci vašeho tenanta, pokud vaše místní služby selžou nebo se stanou nedostupnými. Přečtěte si [o přidání účtu globálního správce pouze pro cloud](../active-directory-users-create-azure-portal.md). Dokončení tohoto kroku je důležité zajistit, že se nedostanete uzamčenz vašeho tenanta.
2. Přidejte do klienta Azure AD jeden nebo více [vlastních názvů domén.](../active-directory-domains-add-azure-portal.md) Uživatelé se mohou přihlásit pomocí jednoho z těchto názvů domén.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikace hostitelského serveru přilehlého k doméně se systémem Windows Server 2012 R2 nebo vyšším s minimálně 4 GB paměti RAM a za běhu .NET 4.7.1+ 

2. Pokud je brána firewall mezi servery a Azure AD, nakonfigurujte následující položky:
   - Ujistěte se, že agenti mohou provádět *odchozí* požadavky na Azure AD přes následující porty:

     | Číslo portu | Jak se používá |
     | --- | --- |
     | **80** | Stáhne seznamy odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL. |
     | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
     | **8080** (volitelně) | Agenti hlásí svůj stav každých 10 minut přes port 8080, pokud port 443 není k dispozici. Tento stav se zobrazí na portálu Azure AD. |
     
     Pokud brána firewall vynucuje pravidla podle původních uživatelů, otevřete tyto porty pro přenosy ze služeb systému Windows, které běží jako síťová služba.
   - Pokud brána firewall nebo proxy server umožňuje zadat bezpečné přípony, přidejte připojení t do ** \*.msappproxy.net** a ** \*.servicebus.windows.net**. Pokud ne, povolte přístup k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují každý týden.
   - Vaši agenti potřebují přístup k **login.windows.net** a **login.microsoftonline.com** pro počáteční registraci. Otevřete také bránu firewall pro tyto adresy URL.
   - Pro ověření certifikátu odblokujte následující adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**a **www\.microsoft.com:80**. Vzhledem k tomu, že tyto adresy URL se používají pro ověření certifikátu s jinými produkty společnosti Microsoft, je možné, že tyto adresy URL již byly odblokovány.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalace agenta zřizování Azure AD Connect
1. Přihlaste se k serveru připojovat k doméně.  Pokud používáte [základní a disponujea a azure prostředí](tutorial-basic-ad-azure.md) kurz, bude DC1.
2. Přihlaste se k portálu Azure pomocí přihlašovacích údajů globálního správce pouze pro cloud.
3. Na levé straně vyberte **Azure Active Directory**, klikněte na Azure **AD Connect**a na střed vyberte Spravovat **zřizování (preview).**

   ![portál Azure](media/how-to-install/install6.png)

4. Klepněte **na položku Zprostředkovatel stahování**.
5. Spusťte agenta zřizování Azure AD Connect.
6. Na úvodní obrazovce **přijměte** licenční podmínky a klepněte na tlačítko **Instalovat**.

   ![Obrazovka Vítejte](media/how-to-install/install1.png)

7. Po dokončení této operace se spustí průvodce konfigurací.  Přihlaste se pomocí účtu globálního správce Azure AD.  Všimněte si, že pokud máte iE rozšířené zabezpečení povoleno to bude blokovat přihlášení.  V takovém případě zavřete instalaci, zakažte rozšířené zabezpečení aplikace IE ve Správci serveru a klepnutím na **Průvodce zřízením služby AAD Connect** restartujte instalaci.
8. Na obrazovce **Připojit službu Active Directory** klikněte na Přidat **adresář** a přihlaste se pomocí účtu správce domény služby Active Directory.  Poznámka: Účet správce domény by neměl mít požadavky na změnu hesla. V případě, že platnost hesla vyprší nebo se změní, budete muset znovu nakonfigurovat agenta pomocí nových pověření. Tato operace přidá místní adresář.  Klikněte na **Další**.

   ![Obrazovka Vítejte](media/how-to-install/install3.png)

9. Na obrazovce **Dokončení konfigurace** klepněte na **tlačítko Potvrdit**.  Tato operace zaregistruje a restartuje agenta.

   ![Obrazovka Vítejte](media/how-to-install/install4.png)

10. Po dokončení této operace byste měli vidět upozornění: **Konfigurace agenta byla úspěšně ověřena.**  Klepněte na tlačítko **Ukončit**.</br>
![Obrazovka Vítejte](media/how-to-install/install5.png)</br>
11. Pokud se stále zobrazí úvodní úvodní obrazovka, klepněte na **tlačítko Zavřít**.


## <a name="verify-agent-installation"></a>Ověření instalace agenta
K ověření agenta dochází na portálu Azure a na místním serveru, na který agenta běží.

### <a name="azure-portal-agent-verification"></a>Ověření agenta portálu Azure
Chcete-li ověřit agenta se zoažená v Azure postupujte takto:

1. Přihlaste se k portálu Azure.
2. Na levé straně vyberte **Azure Active Directory**, klikněte na Azure **AD Connect** a na střed vyberte Spravovat **zřizování (preview).**</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na obrazovce **Azure AD Provisioning (preview)** klikněte na **Zkontrolovat všechny agenty**.
![Zřizování Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **obrazovce Místní zřizovací agenti** uvidíte agenty, které jste nainstalovali.  Ověřte, zda je dotyčný agent k dispozici a zda je označen **jako aktivní**.
![Zřizovací agenti](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Na místním serveru
Chcete-li ověřit, zda je agent spuštěn, postupujte takto:

1.  Přihlášení k serveru pomocí účtu správce
2.  Spusťte **služby** buď navigací na něj, nebo přechodem na Start/Run/Services.msc.
3.  V části **Služby**zkontrolujte, jestli jsou k dispozici **zprostředkovatel i updater agenta Microsoft Azure AD Connect** a agent aprovoz zabezpečení microsoft azure a **služby AD Connect** a stav je **spuštěný**.
![Služby](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurace zřizování cloudu Azure AD Connect
 Ke konfiguraci zřizování použijte následující kroky

1.  Přihlaste se k portálu Azure AD.
2.  Klikněte na **Službu Active Directory služby Azure.**
3.  Klikněte na **Azure AD Connect**
4.  Vyberte **Spravovat zřizování (náhled)**
![](media/how-to-configure/manage1.png)
5.  Klikněte na **Nová konfigurace.**
![](media/tutorial-single-forest/configure1.png)
7.  Na konfigurační obrazovce zadejte **e-mail s oznámením**, přesuňte volič na **Povolit** a klepněte na **tlačítko Uložit**.
![](media/tutorial-single-forest/configure2.png)
1.  Stav konfigurace by nyní měl být **v pořádku**.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Ověřte, zda jsou uživatelé vytvořeni a dochází k synchronizaci.
Teď ověříte, že uživatelé, které jste měli v našem místním adresáři, byli synchronizováni a teď existují v našem tenantovi Azure AD.  Uvědomte si, že to může trvat několik hodin.  Chcete-li ověřit, zda jsou uživatelé synchronizováni, postupujte takto.


1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory.**
3. V části **Spravovat** vyberte **Uživatele**.
4. Ověřte, zda se v našem tenantovi zobrazují noví uživatelé.</br>
![Synchronizace](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Otestujte přihlášení s jedním z našich uživatelů

1. Přejít na[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našem novém tenantovi.  Budete se muset přihlásit v následujícímuser@domain.onmicrosoft.comformátu: ( ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.</br>
   ![Ověřit](media/tutorial-single-forest/verify1.png)</br>

Nyní jste úspěšně nastavili prostředí hybridní identity, které můžete použít k testování a seznámení s tím, co Azure nabízí.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
