---
title: Řešení potíží s konfiguračním serverem během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro nasazení konfiguračního serveru pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/14/2019
ms.author: ramamill
ms.openlocfilehash: a720b264c4283498604d1446283c5a2242fdb8b3
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381801"
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

Aby bylo možné vyřešit chyby zjišťování serveru vCenter, zajistěte, aby že tento server vCenter je přidaná do nastavení proxy serveru seznamu jednorázové přihlášení. K provedení této aktivity

- Stáhněte si nástroj PsExec z [tady](https://aka.ms/PsExec) pro přístup k obsahu pro uživatele systému.
- Spusťte aplikaci Internet Explorer v systému uživatele obsahu spuštěním následujícího příkazového řádku psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Přidat nastavení proxy serveru v Internet Exploreru a restartujte službu tmanssvc.
- Konfigurace nastavení proxy serveru DRA, spuštěním cd C:\Program Files\Microsoft Azure Site Recovery Provider
- V dalším kroku spusťte DRCONFIGURATOR. Soubor EXE / configure /AddBypassUrls [přidat IP adresu nebo plně kvalifikovaný název domény systému vCenter Server k dispozici během **konfigurovat vCenter serveru nebo serveru vSphere ESXi** kroku [nasazení konfiguračního serveru](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Změňte IP adresu konfiguračního serveru

Důrazně doporučujeme, že nechcete změnit IP adresu konfiguračního serveru. Ujistěte se, že všechny IP adresy, které jsou přiřazeny ke konfiguračnímu serveru jsou statické IP adresy. Nepoužívejte IP adresy služby DHCP.
>>>>>>> c842cff5a0480caa5183dbb7afe5016a7061c7b9

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token je neplatný.

Lze vyvarovat této chyby, zkontrolujte, jestli, který čas systémových hodin se liší od místního času o více než 15 minut. Znovu spusťte instalační program a dokončete registraci.

## <a name="failed-to-create-a-certificate"></a>Nepovedlo se vytvořit certifikát

Nelze vytvořit certifikát, který má vyžadovaný k ověření v Site Recovery. Po zajištění, že instalační program spouštíte jako místní správce. Spusťte znovu instalační program.

## <a name="register-the-source-machine-with-the-configuration-server"></a>Zdrojový počítač zaregistrovat u konfiguračního serveru

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

