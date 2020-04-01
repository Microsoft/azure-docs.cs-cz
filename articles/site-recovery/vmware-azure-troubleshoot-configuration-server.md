---
title: Řešení problémů s konfiguračním serverem během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o řešení potíží pro nasazení konfiguračního serveru pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 0383a512dfb7c2bb1ae2422b9ade1e3c7387a70c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478302"
---
# <a name="troubleshoot-configuration-server-issues"></a>Řešení potíží s konfiguračním serverem

Tento článek vám pomůže vyřešit problémy při nasazení a správě konfiguračního serveru [Azure Site Recovery.](site-recovery-overview.md) Konfigurační server funguje jako server pro správu. Pomocí konfiguračního serveru můžete nastavit obnovení po havárii místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí site recovery. Následující části popisují nejběžnější chyby, ke kterým může dojít při přidání nového konfiguračního serveru a při správě konfiguračního serveru.

## <a name="registration-failures"></a>Selhání registrace

Zdrojový počítač se zaregistruje u konfiguračního serveru při instalaci agenta mobility. Během tohoto kroku můžete ladit všechny chyby podle následujících pokynů:

1. Otevřete soubor C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (Složka ProgramData může být skrytá složka. Pokud složku ProgramData nevidíte, v Průzkumníkovi souborů na kartě **Zobrazení** zaškrtněte políčko **Zobrazit nebo skrýt** políčko **Skryté položky.)** Chyby mohou být způsobeny více problémy.

2. Vyhledejte řetězec **Nebyla nalezena žádná platná adresa IP**. Pokud je řetězec nalezen:
   1. Ověřte, zda je požadované ID hostitele stejné jako ID hostitele zdrojového počítače.
   2. Ověřte, zda má zdrojový počítač alespoň jednu adresu IP přiřazenou fyzické nic. Aby byla registrace agenta s konfiguračním serverem úspěšná, musí mít zdrojový počítač alespoň jednu platnou adresu IP v4 přiřazenou fyzické nic.
   3. Spusťte jeden z následujících příkazů ve zdrojovém počítači, abyste získali všechny ADRESY IP zdrojového počítače:
      - Pro Windows:`> ipconfig /all`
      - Pro Linux:`# ifconfig -a`

3. Pokud nebyl nalezen řetězec **Žádná platná adresa IP,** vyhledejte řetězec **Reason=>NULL**. K této chybě dochází, pokud zdrojový počítač používá k registraci u konfiguračního serveru prázdného hostitele. Pokud je řetězec nalezen:
    - Po vyřešení problémů postupujte podle pokynů v [části Registrace zdrojového počítače s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) a zopakujte registraci ručně.

4. Pokud řetězec **Reason=>NULL** není nalezen, otevřete ve zdrojovém počítači soubor C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Složka ProgramData může být skrytá složka. Pokud složku ProgramData nevidíte, v Průzkumníkovi souborů na kartě **Zobrazení** zaškrtněte políčko **Zobrazit nebo skrýt** políčko **Skryté položky.)** Chyby mohou být způsobeny více problémy. 

5. Vyhledejte požadavek na řetězec **příspěvku: (7) - Nelze se připojit k serveru**. Pokud je řetězec nalezen:
    1. Vyřešte problémy se sítí mezi zdrojovým počítačem a konfiguračním serverem. Ověřte, zda je konfigurační server dostupný ze zdrojového počítače pomocí síťových nástrojů, jako je ping, traceroute nebo webový prohlížeč. Ujistěte se, že zdrojový počítač může dosáhnout konfiguračního serveru prostřednictvím portu 443.
    2. Zkontrolujte, zda všechna pravidla brány firewall ve zdrojovém počítači blokují spojení mezi zdrojovým počítačem a konfiguračním serverem. Spolupracujte se správci sítě a odblokujte případné problémy s připojením.
    3. Ujistěte se, že složky uvedené v [seznamu Vyloučení složky Site Recovery z antivirových programů](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) jsou vyloučeny z antivirového softwaru.
    4. Po vyřešení problémů se sítí opakujte registraci podle pokynů v [seznamu Registrovat zdrojový počítač s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Pokud požadavek na řetězec **příspěvku: (7) - Nelze se připojit k serveru** nebyl nalezen, vyhledejte ve stejném souboru protokolu požadavek na **řetězec: (60) - Peer certifikát nelze ověřit pomocí daných certifikátů certifikační autority**. K této chybě může dojít, protože vypršela platnost certifikátu konfiguračního serveru nebo zdrojový počítač nepodporuje protokoly TLS 1.0 nebo novější. Může také dojít, pokud brána firewall blokuje komunikaci TLS mezi zdrojovým počítačem a konfiguračním serverem. Pokud je řetězec nalezen: 
    1. Chcete-li to vyřešit, připojte se k adrese IP konfiguračního serveru pomocí webového prohlížeče ve zdrojovém počítači. Použijte identifikátor URI\/ \/ https:<\>IP adresu konfiguračního serveru :443/. Ujistěte se, že zdrojový počítač může dosáhnout konfiguračního serveru prostřednictvím portu 443.
    2. Zkontrolujte, zda je třeba přidat nebo odebrat všechna pravidla brány firewall ve zdrojovém počítači, aby zdrojový počítač mohl hovořit s konfiguračním serverem. Vzhledem k rozmanitosti softwaru brány firewall, který může být používán, nemůžeme uvést všechny požadované konfigurace brány firewall. Spolupracujte se správci sítě a odblokujte případné problémy s připojením.
    3. Ujistěte se, že složky uvedené v [seznamu Vyloučení složky Site Recovery z antivirových programů](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) jsou vyloučeny z antivirového softwaru.  
    4. Po vyřešení problémů opakujte registraci podle následujících pokynů v [seznamu Registrovat zdrojový počítač s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. Na Linuxu, pokud je hodnota\>platformy v <INSTALLATION_DIR /etc/drscout.conf poškozena, registrace se nezdaří. Chcete-li tento problém identifikovat, otevřete soubor /var/log/ua_install.log. Vyhledejte řetězec **Aborting konfigurace jako VM_PLATFORM hodnota je buď null nebo není VmWare/Azure**. Platforma by měla být nastavena na **VmWare** nebo **Azure**. Pokud je soubor drscout.conf poškozen, doporučujeme [odinstalovat agenta mobility](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) a potom agenta mobility znovu nainstalovat. Pokud se odinstalace nezdaří, proveďte následující kroky: a. Otevřete soubor Installation_Directory/uninstall.sh a zakomentujte volání funkce **StopServices.**
    b. Otevřete soubor Installation_Directory/Vx/bin/uninstall.sh a zakomentujte volání **funkce stop_services.**
    c. Otevřete soubor Installation_Directory/Fx/uninstall.sh a zakomentujte celou sekci, která se snaží zastavit službu Fx.
    d. [Odinstalujte](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) agenta mobility. Po úspěšné odinstalaci restartujte systém a zkuste přeinstalovat agenta mobility.

## <a name="installation-failure-failed-to-load-accounts"></a>Selhání instalace: Načtení účtů se nezdařilo.

K této chybě dochází, když služba nemůže číst data z přenosového připojení při instalaci agenta mobility a registraci s konfiguračním serverem. Chcete-li tento problém vyřešit, ujistěte se, že tls 1.0 je povolena na zdrojovém počítači.

## <a name="vcenter-discovery-failures"></a>Selhání zjišťování aplikace vCenter

Chcete-li vyřešit selhání zjišťování vCenter, přidejte server vCenter do nastavení proxy seznamu byPass. 

- Stáhněte si nástroj PsExec [zde](https://aka.ms/PsExec) pro přístup k uživatelskému obsahu systému.
- Spouštět aplikaci Internet Explorer v systémovém uživatelském obsahu spuštěním následujícího příkazového řádku psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Přidejte nastavení proxy serveru v aplikaci IE a restartujte službu tmanssvc.
- Chcete-li konfigurovat nastavení serveru proxy DRA, spusťte cd C:\Program Files\Microsoft Azure Site Recovery Provider
- Dále proveďte DRCONFIGURATOR. EXE /configure /AddBypassUrls [přidat IP adresu/FQDN serveru vCenter, který je k dispozici během **konfigurace serveru vCenter Server/vSphere ESXi** krok [nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Změna adresy IP konfiguračního serveru

Důrazně doporučujeme, abyste neměnili IP adresu konfiguračního serveru. Ujistěte se, že všechny adresy IP přiřazené konfiguračnímu serveru jsou statické adresy IP. Nepoužívejte ip adresy DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Token SAML je neplatný

Chcete-li se této chybě vyhnout, ujistěte se, že čas na systémových hodinách se neliší od místního času o více než 15 minut. Znovu spusťte instalační program a dokončete registraci.

## <a name="failed-to-create-a-certificate"></a>Vytvoření certifikátu se nezdařilo.

Certifikát, který je nutný k ověření obnovení webu nelze vytvořit. Po opětovném spuštění instalace po zajištění spuštění nastavení jako místního správce.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Neaktivace licence systému Windows ze standardního vyhodnocení serveru na server Standard

1. V rámci nasazení konfiguračního serveru prostřednictvím OVF se používá zkušební licence, která je platná po dobu 180 dnů. Tuto licenci je třeba aktivovat dříve, než vyprší její platnost. Jinak to může mít za následek časté vypnutí konfiguračního serveru a tím způsobit překážku replikačních aktivit.
2. Pokud se vám nedaří aktivovat licenci systému Windows, spojte se s [týmem podpory systému Windows,](https://aka.ms/Windows_Support) který problém vyřeší.

## <a name="register-source-machine-with-configuration-server"></a>Registrace zdrojového počítače s konfiguračním serverem

### <a name="if-the-source-machine-runs-windows"></a>Pokud zdrojový počítač běží windows

Ve zdrojovém počítači spusťte následující příkaz:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Nastavení | Podrobnosti
--- | ---
Využití | UnifiedAgentConfigurator.exe /CSEndPoint <adresa\> IP konfiguračního serveru /PassphraseFilePath <cestu k souboru hesel\>
Protokoly konfigurace agenta | Nachází se pod %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Povinný parametr. Určuje adresu IP konfiguračního serveru. Použijte libovolnou platnou adresu IP.
/PassphraseFilePath |  Povinné. Umístění přístupové fráze. Použijte libovolnou platnou cestu UNC nebo místního souboru.

### <a name="if-the-source-machine-runs-linux"></a>Pokud zdrojový počítač běží Linux

Ve zdrojovém počítači spusťte následující příkaz:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Nastavení | Podrobnosti
--- | ---
Využití | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <adrese\> IP konfiguračního serveru -P <cesta k souboru heslem\>
-i | Povinný parametr. Určuje adresu IP konfiguračního serveru. Použijte libovolnou platnou adresu IP.
-P |  Povinné. Úplná cesta k souboru, do kterého je uloženo přístupové heslo. Použijte libovolnou platnou složku.

## <a name="unable-to-configure-the-configuration-server"></a>Konfigurace konfiguračního serveru nelze nakonfigurovat.

Pokud do virtuálního počítače nainstalujete jiné aplikace než konfigurační server, nebude pravděpodobně možné nakonfigurovat hlavní cíl. 

Konfigurační server musí být server s jedním účelem a jeho použití jako sdíleného serveru není podporováno. 

Další informace naleznete v nejčastějších dotazech ke konfiguraci v [tématu Nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Odebrání zastaralých položek chráněných položek z databáze konfiguračního serveru 

Chcete-li odebrat zastaralý chráněný počítač na konfiguračním serveru, použijte následující kroky. 
 
1. Určení zdrojového počítače a ADRESY IP zastaralé položky: 

    1. Otevřete cmdline MYSQL v režimu správce. 
    2. Proveďte následující příkazy. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Tím se vrátí seznam registrovaných strojů spolu s jejich IP adresami a posledním srdečním rytmem. Najděte hostitele, který má zastaralé dvojice replikace.

2. Otevřete příkazový řádek se zvýšenými oprávněními a přejděte na c:\ProgramData\ASR\home\svsystems\bin. 
4. Chcete-li odebrat podrobnosti o registrovaných hostitelích a zastaralé informace o zadávání z konfiguračního serveru, spusťte následující příkaz pomocí zdrojového počítače a ip adresy zastaralé položky. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Pokud máte položku zdrojového serveru "OnPrem-VM01" s ip adresou 10.0.0.4, použijte místo toho následující příkaz.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Restartujte následující služby ve zdrojovém počítači a znovu se zaregistrujte pomocí konfiguračního serveru. 
 
    - InMage Scout Application Service
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Upgrade se nezdaří, když se služby nepodaří zastavit

Upgrade konfiguračního serveru se nezdaří, pokud se některé služby nezastaví. 

Chcete-li tento problém identifikovat, přejděte na konfiguračním serveru na položku C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile. Pokud zjistíte následující chyby, použijte následující postup k vyřešení problému: 

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

Chcete-li tento problém vyřešit:

Ručně zastavte následující služby:

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost, 
- Agent služby Microsoft Azure Recovery Services, 
- Služba obnovení webu Microsoft Azure, 
- tmansvc
  
Chcete-li aktualizovat konfigurační server, spusťte [sjednocené nastavení](service-updates-how-to.md#links-to-currently-supported-update-rollups) znovu.

## <a name="azure-active-directory-application-creation-failure"></a>Selhání vytváření aplikací služby Azure Active Directory

Nemáte dostatečná oprávnění k vytvoření aplikace ve službě Azure Active Directory (AAD) pomocí šablony [Open Virtualization Application (OVA).](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
)

Pokud problém vyřešíte, přihlaste se na portál Azure a udělejte jednu z těchto věcí:

- Vyžádejte si roli vývojáře aplikací v aad. Další informace o roli Vývojář aplikací najdete v [tématu Oprávnění rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Ověřte, zda **je příznak aplikace nastaven** na *hodnotu true* v anad. Další informace najdete v [tématu Postup: Pomocí portálu k vytvoření aplikace Azure AD a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Procesní server nebo hlavní cíl nemohou komunikovat s konfiguračním serverem 

Moduly procesního serveru (PS) a Hlavní cíl (MT) nejsou schopny komunikovat s konfiguračním serverem (CS) a jejich stav se zobrazí jako nepřipojený na webu Azure Portal.

Obvykle je to způsobeno chybou s portem 443. Pomocí následujících kroků odblokujte port a znovu povolte komunikaci s cs.

**Ověřte, zda je agent MARS vyvolán agentem hlavního cíle.**

Chcete-li ověřit, zda hlavní cílový agent může vytvořit relaci TCP pro adresu IP konfiguračního serveru, vyhledejte trasování podobné následujícímu v protokolech agenta hlavního cíle:

TCP \<Nahradit IP s CS IP zde \<>:52739 Nahradit IP cs IP zde>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT // Nahradit IP s CS IP zde

Pokud zjistíte trasování podobné následující v protokolech agenta MT, MT Agent hlásí chyby na portu 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
K této chybě může dojít, pokud ostatní aplikace také používají port 443 nebo z důvodu nastavení brány firewall, které blokuje port.

Chcete-li tento problém vyřešit:

- Ověřte, zda brána firewall neblokuje port 443.
- Pokud je port nedostupný kvůli jiné aplikaci, která tento port používá, zastavte a odinstalujte aplikaci.
  - Pokud zastavení aplikace není možné, nastavte nový čistý CS.
- Restartujte konfigurační server.
- Restartujte službu IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurační server není připojen z důvodu nesprávných položek UUID

K této chybě může dojít, pokud existuje více položek instance uuid instance konfiguračního serveru (CS) v databázi. K problému často dochází při klonování virtuálního počítače konfiguračního serveru.

Chcete-li tento problém vyřešit:

1. Odebrat zastaralé/staré cs virtuální počítač z vCenter. Další informace naleznete v tématu [Odebrání serverů a zakázání ochrany](site-recovery-manage-registration-and-protection.md).
2. Přihlaste se k virtuálnímu počítače konfiguračního serveru a připojte se k databázi MySQL svsdb1. 
3. Spusťte následující dotaz:

    > [!IMPORTANT]
    >
    > Ověřte, zda zadáváte podrobnosti uuid klonovaného konfiguračního serveru nebo zastaralé položky konfiguračního serveru, který se již nepoužívá k ochraně virtuálních počítačů. Zadání nesprávné uuid bude mít za následek ztrátu informací pro všechny existující chráněné položky.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Aktualizujte stránku portálu.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Nekonečné přihlašovací smyčka dochází při zadávání přihlašovacích údajů

Po zadání správné uživatelské jméno a heslo na konfiguračním serveru OVF, Přihlášení Azure nadále výzvu k zadání správné přihlašovací údaje.

K tomuto problému může dojít, pokud je nesprávný systémový čas.

Chcete-li tento problém vyřešit:

Nastavte správný čas v počítači a opakujte přihlášení. 
 