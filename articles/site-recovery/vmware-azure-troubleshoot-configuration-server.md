---
title: Řešení potíží s konfiguračním serverem během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro nasazení konfiguračního serveru pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: ab72091c58420459620352c8169773111149316d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245724"
---
# <a name="troubleshoot-configuration-server-issues"></a>Řešení potíží s konfigurací serveru

Tento článek vám pomůže vyřešit problémy při nasazení a správa [Azure Site Recovery](site-recovery-overview.md) konfiguračního serveru. Konfigurační server funguje jako server pro správu. Nastavení zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí Site Recovery pomocí konfiguračního serveru. Následující části popisují nejběžnějších chyb, které se mohou vyskytnout při přidání nové konfigurační server a při správě konfiguračního serveru.

## <a name="registration-failures"></a>Selhání registrace

Zdrojový počítač zaregistruje s konfiguračním serverem při instalaci agenta mobility. Během tohoto kroku můžete ladit všechny chyby podle následujících pokynů:

1. Otevřete soubor C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (Že složka ProgramData může být skrytá složka. Pokud nevidíte složka ProgramData, v Průzkumníku souborů na **zobrazení** kartě **zobrazit/skrýt** vyberte **skryté položky** zaškrtávací políčko.) Selhání může být způsobeno více problémů.

2. Vyhledejte řetězec **ne platnou IP adresu najít**. Pokud je nalezen řetězec:
    1. Ověřte, že ID požadovaného hostitele je stejný jako ID hostitele zdrojového počítače.
    2. Ověřte, zda zdrojový počítač má alespoň jednu IP adresu přiřadit fyzický síťový adaptér. Pro registraci agenta s konfiguračním serverem proběhla úspěšně zdrojový počítač musí mít aspoň jeden platný v4 přiřazenou IP adresu na fyzický síťový adaptér.
    3. Na zdrojovém počítači zobrazíte všechny IP adresy zdrojového počítače spusťte jeden z následujících příkazů:
      - Pro Windows: `> ipconfig /all`
      - Pro Linux: `# ifconfig -a`

3. Pokud řetězec **ne platnou IP adresu najít** nenajde, vyhledejte řetězec **důvod = > hodnotu NULL**. K této chybě dochází, pokud zdrojový počítač používá prázdný hostitele zaregistrovat u konfiguračního serveru. Pokud je nalezen řetězec:
    - Až vyřešíte problémy, postupujte podle pokynů v [zdrojový počítač zaregistrovat u konfiguračního serveru](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) pokus o registraci ručně.

4. Pokud řetězec **důvod = > hodnotu NULL** nebyl nalezen na zdrojovém počítači, otevřete soubor C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Že složka ProgramData může být skrytá složka. Pokud nevidíte složka ProgramData, v Průzkumníku souborů na **zobrazení** kartě **zobrazit/skrýt** vyberte **skryté položky** zaškrtávací políčko.) Selhání může být způsobeno více problémů. 

5. Vyhledejte řetězec **odeslání požadavku: (7): Nelze se připojit k serveru**. Pokud je nalezen řetězec:
    1. Vyřešte problémy sítě mezi zdrojovým počítačem a konfiguračním serverem. Ověřte, zda je konfigurační server dostupný ze zdrojového počítače pomocí sítě nástroje, jako je ping, traceroute nebo ve webovém prohlížeči. Ujistěte se, že zdrojový počítač může dosáhnout přes port 443 konfiguračního serveru.
    2. Zkontrolujte, zda všechny brány firewall pravidla na zdrojový počítač blok připojení mezi zdrojovým počítačem a konfiguračním serverem. Práce s správcům sítě odblokovat jakékoli problémy s připojením.
    3. Ujistěte se, že složek v [Site Recovery vyloučení složek z antivirové programy](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) jsou vyloučené z antivirového softwaru.
    4. Po vyřešení problémů se sítí se zkuste registraci tak, že následující pokyny z [zaregistrovat zdrojový počítač s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Pokud řetězec **odeslání požadavku: (7): Nelze se připojit k serveru** není nalezen, v rámci jednoho souboru protokolu vyhledejte řetězec **žádosti: (60) - peer certifikát nelze ověřit pomocí dané certifikáty certifikační Autority**. K této chybě může dojít, protože vypršela platnost certifikátu serveru konfigurace nebo zdrojový počítač nepodporuje protokol TLS 1.0 nebo novější protokoly SSL. Je také může dojít, pokud brána firewall blokuje komunikaci prostřednictvím protokolu SSL mezi zdrojovým počítačem a konfiguračním serverem. Pokud je nalezen řetězec: 
    1. Pokud chcete vyřešit, připojení ke konfiguračnímu serveru IP adresu pomocí webového prohlížeče na zdrojovém počítači. Používání identifikátoru URI protokolu https:\/\/< IP adresa konfiguračního serveru\>: 443 /. Ujistěte se, že zdrojový počítač může dosáhnout přes port 443 konfiguračního serveru.
    2. Zkontrolujte, jestli všechna pravidla brány firewall na zdrojovém počítači je potřeba přidat nebo odebrat u tohoto zdrojového počítače komunikovat s konfiguračním serverem. Z důvodu nejrůznějších software brány firewall, která může být používán jsme nejde vypsat všechny brány firewall na požadované konfigurace. Práce s správcům sítě odblokovat jakékoli problémy s připojením.
    3. Ujistěte se, že složek v [Site Recovery vyloučení složek z antivirové programy](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) jsou vyloučené z antivirového softwaru.  
    4. Až problémy vyřešíte, zkuste registraci tak, že následující pokyny v [zaregistrovat zdrojový počítač s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. V Linuxu Pokud hodnota platformy v < INSTALLATION_DIR\>/etc/drscout.conf je poškozený, registrace selže. Pokud chcete tento problém identifikovat, otevřete soubor /var/log/ua_install.log. Vyhledejte řetězec **přerušení konfigurace jako VM_PLATFORM hodnota je null nebo je není VmWare/Azure**. Platforma by měl být nastaven na hodnotu **VmWare** nebo **Azure**. Pokud soubor drscout.conf je poškozený, doporučujeme vám [odinstalujte agenta mobility](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) a poté znovu nainstalujte agenta mobility. Pokud odinstalace narazí, proveďte následující kroky:
    1. Otevřete soubor Installation_Directory/uninstall.sh a Odkomentujte volání **StartServices** funkce.
    2. Otevřete soubor Installation_Directory/Vx/bin/uninstall.sh a Odkomentujte volání **stop_services** funkce.
    3. Otevřete soubor Installation_Directory/Fx/uninstall.sh a Odkomentujte celý oddíl, který se pokouší zastavit službu Fx.
    4. [Odinstalujte](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) agenta mobility. Po úspěšné odinstalaci, restartujte počítač systému a poté přeinstalujte agenta mobility.

## <a name="installation-failure-failed-to-load-accounts"></a>Instalace se nezdařila: Nepovedlo se načíst účty.

Tato chyba nastane, pokud služba nelze číst data z přenosového připojení, když je instalace agenta mobility a registrace konfiguračního serveru. Chcete-li vyřešit tento problém, povolte protokol TLS 1.0 je na zdrojovém počítači.

## <a name="vcenter-discovery-failures"></a>selhání rozpoznávání vCenter

Chcete-li vyřešit chyby zjišťování serveru vCenter, přidání serveru vCenter s nastavením proxy seznamu jednorázové přihlášení. 

- Stáhněte si nástroj PsExec z [tady](https://aka.ms/PsExec) pro přístup k obsahu pro uživatele systému.
- Spusťte aplikaci Internet Explorer v systému uživatele obsahu spuštěním následujícího příkazového řádku psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Přidat nastavení proxy serveru v Internet Exploreru a restartujte službu tmanssvc.
- Konfigurace nastavení proxy serveru DRA, spuštěním cd C:\Program Files\Microsoft Azure Site Recovery Provider
- V dalším kroku spusťte DRCONFIGURATOR. Soubor EXE / configure /AddBypassUrls [přidat IP adresu nebo plně kvalifikovaný název domény systému vCenter Server k dispozici během **konfigurovat vCenter serveru nebo serveru vSphere ESXi** kroku [nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Změňte IP adresu konfiguračního serveru

Důrazně doporučujeme, že nechcete změnit IP adresu konfiguračního serveru. Ujistěte se, že všechny IP adresy, které jsou přiřazeny ke konfiguračnímu serveru jsou statické IP adresy. Nepoužívejte IP adresy služby DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token je neplatný.

Lze vyvarovat této chyby, zkontrolujte, jestli, který čas systémových hodin se liší od místního času o více než 15 minut. Znovu spusťte instalační program a dokončete registraci.

## <a name="failed-to-create-a-certificate"></a>Nepovedlo se vytvořit certifikát

Nelze vytvořit certifikát, který má vyžadovaný k ověření v Site Recovery. Po zajištění, že instalační program spouštíte jako místní správce. Spusťte znovu instalační program.

## <a name="failure-to-activate-windows-licence-from-server-standard-evaluation-to-server-standard"></a>Nepodařilo se aktivovat licenci Windows ze standardní zkušební verze serveru na Server Standard

1. Jako součást nasazení konfiguračního serveru pomocí OVF se používá vyhodnocení licence platí po dobu 180 dnů. Potřebujete aktivovat tuto licenci předtím, než to vyprší. Jinak to mít za následek časté vypnutí konfiguračního serveru a hinderance replikace aktivitám, čímž vznikne.
2. Pokud se nemůžete aktivovat licenci Windows, kontaktujte [tým podpory Windows](https://aka.ms/Windows_Support) k vyřešení daného problému.

## <a name="register-source-machine-with-configuration-server"></a>Zdrojový počítač zaregistrovat u konfiguračního serveru

### <a name="if-the-source-machine-runs-windows"></a>Pokud na zdrojovém počítači běží Windows

Na zdrojovém počítači spusťte následující příkaz:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Nastavení | Podrobnosti
--- | ---
Využití | / Csendpoint UnifiedAgentConfigurator.exe < IP adresa konfiguračního serveru \> /passphrasefilepath < cesta k souboru heslo\>
Protokoly konfigurace agenta | Přidávaném % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
/PassphraseFilePath |  Povinné. Umístění přístupové heslo. Použijte libovolný platný název UNC nebo místní cesta k souboru.

### <a name="if-the-source-machine-runs-linux"></a>Pokud na zdrojovém počítači běží Linux

Na zdrojovém počítači spusťte následující příkaz:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Nastavení | Podrobnosti
--- | ---
Využití | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh – i < IP adresa konfiguračního serveru\> - P < cesta k souboru heslo\>
-i | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
-P |  Povinné. Úplný soubor cestu k souboru, ve kterém se uloží přístupové heslo. Použijte libovolnou platnou složku.

## <a name="unable-to-configure-the-configuration-server"></a>Nejde nakonfigurovat konfiguračního serveru

Pokud nainstalujete aplikace než konfigurační server na virtuálním počítači, může nejde nakonfigurovat na hlavním cíli. 

Konfigurační server musí být server jedinému účelu a použití jako sdílený server není podporován. 

Další informace najdete v tématu Nejčastější dotazy týkající se konfigurace v [nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Odebrat zastaralé položky chráněných položek z databáze konfigurace serveru 

K odebrání zastaralých chráněného počítače na konfiguračním serveru, použijte následující kroky. 
 
1. Chcete-li zjistit zdrojovým počítačem a IP adresu zastaralý záznam: 

    1. Otevřete příkazový řádek MYSQL v režimu správce. 
    2. Spusťte následující příkazy. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Vrátí seznam registrovaných počítačů spolu s jejich IP adresy a poslední prezenční signál. Najdete hostitele, který má zastaralé replikační páry.

2. Otevřete příkazový řádek se zvýšenými oprávněními a přejděte do C:\ProgramData\ASR\home\svsystems\bin. 
4. Odebrání registrovaných hostitele podrobnosti a informace o zastaralý záznam z konfiguračního serveru, spusťte následující příkaz pomocí zdrojovým počítačem a IP adresu zastaralé položky. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Pokud máte záznam serveru zdroj "OnPrem VM01" s IP adresu z 10.0.0.4 následně místo toho použijte následující příkaz.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Restartuje následující služby na zdrojovém počítači zaregistrujte s konfiguračním serverem. 
 
    - InMage Scout Application Service
    - Nástroje InMage Scout VX Agent – Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Upgrade selže, když dojde k selhání služeb se zastavit

Konfigurace serveru upgrade selže při některých služeb nezastaví. 

Chcete-li identifikovat problém, přejděte na C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile na konfiguračním serveru. Pokud najdete následující chyby, použijte následující postup k vyřešení daného problému: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Řešení tohoto problému:

Ručně zastavte tyto služby:

- cxprocessserver
- Nástroje InMage Scout VX Agent – Sentinel/Outpost, 
- Agent Microsoft Azure Recovery Services, 
- Služba Microsoft Azure Site Recovery 
- tmansvc
  
Chcete-li aktualizovat konfigurační server, spusťte [sjednocené instalace](service-updates-how-to.md#links-to-currently-supported-update-rollups) znovu.

## <a name="azure-active-directory-application-creation-failure"></a>Selhání vytvoření aplikace služby Azure Active Directory

Nemáte dostatečná oprávnění k vytvoření aplikace pomocí Azure Active Directory (AAD) [otevřít virtualizace aplikace OVA ()](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) šablony.

Chcete-li vyřešit tento problém, přihlaste se k webu Azure portal a proveďte jednu z následujících akcí:

- Žádost o roli pro vývojáře aplikací v AAD. Další informace o roli pro vývojáře aplikací, najdete v části [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Ověřte, že **uživatele můžete vytvořit aplikaci** příznak je nastaven na *true* v adresáři AAD. Další informace najdete v tématu [jak: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Proces server nebo hlavní cíl se nemůže komunikovat s konfiguračním serverem 

Procesový server (Powershell) a moduly hlavní cíl (MT) se nemůže komunikovat s konfiguračním serverem (CS) a jejich stav se zobrazuje jako nepřipojený na webu Azure portal.

Obvykle je to z důvodu chyby s portem 443. Pomocí následujících kroků a odblokovat port a znovu povolit komunikaci s CS.

**Ověřte, že hlavní cílový agent vyvolá agenta MARS**

Pokud chcete ověřit, že hlavního cílového agenta můžete vytvořit relaci protokolu TCP pro IP adresa konfiguračního serveru, vyhledejte zobrazí trasování podobné následujícímu v protokolech agenta hlavní cílový:

TCP <Replace IP with CS IP here>:52739 <Replace IP with CS IP here>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT / / nahraďte IP adresa se tady CS IP

Pokud zjistíte trasování podobné následujícímu v protokolech agenta MT, MT Agent hlásí chyby na portu 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
K této chybě může při dalších aplikací se rovněž na portu 443, nebo z důvodu nastavení brány firewall blokuje port došlo k.

Řešení tohoto problému:

- Ověřte, že port 443 není blokovaný bránou firewall.
- Jestliže port nedostupný z důvodu jiná aplikace pomocí tohoto portu, zastavit a odinstalovat aplikaci.
  - Pokud se zastavuje se aplikace není proveditelné, nastavte nové čisté CS.
- Konfigurační server restartujte.
- Restartujte službu IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurační server není připojený z důvodu nesprávných zadání UUID

Této chybě může dojít, pokud existuje více položek UUID instance konfigurační server (CS) v databázi. K problému dochází často při klonování virtuálního počítače konfiguračního serveru.

Řešení tohoto problému:

1. Odeberte zastaralé/old CS virtuálního počítače ze serveru vCenter. Další informace najdete v tématu [odebrání serverů a zakázání ochrany](site-recovery-manage-registration-and-protection.md).
2. Přihlaste se ke konfiguračnímu serveru virtuálního počítače a připojení k databázi MySQL svsdb1. 
3. Spusťte následující dotaz:

    > [!IMPORTANT]
    >
    > Ověřte, že zadáváte podrobnosti UUID naklonované konfiguračního serveru nebo zastaralý záznam konfiguračního serveru, který se už používá k ochraně virtuálních počítačů. Zadání nesprávný identifikátor UUID bude výsledkem ztráty informací pro všechny stávající chráněné položky.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Aktualizace stránky portálu.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Znaménko nekonečné smyčky vyvolá se při zadávání přihlašovacích údajů

Po zadání správného uživatelského jména a hesla na konfiguračním serveru OVF, přihlášení Azure nadále výzvu k zadání správné přihlašovací údaje.

Tomuto problému může dojít, když systémového času je nesprávné.

Řešení tohoto problému:

Nastavte správný čas v počítači a opakujte přihlášení. 
 