---
title: Kurz – integrace jedné doménové struktury s jedním tenant Azure AD
description: V tomto tématu jsou popsány předpoklady a hardwarové požadavky na zřízení cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce5658fb79a893e0aca9d78faf090a886a2ee591
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651460"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Kurz: integrace jedné doménové struktury s jedním tenant Azure AD

Tento kurz vás provede vytvořením hybridního prostředí identity pomocí služby Azure Active Directory (Azure AD) připojení cloudového zřizování.

![Vytvořit](media/tutorial-single-forest/diagram1.png)

Prostředí, které vytvoříte v tomto kurzu, můžete použít pro testování nebo pro více znalostí s zřizováním cloudu.

## <a name="prerequisites"></a>Požadavky
### <a name="in-the-azure-active-directory-admin-center"></a>V centru pro správu Azure Active Directory

1. Vytvořte v tenantovi Azure AD jenom cloudový účet globálního správce. Tímto způsobem můžete spravovat konfiguraci vašeho tenanta, pokud vaše místní služby selžou nebo nebudou k dispozici. Seznamte [se s přidáním účtu globálního správce jenom pro Cloud](../fundamentals/add-users-azure-active-directory.md). Dokončení tohoto kroku je důležité, aby se zajistilo, že nebudete mít uzamčený přístup k vašemu tenantovi.
2. Přidejte jeden nebo více [vlastních názvů domén](../fundamentals/add-custom-domain.md) do svého tenanta služby Azure AD. Uživatelé se můžou přihlásit pomocí některého z těchto názvů domén.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikace hostitelského serveru připojeného k doméně, na kterém běží Windows Server 2012 R2 nebo vyšší, s minimálním počtem 4 GB paměti RAM a .NET 4.7.1 + runtime 

2. Pokud je mezi vašimi servery a službou Azure AD brána firewall, nakonfigurujte následující položky:
   - Zajistěte, aby agenti mohli vytvářet *odchozí* požadavky do služby Azure AD prostřednictvím následujících portů:

     | Číslo portu | Jak se používá |
     | --- | --- |
     | **80** | Stahuje seznamy odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL. |
     | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
     | **8082** | Vyžadováno pro instalaci|
     | **8080** (volitelné) | Agenti hlásí svůj stav každých 10 minut přes port 8080, pokud není k dispozici port 443. Tento stav se zobrazuje na portálu Azure AD. |
     
     Pokud brána firewall vynutila pravidla podle prvotních uživatelů, otevřete tyto porty pro provoz ze služeb systému Windows, které jsou spuštěny jako síťová služba.
   - Pokud vaše brána firewall nebo proxy server umožňují zadat bezpečné přípony, přidejte připojení t k **\* . msappproxy.NET** a **\* . ServiceBus.Windows.NET**. V takovém případě povolte přístup k [rozsahům IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují týdně.
   - Vaši agenti potřebují přístup k **Login.Windows.NET** a **Login.microsoftonline.com** pro počáteční registraci. Otevřete taky bránu firewall pro tyto adresy URL.
   - Pro ověření certifikátu Odblokujte následující adresy URL: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80** a **www \. Microsoft.com:80**. Vzhledem k tomu, že se tyto adresy URL používají pro ověřování certifikátů s jinými produkty Microsoftu, tyto adresy URL už možná máte odblokované.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalace agenta pro zřizování Azure AD Connect
1. Přihlaste se k serveru připojenému k doméně.  Pokud používáte základní kurz pro  [prostředí AD a Azure](tutorial-basic-ad-azure.md) , bude to DC1.
2. Přihlaste se k Azure Portal pomocí přihlašovacích údajů globálního správce jenom pro Cloud.
3. Na levé straně vyberte **Azure Active Directory**, klikněte na **Azure AD Connect** a ve středu vyberte **Spravovat zřizování (Preview)**.

   ![portál Azure](media/how-to-install/install-6.png)

4. Klikněte na **Stáhnout agenta**.
5. Spusťte agenta zřizování Azure AD Connect.
6. Na úvodní obrazovce **přijměte** licenční podmínky a klikněte na **nainstalovat**.

   ![Snímek obrazovky s úvodní obrazovkou balíčku pro zřizovacího agenta pro připojení Microsoft Azure](media/how-to-install/install-1.png)

7. Po dokončení této operace se spustí Průvodce konfigurací nástroje.  Přihlaste se pomocí účtu globálního správce služby Azure AD.  Všimněte si, že pokud máte povolené rozšířené zabezpečení aplikace Internet Explorer, bude přihlášení zablokované.  Pokud se jedná o tento případ, zavřete instalaci, zakažte v Správce serveru rozšířené zabezpečení IE a restartujte instalaci kliknutím na **Průvodce agentem zřizování AAD Connect** .
8. Na obrazovce **připojit ke službě Active Directory** klikněte na **Přidat adresář** a pak se přihlaste pomocí účtu správce domény služby Active Directory.  Poznámka: účet správce domény by neměl mít požadavky na změnu hesla. V případě vypršení platnosti nebo změny hesla budete muset agenta znovu nakonfigurovat s novými přihlašovacími údaji. Tato operace přidá váš místní adresář.  Klikněte na **Next** (Další).

   ![Snímek obrazovky připojit ke službě Active Directory](media/how-to-install/install-3.png)

9. Na obrazovce **Konfigurace byla dokončena** klikněte na **Potvrdit**.  Tato operace provede registraci a restart agenta.

   ![Snímek obrazovky zobrazující obrazovku "konfigurace dokončena".](media/how-to-install/install-4a.png)

10. Po dokončení této operace by se měla zobrazit Poznámka: **vaše konfigurace agenta byla úspěšně ověřena.**  Můžete kliknout na tlačítko **konec**.</br>
![Obrazovka Vítejte](media/how-to-install/install-5.png)</br>
11. Pokud se stále zobrazuje úvodní úvodní obrazovka, klikněte na **Zavřít**.


## <a name="verify-agent-installation"></a>Ověřit instalaci agenta
K ověření agenta dochází v Azure Portal a na místním serveru, na kterém je spuštěný agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal
Pokud chcete ověřit, že se agent zobrazuje v Azure, postupujte podle těchto kroků:

1. Přihlaste se k webu Azure Portal.
2. Na levé straně vyberte **Azure Active Directory**, klikněte na **Azure AD Connect** a ve středu vyberte **Spravovat zřizování (Preview)**.</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  Na obrazovce **zřizování Azure AD (Preview)** klikněte na **zkontrolovat všechny agenty**.
![Zřizování Azure AD](media/how-to-install/install-7.png)</br>
 
4. Na **obrazovce místní zřizovací agenti** se zobrazí agenti, které jste nainstalovali.  Ověřte, zda je příslušný agent a označen jako **aktivní**.
![Zřizování agentů](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Na místním serveru
Pokud chcete ověřit, jestli je agent spuštěný, postupujte takto:

1.  Přihlaste se k serveru pomocí účtu správce.
2.  Otevřete **služby** tak, že k němu přejdete nebo přejdete na Start/Run/Services. msc.
3.  V části **služby** se ujistěte, že je k dispozici aktualizace **agenta Microsoft Azure AD connect** a **Agent Microsoft Azure AD Connect zřizování** a stav je **spuštěno**.
![Služby](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurace zřizování cloudu Azure AD Connect
 Při konfiguraci zřizování použijte následující postup.

1.  Přihlaste se k portálu Azure AD.
2.  Klikněte na **Azure Active Directory**
3.  Klikněte na **Azure AD Connect**
4.  Vyberte možnost **Spravovat zřizování (Preview)** 
 ![ snímek obrazovky s odkazem spravovat zřizování (Preview).](media/how-to-configure/manage1.png)
5.  Klikněte na **Nový** 
 ![ snímek obrazovky konfigurace na obrazovce zřizování služby Azure AD (Preview) s zvýrazněným odkazem nová konfigurace.](media/tutorial-single-forest/configure1.png)
7.  Na obrazovce konfigurace zadejte **e-mailové oznámení**, přesuňte selektor, který chcete **Povolit** , a klikněte na **Uložit**.
![Obrazovka Konfigurace obrazovky s vyplněným e-mailem oznámení a vybraným povolením](media/how-to-configure/configure2.png)
1.  Stav konfigurace by teď měl být **v pořádku**.
![Obrazovka obrazovky zřizování služby Azure AD (Preview) zobrazující stav v pořádku](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Ověřte, že se vytvářejí uživatelé a dochází k synchronizaci.
Nyní ověříte, že uživatelé, kteří byli v místním adresáři, byli synchronizováni a nyní existují v našem tenantovi služby Azure AD.  Mějte na paměti, že dokončení tohoto může trvat několik hodin.  Chcete-li ověřit, zda jsou uživatelé synchronizováni, postupujte následovně.


1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory**
3. V části **Spravovat** vyberte **Uživatelé**.
4. Ověřte, že se v našem tenantovi zobrazují noví uživatelé.</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Vyzkoušejte si přihlašování jedním z našich uživatelů

1. Přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Přihlaste se pomocí uživatelského účtu vytvořeného v našem novém tenantovi.  Budete se muset přihlásit pomocí následujícího formátu: ( user@domain.onmicrosoft.com ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.</br>
   ![Ověření](media/tutorial-single-forest/verify-1.png)</br>

Nyní jste úspěšně nastavili hybridní prostředí identity, které můžete použít k otestování a seznámení s tím, co Azure nabízí.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)