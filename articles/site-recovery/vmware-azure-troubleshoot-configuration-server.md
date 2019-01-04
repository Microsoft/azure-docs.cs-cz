---
title: Řešení potíží s konfiguračním serverem během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro nasazení konfiguračního serveru pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998961"
---
# <a name="troubleshoot-configuration-server-issues"></a>Řešení potíží s konfigurací serveru

V tomto článku vám pomůžou s řešením problémů při nasazování a správa [Azure Site Recovery](site-recovery-overview.md) konfiguračního serveru. Konfigurační server funguje jako server pro správu a slouží k nastavení zotavení po havárii pro místní virtuální počítače VMware a fyzických serverů do Azure pomocí Site Recovery. Následující sekce popisuje nejběžnější selhání viděli při přidání nového serveru konfigurace a správa konfiguračního serveru.

## <a name="registration-failures"></a>Selhání registrace

Zdrojový počítač zaregistruje se konfigurační server během instalace agenta mobility. Všechny chyby během tohoto kroku můžete ladit podle níže uvedených pokynů:

1. Přejděte k souboru C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. ProgramData může být skrytá složka. Pokud není možné najít, zkuste zrušit skrýt složky. Chyby může být způsobeno několika problémy.
2. Hledání řetězce "Platný nenašla žádná IP adresa". Pokud se řetězec najde,
    - Ověřte, jestli je stejný jako zdrojový počítač id požadovaného hostitele.
    - Zdrojový počítač by měl mít přiřazenou na fyzickou síťovou kartu pro registraci agenta s CS úspěšné alespoň jednu IP adresu.
    - Na zdrojovém počítači spusťte příkaz `> ipconfig /all` (pro operační systém Windows) a `# ifconfig -a` (pro operační systém Linux) Chcete-li získat všechny IP adresy zdrojového počítače.
    - Mějte prosím na paměti, že registrace agenta vyžaduje platnou adresu IP v4 přiřazené fyzický síťový adaptér.
3. Pokud řetězec uvedený výše není nalezen, hledání řetězce "Důvod" = > "NULL". Pokud se najde,
    - Pokud zdrojový počítač používá prázdný hostitele, pokud se chcete zaregistrovat u konfiguračního serveru, dojde k této chybě.
    - Po vyřešení problémů, postupujte podle pokynů uvedených [tady](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) pokus o registraci ručně.
4. Pokud není nalezen řetězec uvedený výše, přejděte na zdrojovém počítači a v protokolu C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log ProgramData může být skrytá složka. Pokud není možné najít, zkuste zrušit skrýt složky. Chyby může být způsobeno několika problémy. Vyhledejte řetězec "odeslání požadavku: (7) - nepodařilo připojit k serveru". Pokud se najde,
    - Vyřešte problémy sítě mezi zdrojového počítače a konfigurace serveru. Ověřte, že konfigurační server je dostupný ze zdrojového počítače pomocí sítě nástroje, jako je ping, traceroute, webový prohlížeč atd., ujistěte se, že tento zdrojový počítač je schopný připojit přes port 443 konfiguračního serveru.
    - Zkontrolujte, jestli nejsou žádná pravidla brány firewall na zdrojovém počítači blokují připojení mezi zdrojového počítače a konfigurace serveru. Práce se správce sítě pro odblokování problémy s připojením.
    - Zkontrolujte složky uvedené [tady](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) jsou vyloučené z antivirového softwaru.
    - Po vyřešení problémů se sítí, zkuste registraci tak, že následující pokyny uvedené [tady](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Pokud není nalezena ve stejném protokolu vyhledejte řetězec "požadavku: (60) - peer certifikát nelze ověřit pomocí dané certifikáty certifikační Autority. ". Pokud se najde, 
    - Tato chyba mohla být, protože vypršela platnost certifikátu serveru konfigurace zdrojového počítače nepodporuje protokol TLS 1.0 a nad SSL protokoly nebo je brána firewall, která blokuje komunikaci prostřednictvím protokolu SSL mezi zdrojového počítače a konfigurace serveru.
    - Pokud chcete vyřešit, připojení k IP adresa konfiguračního serveru pomocí webového prohlížeče na zdrojovém počítači pomocí identifikátoru URI https://<CSIPADDRESS>: 443 /. Ujistěte se, že se že tento zdrojový počítač je schopný připojit přes port 443 konfiguračního serveru.
    - Zkontrolujte, jestli nejsou žádná pravidla brány firewall na zdrojovém počítači blokují připojení mezi zdrojového počítače a konfigurace serveru. Práce se správce sítě pro odblokování problémy s připojením.
    - Zkontrolujte složky uvedené [tady](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) jsou vyloučené z antivirového softwaru.  
    - Po vyřešení problémů, zkuste registraci tak, že následující pokyny uvedené [tady](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. V systému Linux Pokud hodnota platformu z /etc/drscout.conf < INSTALLATION_DIR > je poškozený, pak registrace se nezdaří. Pokud chcete zjistit, přejděte na /var/log/ua_install.log protokolu. Zjistíte řetězec "Přerušení konfigurace jako VM_PLATFORM hodnota je null nebo je není VmWare/Azure." Platformu je třeba nastavit na "VmWare" nebo "Azure". Protože je poškozen soubor drscout.conf, doporučuje se [odinstalovat](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) agentem mobility a znovu nainstalujte. Pokud se odinstalace nezdaří, postupujte podle následujících kroků:
    - Otevřete soubor Installation_Directory/uninstall.sh a Odkomentujte volání funkce *StartServices*
    - Otevřete soubor Installation_Directory/Vx/bin/uninstall a nastavte komentář volání funkce `stop_services`
    - Otevřete soubor Installation_Directory/Fx/odinstalovat a Odkomentujte kompletní oddílu, který se pokouší zastavit službu Fx.
    - Nyní pokusí [odinstalovat](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) agenta mobility. Po úspěšném odinstalace restartování systému a pokuste se znovu nainstalovat agenta.

## <a name="installation-failure---failed-to-load-accounts"></a>Instalace se nezdařila – nepovedlo se načíst účty

K této chybě dochází, když služba nelze číst data z přenosového připojení během instalace agenta mobility a registrace konfiguračního serveru. Pokud chcete vyřešit, zajistěte, aby protokol TLS 1.0 je povolit na zdrojovém počítači.

## <a name="change-ip-address-of-configuration-server"></a>Změňte IP adresu konfiguračního serveru

Důrazně doporučujeme neměnit IP adresu konfiguračního serveru. Zkontrolujte všechny IP adresy přiřazené ke konfiguračnímu serveru jsou statické IP adresy a ne IP adresy DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token je neplatný.

Lze vyvarovat této chyby, ujistěte se, že čas na systémových hodin není více než 15 minut od místního času. Znovu spusťte instalační program a dokončete registraci.

## <a name="failed-to-create-certificate"></a>Nepovedlo se vytvořit certifikát

Nelze vytvořit certifikát vyžadovaný k ověření v Site Recovery. Spusťte instalační program znovu, až se ujistíte, že instalační program spouštíte jako místní správce.

## <a name="register-source-machine-with-configuration-server"></a>Zdrojový počítač zaregistrovat u konfiguračního serveru

### <a name="if-source-machine-has-windows-os"></a>Pokud má zdrojový počítač operačního systému Windows

Spusťte následující příkaz na zdrojový počítač

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Nastavení** | **Podrobnosti**
--- | ---
Využití | / Csendpoint UnifiedAgentConfigurator.exe  <CSIP> /passphrasefilepath <PassphraseFilePath>
Protokoly konfigurace agenta | V části % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/ CSEndPoint | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
/PassphraseFilePath |  Povinné. Umístění přístupové heslo. Použijte libovolný platný název UNC nebo místní cesta k souboru.

### <a name="if-source-machine-has-linux-os"></a>Pokud má zdrojový počítač, operační systém Linux

Spusťte následující příkaz na zdrojový počítač

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Nastavení** | **Podrobnosti**
--- | ---
Využití | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
-P |  Povinné. Úplnou cestu souboru, ve kterém se heslo uloží. Všechny platné složky