---
title: Nasazení místní ochrany heslem Azure AD
description: Naučte se plánovat a nasazovat ochranu heslem Azure AD v místním prostředí Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66df1bbe531c072ff5aa2bebe7b197201e6931a2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077723"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Plánování a nasazení místní Azure Active Directory ochrany heslem

Uživatelé často vytvářejí hesla, která používají běžná místní slova, jako je škola, sportovní tým nebo slavných osoba. Tato hesla se dají snadno uhodnout a slabá na základě slovníkových útoků. K vynucení silných hesel ve vaší organizaci poskytuje Azure Active Directory (Azure AD) Ochrana heslem globální a vlastní seznam zakázaných hesel. Pokud se v seznamu zakázaných hesel vyskytne shoda, žádost o změnu hesla se nezdařila.

K ochraně místního prostředí Active Directory Domain Services (služba AD DS) můžete nainstalovat a nakonfigurovat ochranu heslem Azure AD pro práci s vaším Prem řadičem domény. V tomto článku se dozvíte, jak nainstalovat a zaregistrovat službu proxy ochrany heslem Azure AD a agenta řadiče domény Azure AD pro ochranu hesel v místním prostředí.

Další informace o tom, jak funguje ochrana heslem Azure AD v místním prostředí, najdete v tématu věnovaném [vymáhání ochrany heslem Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Strategie nasazení

Následující diagram znázorňuje, jak se základní komponenty ochrany heslem Azure AD vzájemně spolupracují v místním prostředí Active Directory:

![Jak spolupracují komponenty ochrany hesel Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Je dobré si před nasazením zkontrolovat, jak software funguje. Další informace najdete v tématu [koncepční přehled ochrany heslem Azure AD](concept-password-ban-bad-on-premises.md).

Doporučujeme spustit nasazení v režimu *auditování* . Režim auditu je výchozí počáteční nastavení, ve kterém můžou být hesla nadále nastavená. Hesla, která by byla zablokovaná, se zaznamenávají v protokolu událostí. Po nasazení proxy serverů a agentů řadiče domény v režimu auditování Sledujte vliv zásad hesla na uživatele, když se zásady vynutily.

V rámci fáze auditu mnoho organizací zjistí, že platí následující situace:

* Potřebují zlepšit stávající provozní procesy, aby používaly bezpečnější hesla.
* Uživatelé často používají nezabezpečená hesla.
* Potřebují uživatele informovat o nadcházející změně v vynucování zabezpečení, o možných dopadech na ně a o tom, jak zvolit bezpečnější hesla.

Je taky možné, že se silnější ověřování hesla bude týkat stávající automatizace nasazení řadiče domény služby Active Directory. Doporučujeme, abyste při vyhodnocení období auditů nekryli aspoň jednu povýšení řadiče domény a jedno snížení úrovně řadiče domény, aby bylo možné tyto problémy obejít. Další informace najdete v následujících článcích:

* [Ntdsutil.exe není možné nastavit slabé heslo režimu opravy adresářových služeb.](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Povýšení repliky řadiče domény se nepovedlo kvůli slabému heslu režimu opravy adresářových služeb.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Degradování řadiče domény se nezdařilo kvůli slabému heslu místního správce.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Po rozumnou dobu funguje v režimu auditu v režimu auditování, a pokud chcete *Vynutit* , aby se vyžadovalo bezpečnější heslo, můžete přepnout konfiguraci z *auditu* . Dalším monitorováním v této době je dobrý nápad.

Je důležité si uvědomit, že ochrana heslem Azure AD může ověřovat hesla jenom během operace změny a nastavení hesel. Hesla přijatá a uložená ve službě Active Directory před nasazením ochrany heslem služby Azure AD nikdy nebudou ověřená a budou dál fungovat tak, jak je. V průběhu času budou všichni uživatelé a účty nakonec začít používat hesla ověřená ochranou hesel služby Azure AD, protože jejich stávající hesla jsou normálně vypršet. Účty nakonfigurované s heslem bez vypršení platnosti jsou z této výjimky.

### <a name="multiple-forest-considerations"></a>Více informací o více doménových strukturách

Neexistují žádné další požadavky na nasazení ochrany heslem Azure AD napříč více doménovými strukturami.

Každá doménová struktura je nezávislá na konfiguraci, jak je popsáno v následující části [nasazení Prem Azure AD Password Protection](#download-required-software). Každý proxy server ochrany heslem Azure AD může podporovat jenom řadiče domény z doménové struktury, ke které je připojený.

Software ochrany heslem služby Azure AD v jakékoli doménové struktuře neznáte software ochrany heslem, který je nasazený v jiných doménových strukturách, bez ohledu na konfiguraci důvěryhodnosti služby Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Informace o řadiči domény jen pro čtení

Události změny nebo nastavení hesla nejsou zpracovávány a uchovány na řadičích domény jen pro čtení (RODC). Místo toho jsou předávány na řadiče domény s možností zápisu. Nemusíte instalovat software agenta Azure AD Password Protection pro řadiče domény jen pro čtení.

Kromě toho se nepodporuje spouštění služby proxy ochrany heslem Azure AD na řadiči domény jen pro čtení.

### <a name="high-availability-considerations"></a>Požadavky na vysokou dostupnost

Hlavním problémem ochrany heslem je dostupnost proxy serverů ochrany heslem Azure AD, když se řadiče domény v doménové struktuře snaží stáhnout nové zásady nebo jiná data z Azure. Každý agent řadiče domény Azure AD pro ochranu heslem při rozhodování o tom, které proxy server volat, používá jednoduchý algoritmus kruhového dotazování. Agent přeskočí proxy servery, které nereagují.

Pro většinu plně připojených nasazení služby Active Directory, které mají v dobrém stavu replikaci adresáře a složky SYSVOL, stačí dva proxy servery ochrany heslem Azure AD, aby se zajistila dostupnost. Tato konfigurace má za následek včasné stažení nových zásad a dalších dat. V případě potřeby můžete nasadit další servery proxy ochrany heslem služby Azure AD.

Návrh softwaru agenta pro ochranu hesel Azure AD snižuje běžné problémy, které jsou spojené s vysokou dostupností. Agent DC pro ochranu heslem služby Azure AD udržuje místní mezipaměť naposledy stažených zásad hesel. I když budou všechny registrované proxy servery nedostupné, agenti řadiče domény Azure AD pro ochranu hesel nadále vynutili jejich zásady hesel v mezipaměti.

Přiměřená frekvence aktualizace zásad hesel ve velkém nasazení je obvykle dny, ne hodiny nebo méně. To znamená, že krátké výpadky proxy serverů významně neovlivňují ochranu heslem Azure AD.

## <a name="deployment-requirements"></a>Požadavky nasazení

Informace o licencování najdete v tématu [požadavky na licencování ochrany heslem služby Azure AD](concept-password-ban-bad.md#license-requirements).

Platí následující základní požadavky:

* Všechny počítače, včetně řadičů domény, které mají nainstalované komponenty ochrany hesel služby Azure AD, musí mít nainstalovaný modul Universal C Runtime.
    * Modul runtime můžete získat tak, že zajistíte, že máte všechny aktualizace z web Windows Update. Nebo ho můžete získat v balíčku aktualizací specifických pro konkrétní operační systém. Další informace najdete v tématu [aktualizace pro Universal C Runtime v systému Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Potřebujete účet, který má oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury k registraci doménové struktury služby Active Directory Windows serveru v Azure AD.
* Služba distribuce klíčů musí být povolená na všech řadičích domény v doméně, která běží na Windows Serveru 2012. Ve výchozím nastavení je tato služba povolená přes ruční spuštění aktivační události.
* Mezi aspoň jedním řadičem domény v každé doméně a aspoň jedním serverem, který hostuje proxy službu pro ochranu hesel Azure AD, musí existovat síťové připojení. Toto připojení musí řadiči domény dovolit přístup k portu mapovače koncových bodů RPC 135 a portu serveru RPC na proxy službě.
    * Ve výchozím nastavení je port serveru RPC dynamickým portem RPC, ale je možné ho nakonfigurovat tak, aby [používal statický port](#static).
* Všechny počítače, ve kterých bude nainstalovaná služba proxy ochrany heslem Azure AD, musí mít síťový přístup k následujícím koncovým bodům:

    |**Koncový bod**|**Účel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Žádosti o ověření|
    |`https://enterpriseregistration.windows.net`|Funkce ochrany heslem Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agent DC pro ochranu heslem Azure AD

Na agenta řadiče domény služby Azure AD Password Protection platí následující požadavky:

* Všechny počítače, ve kterých bude nainstalován software agenta řadiče domény Azure AD pro ochranu heslem, musí používat systém Windows Server 2012 nebo novější.
    * Doména nebo doménová struktura služby Active Directory nemusí být na úrovni funkčnosti domény (úrovni funkčnosti domény) nebo úrovně funkčnosti doménové struktury (FFL) systému Windows Server 2012. Jak je uvedeno v [zásadách návrhu](concept-password-ban-bad-on-premises.md#design-principles), není k dispozici žádný minimální úrovni funkčnosti domény ani FFL nutný pro spuštění agenta řadiče domény ani proxy serveru.
* Všechny počítače, na kterých běží agent řadiče domény Azure AD pro ochranu heslem, musí mít nainstalované rozhraní .NET 4,5.
* Jakákoli doména služby Active Directory, která spouští službu agenta řadiče domény Azure AD, musí používat replikaci služby systém souborů DFS (Distributed File System) (DFSR) pro replikaci adresáře SYSVOL.
   * Pokud vaše doména již nepoužívá službu DFSR, je nutné před instalací ochrany hesel služby Azure AD provést migraci. Další informace najdete v tématu [Průvodce migrací replikace SYSVOL: FRS pro replikace DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Software agenta řadiče domény Azure AD pro ochranu heslem se v tuto chvíli nainstaluje na řadičích domény v doménách, které pořád využívají službu replikace souborů (DFSR) pro replikaci SYSVOL, ale software v tomto prostředí nebude správně fungovat.
    >
    > Mezi další negativní vedlejší účinky patří neúspěšná replikace jednotlivých souborů a procedury obnovení adresáře SYSVOL, které jsou úspěšné, ale tiché selhání při replikaci všech souborů.
    >
    > Migrujte doménu tak, aby používala službu DFSR co nejdříve, a to jak pro výhody služby DFSR, tak i pro odblokování nasazení ochrany heslem služby Azure AD. Budoucí verze softwaru budou automaticky zakázány při spuštění v doméně, která stále používá službu replikace souborů (FRS).

### <a name="azure-ad-password-protection-proxy-service"></a>Služba proxy ochrany heslem Azure AD

Na službu proxy ochrany heslem služby Azure AD platí následující požadavky:

* Všechny počítače, ve kterých se služba proxy ochrany heslem Azure AD bude instalovat, musí používat Windows Server 2012 R2 nebo novější.

    > [!NOTE]
    > Nasazení služby proxy ochrany heslem Azure AD je povinný požadavek na nasazení ochrany heslem Azure AD, i když řadič domény může mít odchozí přímé připojení k Internetu.

* Všechny počítače, ve kterých se služba proxy ochrany heslem Azure AD bude instalovat, musí mít nainstalované rozhraní .NET 4,7.
    * Rozhraní .NET 4,7 by již mělo být nainstalováno na plně aktualizovaný systém Windows Server. V případě potřeby si stáhněte a spusťte instalační program, který najdete v [instalačním programu .NET Framework 4,7 offline pro Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Všechny počítače, které hostují službu proxy ochrany heslem Azure AD, musí být nakonfigurované tak, aby řadičům domény udělily možnost přihlásit se k proxy službě. Tato možnost se ovládá přes přiřazení oprávnění "přístup k tomuto počítači ze sítě".
* Všechny počítače, které hostují službu proxy ochrany heslem Azure AD, musí být nakonfigurované tak, aby umožňovaly odchozí přenosy TLS 1,2 HTTP.
* Účet *globálního správce* nebo *Správce zabezpečení* , který zaregistruje službu proxy ochrany heslem Azure AD a doménovou strukturu pomocí Azure AD.
* Pro sadu portů a adres URL, které jsou zadané v [postupech nastavení prostředí proxy aplikací](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment), musí být povolený přístup k síti.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Požadavky aktualizace agenta Microsoft Azure AD Connect

Služba aktualizace agenta Microsoft Azure AD Connect je nainstalovaná souběžně se službou proxy ochrany heslem Azure AD. Aby služba aktualizace agenta Microsoft Azure AD Connect mohla fungovat, je potřeba dodatečnou konfiguraci:

* Pokud vaše prostředí používá proxy server HTTP, postupujte podle pokynů uvedených v části [práce se stávajícími místními proxy servery](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* Služba aktualizace agenta Microsoft Azure AD Connect vyžaduje také kroky TLS 1,2 uvedené v části [požadavky TLS](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Proxy ochrana heslem Azure AD a Azure Proxy aplikací služby AD instalují různé verze služby Microsoft Azure AD Connect agent, což je důvod, proč se pokyny odkazují na obsah proxy aplikací. Tyto různé verze jsou při instalaci vedle sebe nekompatibilní a zabrání tak službě aktualizace agenta, aby kontaktovala kontaktování Azure s aktualizacemi softwaru, takže na stejném počítači byste nikdy neměli instalovat proxy server ochrany heslem a proxy aplikací služby Azure AD.

## <a name="download-required-software"></a>Stáhnout požadovaný software

Pro místní nasazení ochrany heslem Azure AD jsou k dispozici dvě požadované instalační programy:

* Agent DC pro ochranu heslem Azure AD ( *AzureADPasswordProtectionDCAgentSetup.msi* )
* Proxy ochrana heslem Azure AD ( *AzureADPasswordProtectionProxySetup.exe* )

Stáhnout instalační programy z webu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instalace a konfigurace proxy služby

Služba proxy ochrany heslem Azure AD je obvykle na členském serveru v místním prostředí služba AD DS. Po nainstalování služba proxy ochrany heslem Azure AD komunikuje se službou Azure AD a udržuje kopii seznamů globálních a zakázaných hesel pro vašeho tenanta Azure AD.

V další části nainstalujete agenty řadiče domény Azure AD pro ochranu hesel na řadiče domény v místním prostředí služba AD DS. Tito agenti DC komunikují se službou proxy, aby získali nejnovější seznamy zakázaných hesel, které je možné použít při zpracování událostí změny hesla v doméně.

Vyberte jeden nebo více serverů, které budou hostovat službu proxy ochrany heslem služby Azure AD. Pro servery platí následující požadavky:

* Každá taková služba může poskytovat jenom zásady pro hesla pro jednu doménovou strukturu. Hostitelský počítač musí být připojený k jakékoli doméně v této doménové struktuře.
* Podporuje instalaci proxy služby buď v kořenové doméně, nebo v podřízených doménách, nebo na jejich kombinaci.
* Potřebujete připojení k síti mezi alespoň jedním ŘADIČEm domény v každé doméně doménové struktury a jedním proxy server ochrany heslem.
* Službu proxy ochrany heslem Azure AD můžete spustit na řadiči domény pro účely testování, ale tento řadič domény pak vyžaduje připojení k Internetu. Toto připojení může být bezpečnostním problémem. Doporučujeme tuto konfiguraci pouze pro testování.
* Pro zajištění redundance doporučujeme aspoň dva proxy servery ochrany heslem Azure AD na doménovou strukturu, jak je uvedeno v předchozí části týkající se [vysoké dostupnosti](#high-availability-considerations).
* Spuštění služby proxy ochrany heslem Azure AD na řadiči domény jen pro čtení se nepodporuje.

Pokud chcete nainstalovat službu Azure AD Password Protection proxy, proveďte následující kroky:

1. Pokud chcete nainstalovat službu Azure AD Password Protection proxy, spusťte `AzureADPasswordProtectionProxySetup.exe` instalační program softwaru.

    Instalace softwaru nevyžaduje restart a může být automatizovaná pomocí standardních procedur MSI, jako v následujícím příkladu:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Před instalací balíčku musí být spuštěná služba Brána Windows Firewall, aby nedošlo `AzureADPasswordProtectionProxySetup.exe` k chybě instalace.
    >
    > Pokud je konfigurace brány Windows Firewall nakonfigurovaná tak, aby se nespouštěla, je alternativním řešením dočasné povolení a spuštění služby brány firewall během instalace. Po instalaci nemá software proxy žádnou konkrétní závislost na bráně Windows Firewall.
    >
    > Pokud používáte bránu firewall jiného výrobce, musí být stále nakonfigurovaná tak, aby splňovala požadavky na nasazení. Mezi ně patří povolení příchozího přístupu k portu 135 a port serveru proxy RPC. Další informace najdete v předchozí části o požadavcích na [nasazení](#deployment-requirements).

1. Software proxy ochrany heslem Azure AD zahrnuje nový modul PowerShellu, `AzureADPasswordProtection` . V následujících krocích spustíte z tohoto modulu PowerShellu různé rutiny.

    Chcete-li použít tento modul, otevřete okno prostředí PowerShell jako správce a importujte nový modul následujícím způsobem:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Pokud chcete ověřit, jestli je spuštěná služba proxy ochrany heslem Azure AD, použijte následující příkaz PowerShellu:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Výsledek by měl zobrazovat **stav** *spuštěno* .

1. Služba proxy je v počítači spuštěná, ale nemá přihlašovací údaje ke komunikaci se službou Azure AD. Pomocí rutiny Zaregistrujte proxy server ochrany heslem Azure AD pomocí služby Azure AD `Register-AzureADPasswordProtectionProxy` .

    Tato rutina vyžaduje pro vašeho tenanta Azure buď pověření *globálního správce* nebo *Správce zabezpečení* . Tuto rutinu je taky potřeba spustit pomocí účtu s oprávněními místního správce.

    Po úspěšném provedení tohoto příkazu pro službu proxy ochrany heslem služby Azure AD je další vyvolání úspěšné, ale nepotřebujeme je.

    `Register-AzureADPasswordProtectionProxy`Rutina podporuje následující tři režimy ověřování. První dva režimy podporují Azure Multi-Factor Authentication ale třetí režim ne.

    > [!TIP]
    > Před dokončením této rutiny pro konkrétního tenanta Azure může nastat znatelné zpoždění. Pokud není nahlášená chyba, nedělejte si starosti s touto prodlevou.

     * Režim interaktivního ověřování:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Tento režim nefunguje na operačních systémech jádra serveru. Místo toho použijte jeden z následujících režimů ověřování. Tento režim může také selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat tuto konfiguraci, zaregistrovat proxy server a pak ho znovu povolit.

     * Režim ověřování kódu zařízení:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po zobrazení výzvy postupujte podle odkazu a otevřete webový prohlížeč a zadejte ověřovací kód.

     * Režim ověřování v tichém režimu (založený na hesle):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdařil, pokud pro váš účet potřebujete Azure Multi-Factor Authentication. V takovém případě použijte jeden z předchozích dvou režimů ověřování nebo místo toho použijte jiný účet, který nevyžaduje MFA.
        >
        > Je také možné, že se vyžaduje ověřování MFA, pokud je registrace zařízení Azure (která se používá v rámci zabezpečení Azure AD heslem) nakonfigurovaná tak, aby globálně vyžadovala MFA. Pokud chcete tento požadavek vyřešit, můžete použít jiný účet, který podporuje MFA s jedním z předchozích dvou režimů ověřování, nebo můžete také dočasně uvolnit požadavek MFA pro registraci zařízení Azure.
        >
        > Tuto změnu provedete tak, že vyhledáte a vyberete **Azure Active Directory** v Azure Portal a pak vyberete **zařízení > nastavení zařízení** . Nastavte **vyžadovat vícefaktorové ověřování, aby se zařízení připojila** k *žádnému* . Nezapomeňte znovu nakonfigurovat toto nastavení zpět na *Ano* , jakmile se registrace dokončí.
        >
        > Pro účely testování doporučujeme vynechat požadavky na vícefaktorové ověřování.

    V tuto chvíli nemusíte zadávat parametr *-ForestCredential* , který je vyhrazený pro budoucí funkce.

    Registrace služby proxy ochrany heslem Azure AD je nutná jenom jednou za dobu života služby. Potom bude služba proxy ochrany heslem Azure AD automaticky provádět jakoukoli další nezbytnou údržbu.

1. Nyní Zaregistrujte místní doménovou strukturu služby Active Directory s potřebnými přihlašovacími údaji ke komunikaci s Azure pomocí `Register-AzureADPasswordProtectionForest` rutiny prostředí PowerShell.

    > [!NOTE]
    > Pokud je ve vašem prostředí nainstalované víc proxy serverů ochrany heslem Azure AD, nezáleží na tom, který proxy server používáte k registraci doménové struktury.

    Rutina vyžaduje pro vašeho tenanta Azure buď pověření *globálního správce* nebo *Správce zabezpečení* . Vyžaduje taky místní oprávnění podnikového Správce služby Active Directory. Tuto rutinu musíte spustit taky pomocí účtu s oprávněními místního správce. Účet Azure, který se používá k registraci doménové struktury, se může lišit od místního účtu služby Active Directory.
    
    Tento krok se spouští jednou pro každou doménovou strukturu.

    `Register-AzureADPasswordProtectionForest`Rutina podporuje následující tři režimy ověřování. První dva režimy podporují Azure Multi-Factor Authentication ale třetí režim ne.

    > [!TIP]
    > Před dokončením této rutiny pro konkrétního tenanta Azure může nastat znatelné zpoždění. Pokud není nahlášená chyba, nedělejte si starosti s touto prodlevou.

     * Režim interaktivního ověřování:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Tento režim nebude fungovat na operačních systémech jádra serveru. Místo toho použijte jeden z následujících dvou režimů ověřování. Tento režim může také selhat, pokud je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Alternativním řešením je zakázat tuto konfiguraci, zaregistrovat doménovou strukturu a pak ji znovu povolit.  

     * Režim ověřování kódu zařízení:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po zobrazení výzvy postupujte podle odkazu a otevřete webový prohlížeč a zadejte ověřovací kód.

     * Režim ověřování v tichém režimu (založený na hesle):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdařil, pokud pro váš účet potřebujete Azure Multi-Factor Authentication. V takovém případě použijte jeden z předchozích dvou režimů ověřování nebo místo toho použijte jiný účet, který nevyžaduje MFA.
        >
        > Je také možné, že se vyžaduje ověřování MFA, pokud je registrace zařízení Azure (která se používá v rámci zabezpečení Azure AD heslem) nakonfigurovaná tak, aby globálně vyžadovala MFA. Pokud chcete tento požadavek vyřešit, můžete použít jiný účet, který podporuje MFA s jedním z předchozích dvou režimů ověřování, nebo můžete také dočasně uvolnit požadavek MFA pro registraci zařízení Azure.
        >
        > Tuto změnu provedete tak, že vyhledáte a vyberete **Azure Active Directory** v Azure Portal a pak vyberete **zařízení > nastavení zařízení** . Nastavte **vyžadovat vícefaktorové ověřování, aby se zařízení připojila** k *žádnému* . Nezapomeňte znovu nakonfigurovat toto nastavení zpět na *Ano* , jakmile se registrace dokončí.
        >
        > Pro účely testování doporučujeme vynechat požadavky na vícefaktorové ověřování.

       Tyto příklady jsou úspěšné pouze v případě, že aktuálně přihlášený uživatel je zároveň správcem domény služby Active Directory pro kořenovou doménu. V takovém případě můžete alternativní přihlašovací údaje domény zadat pomocí parametru *-ForestCredential* .

    Registrace doménové struktury služby Active Directory je pro celou dobu života doménové struktury nutná jenom jednou. Potom agenti řadiče domény Azure AD pro ochranu heslem v doménové struktuře automaticky provedou veškerou potřebnou údržbu. Po `Register-AzureADPasswordProtectionForest` úspěšném spuštění pro doménovou strukturu se další vyvolání rutiny zdaří, ale nepotřebná.
    
    `Register-AzureADPasswordProtectionForest`Aby bylo úspěšné, musí být alespoň jeden řadič domény se systémem Windows Server 2012 nebo novější dostupný v doméně proxy server ochrany heslem služby Azure AD. Software agenta řadiče domény Azure AD pro ochranu heslem nemusí být nainstalovaný na žádném řadiči domény před tímto krokem.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurace proxy služby pro komunikaci prostřednictvím proxy serveru HTTP

Pokud vaše prostředí vyžaduje ke komunikaci s Azure konkrétní proxy server HTTP, použijte následující postup ke konfiguraci služby ochrany hesel Azure AD.

Ve složce vytvořte soubor *AzureADPasswordProtectionProxy.exe.config* `%ProgramFiles%\Azure AD Password Protection Proxy\Service` . Zahrnout následující obsah:

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

V obou případech nahraďte `http://yourhttpproxy.com:8080` adresou a portem konkrétního proxy server protokolu HTTP.

Pokud je váš proxy server HTTP nakonfigurovaný tak, aby používal zásady autorizace, musíte udělit přístup k účtu počítače služby Active Directory počítače, který je hostitelem služby proxy, pro ochranu heslem.

Po vytvoření nebo aktualizaci souboru *AzureADPasswordProtectionProxy.exe.config* Doporučujeme zastavit a restartovat službu Azure AD Password Protection proxy.

Proxy služba nepodporuje použití konkrétních přihlašovacích údajů pro připojení k proxy serveru HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Nakonfigurujte proxy službu tak, aby naslouchala na určitém portu.

Software agenta řadiče domény Azure AD pro ochranu heslem používá ke komunikaci s proxy službou protokol RPC přes protokol TCP. Ve výchozím nastavení služba proxy ochrany heslem Azure AD naslouchá na jakémkoli dostupném dynamickém koncovém bodu RPC. Službu můžete nakonfigurovat tak, aby naslouchala na specifickém portu TCP v případě potřeby v důsledku síťové topologie nebo požadavků na bránu firewall ve vašem prostředí.

<a id="static" /></a>Pokud chcete službu nakonfigurovat tak, aby běžela pod statickým portem, použijte `Set-AzureADPasswordProtectionProxyConfiguration` rutinu následujícím způsobem:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Aby se tyto změny projevily, musíte zastavit a restartovat službu proxy ochrany heslem služby Azure AD.

Pokud chcete službu nakonfigurovat tak, aby běžela pod dynamickým portem, použijte stejný postup, ale nastavte *StaticPort* zpět na nula:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Aby se tyto změny projevily, musíte zastavit a restartovat službu proxy ochrany heslem služby Azure AD.

Služba proxy ochrany heslem Azure AD vyžaduje ruční restartování po každé změně konfigurace portu. Po provedení těchto změn konfigurace nemusíte restartovat službu agenta řadiče domény služby Azure AD Password na řadičích domény.

K dotazování na aktuální konfiguraci služby použijte `Get-AzureADPasswordProtectionProxyConfiguration` rutinu, jak je znázorněno v následujícím příkladu.

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Následující příklad výstupu ukazuje, že služba proxy ochrany heslem Azure AD používá dynamický port:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instalace služby agenta řadiče domény

Pokud chcete nainstalovat službu agenta řadiče domény služby Azure AD Password, spusťte `AzureADPasswordProtectionDCAgentSetup.msi` balíček.

Instalaci softwaru můžete automatizovat pomocí standardních procedur MSI, jak je znázorněno v následujícím příkladu:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

`/norestart`Příznak lze vynechat, pokud upřednostňujete, aby instalační program automaticky restartoval počítač.

Instalace nebo odinstalace softwaru vyžaduje restart. Důvodem je to, že se knihovny DLL filtru hesel načítají nebo odhrávají jenom po restartování.

Po instalaci softwaru agenta DC na řadič domény a restartování tohoto počítače se dokončí instalace služby Prem Azure AD Password Protection. Žádná jiná konfigurace není vyžadována nebo možná. Události změny hesla na řadičích domény Prem používají nakonfigurované seznamy zakázaných hesel ze služby Azure AD.

Pokud chcete povolit ochranu heslem Prem Azure AD z Azure Portal nebo nakonfigurovat vlastní zakázaná hesla, přečtěte si téma [Povolení ochrany heslem v místní službě Azure AD](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Na počítač, který ještě není řadičem domény, můžete nainstalovat agenta Azure AD Password Protection DC. V tomto případě se služba spustí a spustí, ale zůstane neaktivní, dokud nebude počítač povýšen na řadič domény.

## <a name="upgrading-the-proxy-service"></a>Upgrade služby proxy

Služba proxy ochrany heslem Azure AD podporuje automatický upgrade. Automatický upgrade používá službu Microsoft Azure AD Connect agent aktualizační službu, která je nainstalovaná souběžně se službou proxy serveru. Automatický upgrade je ve výchozím nastavení zapnutý a mohl by být povolený nebo zakázaný pomocí `Set-AzureADPasswordProtectionProxyConfiguration` rutiny.

Pomocí rutiny se dá zadat dotaz na aktuální nastavení `Get-AzureADPasswordProtectionProxyConfiguration` . Doporučujeme, aby nastavení automatického upgradu bylo vždy povolené.

`Get-AzureADPasswordProtectionProxy`Rutina se dá použít k dotazování verze softwaru všech aktuálně nainstalovaných proxy serverů ochrany heslem služby Azure AD v doménové struktuře.

### <a name="manual-upgrade-process"></a>Proces ručního upgradu

Ruční upgrade je zajištěn spuštěním nejnovější verze `AzureADPasswordProtectionProxySetup.exe` instalačního programu softwaru. Nejnovější verzi softwaru najdete na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

Není nutné odinstalovat aktuální verzi služby proxy ochrany heslem Azure AD – instalační program provede místní upgrade. Při upgradování proxy služby by se neměl vyžadovat restart. Upgrade softwaru může být automatizovaný pomocí standardních procedur MSI, jako je například `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>Upgrade agenta řadiče domény

Pokud je k dispozici novější verze softwaru agenta Azure AD Password Protection, upgrade se provádí spuštěním nejnovější verze `AzureADPasswordProtectionDCAgentSetup.msi` softwarového balíčku. Nejnovější verzi softwaru najdete na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

Není nutné odinstalovat aktuální verzi softwaru agenta DC – instalační program provede místní upgrade. Při upgradu softwaru agenta DC se vždy vyžaduje restart – tento požadavek způsobuje základní chování Windows.

Upgrade softwaru může být automatizovaný pomocí standardních procedur MSI, jako je například `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

Příznak můžete vynechat, `/norestart` Pokud budete chtít, aby instalační program automaticky restartoval počítač.

`Get-AzureADPasswordProtectionDCAgent`Rutina se dá použít k dotazování verze softwaru všech aktuálně nainstalovaných agentů řadiče domény Azure AD s ochranou hesel v doménové struktuře.

## <a name="next-steps"></a>Další kroky

Teď, když jste nainstalovali služby, které potřebujete pro ochranu heslem Azure AD na místních serverech, [Povolte ochranu heslem Prem Azure AD v Azure Portal](howto-password-ban-bad-on-premises-operations.md) k dokončení nasazení.