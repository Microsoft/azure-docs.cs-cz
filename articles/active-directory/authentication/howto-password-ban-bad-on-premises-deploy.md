---
title: Nasazení místní ochrany heslem Azure AD
description: Zjistěte, jak naplánovat a nasadit azure ad ochranu heslem v místním prostředí služby Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671739"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Plánování a nasazení místní ochrany heslem služby Azure Active Directory

Uživatelé často vytvářejí hesla, která používají běžná místní slova, například školu, sportovní tým nebo slavnou osobu. Tato hesla jsou snadno uhodnout, a slabé proti slovníku-založené útoky. Chcete-li vynutit silná hesla ve vaší organizaci, Azure Active Directory (Azure AD) Password Protection poskytuje globální a vlastní seznam zakázaných hesel. Požadavek na změnu hesla se nezdaří, pokud je v těchto seznamu zakázaných hesel shoda.

Chcete-li chránit místní prostředí služby Active Directory Domain Services (AD DS), můžete nainstalovat a nakonfigurovat ochranu heslem služby Azure AD tak, aby fungovala s řadičem domény. Tento článek ukazuje, jak nainstalovat a zaregistrovat službu proxy azure ad password protection a agenta Azure AD Password Protection DC ve vašem místním prostředí.

Další informace o tom, jak funguje ochrana heslem Azure AD v místním prostředí, najdete v tématu [Jak vynutit ochranu heslem Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Strategie nasazení

Následující diagram znázorňuje, jak základní součásti azure ad heslábna spolupracovat v místním prostředí služby Active Directory:

![Jak komponenty Azure AD Password Protection spolupracují](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Před nasazením je vhodné software zkontrolovat. Další informace naleznete [v tématu Koncepční přehled ochrany heslem Azure AD](concept-password-ban-bad-on-premises.md).

Doporučujeme spustit nasazení v režimu *auditování.* Režim auditování je výchozí počáteční nastavení, kde lze hesla nadále nastavovat. Hesla, která by byla blokována, jsou zaznamenána do protokolu událostí. Po nasazení proxy serverů a agentů řadiče domény v režimu auditování sledujte dopad zásad hesel na uživatele při vynucení zásady.

Během fáze auditu mnoho organizací zjistí, že platí následující situace:

* Potřebují zlepšit stávající provozní procesy, aby mohli používat bezpečnější hesla.
* Uživatelé často používají nezabezpečená hesla.
* Musí uživatele informovat o nadcházející změně v ynualaci zabezpečení, možném dopadu na ně a o tom, jak zvolit bezpečnější hesla.

Je také možné, že silnější ověření hesla ovlivní stávající automatizaci nasazení řadiče domény služby Active Directory. Doporučujeme, aby alespoň jeden dc propagace a jeden snížení úrovně řadiče domény dojít během hodnocení období auditu pomoci odhalit tyto problémy. Další informace najdete v těchto článcích:

* [Soubor Ntdsutil.exe nemůže nastavit slabé heslo režimu opravy adresářových služeb.](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Povýšení repliky řadiče domény se nezdaří z důvodu slabého hesla režimu opravy adresářových služeb](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Snížení úrovně řadiče domény se nezdaří z důvodu slabého hesla místního správce](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Po spuštění funkce v režimu auditování po přiměřenou dobu můžete přepnout konfiguraci z *auditu* na *Vynutit* tak, aby vyžadovala bezpečnější hesla. Další monitorování během této doby je dobrý nápad.

### <a name="multiple-forest-considerations"></a>Více aspektů doménové struktury

Neexistují žádné další požadavky na nasazení Azure AD Password Protection ve více doménových strukturách.

Každá doménová struktura je nezávisle nakonfigurována, jak je popsáno v následující části pro [nasazení ochrany heslem Azure AD](#download-required-software). Každý proxy server Azure AD Password Protection může podporovat jenom řadiče domény z doménové struktury, ke které je připojen.

Software Azure AD Password Protection v libovolné doménové struktuře neví o softwaru pro ochranu heslem, který je nasazený v jiných doménových strukturách, bez ohledu na konfigurace důvěryhodnosti služby Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Důležité informace o řadiči domény jen pro čtení

Změny hesla nebo nastavené události nejsou zpracovány a trvalé v řadičích domény jen pro čtení (ROD KS). Místo toho jsou předány zapisovatelným řadičům domény. Není nutné instalovat software agenta Azure AD Password Protection DC na řadiče domény řadiče domény.

Dále není podporováno spuštění proxy služby Azure AD Password Protection na řadiči domény jen pro čtení.

### <a name="high-availability-considerations"></a>Důležité informace o vysoké dostupnosti

Hlavním problémem ochrany heslem je dostupnost proxy serverů Azure AD Password Protection, když se řadiče domény v doménové struktuře pokusí stáhnout nové zásady nebo jiná data z Azure. Každý agent řadiče domény ochrany heslem Azure AD používá při rozhodování o tom, který proxy server volat, jednoduchý algoritmus stylu kruhového dotazování. Agent přeskočí proxy servery, které nereagují.

Pro většinu plně připojených nasazení služby Active Directory, která mají v pořádku replikaci adresáře i stavu složky sysvol, stačí k zajištění dostupnosti dva proxy servery Azure AD Password Protection. Výsledkem této konfigurace je včasné stažení nových zásad a dalších dat. V případě potřeby můžete nasadit další proxy servery Azure AD Password Protection.

Návrh softwaru agenta řadiče domény ochrany heslem Azure AD zmírňuje obvyklé problémy, které jsou spojeny s vysokou dostupností. Agent řadiče domény ochrany heslem Azure AD udržuje místní mezipaměť naposledy stažených zásad hesel. I v případě, že všechny registrované proxy servery stanou nedostupnými, agenti řadiče domény ochrany hesel Azure AD nadále vynucují své zásady hesel uložené v mezipaměti.

Přiměřená četnost aktualizací zásad hesel ve velkém nasazení je obvykle dny, nikoli hodiny nebo méně. Krátké výpadky proxy serverů tedy nemají významný vliv na ochranu heslem Azure AD.

## <a name="deployment-requirements"></a>Požadavky nasazení

Informace o licencování najdete v tématu požadavky na [licencování azure ad password protection](concept-password-ban-bad.md#license-requirements).

Platí tyto základní požadavky:

* Všechny počítače, včetně řadičů domény, které mají nainstalované součásti Azure AD Password Protection, musí mít nainstalovanou dobu runtime Universal C.
    * Můžete získat runtime tím, že se ujistíte, že máte všechny aktualizace ze služby Windows Update. Nebo jej můžete získat v balíčku aktualizací specifických pro operační systém. Další informace naleznete v [tématu Aktualizace univerzálního prostředí C Runtime v systému Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* K registraci doménové struktury služby Windows Server Active Directory pomocí služby Azure AD potřebujete účet, který má oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury.
* Služba distribuce klíčů musí být povolena ve všech řadičích domény v doméně se systémem Windows Server 2012. Ve výchozím nastavení je tato služba povolena ručním spuštěním aktivační události.
* Síťové připojení musí existovat mezi alespoň jedním řadičem domény v každé doméně a alespoň jedním serverem, který je hostitelem proxy služby pro ochranu heslem Azure AD. Toto připojení musí umožnit řadiči domény přístup k portu mapovače koncových bodů Vzdáleného volání procedur 135 a k portu serveru RPC ve službě proxy.
    * Ve výchozím nastavení je port serveru RPC dynamickým portem vzdáleného volání procedur, ale lze jej nakonfigurovat tak, aby [používal statický port](#static).
* Všechny počítače, kde bude nainstalována služba Proxy ochrany heslem Azure AD, musí mít přístup k síti k následujícím koncovým bodům:

    |**Koncový bod**|**Účel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Požadavky na ověření|
    |`https://enterpriseregistration.windows.net`|Funkce ochrany heslem Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agent řadiče domény ochrany heslem Azure AD

Následující požadavky platí pro agenta řadiče domény ochrany heslem Azure AD:

* Všechny počítače, kde bude nainstalován software agenta řadiče domény ochrany hesel Azure AD, musí být spuštěn windows server 2012 nebo novější.
    * Doména nebo doménová struktura služby Active Directory nemusí být na úrovni funkčnosti domény systému Windows Server 2012 (DFL) nebo úrovně funkčnosti doménové struktury (FFL). Jak je uvedeno v [principech návrhu](concept-password-ban-bad-on-premises.md#design-principles), neexistuje žádné minimální DFL nebo FFL potřebné pro agenta DC nebo proxy software ke spuštění.
* Všechny počítače, které spouštějí agenta řadiče domény ochrany heslem Azure AD, musí mít nainstalovanou rozhraní .NET 4.5.
* Každá doména služby Active Directory, ve které je spuštěna služba agenta řadiče domény ochrany hesel Azure AD, musí pro replikaci sysvol používat replikaci distribuovaného systému souborů (DFSR).
   * Pokud vaše doména ještě nepoužívá DFSR, musíte před instalací ochrany heslem Azure AD migrovat. Další informace naleznete v [příručce k migraci replikace SYSVOL: FRS to DFS Replikace](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Software agenta řadiče domény ochrany hesel Azure AD se v současné době nainstaluje do řadičů domény v doménách, které stále používají FRS (předchůdce technologie DFSR) pro replikaci sysvol, ale software nebude fungovat správně v tomto prostředí.
    >
    > Mezi další negativní vedlejší účinky patří, že se jednotlivé soubory nepodaří replikovat, a postupy obnovení sysvol, které se zdají být úspěšné, ale tiše nedaří replikovat všechny soubory.
    >
    > Migrujte do domény, abyste co nejdříve použili dfsr, a to jak pro vlastní výhody DFSR, tak pro odblokování nasazení ochrany heslem Azure AD. Budoucí verze softwaru budou automaticky zakázány při spuštění v doméně, která stále používá FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Služba proxy azure ad ochrany heslem

Následující požadavky platí pro službu proxy služby Azure AD Password Protection:

* Všechny počítače, kde bude nainstalována služba proxy Azure AD Password Protection, musí spustit Windows Server 2012 R2 nebo novější.

    > [!NOTE]
    > Nasazení služby proxy služby Azure AD Password Protection je povinný požadavek na nasazení ochrany heslem Služby Azure AD, i když řadič domény může mít odchozí přímé připojení k internetu.

* Všechny počítače, kde bude nainstalována služba proxy Azure AD Password Protection, musí mít nainstalovanou .NET 4.7.
    * Síť .NET 4.7 by již měla být nainstalována v plně aktualizovaném systému Windows Server. V případě potřeby stáhněte a spusťte instalační program, který najdete v [offline instalačním programu rozhraní .NET Framework 4.7 pro systém Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Všechny počítače, které jsou hostiteli služby proxy Azure AD Password Protection, musí být nakonfigurovány tak, aby udělovaly řadičům domény možnost přihlásit se ke službě proxy. Tato schopnost je řízena prostřednictvím přiřazení oprávnění Přístup k tomuto počítači ze sítě.
* Všechny počítače, které jsou hostiteli služby proxy azure ad password protection, musí být nakonfigurovány tak, aby umožňovaly odchozí přenosy PROTOKOLU TLS 1.2 HTTP.
* Účet *globálního správce* pro registraci služby proxy azure ad password protection a doménové struktury pomocí Azure AD.
* Přístup k síti musí být povolen pro sadu portů a adres URL zadanou v [postupech nastavení prostředí proxy aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Požadavky aktualizace agenta Microsoft Azure AD Connect Updater

Služba Microsoft Azure AD Connect Agent Updater se instaluje vedle služby Proxy ochrany hesel Azure AD. Aby mohla služba Microsoft Azure AD Connect Agent Updater fungovat, je vyžadována další konfigurace:

* Pokud vaše prostředí používá proxy server HTTP, postupujte podle pokynů uvedených v [části Práce s existujícími místními proxy servery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).
* Služba Microsoft Azure AD Connect Agent Updater také vyžaduje kroky TLS 1.2 zadané v [požadavcích TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

> [!WARNING]
> Proxy proxy azure ad ochrany heslem a proxy aplikace Azure AD nainstalovat různé verze služby Microsoft Azure AD Connect Agent Updater, což je důvod, proč pokyny odkazují na obsah proxy aplikace. Tyto různé verze jsou nekompatibilní při instalaci vedle sebe, takže se nedoporučuje instalovat proxy server Azure AD Password Protection a proxy aplikace ve stejném počítači.

## <a name="download-required-software"></a>Stáhnout požadovaný software

Existují dva požadované instalační programy pro místní nasazení služby Azure AD Password Protection:

* Agent řadiče domény ochrany heslem Azure AD *(AzureADPasswordProtectionDCAgent.msi)*
* Proxy server ochrany hesel azure ad *(AzureADPasswordProtectionProxySetup.exe)*

Stáhněte si oba instalační programy ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instalace a konfigurace služby proxy

Služba proxy azure ad password protection se obvykle používá na členském serveru v místním prostředí služby AD DS. Po instalaci služba proxy Azure AD Password Protection komunikuje se službou Azure AD a udržuje kopii globálních seznamů hesel zakázaných zákazníků pro vašeho klienta Azure AD.

V další části nainstalujete agenty řadiče domény ochrany heslem Azure AD do řadičů domény v místním prostředí služby AD DS. Tito agenti řadiče domény komunikují se službou proxy, aby získali nejnovější seznamy zakázaných hesel pro použití při zpracování událostí změny hesla v doméně.

Zvolte jeden nebo více serverů, které budou hostovat proxy službu Azure AD Password Protection. Následující úvahy platí pro servery):

* Každá taková služba může poskytovat zásady hesel pouze pro jednu doménovou strukturu. Hostitelský počítač musí být připojen k doméně v této doménové struktuře. Kořenové i podřízené domény jsou podporovány. Potřebujete síťové připojení mezi alespoň jedním řadičem domény v každé doméně doménové struktury a počítačem pro ochranu heslem.
* Službu proxy služby Azure AD Password Protection můžete spustit na řadiči domény pro testování, ale tento řadič domény pak vyžaduje připojení k internetu. Toto připojení může být problém zabezpečení. Tuto konfiguraci doporučujeme pouze pro testování.
* Doporučujeme alespoň dva proxy servery Azure AD Password Protection pro redundanci, jak je uvedeno v předchozí části [o vysoké dostupnosti aspekty](#high-availability-considerations).
* Není podporováno pro spuštění služby proxy azure ad password protection na řadiči domény jen pro čtení.

Chcete-li nainstalovat službu proxy služby Azure AD Password Protection, proveďte následující kroky:

1. Chcete-li nainstalovat službu proxy služby `AzureADPasswordProtectionProxySetup.exe` Azure AD Password Protection, spusťte instalační program softwaru.

    Instalace softwaru nevyžaduje restartování a může být automatizována pomocí standardních postupů MSI, jako v následujícím příkladu:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Před instalací `AzureADPasswordProtectionProxySetup.exe` balíčku musí být spuštěna služba Brána Windows Firewall, aby nedošlo k chybě instalace.
    >
    > Pokud je brána Windows Firewall nakonfigurována tak, aby se nespouštěla, je toto řešení dočasně povolit a spustit službu Firewall během instalace. Proxy software nemá po instalaci žádnou specifickou závislost na bráně Windows Firewall.
    >
    > Pokud používáte bránu firewall jiného výrobce, musí být stále nakonfigurovántak, aby splňoval požadavky na nasazení. Patří mezi ně povolení příchozího přístupu k portu 135 a portu serveru RPC serveru proxy. Další informace naleznete v předchozí části o [požadavcích](#deployment-requirements)na nasazení .

1. Software proxy Azure AD Password Protection obsahuje `AzureADPasswordProtection`nový modul PowerShellu . Následující kroky spustit různé rutiny z tohoto modulu Prostředí PowerShell.

    Chcete-li použít tento modul, otevřete okno Prostředí PowerShell jako správce a importujte nový modul následujícím způsobem:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Chcete-li zkontrolovat, zda je spuštěná služba proxy služby Azure AD Password Protection, použijte následující příkaz Prostředí PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Výsledek by měl zobrazit **stav** *běhu*.

1. Služba proxy je spuštěna v počítači, ale nemá pověření pro komunikaci s Azure AD. Zaregistrujte proxy server Azure AD Password Protection `Register-AzureADPasswordProtectionProxy` pomocí služby Azure AD pomocí rutiny.

    Tato rutina vyžaduje pověření globálního správce pro vašeho tenanta Azure. Potřebujete také místní oprávnění správce domény služby Active Directory v kořenové doméně doménové struktury. Tuto rutinu je nutné spustit také pomocí účtu s oprávněními místního správce:

    Poté, co tento příkaz uspěje jednou pro službu proxy azure ad password protection, další vyvolání je úspěšné, ale jsou zbytečné.

    Rutina `Register-AzureADPasswordProtectionProxy` podporuje následující tři režimy ověřování. První dva režimy podporují Azure Multi-Factor Authentication, ale třetí režim není.

    > [!TIP]
    > Může být znatelné zpoždění před dokončením poprvé, že tato rutina je spuštěna pro konkrétního klienta Azure. Pokud není hlášena chyba, nemusíte se obávat tohoto zpoždění.

     * Režim interaktivního ověřování:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Tento režim nefunguje v operačních systémech Jádra serveru. Místo toho použijte jeden z následujících režimů ověřování. Tento režim může také selhat, pokud je povolena konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Řešení je zakázat tuto konfiguraci, zaregistrovat proxy server a znovu jej povolit.

     * Režim ověřování kódu zařízení:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po zobrazení výzvy otevřete pomocí odkazu webový prohlížeč a zadejte ověřovací kód.

     * Tichý (heslo-based) režim ověřování:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdaří, pokud azure vícefaktorové ověřování je vyžadováno pro váš účet. V takovém případě použijte jeden z předchozích dvou režimů ověřování nebo místo toho použijte jiný účet, který nevyžaduje vícefaktorové ověřování.
        >
        > MFA se může také zobrazit v ynažech, pokud registrace zařízení Azure (která se používá pod kryty službou Azure AD Password Protection) byla nakonfigurovaná globálně vyžadovat vícefaktorové ověřování. Chcete-li tento požadavek vyřešit, můžete použít jiný účet, který podporuje vícefaktorové ověřování s jedním z předchozích dvou režimů ověřování, nebo můžete také dočasně uvolnit požadavek MFA registrace zařízení Azure.
        >
        > Pokud chcete tuto změnu provést, vyhledejte a vyberte **Azure Active Directory** na webu Azure Portal a pak vyberte Zařízení > Nastavení **zařízení**. Nastavit **vyžadovat vícefaktorové ukazovátko pro připojení zařízení** k *ne*. Ujistěte se, že překonfigurovat toto nastavení zpět na *Ano* po dokončení registrace.
        >
        > Doporučujeme, aby požadavky mfa být vynechány pouze pro testovací účely.

    V současné době není nutné zadat *parametr -ForestCredential,* který je vyhrazen pro budoucí funkce.

    Registrace služby proxy Azure AD Password Protection je nezbytná pouze jednou za dobu životnosti služby. Poté služba proxy služby Azure AD Password Protection automaticky provede jakoukoli další nezbytnou údržbu.

1. Teď zaregistrujte místní doménovou strukturu služby Active Directory s `Register-AzureADPasswordProtectionForest` potřebnými přihlašovacími údaji pro komunikaci s Azure pomocí rutiny prostředí PowerShell.

    > [!NOTE]
    > Pokud je ve vašem prostředí nainstalováno více proxy serverů Azure AD Password Protection, nezáleží na tom, který proxy server používáte k registraci doménové struktury.

    Rutina vyžaduje pověření globálního správce pro vašeho tenanta Azure. Tuto rutinu je také nutné spustit pomocí účtu s oprávněními místního správce. Vyžaduje také místní oprávnění správce služby Active Directory Enterprise Administrator. Tento krok je spuštěn jednou za doménovou strukturu.

    Rutina `Register-AzureADPasswordProtectionForest` podporuje následující tři režimy ověřování. První dva režimy podporují Azure Multi-Factor Authentication, ale třetí režim není.

    > [!TIP]
    > Může být znatelné zpoždění před dokončením poprvé, že tato rutina je spuštěna pro konkrétního klienta Azure. Pokud není hlášena chyba, nemusíte se obávat tohoto zpoždění.

     * Režim interaktivního ověřování:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Tento režim nebude fungovat v operačních systémech Jádra serveru. Místo toho použijte jeden z následujících dvou režimů ověřování. Tento režim může také selhat, pokud je povolena konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Řešení je zakázat tuto konfiguraci, zaregistrovat doménovou strukturu a znovu ji povolit.  

     * Režim ověřování kódu zařízení:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po zobrazení výzvy otevřete pomocí odkazu webový prohlížeč a zadejte ověřovací kód.

     * Tichý (heslo-based) režim ověřování:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Tento režim se nezdaří, pokud azure vícefaktorové ověřování je vyžadováno pro váš účet. V takovém případě použijte jeden z předchozích dvou režimů ověřování nebo místo toho použijte jiný účet, který nevyžaduje vícefaktorové ověřování.
        >
        > MFA se může také zobrazit v ynažech, pokud registrace zařízení Azure (která se používá pod kryty službou Azure AD Password Protection) byla nakonfigurovaná globálně vyžadovat vícefaktorové ověřování. Chcete-li tento požadavek vyřešit, můžete použít jiný účet, který podporuje vícefaktorové ověřování s jedním z předchozích dvou režimů ověřování, nebo můžete také dočasně uvolnit požadavek MFA registrace zařízení Azure.
        >
        > Pokud chcete tuto změnu provést, vyhledejte a vyberte **Azure Active Directory** na webu Azure Portal a pak vyberte Zařízení > Nastavení **zařízení**. Nastavit **vyžadovat vícefaktorové ukazovátko pro připojení zařízení** k *ne*. Ujistěte se, že překonfigurovat toto nastavení zpět na *Ano* po dokončení registrace.
        >
        > Doporučujeme, aby požadavky mfa být vynechány pouze pro testovací účely.

       Tyto příklady jsou úspěšné pouze v případě, že aktuálně přihlášený uživatel je také správcem domény služby Active Directory pro kořenovou doménu. Pokud tomu tak není, můžete zadat alternativní pověření domény prostřednictvím parametru *-ForestCredential.*

    Registrace doménové struktury služby Active Directory je nutná pouze jednou za dobu životnosti doménové struktury. Poté agenti řadiče domény ochrany heslem Azure AD v doménové struktuře automaticky provádějí další nezbytnou údržbu. Po `Register-AzureADPasswordProtectionForest` úspěšném spuštění pro doménovou strukturu, další vyvolání rutiny úspěšné, ale jsou zbytečné.
    
    Aby `Register-AzureADPasswordProtectionForest` bylo možné uspět, musí být v doméně proxy serveru Azure AD Password Protection k dispozici alespoň jeden řadič domény se systémem Windows Server 2012 nebo novějším. Před tímto krokem není nutné nainstalovaný na žádném řadiči domény azure ad password protection.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurace služby proxy pro komunikaci prostřednictvím serveru proxy HTTP

Pokud vaše prostředí vyžaduje použití konkrétního http proxy pro komunikaci s Azure, použijte následující kroky ke konfiguraci služby Azure AD Password Protection.

Vytvořte soubor *AzureADPasswordProtectionProxy.exe.config* ve `%ProgramFiles%\Azure AD Password Protection Proxy\Service` složce. Uveďte následující obsah:

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

Pokud proxy server HTTP vyžaduje ověření, přidejte značku *useDefaultCredentials:*

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

V obou případech `http://yourhttpproxy.com:8080` nahraďte adresou a portem konkrétního serveru HTTP proxy.

Pokud je proxy server HTTP nakonfigurován tak, aby používal zásady autorizace, je nutné udělit přístup k účtu počítače služby Active Directory počítače, který je hostitelem služby proxy, pro ochranu heslem.

Doporučujeme zastavit a restartovat službu proxy služby Azure AD Password Protection po vytvoření nebo aktualizaci souboru *AzureADPasswordProtectionProxy.exe.config.*

Služba proxy nepodporuje použití určitých pověření pro připojení k proxy serveru HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Konfigurace služby proxy pro naslouchání na konkrétním portu

Software agenta řadiče domény ochrany hesel Azure AD používá ke komunikaci se službou proxy protokol RPC přes Protokol TCP. Ve výchozím nastavení služba proxy služby Azure AD Password Protection naslouchá na libovolném dostupném dynamickém koncovém bodu Vzdáleného volání procedur. Službu můžete nakonfigurovat tak, aby naslouchala na konkrétním portu TCP, v případě potřeby z důvodu síťové topologie nebo požadavků na bránu firewall ve vašem prostředí.

<a id="static" /></a>Chcete-li nakonfigurovat spuštění služby pod `Set-AzureADPasswordProtectionProxyConfiguration` statickým portem, použijte rutinu následujícím způsobem:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Tyto změny se projeví až po zastavení a restartování proxy služby Azure AD Password Protection.

Chcete-li službu nakonfigurovat tak, aby byla spuštěna pod dynamickým portem, použijte stejný postup, ale nastavte *staticport* zpět na nulu:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Tyto změny se projeví až po zastavení a restartování proxy služby Azure AD Password Protection.

Služba proxy azure ad password protection vyžaduje ruční restartování po jakékoli změně konfigurace portu. Po provádění těchto změn konfigurace není nutné restartovat službu agenta řadiče domény Azure AD Password Protection DC v řadičích domény.

Chcete-li zadat dotaz na aktuální `Get-AzureADPasswordProtectionProxyConfiguration` konfiguraci služby, použijte rutinu, jak je znázorněno v následujícím příkladu

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

Chcete-li nainstalovat službu agenta řadiče `AzureADPasswordProtectionDCAgentSetup.msi` domény ochrany hesel Azure AD, spusťte balíček.

Instalaci softwaru můžete automatizovat pomocí standardních postupů MSI, jak je znázorněno v následujícím příkladu:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Příznak `/norestart` může být vynechán, pokud dáváte přednost instalačnímu programu automaticky restartovat počítač.

Instalace softwaru nebo odinstalace vyžaduje restartování. Tento požadavek je, protože knihovny DLL filtru hesel jsou načteny nebo uvolněny pouze restartováním.

Instalace on-prem Azure AD Password Protection je dokončena po nainstalován software agenta řadiče domény na řadič i počítač restartován. Žádná jiná konfigurace není vyžadována ani možná. Události změny hesla proti řadičům domény on-prem používají nakonfigurované seznamy zakázaných hesel z Azure AD.

Pokud chcete povolit ochranu heslem Azure AD na webu Azure Portal nebo nakonfigurovat vlastní zakázaná hesla, přečtěte si informace [o povolení místní ochrany heslem Azure AD](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Agenta řadiče domény ochrany heslem Azure AD můžete nainstalovat do počítače, který ještě není řadičem domény. V takovém případě se služba spustí a spustí, ale zůstane neaktivní, dokud není počítač povýšen na řadič domény.

## <a name="upgrading-the-proxy-service"></a>Inovace služby proxy

Služba proxy azure ad password protection podporuje automatický upgrade. Automatický upgrade používá službu Microsoft Azure AD Connect Agent Updater, která je nainstalována vedle služby proxy. Automatický upgrade je ve výchozím nastavení zapnutý a `Set-AzureADPasswordProtectionProxyConfiguration` může být pomocí rutiny povolen nebo zakázán.

Aktuální nastavení lze dotazovat `Get-AzureADPasswordProtectionProxyConfiguration` pomocí rutiny. Doporučujeme, aby bylo vždy povoleno nastavení automatického upgradu.

Rutina `Get-AzureADPasswordProtectionProxy` může být použita k dotazování verze softwaru všech aktuálně nainstalovaných proxy serverů Azure AD Password Protection v doménové struktuře.

### <a name="manual-upgrade-process"></a>Proces ručního upgradu

Ruční upgrade se provádí spuštěním nejnovější `AzureADPasswordProtectionProxySetup.exe` verze instalačního programu softwaru. Nejnovější verze softwaru je k dispozici na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

Není nutné odinstalovat aktuální verzi proxy služby Azure AD Password Protection – instalační program provede upgrade na místě. Při upgradu služby proxy by nemělo být vyžadováno žádné restartování. Upgrade softwaru může být automatizován pomocí standardních postupů MSI, například `AzureADPasswordProtectionProxySetup.exe /quiet`.

## <a name="upgrading-the-dc-agent"></a>Upgrade agenta řadiče domény

Když je k dispozici novější verze softwaru agenta řadiče domény ochrany heslem Azure `AzureADPasswordProtectionDCAgentSetup.msi` AD, upgrade se provádí spuštěním nejnovější verze softwarového balíčku. Nejnovější verze softwaru je k dispozici na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

Není nutné odinstalovat aktuální verzi softwaru agenta řadiče domény - instalační program provádí upgrade na místě. Při upgradu softwaru agenta řadiče domény je vždy nutné restartovat počítač – tento požadavek je způsoben základním chováním systému Windows.

Upgrade softwaru může být automatizován pomocí standardních postupů MSI, například `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Příznak můžete vynechat, `/norestart` pokud dáváte přednost automatickému restartování počítače instalačním programem.

Rutina `Get-AzureADPasswordProtectionDCAgent` může být použita k dotazování verze softwaru všech aktuálně nainstalovaných agentů řadiče domény ochrany heslem Azure AD v doménové struktuře.

## <a name="next-steps"></a>Další kroky

Teď, když jste nainstalovali služby, které potřebujete pro azure ad ochranu heslem na místních serverech, [povolte on-prem Azure AD Password Protection na webu Azure Portal](howto-password-ban-bad-on-premises-operations.md) k dokončení nasazení.
