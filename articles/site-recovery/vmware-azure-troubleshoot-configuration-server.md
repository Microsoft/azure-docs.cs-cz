---
title: Řešení potíží s konfiguračním serverem při zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje informace o řešení potíží s nasazením konfiguračního serveru pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: b5fd014732fd4cdfaa52f971b5e4d2c74db580d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371949"
---
# <a name="troubleshoot-configuration-server-issues"></a>Řešení potíží s konfiguračním serverem

Tento článek vám pomůže při řešení potíží při nasazení a správě konfiguračního serveru [Azure Site Recovery](site-recovery-overview.md) . Konfigurační server funguje jako management server. Konfigurační server slouží k nastavení zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí Site Recovery. Následující části popisují nejběžnější chyby, se kterými se můžete setkat při přidávání nového konfiguračního serveru a při správě konfiguračního serveru.

## <a name="registration-failures"></a>Selhání registrace

Zdrojový počítač se registruje s konfiguračním serverem při instalaci agenta mobility. V průběhu tohoto kroku můžete ladit jakékoli chyby pomocí následujících pokynů:

1. Otevřete soubor C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log. (Složka Složka ProgramData může být skrytá složka. Pokud nevidíte složku Složka ProgramData, klikněte v Průzkumníkovi souborů na kartě **zobrazení** v části **Zobrazit/skrýt** na zaškrtávací políčko **skryté položky** .) Selhání může být způsobeno několika problémy.

2. Hledání řetězce: **nebyla nalezena žádná platná IP adresa**. Pokud se řetězec najde:
   1. Ověřte, jestli je požadované ID hostitele stejné jako ID hostitele zdrojového počítače.
   2. Ověřte, zda má zdrojový počítač alespoň jednu IP adresu přiřazenou fyzické síťové kartě. Aby byla registrace agenta u konfiguračního serveru úspěšná, musí mít zdrojový počítač alespoň jednu platnou IP adresu, která je přiřazená k fyzické síťové kartě.
   3. Na zdrojovém počítači spusťte jeden z následujících příkazů, abyste získali všechny IP adresy zdrojového počítače:
      - Pro Windows: `> ipconfig /all`
      - Pro Linux: `# ifconfig -a`

3. Pokud není nalezen řetězec **žádná platná IP adresa** , vyhledejte řetězec **důvod =>null**. K této chybě dochází, pokud zdrojový počítač používá k registraci na konfiguračním serveru prázdného hostitele. Pokud se řetězec najde:
    - Po vyřešení těchto potíží postupujte podle pokynů v části [registrace zdrojového počítače s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) a opakujte registraci ručně.

4. Pokud se na zdrojovém počítači nenajde řetězec **důvod =>null** , otevřete soubor C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Složka Složka ProgramData může být skrytá složka. Pokud nevidíte složku Složka ProgramData, klikněte v Průzkumníkovi souborů na kartě **zobrazení** v části **Zobrazit/skrýt** na zaškrtávací políčko **skryté položky** .) Selhání může být způsobeno několika problémy. 

5. Vyhledejte **žádost o odeslání řetězce: (7) – nepovedlo se připojit k serveru**. Pokud se řetězec najde:
    1. Vyřešte problémy se sítí mezi zdrojovým počítačem a konfiguračním serverem. Ověřte, jestli je konfigurační server dosažitelný ze zdrojového počítače pomocí síťových nástrojů, jako je třeba příkaz traceroute nebo webový prohlížeč. Ujistěte se, že zdrojový počítač může získat přístup ke konfiguračnímu serveru prostřednictvím portu 443.
    2. Ověřte, jestli některá pravidla firewallu na zdrojovém počítači blokují připojení mezi zdrojovým počítačem a konfiguračním serverem. Pokud chcete odblokovat problémy s připojením, spolupracujte se správci vaší sítě.
    3. Zajistěte, aby se z antivirového softwaru vyloučily složky uvedené v [Site Recovery složky vyloučené z antivirových programů](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) .
    4. Při řešení problémů se sítí zkuste registraci zopakovat podle pokynů v části [registrace zdrojového počítače s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Pokud se nedaří najít **požadavek post typu String (7) – nelze se připojit k serveru** , vyhledejte ve stejném souboru protokolu požadavek na řetězec **: (60)-partnerský certifikát nelze pomocí daných certifikátů certifikační autority ověřit**. K této chybě může dojít, protože vypršela platnost certifikátu konfiguračního serveru nebo zdrojový počítač nepodporuje protokoly TLS 1,0 nebo novější. K tomu může dojít také v případě, že brána firewall blokuje komunikaci TLS mezi zdrojovým a konfiguračním serverem. Pokud se řetězec najde: 
    1. Pokud ho chcete vyřešit, připojte se k IP adrese konfiguračního serveru pomocí webového prohlížeče na zdrojovém počítači. Použijte identifikátor URI https: \/ \/<IP adresa konfiguračního serveru \> : 443/. Ujistěte se, že zdrojový počítač může získat přístup ke konfiguračnímu serveru prostřednictvím portu 443.
    2. Ověřte, jestli je potřeba přidat nebo odebrat pravidla firewallu na zdrojovém počítači, aby mohl zdrojový počítač komunikovat s konfiguračním serverem. Z důvodu nejrůznějšího softwaru brány firewall, který se může používat, nemůžeme zobrazit seznam všech požadovaných konfigurací brány firewall. Pokud chcete odblokovat problémy s připojením, spolupracujte se správci vaší sítě.
    3. Zajistěte, aby se z antivirového softwaru vyloučily složky uvedené v [Site Recovery složky vyloučené z antivirových programů](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) .  
    4. Po vyřešení těchto problémů zkuste registraci zopakovat podle pokynů v části [registrace zdrojového počítače s konfiguračním serverem](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. Pokud je v systému Linux hodnota platformy v <INSTALLATION_DIR \> /etc/drscout.conf poškozená, registrace se nezdařila. Chcete-li tento problém identifikovat, otevřete soubor/var/log/ua_install. log. Vyhledejte **konfiguraci přerušování řetězce, protože VM_PLATFORM hodnota je buď null, nebo není VMware/Azure**. Platforma by měla být nastavená buď na **VMware** , nebo na **Azure**. Pokud je soubor drscout. conf poškozený, doporučujeme, abyste [agenta mobility odinstalovali](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) a pak znovu přeinstalovali agenta mobility. Pokud se odinstalace nezdařila, proveďte následující kroky: a. Otevřete soubor Installation_Directory/Uninstall.sh a zakomentujte volání funkce **StopServices** .
    b. Otevřete soubor Installation_Directory/VX/bin/Uninstall.sh a zakomentujte volání funkce **stop_services** .
    c. Otevřete soubor Installation_Directory/FX/Uninstall.sh a zakomentujte celý oddíl, který se pokouší zastavit službu FX.
    d. [Odinstalujte](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) agenta mobility. Po úspěšné odinstalaci restartujte systém a pak zkuste agenta mobility znovu nainstalovat.

8. Zajistěte, aby se pro uživatelský účet nepovolilo Multi-Factor Authentication. Azure Site Recovery pro uživatelský účet nyní nepodporuje službu Multi-Factor Authentication. Zaregistrujte konfigurační server bez povoleného uživatelského účtu pro službu Multi-Factor Authentication.  

## <a name="installation-failure-failed-to-load-accounts"></a>Chyba instalace: Nepodařilo se načíst účty.

K této chybě dojde, když služba nemůže číst data z přenosového připojení při instalaci agenta mobility a registrace na konfiguračním serveru. Pokud chcete tento problém vyřešit, ujistěte se, že na zdrojovém počítači je povolený protokol TLS 1,0.

## <a name="vcenter-discovery-failures"></a>selhání zjišťování vCenter

Pokud chcete vyřešit selhání zjišťování vCenter, přidejte Server vCenter do nastavení proxy serveru pro obejití seznamu. 

- Stáhněte si [sem](/sysinternals/downloads/psexec) nástroj PsExec pro přístup k systémovému obsahu uživatele.
- Spusťte aplikaci Internet Explorer v uživatelském obsahu systému spuštěním následujícího příkazového řádku PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe".
- Přidejte nastavení proxy serveru v IE a restartujte službu tmanssvc.
- Pokud chcete nakonfigurovat nastavení proxy serveru DRA, spusťte CD C:\Program Files\Microsoft Azure Site Recovery Provider.
- Dále spusťte DRCONFIGURATOR.EXE/Configure/AddBypassUrls [Přidání IP adresy nebo plně kvalifikovaného názvu domény vCenter Server poskytnuté během **Konfigurace serveru vCenter Server/vSphere ESXi** krok [nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Změna IP adresy konfiguračního serveru

Důrazně doporučujeme, abyste nezměnili IP adresu konfiguračního serveru. Zajistěte, aby všechny IP adresy, které jsou přiřazené ke konfiguračnímu serveru, byly statické IP adresy. Nepoužívejte IP adresy DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: token SAML je neplatný.

Chcete-li se této chybě vyhnout, zajistěte, aby se hodiny systémového času nelišily od místního času o více než 15 minut. Znovu spusťte instalační program a dokončete registraci.

## <a name="failed-to-create-a-certificate"></a>Nepovedlo se vytvořit certifikát.

Certifikát, který se vyžaduje pro ověření Site Recovery, se nedá vytvořit. Spusťte instalační program znovu, až budete mít jistotu, že jste instalační program spustili jako místní správce.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Nepovedlo se aktivovat licenci Windows ze serveru Standard EVALUATION na Server Standard.

1. Jako součást nasazení konfiguračního serveru prostřednictvím OVF se používá zkušební licence, která je platná po dobu 180 dnů. Tuto licenci musíte aktivovat dřív, než vyprší její platnost. V opačném případě to může vést k častému vypnutí konfiguračního serveru a tím i k tomu, že by mohlo dojít k výpadkům replikace.
2. Pokud nemůžete aktivovat licenci Windows, vyřešte problém podle [týmu podpory pro Windows](https://aka.ms/Windows_Support) .

## <a name="register-source-machine-with-configuration-server"></a>Registrovat zdrojový počítač ke konfiguračnímu serveru

### <a name="if-the-source-machine-runs-windows"></a>Pokud na zdrojovém počítači běží Windows

Na zdrojovém počítači spusťte následující příkaz:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Nastavení | Podrobnosti
--- | ---
Využití | UnifiedAgentConfigurator.exe/CSEndPoint <IP adresa konfiguračního serveru \> /PassphraseFilePath <cesta k souboru hesla\>
Protokoly konfigurace agenta | Nacházející se pod%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte jakoukoli platnou IP adresu.
/PassphraseFilePath |  Povinné. Umístění přístupového hesla. Použijte jakoukoli platnou cestu UNC nebo místní cestu k souboru.

### <a name="if-the-source-machine-runs-linux"></a>Pokud zdrojový počítač používá Linux

Na zdrojovém počítači spusťte následující příkaz:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Nastavení | Podrobnosti
--- | ---
Využití | /usr/local/ASR/Vx/bin CD<br /><br /> UnifiedAgentConfigurator.sh-i <IP adresa konfiguračního serveru \> -P <cesta k souboru hesla\>
-i | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte jakoukoli platnou IP adresu.
-P |  Povinné. Úplná cesta k souboru, ve kterém je heslo uloženo. Použijte libovolnou platnou složku.

## <a name="unable-to-configure-the-configuration-server"></a>Konfigurační server nelze nakonfigurovat.

Pokud na virtuálním počítači instalujete jiné aplikace než konfigurační server, možná nebudete moci nakonfigurovat hlavní cíl. 

Konfigurační server musí být jedním z účelových serverů a používat ho jako sdílený server, který není podporován. 

Další informace najdete v tématu Nejčastější dotazy týkající se konfigurace v [nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Odebrat zastaralé položky pro chráněné položky z databáze konfiguračního serveru 

K odebrání zastaralého chráněného počítače na konfiguračním serveru použijte následující postup. 
 
1. Určení zdrojového počítače a IP adresy zastaralého záznamu: 

    1. Otevřete položku MYSQL cmdline v režimu správce. 
    2. Spusťte následující příkazy. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Tím se vrátí seznam registrovaných počítačů spolu s jejich IP adresami a poslední prezenční signál. Vyhledejte hostitele, který má zastaralé páry replikace.

2. Otevřete příkazový řádek se zvýšenými oprávněními a přejděte na C:\ProgramData\ASR\home\svsystems\bin.. 
4. Pokud chcete z konfiguračního serveru odebrat podrobnosti o registrovaných hostitelích a zastaralá data, spusťte následující příkaz pomocí zdrojového počítače a IP adresy zastaralého záznamu. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Pokud máte položku zdrojového serveru "OnPrem-VM01" s IP adresou 10.0.0.4, použijte místo toho následující příkaz.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Restartujte následující služby na zdrojovém počítači pro opětovné zaregistrování na konfiguračním serveru. 
 
    - InMage Scout Application Service
    - InMage Scout VX agent – Sentinel/vystavení

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Upgrade se nepovede, když se služby nepodaří zastavit.

Upgrade konfiguračního serveru se nezdařil, pokud se některé služby nezastaví. 

Chcete-li identifikovat problém, přejděte na C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile na konfiguračním serveru. Pokud zjistíte následující chyby, vyřešte problém pomocí následujících kroků: 

```output
2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
2018-06-28 14:28:12.944   Stopping svagents service.
2018-06-28 14:31:32.949   Unable to stop svagents service.
2018-06-28 14:31:32.949   Stopping svagents service.
2018-06-28 14:34:52.960   Unable to stop svagents service.
2018-06-28 14:34:52.960   Stopping svagents service.
2018-06-28 14:38:12.971   Unable to stop svagents service.
2018-06-28 14:38:12.971   Rolling back the install changes.
2018-06-28 14:38:12.971   Upgrade has failed.
```

Řešení tohoto problému:

Ručně zastavte následující služby:

- cxprocessserver
- InMage Scout VX agent – Sentinel/vystavení 
- Agent Microsoft Azure Recovery Services, 
- Služba Microsoft Azure Site Recovery 
- tmansvc
  
Chcete-li aktualizovat konfigurační server, spusťte [sjednocenou instalaci](service-updates-how-to.md#links-to-currently-supported-update-rollups) znovu.

## <a name="azure-active-directory-application-creation-failure"></a>Chyba při vytváření Azure Active Directory aplikace

Nemáte dostatečná oprávnění k vytvoření aplikace v Azure Active Directory (AAD) pomocí šablony [Open virtualized Application (vajíčk)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) .

Chcete-li tento problém vyřešit, přihlaste se k Azure Portal a proveďte jednu z následujících akcí:

- Vyžádejte si roli vývojáře aplikace v AAD. Další informace o roli vývojář aplikace najdete [v tématu oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).
- Ověřte, jestli je **možnost uživatel může vytvořit příznak aplikace** nastavená na *hodnotu true* v AAD. Další informace najdete v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Procesový Server nebo hlavní cíl nemůžou komunikovat s konfiguračním serverem. 

Moduly procesového serveru (PS) a hlavní cíl (MT) nemůžou komunikovat s konfiguračním serverem (CS) a jejich stav se zobrazuje jako nepřipojený v Azure Portal.

Obvykle je to kvůli chybě s portem 443. Pomocí následujících kroků odblokujte port a znovu aktivujte komunikaci s CS.

**Ověření, že agent MARS je vyvolán hlavním cílovým agentem**

Chcete-li ověřit, zda může hlavní cílový agent vytvořit relaci protokolu TCP pro IP adresu konfiguračního serveru, vyhledejte v protokolech hlavního cílového agenta trasování podobné následujícímu:

TCP \<Replace IP with CS IP here> : 52739 \<Replace IP with CS IP here> : 443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT//nahraďte IP adresou CS.

Pokud v protokolech MT zjistíte trasování, které je podobné následujícímu, agent MT hlásí chyby na portu 443:

```output
#~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
#~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
```
 
K této chybě může dojít, pokud jiné aplikace používají také port 443 nebo kvůli nastavení brány firewall blokujícího port.

Řešení tohoto problému:

- Ověřte, že brána firewall neblokuje port 443.
- Pokud je port nedosažitelný z důvodu jiné aplikace, kterou tento port používá, zastavte a odinstalujte aplikaci.
  - Pokud je zastavování aplikace neproveditelné, nastavte novou čistou CS.
- Restartujte konfigurační server.
- Restartujte službu IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurační server není připojen z důvodu nesprávných záznamů UUID.

K této chybě může dojít, pokud je v databázi více položek identifikátoru UUID instance konfiguračního serveru (CS). K tomuto problému dochází často při klonování virtuálního počítače konfiguračního serveru.

Řešení tohoto problému:

1. Odeberte zastaralý virtuální počítač CS z vCenter. Další informace najdete v tématu  [odebrání serverů a zakázání ochrany](site-recovery-manage-registration-and-protection.md).
2. Přihlaste se k virtuálnímu počítači konfiguračního serveru a připojte se k databázi MySQL svsdb1. 
3. Spusťte následující dotaz:

    > [!IMPORTANT]
    >
    > Ověřte, že zadáváte podrobnosti identifikátoru UUID naklonovaného konfiguračního serveru nebo jste zastaralou položku konfiguračního serveru, který se už nepoužívá k ochraně virtuálních počítačů. Pokud zadáte nesprávný UUID, ztratí se informace pro všechny existující chráněné položky.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Aktualizujte stránku portálu.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Při zadávání přihlašovacích údajů probíhá nekonečná smyčka přihlášení.

Po zadání správného uživatelského jména a hesla na konfiguračním serveru OVF se přihlášení Azure stále vyzve ke správnému zadání přihlašovacích údajů.

K tomuto problému může dojít v případě, že systémový čas není správný.

Řešení tohoto problému:

Nastavte na počítači správný čas a znovu se přihlaste. 
