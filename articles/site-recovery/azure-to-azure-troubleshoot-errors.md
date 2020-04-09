---
title: Poradce při potížích s replikací virtuálních stránek Azure v Azure Site Recovery
description: Poradce při potížích při replikaci virtuálních počítačů Azure pro zotavení po havárii.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 0882eaa8b54966c7a804cf78a3928771b238e056
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885000"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Poradce při potížích s chybami replikace virtuálních počítačů Azure-to-Azure

Tento článek popisuje, jak řešit běžné chyby v Azure Site Recovery během replikace a obnovení virtuálních počítačů Azure (VM) z jedné oblasti do druhé. Další informace o podporovaných konfiguracích najdete v [matici podpory pro replikaci virtuálních počítačů Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy s kvótami prostředků Azure (kód chyby 150097)

Ujistěte se, že vaše předplatné je povoleno k vytvoření virtuálních počítačích Azure v cílové oblasti, kterou chcete použít jako oblast zotavení po havárii .DR. Vaše předplatné potřebuje dostatečnou kvótu k vytvoření virtuálních počítačů potřebných velikostí. Ve výchozím nastavení site recovery zvolí cílovou velikost virtuálního počítače, která je stejná jako velikost zdrojového virtuálního počítače. Pokud odpovídající velikost není k dispozici, site recovery automaticky vybere nejbližší dostupnou velikost.

Pokud neexistuje žádná velikost, která podporuje konfiguraci zdrojového virtuálního počítače, zobrazí se následující zpráva:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Možné příčiny

- ID předplatného není povoleno k vytvoření žádné virtuální počítače v umístění cílové oblasti.
- ID předplatného není povolené nebo nemá dostatečnou kvótu k vytvoření konkrétních velikostí virtuálních virtuálních zařízení v umístění cílové oblasti.
- Není nalezena žádná vhodná velikost cílového virtuálního počítače, která by odpovídala počtu zdrojových síťových karet virtuálního počítače (NIC) pro ID předplatného v umístění cílové oblasti.

### <a name="fix-the-problem"></a>Oprava problému

Obraťte se na [podporu fakturace Azure](/azure/azure-portal/supportability/resource-manager-core-quotas-request) a povolte svému předplatnému vytvářet virtuální počítače požadovaných velikostí v cílovém umístění. Potom opakujte neúspěšnou operaci.

Pokud má cílové umístění omezení kapacity, zakažte replikaci do tohoto umístění. Potom povolte replikaci do jiného umístění, kde vaše předplatné má dostatečnou kvótu k vytvoření virtuálních počítačů požadovaných velikostí.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodné kořenové certifikáty (kód chyby 151066)

Pokud nejsou na virtuálním počítači k dispozici všechny nejnovější důvěryhodné kořenové certifikáty, může se nezdaří vaše úloha povolit replikaci pro obnovení lokality. Ověřování a autorizace volání služby Site Recovery z virtuálního zařízení se nezdaří bez těchto certifikátů.

Pokud se úloha povolit replikaci nezdaří, zobrazí se následující zpráva:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Možná příčina

Důvěryhodné kořenové certifikáty potřebné pro autorizaci a ověřování nejsou ve virtuálním počítači k dispozici.

### <a name="fix-the-problem"></a>Oprava problému

#### <a name="windows"></a>Windows

Pro virtuální počítač s operačním systémem Windows nainstalujte nejnovější aktualizace systému Windows tak, aby všechny důvěryhodné kořenové certifikáty byly k dispozici na virtuálním počítači. Postupujte podle typického procesu správy aktualizací systému Windows nebo správy aktualizací certifikátů ve vaší organizaci a získejte nejnovější kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů na virtuálních počítačích.

- Pokud se právě v odpojeném prostředí uvidíte podle standardního procesu aktualizace systému Windows ve vaší organizaci, abyste získali certifikáty.
- Pokud požadované certifikáty nejsou k dispozici na virtuálním počítači, volání služby Site Recovery nezdaří z bezpečnostních důvodů.

Pokud chcete ověřit, že je `login.microsoftonline.com` problém vyřešený, přejděte z prohlížeče ve vašem virtuálním počítači.

Další informace naleznete v [tématu Konfigurace důvěryhodných kořenových adresářů a nepovolených certifikátů](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Postupujte podle pokynů, které poskytuje distributor verze operačního systému Linux získat nejnovější důvěryhodné kořenové certifikáty a nejnovější seznam odvolaných certifikátů na virtuálním počítači.

Vzhledem k tomu, že SUSE Linux používá symbolické odkazy nebo symbolické odkazy k udržování seznamu certifikátů, postupujte takto:

1. Přihlaste se jako **root** uživatel. Výchozí příkazový`#`řádek je symbol hash ( ).

1. Chcete-li změnit adresář, spusťte tento příkaz:

   `cd /etc/ssl/certs`

1. Zkontrolujte, zda je k dispozici kořenový certifikát certifikační autority společnosti Symantec:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Pokud není kořenový certifikát certifikační autority symantec nalezen, stáhněte soubor následujícím příkazem. Zkontrolujte případné chyby a postupujte podle doporučených akcí pro selhání sítě.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Zkontrolujte, zda je k dispozici kořenový certifikát certifikační autority Baltimore:

   `ls Baltimore_CyberTrust_Root.pem`

   - Pokud není kořenový certifikát certifikační autority Baltimore nalezen, stáhněte certifikát spusťte tento příkaz:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Zkontrolujte, zda je k dispozici certifikát DigiCert_Global_Root_CA:

   `ls DigiCert_Global_Root_CA.pem`

    - Pokud DigiCert_Global_Root_CA není nalezen, stáhněte certifikát následujícími příkazy:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Chcete-li aktualizovat stavy hash subjektu certifikátu pro nově stažené certifikáty, spusťte skript opakování:

   `c_rehash`

1. Chcete-li zkontrolovat, zda byly pro certifikáty vytvořeny stavy hashe subjektu jako symbolické odkazy, spusťte tyto příkazy:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Vytvořte kopii souboru _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ s názvem souboru _b204d74a.0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Vytvořte kopii souboru _Baltimore_CyberTrust_Root.pem_ s názvem souboru _653b494a.0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Vytvořte kopii souboru _DigiCert_Global_Root_CA.pem_ s názvem souboru _3513523f.0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Zkontrolujte, zda jsou soubory přítomny:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro adresy URL obnovení lokality nebo rozsahy IP adres (kód chyby 151037 nebo 151072)

Aby replikace obnovení lokality fungovala, je z virtuálního serveru vyžadováno odchozí připojení k určitým adresám URL. Pokud váš virtuální počítač je za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozí připojení, může čelit jeden z těchto problémů. I když nadále podporujeme odchozí přístup prostřednictvím adres URL, použití seznamu povolených rozsahů IP adres již není podporováno.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problém 1: Nepodařilo se zaregistrovat virtuální počítač Azure pomocí site recovery (151195)

#### <a name="possible-causes"></a>Možné příčiny

- Připojení nelze navázat na koncové body obnovení lokality z důvodu selhání překladu dns (Domain Name System).
- Tento problém je častější při opětovném protekci, když jste selhali ve virtuálním počítači, ale server DNS není dostupný z oblasti zotavení po havárii (DR).

#### <a name="fix-the-problem"></a>Oprava problému

Pokud používáte vlastní DNS, ujistěte se, že dns server je přístupný z oblasti zotavení po havárii.

Chcete-li zkontrolovat, jestli virtuální hod používá vlastní nastavení DNS:

1. Otevřete **virtuální počítače** a vyberte virtuální počítač.
1. Přejděte na **nastavení** virtuálních počítače a vyberte **síť .**
1. Ve **virtuální síti nebo podsíti**vyberte odkaz pro otevření stránky prostředků virtuální sítě.
1. Přejděte na **Nastavení** a vyberte **servery DNS**.

Pokuste se získat přístup k serveru DNS z virtuálního počítače. Pokud server DNS není přístupný, zpřístupníte jej buď selháním přes server DNS, nebo vytvořením řádku sítě mezi sítí Zotavení po havárii a službou DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-chyba.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace obnovení lokality se nezdařila (151196)

#### <a name="possible-cause"></a>Možná příčina

Připojení nelze navázat na koncové body office 365 ověřování a identity IP4.

#### <a name="fix-the-problem"></a>Oprava problému

Azure Site Recovery vyžaduje přístup k rozsahům IP adres Office 365 pro ověřování.
Pokud používáte pravidla/proxy serveru firewall skupiny Azure Network Security Group (NSG) k řízení odchozího připojení k síti na virtuálním počítači, ujistěte se, že používáte pravidlo nsg na [základě značky služby Azure Active Directory (AAD)](/azure/virtual-network/security-overview#service-tags) pro povolení přístupu k AAD. Již nepodporujeme pravidla nsg založené na IP adresách.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace obnovení lokality se nezdařila (151197)

#### <a name="possible-cause"></a>Možná příčina

Připojení nelze navázat na koncové body služby Azure Site Recovery.

#### <a name="fix-the-problem"></a>Oprava problému

Pokud používáte pravidla/proxy firewall skupiny Azure Network Security Group (NSG) k řízení odchozího připojení k síti na virtuálním počítači, ujistěte se, že používáte značky služeb. Už nepodporujeme použití seznamu povolených IP adres prostřednictvím sítí zabezpečení sítě pro azure site recovery.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problém 4: Replikace Azure do Azure se nezdařila, když síťový provoz prochází místním proxy serverem (151072)

#### <a name="possible-cause"></a>Možná příčina

Vlastní nastavení proxy serveru je neplatné a agent služby Mobility automaticky nezjistil nastavení proxy serveru z aplikace Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Oprava problému

1. Agent služby Mobility detekuje nastavení proxy serveru `/etc/environment` z aplikace IE v systému Windows a v systému Linux.
1. Pokud dáváte přednost nastavení proxy pouze pro službu Mobility, můžete poskytnout podrobnosti proxy v _ProxyInfo.conf_ se nachází na adrese:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ by měl mít nastavení proxy serveru v následujícím formátu _INI._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent služby Mobility podporuje pouze **neověřené proxy servery**.

### <a name="more-information"></a>Další informace

Pokud chcete určit [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo požadované [rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), postupujte podle pokynů v části [O sítích v Azure replikaci Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disk nebyl v počítači nalezen (kód chyby 150039)

Nový disk připojený k virtuálnímu počítače musí být inicializován. Pokud disk není nalezen, zobrazí se následující zpráva:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Možné příčiny

- Nový datový disk byl připojen k virtuálnímu počítače, ale nebyl inicializován.
- Datový disk uvnitř virtuálního počítače není správně hlášení logické jednotky číslo (LUN) hodnotu, při které byl disk připojen k virtuálnímu počítače.

### <a name="fix-the-problem"></a>Oprava problému

Ujistěte se, že datové disky jsou inicializovány a opakujte operaci.

- **Windows**: [Připojení a inicializaci nového disku](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Inicializovat nový datový disk v Linuxu](/azure/virtual-machines/linux/add-disk).

Pokud problém přetrvává, obraťte se na podporu.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Jeden nebo více disků je k dispozici pro ochranu (kód chyby 153039)

### <a name="possible-causes"></a>Možné příčiny

- Jeden nebo více disků byly nedávno přidány do virtuálního počítače po ochraně.
- Jeden nebo více disků byly inicializovány po ochraně virtuálního počítače.

### <a name="fix-the-problem"></a>Oprava problému

Chcete-li, aby byl stav replikace virtuálního počítače znovu v pořádku, můžete zvolit ochranu disků nebo upozornění.

#### <a name="to-protect-the-disks"></a>Chcete-li chránit disky

1. Přejděte na **název replikovaných položek** > _virtuálních počítačů_ > **disky**.
1. Vyberte nechráněný disk a pak vyberte **Povolit replikaci**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Povolte replikaci na discích virtuálních počítačů.":::

#### <a name="to-dismiss-the-warning"></a>Zrušení upozornění

1. Přejděte na_název virtuálního serveru_ **replikovaných položek** > .
1. Vyberte upozornění v části **Přehled** a pak vyberte **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Zavřete upozornění na nový disk.":::

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Odebrání virtuálního počítače z trezoru dokončeného s informacemi (kód chyby 150225)

Když Site Recovery chrání virtuální počítač, vytvoří odkazy na zdrojovém virtuálním počítači. Pokud odeberete ochranu nebo zakážete replikaci, site recovery odebere tato propojení jako součást úlohy vyčištění. Pokud má virtuální počítač zámek prostředků, úloha vyčištění se dokončí s informacemi. Informace říkají, že virtuální počítač byl odebrán z trezoru služby Recovery Services, ale že některé zastaralé odkazy nelze vyčistit ve zdrojovém počítači.

Toto upozornění můžete ignorovat, pokud už nikdy nechcete tento virtuální počítač chránit. Ale pokud budete muset chránit tento virtuální počítač později, postupujte podle kroků v této části vyčistit odkazy.

> [!WARNING]
> Pokud nechcete provést vyčištění:
>
> - Pokud povolíte replikaci pomocí trezoru služby Recovery Services, virtuální počítač nebude uveden.
> - Pokud se pokusíte chránit virtuální počítač pomocí obnovení zotavení po**havárii****nastavení** >  **virtuálního počítače** > , operace se nezdaří se zprávou **Replikace nelze povolit z důvodu existující zastaralá propojení prostředků na virtuálním počítači**.

### <a name="fix-the-problem"></a>Oprava problému

> [!NOTE]
> Site Recovery neodstraní zdrojový virtuální počítač ani ho nijak neovlivní při provádění těchto kroků.

1. Odeberte zámek ze skupiny prostředků virtuálního nebo virtuálního soudu. Například v následujícím obrázku musí být odstraněn `MoveDemo` zámek prostředků na virtuálním počítači s názvem:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Odebrat zámek z virtuálního virtuálního mísy.":::

1. Stáhněte si skript a [odeberte zastaralou konfiguraci site recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript _Cleanup-stale-asr-config-Azure-VM.ps1_. Zadejte **ID předplatného**, **skupinu prostředků virtuálních aplikací**a název **virtuálního aplikace** jako parametry.
1. Pokud se zobrazí výzva pro přihlašovací údaje Azure, zadejte je. Potom ověřte, zda je skript spuštěn bez jakýchkoli chyb.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replikace nelze povolit z důvodu zastaralých propojení prostředků na virtuálním počítači (kód chyby 150226)

### <a name="possible-causes"></a>Možné příčiny

Virtuální počítač má zastaralou konfiguraci z předchozí ochrany obnovení webu.

Zastaralá konfigurace může dojít na virtuálním počítači Azure, pokud jste povolili replikaci pro virtuální počítač Azure pomocí site recovery a pak:

- Zakázali jste replikaci, ale zdrojový virtuální virtuální byl uzamčení prostředků.
- Trezor obnovení webu jste odstranili, aniž byste explicitně zakáže replikaci na virtuálním počítači.
- Odstranili jste skupinu prostředků obsahující trezor obnovení webu bez explicitního zakázání replikace na virtuálním počítači.

### <a name="fix-the-problem"></a>Oprava problému

> [!NOTE]
> Site Recovery neodstraní zdrojový virtuální počítač ani ho nijak neovlivní při provádění těchto kroků.

1. Odeberte zámek ze skupiny prostředků virtuálního nebo virtuálního soudu. Například v následujícím obrázku musí být odstraněn `MoveDemo` zámek prostředků na virtuálním počítači s názvem:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Odebrat zámek z virtuálního virtuálního mísy.":::

1. Stáhněte si skript a [odeberte zastaralou konfiguraci site recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript _Cleanup-stale-asr-config-Azure-VM.ps1_. Zadejte **ID předplatného**, **skupinu prostředků virtuálních aplikací**a název **virtuálního aplikace** jako parametry.
1. Pokud se zobrazí výzva pro přihlašovací údaje Azure, zadejte je. Potom ověřte, zda je skript spuštěn bez jakýchkoli chyb.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Nelze zobrazit virtuální počítač Azure nebo skupinu prostředků pro výběr v úloze povolit replikaci

### <a name="issue-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Problém 1: Skupina prostředků a zdrojový virtuální počítač jsou v různých umístěních

Obnovení lokality aktuálně vyžaduje, aby skupina prostředků zdrojové oblasti a virtuální počítače byly ve stejném umístění. Pokud nejsou, nebudete moct najít virtuální počítač nebo skupinu prostředků při pokusu o použití ochrany.

Jako řešení můžete povolit replikaci z virtuálního serveru namísto trezoru služby Recovery Services. Přejděte na **zdrojové** > **vlastnosti virtuálního počítače zotavení** > **po havárii** a povolte replikaci.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problém 2: Skupina prostředků není součástí vybraného předplatného

Pokud skupina prostředků není součástí vybraného předplatného, nemusí být možné najít skupinu prostředků v době ochrany. Ujistěte se, že skupina prostředků patří k předplatnému, které používáte.

### <a name="issue-3-stale-configuration"></a>Problém 3: Zastaralá konfigurace

Nemusí se zobrazit virtuální počítač, který chcete povolit pro replikaci, pokud existuje zastaralá konfigurace obnovení lokality na virtuálním počítači Azure. K této podmínce může dojít, pokud jste povolili replikaci pro virtuální počítač Azure pomocí site recovery a potom:

- Trezor obnovení webu jste odstranili, aniž byste explicitně zakáže replikaci na virtuálním počítači.
- Odstranili jste skupinu prostředků obsahující trezor obnovení webu bez explicitního zakázání replikace na virtuálním počítači.
- Zakázali jste replikaci, ale zdrojový virtuální virtuální byl uzamčení prostředků.

### <a name="fix-the-problem"></a>Oprava problému

> [!NOTE]
> Před použitím skriptu uvedeného v této části nezapomeňte `AzureRM.Resources` modul aktualizovat. Site Recovery neodstraní zdrojový virtuální počítač ani ho nijak neovlivní při provádění těchto kroků.

1. Odeberte zámek, pokud existuje, ze skupiny prostředků virtuálního objektu nebo virtuálního soudu. Například v následujícím obrázku musí být odstraněn `MoveDemo` zámek prostředků na virtuálním počítači s názvem:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Odebrat zámek z virtuálního virtuálního mísy.":::

1. Stáhněte si skript a [odeberte zastaralou konfiguraci site recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript _Cleanup-stale-asr-config-Azure-VM.ps1_. Zadejte **ID předplatného**, **skupinu prostředků virtuálních aplikací**a název **virtuálního aplikace** jako parametry.
1. Pokud se zobrazí výzva pro přihlašovací údaje Azure, zadejte je. Potom ověřte, zda je skript spuštěn bez jakýchkoli chyb.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Nelze vybrat virtuální počítač pro ochranu

### <a name="possible-cause"></a>Možná příčina

Virtuální počítač má rozšíření nainstalované ve stavu selhání nebo neodpovídá

### <a name="fix-the-problem"></a>Oprava problému

Přejděte na Rozšíření**nastavení** > **virtuálních** **počítačů** > a zkontrolujte, zda ve stavu se nezdařilo. Odinstalujte všechny neúspěšné rozšíření a zkuste znovu chránit virtuální počítač.

## <a name="the-vms-provisioning-state-isnt-valid-error-code-150019"></a>Stav zřizování virtuálního zařízení není platný (kód chyby 150019)

Chcete-li povolit replikaci na virtuálním počítači, musí být jeho stav zřizování **úspěšný**. Chcete-li zkontrolovat stav zřizování, postupujte takto:

1. Na webu Azure Portal vyberte **Průzkumník prostředků** ze **všech služeb**.
1. Rozbalte seznam **Odběry** a vyberte předplatné.
1. Rozbalte seznam **ResourceGroups** a vyberte skupinu prostředků virtuálního soudu.
1. Rozbalte seznam **Zdroje** a vyberte virtuální počítač.
1. Zkontrolujte pole **provisioningState** v zobrazení instance na pravé straně.

### <a name="fix-the-problem"></a>Oprava problému

- Pokud **zřizováníState** je **failed**, obraťte se na podporu s podrobnostmi k řešení potíží.
- Pokud **zřizováníState** je **aktualizace**, může být nasazena jiné rozšíření. Zkontrolujte, zda existují nějaké probíhající operace na virtuálním počítači, počkejte na jejich dokončení a opakujte úlohu obnovení sítě, abyste povolili replikaci.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Nelze vybrat cílový virtuální počítač (karta výběru sítě není k dispozici)

### <a name="issue-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problém 1: Virtuální počítač je připojený k síti, která už je namapovaná na cílovou síť.

Pokud je zdrojový virtuální počítač součástí virtuální sítě a jiný virtuální počítač ze stejné virtuální sítě je již namapován se sítí v cílové skupině prostředků, rozevírací seznam výběru sítě není ve výchozím nastavení k dispozici (zobrazí se šedě).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Seznam výběru sítě není k dispozici.":::

### <a name="issue-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Problém 2: Dříve jste zabezpečili virtuální počítač pomocí site recovery a pak jste zakázali replikaci

Zakázáníreplikace virtuálního virtuálního aplikace neodstraní mapování sítě. Mapování musí být odstraněno z trezoru služby Recovery Services, kde byl virtuální počítač chráněn. Přejděte na web **recovery services site** > **recovery infrastructure** > **infrastructure mapping**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Odstranit mapování sítě.":::

Cílovou síť, která byla nakonfigurována během nastavení zotavení po havárii, lze změnit po počátečním nastavení a po ochraně virtuálního počítače:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Upravte mapování sítě.":::

Změna mapování sítě ovlivní všechny chráněné virtuální počítače, které používají stejné mapování sítě.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Chyba služby STÍNOVÁ Kopie modelu COM+ nebo služby Volume (kód chyby 151025)

Dojde-li k této chybě, zobrazí se následující zpráva:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Možné příčiny

- Služba systémových aplikací modelu COM+ je zakázána.
- Služba stínová kopie svazku je zakázána.

### <a name="fix-the-problem"></a>Oprava problému

Nastavte službu systémová aplikace a stínová kopie svazku modelu COM+ do automatického nebo ručního režimu spuštění.

1. Otevřete konzolu Služby v systému Windows.
1. Ujistěte se, že systémová aplikace modelu COM+ a služba stínové kopie svazků nejsou nastaveny na **zakázáno** jako typ **spuštění**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Zkontrolujte typ spuštění služby COM plus Systémová aplikace a stínová kopie svazku.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nepodporovaná velikost spravovaného disku (kód chyby 150172)

Dojde-li k této chybě, zobrazí se následující zpráva:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Možná příčina

Disk je menší než podporovaná velikost 1024 MB.

### <a name="fix-the-problem"></a>Oprava problému

Ujistěte se, že velikost disku je v podporovaném rozsahu velikostí a opakujte operaci.

## <a name="protection-wasnt-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a>Ochrana nebyla povolena, protože konfigurace GRUB obsahuje název zařízení namísto UUID (kód chyby 151126)

### <a name="possible-causes"></a>Možné příčiny

Konfigurační soubory Linux Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_nebo _/etc/default/grub_) mohou určovat skutečné názvy zařízení namísto univerzálně jedinečných identifikátorů (UUID) pro `root` parametry. `resume` Obnovení webu vyžaduje UUID, protože názvy zařízení se mohou změnit. Po restartování virtuálního počítače nemusí přijít se stejným názvem při převzetí služeb při selhání, což má za následek problémy.

Následující příklady jsou řádky ze souborů GRUB, kde se místo požadovaných UUID zobrazují názvy zařízení:

- Soubor _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Soubor: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Oprava problému

Nahraďte každý název zařízení odpovídajícím UUID:

1. Najít UUID zařízení provedením příkazu `blkid <device name>`. Příklad:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Nahraďte název zařízení jeho UUID, `root=UUID=<UUID>` `resume=UUID=<UUID>`ve formátech a . Například po nahrazení bude řádek z _/boot/grub/menu.lst_ vypadat jako následující řádek:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Opakujte ochranu.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Povolení ochrany se nezdařilo, protože zařízení uvedené v konfiguraci GRUB neexistuje (kód chyby 151124)

### <a name="possible-cause"></a>Možná příčina

Konfigurační soubory GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, nebo `rd.lvm.lv` _/etc/default/grub_) mohou obsahovat parametry nebo `rd_LVM_LV`. Tyto parametry identifikují zařízení Správce logických svazků (LVM), která mají být zjištěna při spuštění. Pokud tato zařízení LVM neexistují, samotný chráněný systém se nespustí a bude uvízl v procesu zavádění. Stejný problém se také zobrazí s virtuálním virtuálním voláním s podporou převzetí služeb při selhání. Zde je několik příkladů:

- Soubor: _/boot/grub2/grub.cfg_ na RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Soubor: _/etc/default/grub_ na RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Soubor: _/boot/grub/menu.lst_ na RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

V každém příkladu musí GRUB detekovat dvě zařízení `root` `swap` LVM `rootvg`s názvy a ze skupiny svazků .

### <a name="fix-the-problem"></a>Oprava problému

Pokud zařízení LVM neexistuje, vytvořte jej nebo odeberte odpovídající parametry z konfiguračních souborů GRUB. Potom zkuste znovu povolit ochranu.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizace služby Mobility site recovery byla dokončena s upozorněními (kód chyby 151083)

Služba Site Recovery Mobility má mnoho součástí, z nichž jedna se nazývá ovladač filtru. Ovladač filtru je načten do systémové paměti pouze během restartování systému. Kdykoli aktualizace služby Mobility zahrnuje změny ovladače filtru, počítač se aktualizuje, ale stále se zobrazí upozornění, že některé opravy vyžadují restartování. Upozornění se zobrazí, protože opravy ovladačů filtru se mohou projevit pouze při načtení nového ovladače filtru, k němuž dochází pouze během restartování.

> [!NOTE]
> Tohle je jen varování. Existující replikace pokračuje v práci i po aktualizaci nového agenta. Můžete se kdykoli restartovat, kdykoli budete chtít, aby výhody nového ovladače filtru, ale starý ovladač filtru stále pracuje, pokud nerestartujete.
>
> Kromě ovladače filtru se výhody dalších vylepšení a oprav v aktualizaci služby Mobility projeví bez nutnosti restartování.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Ochranu nelze povolit, protože replika spravovaného disku již existuje bez očekávaných značek v cílové skupině prostředků (kód chyby 150161).

### <a name="possible-cause"></a>Možná příčina

K tomuto problému může dojít, pokud byl virtuální počítač dříve chráněn a pokud byla zakázána replikace, replika disk nebyl odebrán.

### <a name="fix-the-problem"></a>Oprava problému

Odstraňte repliku disku identifikovanou v chybové zprávě a opakujte úlohu ochrany, která selhala.

## <a name="next-steps"></a>Další kroky

[Replikace virtuálních počítačů Azure do jiné oblasti Azure](azure-to-azure-how-to-enable-replication.md)
