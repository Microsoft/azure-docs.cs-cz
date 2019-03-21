---
title: Nasazení ochrany hesel Azure AD
description: Ochrana heslem služby Azure AD se zakázat chybná hesla v místním nasazení
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54d2d600771316b0a88ea0a2486c0dedd0f84594
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286529"
---
# <a name="deploy-azure-ad-password-protection"></a>Nasazení ochrany hesel Azure AD

Teď, když rozumíte [způsob vynucení ochrany hesla Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md), dalším krokem je k plánování a spouštění vašeho nasazení.

## <a name="deployment-strategy"></a>Strategie nasazení

Doporučujeme spouštět nasazení v režimu auditování. Režim auditování je výchozí počáteční nastavení, kde můžete pokračovat nastavit hesla. Hesla, které by se zablokovaly se zaznamenávají do protokolu událostí. Po nasazení proxy servery a agenty řadiče domény v režimu auditování, měli byste sledovat dopad, který zásady hesel bude mít na uživatele a prostředí, když je tato zásada vynucená.

Během fáze auditu zjistit řada organizací, které:

* Potřebují ke zlepšení existující provozní procesy, použijte více zabezpečených hesel.
* Uživatelé často používají nezabezpečených hesel.
* Budou muset informovat uživatele o tuto chystanou změnu vynucení zabezpečení, možný dopad na ně a jak zvolit více zabezpečených hesel.

Za běhu funkci v režimu auditování přiměřené dobu, můžete přepnout konfiguraci z *auditu* k *vynutit* vyžadovat víc zabezpečených hesel. Cílené monitorování během této doby je vhodné.

## <a name="deployment-requirements"></a>Požadavky na nasazení

* Všechny řadiče domény, které získávají agenta pro řadič domény služby pro ochranu heslem služby Azure AD nainstalovaný musí běžet Windows Server 2012 nebo novější.
* Všechny počítače, které získávají proxy server služby pro ochranu heslem služby Azure AD nainstalovaný musí běžet Windows Server 2012 R2 nebo novější.
* Všechny počítače s nainstalovanou službu Proxy ochrana hesel Azure AD musí mít nainstalovaný .NET 4.7.
  .NET 4.7 musí již nainstalován na serveru Windows Server kompletně aktualizovaný. Pokud to není tento případ, stáhněte a spusťte instalační program na [offline instalační program .NET Framework 4.7 pro Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Všechny počítače, včetně řadičů domény, které byly nainstalovány součásti ochrany hesla Azure AD musíte mít Universal C Runtime nainstalovaný. Modul runtime můžete získat tak, že všechny aktualizace ze služby Windows Update. Nebo můžete ho získat balíček aktualizací specifické pro operační systém. Další informace najdete v tématu [aktualizace pro Universal C Runtime ve službě Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Připojení k síti musí existovat mezi aspoň jeden řadič domény v každé doméně a alespoň jeden server, který hostuje službu proxy serveru pro ochranu heslem. Toto připojení musí umožňovat řadič domény pro přístup k vzdálené volání Procedur endpoint mapper port 135 a port serveru RPC na službu proxy serveru. Ve výchozím nastavení, je port serveru RPC dynamický port vzdáleného volání Procedur, ale lze nastavit na [použití statického portu](#static).
* Všechny počítače, které hostují službu proxy musí mít přístup k síti pro následující koncové body:

    |**Koncový bod**|**Účel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Požadavky na ověření|
    |`https://enterpriseregistration.windows.net`|Funkce Ochrana hesel Azure AD|

* Všechny počítače, hostující službu proxy serveru pro ochranu heslem musí být nakonfigurované na Povolit odchozí přenosy protokolu TLS 1.2 HTTP.
* Účet globálního správce Azure AD zaregistrovat službu proxy serveru pro ochranu heslem a doménové struktury.
* Účet, který má oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury pro registraci doménové struktury Windows Server Active Directory s Azure AD.
* Libovolné doméně Active Directory, na kterém běží software agenta pro řadič domény služby musí používat distribuované systému službu replikace souborů (DFSR) k replikaci adresáře sysvol.
* Služba distribuce klíčů musí být povolena na všech řadičích domény v doméně, na kterých běží Windows Server 2012. Ve výchozím nastavení tato služba povolená přes start ruční aktivační události.

## <a name="single-forest-deployment"></a>Nasazení s jednou doménovou strukturou

Následující diagram znázorňuje, jak spolupracují základní součásti služby Azure AD ochrana heslem v prostředí místní služby Active Directory.

![Jak spolupracují součásti ochrany hesla Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Je vhodné zkontrolovat, jak software funguje, před jejím nasazením. Zobrazit [koncepční přehled služby Azure AD ochrana heslem](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Stáhnout software

Existují dva instalační programy požadovaných pro ochranu hesel Azure AD. Jsou k dispozici z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalace a konfigurace služby proxy serveru k ochraně heslem

1. Vyberte jeden nebo více serverů pro hostování služby proxy serveru k ochraně heslem.
   * Každé takové služby může poskytnout pouze zásady hesla pro jednu doménovou strukturu. Hostitelský počítač musí být připojené k doméně v příslušné doménové struktuře. Kořenové a podřízené domény jsou podporované. Budete potřebovat připojení k síti mezi aspoň jeden řadič domény v jednotlivých doménách doménové struktury a počítačem ochrany heslem.
   * Službu proxy můžete spustit na řadiči domény pro účely testování. Ale tento řadič domény pak vyžaduje připojení k Internetu, které mohou být potíže se zabezpečením. Doporučujeme tuto konfiguraci pouze pro testování.
   * Doporučujeme, aby měly aspoň dva proxy servery za účelem zajištění redundance. Zobrazit [vysoké dostupnosti](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Instalace služby Azure AD Proxy ochrany heslem služba používá `AzureADPasswordProtectionProxySetup.exe` instalační program softwaru.
   * Instalace softwaru nevyžaduje restartování. Instalace softwaru může možné automatizovat pomocí standardní postupy MSI, například:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Než budete instalovat balíček AzureADPasswordProtectionProxySetup.msi tak, aby se zabránilo chybě instalace, musí být spuštěna služba brány Windows Firewall. Pokud brána Windows Firewall je nakonfigurovaná a nebudou tedy spuštěny, alternativním řešením je dočasně povolit a spustit službu brány Firewall během instalace. Proxy software nemá žádné konkrétní závislost v bráně Windows Firewall po instalaci. Pokud používáte bránu firewall jiného dodavatele, musí stále probíhá konfigurace splňovat požadavky na nasazení. Patří mezi ně umožní příchozí přístup k portu 135 a proxy port serveru RPC. Zobrazit [požadavky na nasazení](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Otevřete okno Powershellu jako správce.
   * Proxy software ochrany heslo obsahuje nový modul prostředí PowerShell *AzureADPasswordProtection*. Následující postup spustit různé rutiny z tohoto modulu prostředí PowerShell. Importujte nového modulu takto:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Pokud chcete zkontrolovat, zda je spuštěna služba, použijte následující příkaz Powershellu:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Výsledek by se měla zobrazit **stav** "Spustit".

1. Registrace proxy serveru.
   * Po dokončení kroku 3 se službou proxy serveru na počítači. Ale služba ještě nemá potřebné přihlašovací údaje ke komunikaci s Azure AD. Registrace ve službě Azure AD je potřeba:

     `Register-AzureADPasswordProtectionProxy`

     Tato rutina vyžaduje přihlašovací údaje globálního správce pro vašeho tenanta Azure. Budete také potřebovat oprávnění správce v místním Active Directory domény v kořenové doméně doménové struktury. Po úspěšném provedení tohoto příkazu, jakmile pro službu proxy, další vyvolání proběhne úspěšně, ale nejsou potřeba.

      `Register-AzureADPasswordProtectionProxy` Rutina podporuje následujících režimů ověřování tři.

     * Režim interaktivní ověřování:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Tento režim nebude fungovat v operačních systémech jádra serveru. Místo toho použijte jednu z následujících režimů ověřování. Tento režim také může selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat tuto konfiguraci, zaregistrujte proxy serveru a potom ji znovu povolit.

     * Režim ověřování kódu zařízení:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Potom dokončete ověřování podle pokynů zobrazených na jiném zařízení.

     * Režim bezobslužné ověření (založené na heslech):

        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdaří, pokud Azure Multi-Factor Authentication je povinný. V takovém případě použijte jeden z režimů předchozí dvě ověřování.

       K určení nemáte aktuálně *- ForestCredential* parametr, který je vyhrazený pro budoucí funkce.

   
   Registrace služby proxy serveru k ochraně heslem je nezbytné pouze jednou v době životnosti služby. Potom službu proxy automaticky provede všechny potřebné údržby.

   > [!TIP]
   > Může existovat významnému zpoždění před dokončením poprvé, tato rutina se spouští pro konkrétního tenanta Azure. Pokud selhání je nahlášeno, nemusíte dělat starosti o toto zpoždění.

1. Zaregistrujte doménové struktuře.
   * Je třeba inicializovat místní doménové struktuře služby Active Directory se přihlašovací údaje potřebné ke komunikaci s Azure pomocí `Register-AzureADPasswordProtectionForest` rutiny Powershellu. Rutina vyžaduje přihlašovací údaje globálního správce pro vašeho tenanta Azure. Také vyžaduje oprávnění správce v místním Active Directory domény v kořenové doméně doménové struktury. Tento krok spustí jednou pro každou doménovou strukturu.

      `Register-AzureADPasswordProtectionForest` Rutina podporuje následujících režimů ověřování tři.

     * Režim interaktivní ověřování:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Tento režim nebude fungovat v operačních systémech jádra serveru. Místo toho použijte jednu z následujících režimů ověřování dvě. Tento režim také může selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat tuto konfiguraci, zaregistrujte proxy serveru a potom ji znovu povolit.  

     * Režim ověřování kódu zařízení:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Potom dokončete ověřování podle pokynů zobrazených na jiném zařízení.

     * Režim bezobslužné ověření (založené na heslech):
        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdaří, pokud Azure Multi-Factor Authentication je povinný. V takovém případě použijte jeden z režimů předchozí dvě ověřování.

       Tyto příklady úspěšné, pouze pokud aktuálně přihlášený uživatel je také správcem domény služby Active Directory pro kořenovou doménu. Pokud to není tento případ, můžete zadat alternativní pověření prostřednictvím *- ForestCredential* parametru.

   > [!NOTE]
   > Pokud více proxy serverů jsou nainstalovány ve vašem prostředí, nezáleží na který server proxy serveru použijte k registraci doménové struktury.

   > [!TIP]
   > Může existovat významnému zpoždění před dokončením poprvé, tato rutina se spouští pro konkrétního tenanta Azure. Pokud selhání je nahlášeno, nemusíte dělat starosti o toto zpoždění.

   Registrace v doménové struktuře služby Active Directory je nezbytné pouze jednou v době životnosti doménové struktury. Potom agenty řadič domény v doménové struktuře automaticky provede všechny potřebné údržby. Po `Register-AzureADPasswordProtectionForest` spuštěna úspěšně pro doménovou strukturu, další vyvolání rutiny úspěšné, ale nejsou potřeba.

   Pro `Register-AzureADPasswordProtectionForest` úspěšné, musí být aspoň jeden řadič domény s Windows serverem 2012 nebo novějším k dispozici v doméně serveru proxy. Ale software agenta pro řadič domény nemusí být nainstalovaná na žádném řadiči domény před tímto krokem.

1. Nakonfigurujte službu proxy serveru pro ochranu heslem komunikovat přes proxy server HTTP.

   Pokud vaše prostředí vyžaduje použití konkrétní proxy server HTTP pro komunikaci s Azure, použijte tuto metodu: Vytvoření *AzureADPasswordProtectionProxy.exe.config* souboru ve složce %ProgramFiles%\Azure AD Proxy\Service ochrany heslem. Zahrňte následující obsah:

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

   Pokud váš proxy server HTTP vyžaduje ověření, přidejte *useDefaultCredentials* značky:

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

   V obou případech se nahradit `http://yourhttpproxy.com:8080` adresu a port konkrétní proxy serveru HTTP.

   Pokud váš proxy server HTTP je nakonfigurovaný pro nás zásad autorizace, je nutné udělit přístup k účtu počítače služby Active Directory počítače, který hostuje službu proxy serveru pro ochranu heslem.

   Doporučujeme zastavit a restartovat službu proxy můžete vytvořit nebo aktualizovat *AzureADPasswordProtectionProxy.exe.config* souboru.

   Proxy služba nepodporuje použití konkrétní přihlašovací údaje pro připojení k proxy serveru HTTP.

1. Volitelné: Nakonfigurujte službu proxy serveru pro ochranu heslem tak, aby naslouchala na určitém portu.
   * Software agenta řadiče domény k ochraně heslem na řadičích domény. využívá protokol RPC přes protokol TCP ke komunikaci se službou proxy serveru. Ve výchozím nastavení proxy služba naslouchá na všechny dostupné dynamické RPC koncový bod. Ale můžete tuto službu nakonfigurujete tak, aby naslouchala na určitém portu TCP, pokud je to nezbytné vzhledem topologii sítí nebo požadavky na bránu firewall ve vašem prostředí.
      * <a id="static" /></a>Chcete-li službu spustit pod statický port, použijte `Set-AzureADPasswordProtectionProxyConfiguration` rutiny.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

      * Postup konfigurace služby běžet pod dynamický port, stejný postup použijte, ale nastavit *StaticPort* zpět na nulu:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Musíte zastavit a restartovat službu pro tyto změny se projeví.

   > [!NOTE]
   > Službu proxy serveru pro ochranu heslem vyžaduje ruční restartování po každé změně v konfiguraci portů. Ale není nutné restartovat službu software agenta řadiče domény na řadičích domény po provedení těchto změn konfigurace.

   * Dotaz pro aktuální konfiguraci této služby, použijte `Get-AzureADPasswordProtectionProxyConfiguration` rutiny:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Nainstalujte službu agenta řadiče domény

   Instalace agenta pro řadič domény služby k ochraně heslem pomocí `AzureADPasswordProtectionDCAgentSetup.msi` balíčku.

   Instalace softwaru nebo odinstalace, vyžaduje restartování. Je to proto, že knihovny DLL filtru hesel jsou načteny nebo uvolnit technologie restartování.

   Službu řadiče domény agenta můžete nainstalovat na počítači, který dosud není řadičem domény. V tomto případě služba bude spustit a spustit ale zůstat neaktivní, dokud se tento počítač je povýšen na řadič domény.

   Instalace softwaru můžete automatizovat pomocí standardní postupy MSI. Příklad:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > Příkaz msiexec příklad tady způsobí, že okamžitý restart. Chcete-li tomu zabránili, použijte `/norestart` příznak.

Instalace je dokončena po nainstalování softwaru agenta řadiče domény na řadič domény a restartování tohoto počítače. Žádná další konfigurace je vyžaduje nebo je to možné.

## <a name="multiple-forest-deployments"></a>Více nasazení doménové struktury

Neexistují žádné další požadavky na nasazení služby Azure AD ochrana heslem napříč více doménovými strukturami. Každá doménová struktura má nakonfigurované nezávisle na sobě podle popisu v části "jednou doménovou strukturou nasazení". Každý proxy ochrany heslo může podporovat pouze řadiče domény v doménové struktuře, který je připojený k. Tento software ochrany hesla ve všech doménových strukturách nebude vědět o heslo softwaru, který je nasazený v jiných doménových strukturách, bez ohledu na konfigurace vztahu důvěryhodnosti služby Active Directory.

## <a name="read-only-domain-controllers"></a>Řadiče domény jen pro čtení

Heslo. změny nebo nastaví nejsou zpracovány a trvale se uloží na řadiče domény jen pro čtení (RODC). Jsou předány do řadičů domény s možností zápisu. Proto není nutné nainstalovat software agenta pro řadič domény do řadiče jen pro čtení.

## <a name="high-availability"></a>Vysoká dostupnost

Dostupnost hlavním zájmem zajistit u ochrany heslem je dostupnost proxy serverů při pokusu o stažení nové zásady nebo jiná data z Azure řadiče domény v doménové struktuře. Každý Agent řadiče domény používá jednoduché algoritmus round robin stylu při rozhodování o tom, které proxy serveru pro volání. Agenta přeskočí proxy servery, které nereagují. Pro největší plně propojené nasazeních služby Active Directory, které mají v dobrém stavu replikace stavu složky directory i adresáři sysvol stačí dva proxy servery a zajistit tak dostupnost. Výsledkem je včas stáhnout nové zásady a další data. Ale můžete nasadit další proxy servery.

Návrh softwaru agenta DC zmírňuje běžné problémy, které jsou spojeny s vysokou dostupností. Řadič domény Agent udržuje v místní mezipaměti naposledy stažené zásady hesel. I v případě, že všechny registrované servery proxy není dostupná, agentů DC i nadále vynucovat zásady jejich hesla uložená v mezipaměti. Četnost přiměřené aktualizace pro zásady pro hesla ve velkém rozsahu, je obvykle *dnů*, nikoli hodin nebo menší. Ano krátké výpadky proxy servery nebudou mít vliv na výrazně ochrana hesel Azure AD.

## <a name="next-steps"></a>Další postup

Teď, když jste nainstalovali služby, které potřebujete k ochraně heslem služby Azure AD na místních serverech [provedení konfigurace po instalaci a shromažďovat informace o vytváření sestav](howto-password-ban-bad-on-premises-operations.md) k dokončení nasazení.

[Koncepční přehled ochrany hesla Azure AD](concept-password-ban-bad-on-premises.md)
