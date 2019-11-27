---
title: Nasazení ochrany heslem Azure AD – Azure Active Directory
description: Nasazení ochrany heslem Azure AD k zákazu špatných hesel v místním prostředí
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e1d53d63b40ad62a4d21cbad22a67e9e9781b1f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381715"
---
# <a name="deploy-azure-ad-password-protection"></a>Nasazení ochrany hesel Azure AD

Teď, když jste se seznámili [s tím, jak vynutili ochranu heslem Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md), je dalším krokem plánování a provádění nasazení.

## <a name="deployment-strategy"></a>Strategie nasazení

Doporučujeme spustit nasazení v režimu auditování. Režim auditu je výchozí počáteční nastavení, ve kterém můžou být hesla nadále nastavená. Hesla, která by byla zablokovaná, se zaznamenávají v protokolu událostí. Po nasazení proxy serverů a agentů řadiče domény v režimu auditování byste měli monitorovat dopad, který budou zásady hesel používat pro uživatele a prostředí při vykonání zásad.

V rámci fáze auditu mnoho organizací zjistí, že:

* Potřebují zlepšit stávající provozní procesy, aby používaly bezpečnější hesla.
* Uživatelé často používají nezabezpečená hesla.
* Potřebují uživatele informovat o nadcházející změně v vynucování zabezpečení, o možných dopadech na ně a o tom, jak zvolit bezpečnější hesla.

Je taky možné, že se silnější ověřování hesla bude týkat stávající automatizace nasazení řadiče domény služby Active Directory. Doporučujeme, abyste při vyhodnocení období auditů nedocházelo k navýšení aspoň jedné povýšení řadiče domény a k jednomu řadiči domény, aby bylo možné tyto problémy předem odhalit.  Další informace naleznete v tématu:

* [Nástroj Ntdsutil. exe nemůže nastavit slabý stav hesla režimu opravy adresářových služeb.](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Povýšení repliky řadiče domény se nepovedlo kvůli slabému heslu režimu opravy adresářových služeb.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Degradování řadiče domény se nezdařilo kvůli slabému heslu místního správce.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Po rozumnou dobu funguje v režimu auditu v režimu auditování, a pokud chcete *Vynutit* , aby se vyžadovalo bezpečnější heslo, můžete přepnout konfiguraci z *auditu* . Cílené monitorování během této doby je dobrý nápad.

## <a name="deployment-requirements"></a>Požadavky na nasazení

* Licenční požadavky pro ochranu heslem služby Azure AD najdete v článku [odstranění chybných hesel ve vaší organizaci](concept-password-ban-bad.md#license-requirements).
* Všechny počítače, ve kterých bude nainstalován software agenta řadiče domény Azure AD pro ochranu heslem, musí používat systém Windows Server 2012 nebo novější. Tento požadavek neznamená, že doména nebo doménová struktura služby Active Directory musí být také na úrovni funkčnosti domény nebo doménové struktury systému Windows Server 2012. Jak je uvedeno v [zásadách návrhu](concept-password-ban-bad-on-premises.md#design-principles), není k dispozici žádný minimální úrovni funkčnosti domény ani FFL nutný pro spuštění agenta řadiče domény ani proxy serveru.
* Všechny počítače, které mají nainstalovanou službu agenta řadiče domény, musí mít nainstalované rozhraní .NET 4,5.
* Všechny počítače, ve kterých se služba proxy ochrany heslem Azure AD bude instalovat, musí používat Windows Server 2012 R2 nebo novější.
   > [!NOTE]
   > Nasazení proxy služby je povinný požadavek na nasazení ochrany heslem služby Azure AD, i když řadič domény může mít odchozí přímé připojení k Internetu. 
   >
* Všechny počítače, ve kterých se služba proxy ochrany heslem Azure AD bude instalovat, musí mít nainstalované rozhraní .NET 4,7.
  Rozhraní .NET 4,7 by již mělo být nainstalováno na plně aktualizovaný systém Windows Server. V případě potřeby si stáhněte a spusťte instalační program, který najdete v [instalačním programu .NET Framework 4,7 offline pro Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Všechny počítače, včetně řadičů domény, které mají nainstalované komponenty ochrany hesel služby Azure AD, musí mít nainstalovaný modul Universal C Runtime. Modul runtime můžete získat tak, že zajistíte, že máte všechny aktualizace z web Windows Update. Nebo ho můžete získat v balíčku aktualizací specifických pro konkrétní operační systém. Další informace najdete v tématu [aktualizace pro Universal C Runtime v systému Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Mezi aspoň jedním řadičem domény v každé doméně a aspoň jedním serverem, který hostuje proxy službu pro ochranu heslem, musí existovat síťové připojení. Toto připojení musí řadiči domény dovolit přístup k portu mapovače koncových bodů RPC 135 a portu serveru RPC na proxy službě. Ve výchozím nastavení je port serveru RPC dynamickým portem RPC, ale je možné ho nakonfigurovat tak, aby [používal statický port](#static).
* Všechny počítače, ve kterých bude nainstalovaná služba proxy ochrany heslem Azure AD, musí mít síťový přístup k následujícím koncovým bodům:

    |**Služba**|**Účel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Žádosti o ověření|
    |`https://enterpriseregistration.windows.net`|Funkce ochrany heslem Azure AD|
 
* Požadavky aktualizace agenta Microsoft Azure AD Connect

  Služba aktualizace agenta Microsoft Azure AD Connect je nainstalovaná souběžně se službou proxy ochrany heslem Azure AD. Aby služba aktualizace agenta Microsoft Azure AD Connect mohla fungovat, je potřeba dodatečnou konfiguraci:

  Pokud vaše prostředí používá proxy server http, musíte postupovat podle pokynů uvedených v části [práce se stávajícími místními proxy servery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

  Služba aktualizace agenta Microsoft Azure AD Connect vyžaduje také kroky TLS 1,2 uvedené v části [požadavky TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

  Pro sadu portů a adres URL, které jsou zadané v [postupech nastavení prostředí proxy aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment), musí být povolený přístup k síti.

  > [!WARNING]
  > Proxy ochrana heslem a proxy aplikací služby Azure AD instalují různé verze služby Microsoft Azure AD Connect Agent pro aktualizace, což je důvod, proč se pokyny odkazují na obsah proxy aplikací. Tyto různé verze jsou při instalaci vedle sebe nekompatibilní, takže se nedoporučuje instalovat proxy a aplikační proxy aplikace služby Azure AD, a to po straně stejného počítače.

* Všechny počítače, které hostují proxy službu pro ochranu heslem, musí být nakonfigurované tak, aby řadičům domény udělily možnost přihlásit se k proxy službě. Tato možnost se ovládá přes přiřazení oprávnění "přístup k tomuto počítači ze sítě".
* Všechny počítače, které hostují proxy službu pro ochranu heslem, musí být nakonfigurované tak, aby umožňovaly odchozí přenosy TLS 1,2 HTTP.
* Účet globálního správce pro registraci proxy služby pro ochranu heslem a doménovou strukturu s Azure AD.
* Účet, který má oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury k registraci doménové struktury služby Active Directory Windows serveru v Azure AD.
* Doména služby Active Directory, která spouští software agenta řadiče domény, musí používat replikaci systém souborů DFS (Distributed File System) (DFSR) pro replikaci adresáře SYSVOL.

  Pokud vaše doména již nepoužívá službu DFSR, je nutné ji před instalací ochrany hesel služby Azure AD migrovat na používání služby DFSR. Další informace najdete na následujícím odkazu:

  [Průvodce migrací replikace SYSVOL: replikace do Replikace DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Software agenta řadiče domény Azure AD pro ochranu heslem se v tuto chvíli nainstaluje na řadičích domény v doménách, které pořád využívají službu replikace souborů (DFSR) pro replikaci SYSVOL, ale software v tomto prostředí nebude správně fungovat. Mezi další negativní vedlejší účinky patří neúspěšná replikace jednotlivých souborů a procedury obnovení adresáře SYSVOL, které jsou úspěšné, ale tiché selhání při replikaci všech souborů. Vaši doménu byste měli migrovat tak, aby používala službu DFSR co nejdříve, a to jak z hlediska výhod, tak i z důvodu odblokování nasazení ochrany heslem služby Azure AD. Budoucí verze softwaru budou automaticky zakázány při spuštění v doméně, která stále používá službu replikace souborů (FRS).

* Služba distribuce klíčů musí být povolená na všech řadičích domény v doméně, která běží na Windows Serveru 2012. Ve výchozím nastavení je tato služba povolená přes ruční spuštění aktivační události.

## <a name="single-forest-deployment"></a>Nasazení s jednou doménovou strukturou

Následující diagram znázorňuje, jak se základní komponenty ochrany heslem Azure AD vzájemně spolupracují v místním prostředí Active Directory.

![Jak spolupracují komponenty ochrany hesel Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Je dobré si před nasazením zkontrolovat, jak software funguje. Podívejte se [na koncepční přehled ochrany heslem Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Stáhnout software

Pro ochranu heslem Azure AD existují dvě požadované instalační programy. Jsou k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalace a konfigurace proxy služby pro ochranu heslem

1. Vyberte jeden nebo více serverů, které budou hostovat proxy službu pro ochranu heslem.
   * Každá taková služba může poskytovat jenom zásady pro hesla pro jednu doménovou strukturu. Hostitelský počítač musí být připojený k doméně v této doménové struktuře. Jsou podporovány obě kořenové a podřízené domény. Potřebujete připojení k síti mezi alespoň jedním ŘADIČEm domény v každé doméně doménové struktury a počítačem ochrany heslem.
   * Pro účely testování můžete spustit službu proxy serveru na řadiči domény. Tento řadič domény ale pak vyžaduje připojení k Internetu, což se může týkat zabezpečení. Doporučujeme tuto konfiguraci pouze pro testování.
   * Pro zajištění redundance doporučujeme aspoň dva proxy servery. Podívejte se na [vysokou dostupnost](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Nainstalujte službu proxy ochrany heslem Azure AD pomocí instalačního programu `AzureADPasswordProtectionProxySetup.exe` softwaru.
   * Instalace softwaru nevyžaduje restart. Instalaci softwaru je možné automatizovat pomocí standardních procedur MSI, například:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Před instalací balíčku AzureADPasswordProtectionProxySetup. msi musí být spuštěná služba brány Windows Firewall, aby nedošlo k chybě při instalaci. Pokud je konfigurace brány Windows Firewall nakonfigurovaná tak, aby se nespouštěla, je alternativním řešením dočasné povolení a spuštění služby brány firewall během instalace. Po instalaci nemá software proxy žádnou konkrétní závislost na bráně Windows Firewall. Pokud používáte bránu firewall jiného výrobce, musí být stále nakonfigurovaná tak, aby splňovala požadavky na nasazení. Mezi ně patří povolení příchozího přístupu k portu 135 a port serveru proxy RPC. Viz [požadavky na nasazení](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Otevřete okno PowerShellu jako správce.
   * Software proxy ochrany heslem obsahuje nový modul PowerShellu, *AzureADPasswordProtection*. V následujících krocích spustíte z tohoto modulu PowerShellu různé rutiny. Importujte nový modul následujícím způsobem:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Chcete-li ověřit, zda je služba spuštěna, použijte následující příkaz prostředí PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Výsledek by měl zobrazovat **stav** "spuštěno".

1. Zaregistrujte proxy server.
   * Po dokončení kroku 3 se na počítači spustí proxy služba. Služba ale ještě nemá potřebná pověření ke komunikaci se službou Azure AD. Vyžaduje se registrace ve službě Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     Tato rutina vyžaduje pro vašeho tenanta Azure přihlašovací údaje globálního správce. V kořenové doméně doménové struktury budete také potřebovat místní oprávnění správce domény služby Active Directory. Po úspěšném provedení tohoto příkazu u proxy služby bude další vyvolání úspěšné, ale budou zbytečné.

      Rutina `Register-AzureADPasswordProtectionProxy` podporuje následující tři režimy ověřování. První dva režimy podporují Azure Multi-Factor Authentication ale třetí režim ne. Další podrobnosti najdete níže v komentářích.

     * Režim interaktivního ověřování:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Tento režim nefunguje na operačních systémech jádra serveru. Místo toho použijte jeden z následujících režimů ověřování. Tento režim může také selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat tuto konfiguraci, zaregistrovat proxy server a pak ho znovu povolit.

     * Režim ověřování kódu zařízení:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Pak dokončete ověření podle zobrazených pokynů na jiném zařízení.

     * Režim ověřování v tichém režimu (založený na hesle):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdařil, pokud pro váš účet potřebujete Azure Multi-Factor Authentication. V takovém případě použijte jeden z předchozích dvou režimů ověřování nebo místo toho použijte jiný účet, který nevyžaduje MFA.
        >
        > Je také možné, že se vyžaduje ověřování MFA, pokud je registrace zařízení Azure (která se používá v rámci zabezpečení Azure AD heslem) nakonfigurovaná tak, aby globálně vyžadovala MFA. K řešení tohoto problému můžete použít jiný účet, který podporuje VÍCEFAKTOROVÉ ověřování v jednom z předchozích dvou režimů ověřování, nebo můžete také dočasně uvolnit požadavek MFA pro registraci zařízení Azure. Provedete to tak, že přejdete na portál pro správu Azure, přejdete na Azure Active Directory, pak na zařízení a pak na nastavení zařízení a pak nastavíte "vyžadovat vícefaktorové ověřování pro připojení zařízení" na ne. Nezapomeňte znovu nakonfigurovat toto nastavení zpět na Ano, jakmile se registrace dokončí.
        >
        > Pro účely testování doporučujeme vynechat požadavky na vícefaktorové ověřování.

       V tuto chvíli nemusíte zadávat parametr *-ForestCredential* , který je vyhrazený pro budoucí funkce.

   Registrace proxy služby pro ochranu heslem je nutná jenom jednou za dobu života služby. Po této operaci bude proxy služba automaticky provádět jakoukoli další potřebnou údržbu.

   > [!TIP]
   > Před dokončením této rutiny pro konkrétního tenanta Azure může nastat znatelné zpoždění. Pokud není nahlášená chyba, nedělejte si starosti s touto prodlevou.

1. Zaregistrujte doménovou strukturu.
   * Místní doménovou strukturu služby Active Directory musíte inicializovat pomocí nezbytných přihlašovacích údajů ke komunikaci s Azure pomocí rutiny `Register-AzureADPasswordProtectionForest` PowerShellu. Rutina vyžaduje pro vašeho tenanta Azure přihlašovací údaje globálního správce. Vyžaduje taky místní oprávnění podnikového Správce služby Active Directory. Tento krok se spouští jednou pro každou doménovou strukturu.

      Rutina `Register-AzureADPasswordProtectionForest` podporuje následující tři režimy ověřování. První dva režimy podporují Azure Multi-Factor Authentication ale třetí režim ne. Další podrobnosti najdete níže v komentářích.

     * Režim interaktivního ověřování:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Tento režim nebude fungovat na operačních systémech jádra serveru. Místo toho použijte jeden z následujících dvou režimů ověřování. Tento režim může také selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat tuto konfiguraci, zaregistrovat doménovou strukturu a pak ji znovu povolit.  

     * Režim ověřování kódu zařízení:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Pak dokončete ověření podle zobrazených pokynů na jiném zařízení.

     * Režim ověřování v tichém režimu (založený na hesle):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdařil, pokud pro váš účet potřebujete Azure Multi-Factor Authentication. V takovém případě použijte jeden z předchozích dvou režimů ověřování nebo místo toho použijte jiný účet, který nevyžaduje MFA.
        >
        > Je také možné, že se vyžaduje ověřování MFA, pokud je registrace zařízení Azure (která se používá v rámci zabezpečení Azure AD heslem) nakonfigurovaná tak, aby globálně vyžadovala MFA. K řešení tohoto problému můžete použít jiný účet, který podporuje VÍCEFAKTOROVÉ ověřování v jednom z předchozích dvou režimů ověřování, nebo můžete také dočasně uvolnit požadavek MFA pro registraci zařízení Azure. Provedete to tak, že přejdete na portál pro správu Azure, přejdete na Azure Active Directory, pak na zařízení a pak na nastavení zařízení a pak nastavíte "vyžadovat vícefaktorové ověřování pro připojení zařízení" na ne. Nezapomeňte znovu nakonfigurovat toto nastavení zpět na Ano, jakmile se registrace dokončí.
        >
        > Pro účely testování doporučujeme vynechat požadavky na vícefaktorové ověřování.

       Tyto příklady jsou úspěšné pouze v případě, že aktuálně přihlášený uživatel je zároveň správcem domény služby Active Directory pro kořenovou doménu. V takovém případě můžete alternativní přihlašovací údaje domény zadat pomocí parametru *-ForestCredential* .

   > [!NOTE]
   > Pokud je ve vašem prostředí nainstalováno více proxy serverů, nezáleží na tom, který proxy server použít k registraci doménové struktury.
   >
   > [!TIP]
   > Před dokončením této rutiny pro konkrétního tenanta Azure může nastat znatelné zpoždění. Pokud není nahlášená chyba, nedělejte si starosti s touto prodlevou.

   Registrace doménové struktury služby Active Directory je pro celou dobu života doménové struktury nutná jenom jednou. Potom budou agenti řadiče domény v doménové struktuře automaticky provádět všechny další nezbytné údržby. Po úspěšném spuštění `Register-AzureADPasswordProtectionForest` pro doménovou strukturu se další vyvolání rutiny zdařila, ale jsou zbytečná.

   Aby `Register-AzureADPasswordProtectionForest` bylo úspěšné, musí být v doméně proxy server k dispozici alespoň jeden řadič domény se systémem Windows Server 2012 nebo novějším. Software agenta DC není nutné instalovat na žádné řadiče domény před tímto krokem.

1. Nakonfigurujte proxy službu pro ochranu heslem pro komunikaci prostřednictvím proxy serveru HTTP.

   Pokud vaše prostředí vyžaduje použití konkrétního proxy serveru HTTP ke komunikaci s Azure, použijte tuto metodu: Vytvořte soubor *AzureADPasswordProtectionProxy. exe. config* ve složce%ProgramFiles%\Azure AD Password Protection Proxy\Service. Zahrnout následující obsah:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Pokud váš proxy server HTTP vyžaduje ověření, přidejte značku *UseDefaultCredentials* :

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   V obou případech nahraďte `http://yourhttpproxy.com:8080` adresou a portem konkrétní proxy server HTTP.

   Pokud je váš proxy server HTTP nakonfigurovaný tak, aby používal zásady autorizace, musíte udělit přístup k účtu počítače služby Active Directory počítače, který je hostitelem služby proxy, pro ochranu heslem.

   Po vytvoření nebo aktualizaci souboru *AzureADPasswordProtectionProxy. exe. config* Doporučujeme zastavit a restartovat službu proxy serveru.

   Proxy služba nepodporuje použití konkrétních přihlašovacích údajů pro připojení k proxy serveru HTTP.

1. Volitelné: Nakonfigurujte proxy službu, aby ochrana heslem mohla naslouchat na konkrétním portu.
   * Software agenta DC pro ochranu heslem na řadičích domény používá k komunikaci s proxy službou protokol RPC přes protokol TCP. Ve výchozím nastavení služba proxy naslouchá na jakémkoli dostupném dynamickém koncovém bodu RPC. Službu můžete ale nakonfigurovat tak, aby naslouchala na určitém portu TCP, pokud je to nezbytné z důvodu síťové topologie nebo požadavků na bránu firewall ve vašem prostředí.
      * <a id="static" /></a>ke konfiguraci spuštění služby pod statickým portem, použijte rutinu `Set-AzureADPasswordProtectionProxyConfiguration`.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Tyto změny se projeví až po zastavení a restartování služby.

      * Pokud chcete službu nakonfigurovat tak, aby běžela pod dynamickým portem, použijte stejný postup, ale nastavte *StaticPort* zpět na nula:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Tyto změny se projeví až po zastavení a restartování služby.

   > [!NOTE]
   > Proxy služba pro ochranu heslem vyžaduje ruční restartování po každé změně konfigurace portu. Po provedení těchto změn konfigurace ale nemusíte restartovat software služby agenta DC na řadičích domény.

   * K dotazování na aktuální konfiguraci služby použijte rutinu `Get-AzureADPasswordProtectionProxyConfiguration`:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instalace služby agenta řadiče domény

   Pomocí balíčku `AzureADPasswordProtectionDCAgentSetup.msi` nainstalujte do ochrany heslem službu agenta řadiče domény.

   Instalace nebo odinstalace softwaru vyžaduje restart. Důvodem je to, že se knihovny DLL filtru hesel načítají nebo odhrávají jenom po restartování.

   Službu agenta DC můžete nainstalovat na počítač, který ještě není řadičem domény. V takovém případě se služba spustí a spustí, ale zůstane neaktivní, dokud nebude počítač povýšen na řadič domény.

   Instalaci softwaru můžete automatizovat pomocí standardních postupů MSI. Příklad:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Příznak `/norestart` můžete vynechat, pokud chcete, aby instalační program automaticky restartoval počítač.

Instalace se dokončí po instalaci softwaru agenta DC na řadič domény a tento počítač se restartuje. Žádná jiná konfigurace není vyžadována nebo možná.

## <a name="upgrading-the-proxy-agent"></a>Upgrade agenta proxy

Pokud je k dispozici novější verze softwaru proxy ochrany heslem Azure AD, upgrade se provádí spuštěním nejnovější verze instalačního programu `AzureADPasswordProtectionProxySetup.exe` softwaru. Nejnovější verzi softwaru najdete na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

Není nutný k odinstalaci aktuální verze softwaru proxy – instalační program provede místní upgrade. Při upgradu softwaru proxy by se neměl vyžadovat restart. Upgrade softwaru může být automatizovaný pomocí standardních procedur MSI, například: `AzureADPasswordProtectionProxySetup.exe /quiet`.

Agent proxy podporuje automatický upgrade. Automatický upgrade používá službu Microsoft Azure AD Connect agent aktualizační službu, která je nainstalovaná souběžně se službou proxy serveru. Automatický upgrade je ve výchozím nastavení zapnutý a může být povolený nebo zakázaný pomocí rutiny `Set-AzureADPasswordProtectionProxyConfiguration`. Pomocí rutiny `Get-AzureADPasswordProtectionProxyConfiguration` se dá zadat dotaz na aktuální nastavení. Microsoft doporučuje, aby bylo nastavení automatického upgradu vždy povolené.

Rutina `Get-AzureADPasswordProtectionProxy` se dá použít k dotazování verze softwaru všech aktuálně nainstalovaných agentů proxy v doménové struktuře.

## <a name="upgrading-the-dc-agent"></a>Upgrade agenta řadiče domény

Pokud je k dispozici novější verze softwaru agenta Azure AD Password Protection, upgrade se provádí spuštěním nejnovější verze `AzureADPasswordProtectionDCAgentSetup.msi` softwarového balíčku. Nejnovější verzi softwaru najdete na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

Pro odinstalaci aktuální verze softwaru agenta DC není nutné, aby instalační program provedl místní upgrade. Při upgradu softwaru agenta DC se vždy vyžaduje restart – tento požadavek způsobuje základní chování Windows. 

Upgrade softwaru může být automatizovaný pomocí standardních procedur MSI, například: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Příznak `/norestart` můžete vynechat, pokud chcete, aby instalační program automaticky restartoval počítač.

Rutina `Get-AzureADPasswordProtectionDCAgent` se dá použít k dotazování verze softwaru všech aktuálně nainstalovaných agentů DC v doménové struktuře.

## <a name="multiple-forest-deployments"></a>Nasazení s více doménovými strukturami

Neexistují žádné další požadavky na nasazení ochrany heslem Azure AD napříč více doménovými strukturami. Každá doménová struktura je nezávislá na konfiguraci, jak je popsáno v části nasazení s jednou doménovou strukturou. Každý proxy server ochrany heslem může podporovat jenom řadiče domény z doménové struktury, ke které je připojený. Software ochrany heslem v jakékoli doménové struktuře neznáte software ochrany heslem, který je nasazený v jiných doménových strukturách, bez ohledu na konfiguraci důvěryhodnosti služby Active Directory.

## <a name="read-only-domain-controllers"></a>Řadiče domény jen pro čtení

Změny nebo sady hesel nejsou zpracovány a uchovány na řadičích domény jen pro čtení (RODC). Předávají se do řadičů domény s možností zápisu. Nemusíte tedy instalovat software agenta DC na řadič domény jen pro čtení.

## <a name="high-availability"></a>Vysoká dostupnost

Hlavním problémem při dostupnosti ochrany heslem je dostupnost proxy serverů, když se řadiče domény v doménové struktuře snaží stáhnout nové zásady nebo jiná data z Azure. Každý agent DC používá při rozhodování o tom, které proxy server volat, jednoduchý algoritmus kruhového dotazování. Agent přeskočí proxy servery, které nereagují. Pro většinu plně připojených nasazení služby Active Directory, které mají v pořádku replikaci adresáře a stavu složky SYSVOL, jsou pro zajištění dostupnosti k dispozici dva proxy servery. Výsledkem je včasné stažení nových zásad a dalších dat. Můžete ale nasadit další proxy servery.

Návrh softwaru agenta DC snižuje běžné problémy, které jsou spojené s vysokou dostupností. Agent řadiče domény uchovává místní mezipaměť naposledy stažených zásad hesel. I když jsou všechny registrované proxy servery nedostupné, budou agenti řadiče domény nadále vysazovat zásady hesel v mezipaměti. Přiměřená frekvence aktualizace zásad hesel ve velkém nasazení je obvykle dny, ne hodiny nebo méně. To znamená, že krátké výpadky proxy serverů významně neovlivňují ochranu heslem Azure AD.

## <a name="next-steps"></a>Další kroky

Teď, když jste nainstalovali služby, které potřebujete pro ochranu heslem Azure AD na místních serverech, [proveďte konfiguraci po instalaci a shromážděte informace pro vytváření sestav](howto-password-ban-bad-on-premises-operations.md) , abyste mohli nasazení dokončit.

[Koncepční přehled ochrany heslem služby Azure AD](concept-password-ban-bad-on-premises.md)
