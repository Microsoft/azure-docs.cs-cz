---
title: Řešení potíží s replikací virtuálních počítačů Azure v Azure Site Recovery
description: Řešení chyb při replikaci virtuálních počítačů Azure na zotavení po havárii.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900412"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Řešení chyb replikace virtuálních počítačů Azure do Azure

Tento článek popisuje, jak řešit běžné chyby v Azure Site Recovery během replikace a obnovení virtuálních počítačů Azure z jedné oblasti do druhé. Další informace o podporovaných konfiguracích najdete v [matrici podpory pro replikaci virtuálních počítačů Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy s kvótou prostředků Azure (kód chyby 150097)

Ujistěte se, že je ve vašem předplatném povolené vytváření virtuálních počítačů Azure v cílové oblasti, kterou plánujete použít jako oblast zotavení po havárii (DR). Vaše předplatné potřebuje dostatečnou kvótu pro vytvoření virtuálních počítačů nezbytných velikostí. Ve výchozím nastavení Site Recovery zvolí cílovou velikost virtuálního počítače, která je stejná jako velikost zdrojového virtuálního počítače. Pokud není odpovídající velikost k dispozici, Site Recovery automaticky zvolí nejbližší dostupnou velikost.

Pokud neexistují žádné velikosti, která podporuje konfiguraci zdrojového virtuálního počítače, zobrazí se následující zpráva:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Možné příčiny

- Vaše ID předplatného nemá povoleno vytvářet žádné virtuální počítače v umístění cílové oblasti.
- ID vašeho předplatného není povolené nebo nemá dostatečnou kvótu, aby bylo možné vytvořit konkrétní velikosti virtuálních počítačů v umístění cílové oblasti.
- Nenašla se žádná vhodná Cílová velikost virtuálního počítače, která by odpovídala počtu síťových rozhraní (2) zdrojového virtuálního počítače pro ID předplatného v umístění cílové oblasti.

### <a name="fix-the-problem"></a>Oprava problému

Obraťte se na [podporu fakturace Azure](../azure-portal/supportability/resource-manager-core-quotas-request.md) a umožněte vašemu předplatnému vytváření virtuálních počítačů požadovaných velikostí v cílovém umístění. Pak zkuste neúspěšnou operaci zopakovat.

Pokud má cílové umístění omezení kapacity, zakažte replikaci do tohoto umístění. Potom povolte replikaci do jiného umístění, kde má vaše předplatné dostatečnou kvótu pro vytvoření virtuálních počítačů požadovaných velikostí.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodné kořenové certifikáty (kód chyby 151066)

Pokud na virtuálním počítači nejsou všechny nejnovější důvěryhodné kořenové certifikáty, může být úloha povolení replikace pro Site Recovery neúspěšná. Ověřování a autorizace volání služby Site Recovery z virtuálního počítače selžou bez těchto certifikátů.

Pokud se úloha povolení replikace nezdařila, zobrazí se následující zpráva:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Možná příčina

Na virtuálním počítači nejsou k dispozici důvěryhodné kořenové certifikáty, které jsou vyžadovány k autorizaci a ověřování.

### <a name="fix-the-problem"></a>Oprava problému

#### <a name="windows"></a>Windows

V případě virtuálního počítače s operačním systémem Windows nainstalujte nejnovější aktualizace Windows, aby se na virtuálním počítači nacházely všechny důvěryhodné kořenové certifikáty. Při získávání nejnovějších kořenových certifikátů a aktualizovaného seznamu odvolaných certifikátů na virtuálních počítačích použijte typický proces správy aktualizací služby Windows Update nebo certifikátů ve vaší organizaci.

- Pokud jste v odpojeném prostředí, použijte k získání certifikátů standardní postup služby Windows Update ve vaší organizaci.
- Pokud na virtuálním počítači nejsou požadované certifikáty, volání služby Site Recovery z bezpečnostních důvodů selžou.

Pokud chcete ověřit, že se problém vyřešil, `login.microsoftonline.com` v prohlížeči na svém virtuálním počítači.

Další informace najdete v tématu [Konfigurace důvěryhodných kořenových adresářů a zakázaných certifikátů](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Pokud chcete získat nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači, postupujte podle pokynů, které poskytuje distributor vaší verze operačního systému Linux.

Vzhledem k tomu, že SUSE Linux používá k údržbě seznamu certifikátů symbolické odkazy nebo symbolických odkazů, použijte následující postup:

1. Přihlaste se jako uživatel **root** . `#`Výchozím příkazovým řádkem je symbol hash ().

1. Chcete-li změnit adresář, spusťte tento příkaz:

   `cd /etc/ssl/certs`

1. Ověřte, zda je k dispozici certifikát kořenové certifikační autority Symantec:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Pokud se certifikát kořenové certifikační autority Symantec nenajde, spusťte následující příkaz a Stáhněte soubor. Vyhledejte případné chyby a proveďte doporučené akce při selhání sítě.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Ověřte, jestli je přítomný certifikát kořenové certifikační autority Baltimore:

   `ls Baltimore_CyberTrust_Root.pem`

   - Pokud se nenalezne certifikát kořenové certifikační autority Baltimore, spusťte tento příkaz a Stáhněte certifikát:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Ověřte, zda je k dispozici DigiCert_Global_Root_CA certifikát:

   `ls DigiCert_Global_Root_CA.pem`

    - Pokud se DigiCert_Global_Root_CA nenajde, spusťte následující příkazy ke stažení certifikátu:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Chcete-li aktualizovat hodnoty hash subjektu certifikátu u nově stažených certifikátů, spusťte skript znovu hash:

   `c_rehash`

1. Pokud chcete ověřit, jestli se pro certifikáty vytvořily hodnoty hash předmětu jako symbolických odkazů, spusťte tyto příkazy:

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

1. Vytvořte kopii souboru _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem_ s názvem filename _b204d74a. 0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Vytvořte kopii souboru _Baltimore_CyberTrust_Root. pem_ s názvem filename _653b494a. 0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Vytvořte kopii souboru _DigiCert_Global_Root_CA. pem_ s názvem filename _3513523f. 0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Ověřte, zda jsou soubory k dispozici:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí adresy URL nebo rozsahy IP adres (kód chyby 151037 nebo 151072)

Aby mohla replikace Site Recovery fungovat, z virtuálního počítače se vyžaduje odchozí připojení ke konkrétním adresám URL. Pokud je váš virtuální počítač za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozího připojení, můžete se setkat s jedním z těchto problémů. I když dál podporujeme odchozí přístup prostřednictvím adres URL, použití seznamu povolených rozsahů IP adres už není podporované.

#### <a name="possible-causes"></a>Možné příčiny

- Nelze navázat připojení k Site Recovery koncovým bodům z důvodu chyby rozlišení DNS (Domain Name System).
- Tento problém je častější během ochrany při selhání virtuálního počítače, ale server DNS není dosažitelný z oblasti zotavení po havárii (DR).

#### <a name="fix-the-problem"></a>Oprava problému

Pokud používáte vlastní server DNS, ujistěte se, že je server DNS přístupný z oblasti zotavení po havárii.

Pokud chcete zjistit, jestli virtuální počítač používá vlastní nastavení DNS:

1. Otevřete **virtuální počítače** a vyberte virtuální počítač.
1. Přejděte do **Nastavení** virtuálních počítačů a vyberte **sítě**.
1. V části **virtuální síť/podsíť** vyberte odkaz a otevřete stránku prostředku virtuální sítě.
1. Přejít na **Nastavení** a vyberte **servery DNS**.

Pokuste se získat přístup k serveru DNS z virtuálního počítače. Pokud server DNS není přístupný, zpřístupněte ho buď při selhání přes server DNS, nebo při vytváření sítě mezi sítí DR a DNS.

> [!NOTE]
> Pokud používáte soukromé koncové body, zajistěte, aby virtuální počítače mohly vyřešit privátní záznamy DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com – chyba.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace Site Recovery se nezdařila (151196)

#### <a name="possible-cause"></a>Možná příčina

Nelze navázat připojení k Microsoft 365 ověřování a koncovým bodům IP4 identity.

#### <a name="fix-the-problem"></a>Oprava problému

Azure Site Recovery požadovaný přístup pro Microsoft 365 rozsahy IP adres pro ověřování.
Pokud k řízení odchozího připojení k síti na virtuálním počítači používáte pravidla skupiny zabezpečení sítě Azure (NSG) nebo proxy serveru brány firewall, ujistěte se, že používáte pro povolení přístupu k AAD pravidlo NSG na základě [značky služby Azure Active Directory (AAD)](../virtual-network/network-security-groups-overview.md#service-tags) . Už nepodporujeme pravidla NSG založená na IP adresách.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace Site Recovery se nezdařila (151197)

#### <a name="possible-cause"></a>Možná příčina

Nepovedlo se navázat připojení k Azure Site Recovery koncovým bodům služby.

#### <a name="fix-the-problem"></a>Oprava problému

Pokud k řízení odchozího připojení k síti na virtuálním počítači používáte pravidla skupiny zabezpečení sítě Azure (NSG) nebo proxy serveru brány firewall, ujistěte se, že používáte značky služeb. Nepodporujeme používání seznamu povolených IP adres prostřednictvím skupin zabezpečení sítě pro Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problém 4: replikace se nezdařila, pokud síťový provoz používá místní proxy server (151072)

#### <a name="possible-cause"></a>Možná příčina

Vlastní nastavení proxy serveru je neplatné a agent služby mobility automaticky nerozpoznal nastavení proxy serveru z aplikace Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Oprava problému

1. Agent služby mobility detekuje nastavení proxy serveru z IE ve Windows a `/etc/environment` v systému Linux.
1. Pokud dáváte přednost nastavení proxy serveru jenom pro službu mobility, můžete zadat podrobnosti o proxy serveru v souboru _ProxyInfo. conf_ , který najdete na adrese:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ by mělo mít nastavení proxy v následujícím formátu _ini_ .

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent služby mobility podporuje jenom **neověřené proxy servery**.

### <a name="more-information"></a>Další informace

Pokud chcete zadat [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo [požadované rozsahy IP](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)adres, postupujte podle pokynů v článku [o sítích v Azure do replikace Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Na virtuálním počítači se nenašel disk (kód chyby 150039).

Je potřeba inicializovat nový disk připojený k virtuálnímu počítači. Pokud se disk nenajde, zobrazí se následující zpráva:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Možné příčiny

- K virtuálnímu počítači byl připojen nový datový disk, ale nebyl inicializován.
- Datový disk ve virtuálním počítači nehlásí správně hodnotu logické jednotky (LUN), při které byl disk připojen k virtuálnímu počítači.

### <a name="fix-the-problem"></a>Oprava problému

Zajistěte, aby byly datové disky inicializovány, a potom operaci opakujte.

- **Windows**: [připojte a inicializujte nový disk](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [inicializujte nový datový disk v systému Linux](../virtual-machines/linux/add-disk.md).

Pokud se problém opakuje, obraťte se na podporu.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>K dispozici je více disků pro ochranu (kód chyby 153039).

### <a name="possible-causes"></a>Možné příčiny

- Jeden nebo více disků byl po ochraně nedávno přidán do virtuálního počítače.
- Po ochraně virtuálního počítače se inicializoval minimálně jeden disk.

### <a name="fix-the-problem"></a>Oprava problému

Chcete-li znovu nastavit stav replikace virtuálního počítače, můžete zvolit, že chcete chránit disky, nebo zrušit upozornění.

#### <a name="to-protect-the-disks"></a>Ochrana disků

1. Přejít na disky s názvem **replikované položky**  >  _název virtuálního počítače_  >  .
1. Vyberte nechráněný disk a pak vyberte **Povolit replikaci**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Povolte replikaci na discích virtuálních počítačů.":::

#### <a name="to-dismiss-the-warning"></a>Zavření upozornění

1. Přejít na   >  _název virtuálního počítače_ replikovaných položek.
1. V části **Přehled** vyberte upozornění a pak vyberte **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Zavřít upozornění na nový disk":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Virtuální počítač odebraný z trezoru byl dokončen s informacemi (kód chyby 150225).

Když Site Recovery chrání virtuální počítač, vytvoří odkazy na zdrojovém virtuálním počítači. Když ochranu odeberete nebo zakážete, Site Recovery tyto odkazy odebrat jako součást úlohy čištění. Pokud má virtuální počítač zámek prostředků, úloha vyčištění se dokončí s informacemi. Tyto informace říká, že virtuální počítač byl odebrán z trezoru Recovery Services, ale některé ze zastaralých propojení nebylo na zdrojovém počítači možné vyčistit.

Toto upozornění můžete ignorovat, pokud už nemáte v úmyslu chránit tento virtuální počítač znovu. Pokud ale budete chtít virtuální počítač chránit později, postupujte podle kroků v této části a vyčistěte odkazy.

> [!WARNING]
> Pokud toto čištění neprovedete:
>
> - Pokud povolíte replikaci pomocí trezoru Recovery Services, virtuální počítač se nebude zobrazovat v seznamu.
> - Pokud se pokusíte ochránit virtuální počítač pomocí nastavení **virtuálního počítače**  >    >  **zotavení po havárii**, operace se nezdaří a replikace zpráv se **nedá povolit kvůli existujícím zastaralým odkazům na virtuální počítač**.

### <a name="fix-the-problem"></a>Oprava problému

> [!NOTE]
> Při provádění těchto kroků Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.

1. Odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Například na následujícím obrázku musí být odstraněn zámek prostředků na virtuálním počítači s názvem `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Odeberte z virtuálního počítače zámek.":::

1. Stáhněte si skript, kterým [odeberete starou konfiguraci Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript _Cleanup-stale-asr-config-Azure-VM.ps1_. Jako parametry zadejte **ID předplatného**, **skupinu prostředků virtuálního počítače** a **název virtuálního počítače** .
1. Pokud se zobrazí výzva k zadání přihlašovacích údajů Azure, poskytněte je. Pak ověřte, že se skript spouští bez jakýchkoli selhání.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replikace není povolená na virtuálním počítači se zastaralými prostředky (kód chyby 150226).

### <a name="possible-causes"></a>Možné příčiny

Virtuální počítač má nestarou konfiguraci z předchozí ochrany Site Recovery.

Pokud jste povolili replikaci pro virtuální počítač Azure pomocí Site Recovery, může dojít k zastaralým konfiguracím na virtuálním počítači Azure.

- Zakázali jste replikaci, ale zdrojový virtuální počítač měl zámek prostředků.
- Odstranili jste Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.
- Odstranili jste skupinu prostředků obsahující Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.

### <a name="fix-the-problem"></a>Oprava problému

> [!NOTE]
> Při provádění těchto kroků Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.

1. Odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Například na následujícím obrázku musí být odstraněn zámek prostředků na virtuálním počítači s názvem `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Odeberte z virtuálního počítače zámek.":::

1. Stáhněte si skript, kterým [odeberete starou konfiguraci Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript _Cleanup-stale-asr-config-Azure-VM.ps1_. Jako parametry zadejte **ID předplatného**, **skupinu prostředků virtuálního počítače** a **název virtuálního počítače** .
1. Pokud se zobrazí výzva k zadání přihlašovacích údajů Azure, poskytněte je. Pak ověřte, že se skript spouští bez jakýchkoli selhání.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>V úloze povolení replikace nejde vybrat virtuální počítač nebo skupinu prostředků.

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problém 1: Skupina prostředků a zdrojový virtuální počítač jsou v různých umístěních.

Site Recovery aktuálně vyžaduje, aby byla skupina prostředků zdrojové oblasti a virtuální počítače ve stejném umístění. Pokud ne, nebudete při pokusu o použití ochrany moci najít virtuální počítač nebo skupinu prostředků.

Jako alternativní řešení můžete místo Recovery Services trezoru povolit replikaci z virtuálního počítače. Přejít na **vlastnosti zdrojového virtuálního počítače**  >    >  **zotavení po havárii** a povolení replikace.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problém 2: Skupina prostředků není součástí vybraného předplatného.

Pokud skupina prostředků není součástí vybraného předplatného, možná nebudete schopni najít skupinu prostředků v době ochrany. Ujistěte se, že skupina prostředků patří k předplatnému, které používáte.

### <a name="issue-3-stale-configuration"></a>Problém 3: zastaralá konfigurace

Pokud na virtuálním počítači Azure existuje stará Site Recovery konfigurace, možná neuvidíte virtuální počítač, který chcete povolit pro replikaci. K tomuto stavu může dojít, pokud jste povolili replikaci pro virtuální počítač Azure pomocí Site Recovery a pak:

- Odstranili jste Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.
- Odstranili jste skupinu prostředků obsahující Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.
- Zakázali jste replikaci, ale zdrojový virtuální počítač měl zámek prostředků.

### <a name="fix-the-problem"></a>Oprava problému

> [!NOTE]
> `AzureRM.Resources`Před použitím skriptu uvedeného v této části nezapomeňte aktualizovat modul. Při provádění těchto kroků Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.

1. Odeberte zámek (pokud existuje) z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Například na následujícím obrázku musí být odstraněn zámek prostředků na virtuálním počítači s názvem `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Odeberte z virtuálního počítače zámek.":::

1. Stáhněte si skript, kterým [odeberete starou konfiguraci Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript _Cleanup-stale-asr-config-Azure-VM.ps1_. Jako parametry zadejte **ID předplatného**, **skupinu prostředků virtuálního počítače** a **název virtuálního počítače** .
1. Pokud se zobrazí výzva k zadání přihlašovacích údajů Azure, poskytněte je. Pak ověřte, že se skript spouští bez jakýchkoli selhání.

## <a name="unable-to-select-a-vm-for-protection"></a>Nejde vybrat virtuální počítač pro ochranu.

### <a name="possible-cause"></a>Možná příčina

Virtuální počítač má rozšíření nainstalované v neúspěšném nebo nereagující stavu.

### <a name="fix-the-problem"></a>Oprava problému

Přejít na rozšíření nastavení **virtuálních počítačů**  >    >   a vyhledat všechna rozšíření ve stavu selhání. Odinstalujte všechna neúspěšná rozšíření a pak to zkuste znovu, abyste virtuální počítač chránili.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>Stav zřizování virtuálního počítače není platný (kód chyby 150019).

Aby bylo možné povolit replikaci na virtuálním počítači, musí být jeho stav zřizování **úspěšný**. Chcete-li zjistit stav zřizování, postupujte podle následujících kroků:

1. V Azure Portal vyberte **Průzkumník prostředků** ze **všech služeb**.
1. Rozbalte seznam **předplatných** a vyberte své předplatné.
1. Rozbalte seznam **ResourceGroups** a vyberte skupinu prostředků virtuálního počítače.
1. Rozbalte seznam **prostředky** a vyberte svůj virtuální počítač.
1. V zobrazení instance na pravé straně zkontrolujte pole **provisioningState** .

### <a name="fix-the-problem"></a>Oprava problému

- Pokud se **provisioningState** **nezdařil**, obraťte se na podporu s podrobnostmi o řešení potíží.
- Pokud se **ProvisioningState** **aktualizuje**, může se nasadit jiné rozšíření. Zkontrolujte, jestli virtuální počítač neobsahuje nějaké probíhající operace, počkejte, až se dokončí, a potom zkuste úlohu, která selhala Site Recovery, povolit replikaci.

## <a name="unable-to-select-target-vm"></a>Nepovedlo se vybrat cílový virtuální počítač.

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problém 1: virtuální počítač je připojený k síti, která už je namapovaná na cílovou síť.

Pokud je zdrojový virtuální počítač součástí virtuální sítě a v konfiguraci zotavení po havárii je už namapovaný jiný virtuální počítač ze stejné virtuální sítě, rozevírací seznam výběr sítě není k dispozici (ve výchozím nastavení se zobrazuje šedě).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Seznam výběru sítě není dostupný.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problém 2: dříve jste chránili virtuální počítač a potom jste zakázali replikaci.

Vypnutím replikace virtuálního počítače se neodstraní mapování sítě. Mapování je nutné odstranit z Recovery Services trezoru, ve kterém byl virtuální počítač chráněn. Vyberte **Recovery Services trezor** a přejít na **Správa**  >  **Site Recovery infrastruktury**  >  **pro**  >  **mapování sítě** virtuálních počítačů Azure.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Odstraňte mapování sítě.":::

Cílová síť, která byla nakonfigurovaná během instalace pro zotavení po havárii, se dá po počáteční instalaci změnit a po ochraně virtuálního počítače. Pokud chcete **Upravit mapování sítě** , vyberte název sítě:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Upravte mapování sítě.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ nebo VSS (kód chyby 151025)

Při výskytu chyby COM+ nebo služba Stínová kopie svazku (VSS) se zobrazí následující zpráva:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Možné příčiny

- Služba systémová aplikace modelu COM+ je zakázána.
- Služba Stínová kopie svazku je zakázaný.

### <a name="fix-the-problem"></a>Oprava problému

Nastavte systémovou aplikaci modelu COM+ a služba Stínová kopie svazku na automatický nebo ruční režim spouštění.

1. Otevřete konzolu služby v systému Windows.
1. Ujistěte se, že systémová aplikace modelu COM+ a služba Stínová kopie svazku nejsou nastavené na **zakázáno** jako **Typ spouštění**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Ověřte typ spouštění aplikace COM a systémové aplikace a služba Stínová kopie svazku.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nepodporovaná velikost spravovaného disku (kód chyby 150172)

Při výskytu této chyby se zobrazí následující zpráva:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Možná příčina

Disk je menší než podporovaná velikost 1024 MB.

### <a name="fix-the-problem"></a>Oprava problému

Ujistěte se, že velikost disku je v rozsahu podporované velikosti, a potom operaci opakujte.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Ochrana není povolená, pokud GRUB používá název zařízení (kód chyby 151126).

### <a name="possible-causes"></a>Možné příčiny

Konfigurační soubory pro Linux Grand Unified zaváděcí program (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ nebo _/etc/default/grub_) můžou zadat skutečné názvy zařízení namísto univerzálních jedinečných hodnot identifikátorů (UUID) pro `root` parametry a `resume` . Site Recovery vyžaduje identifikátory UUID, protože se můžou změnit názvy zařízení. Po restartování se může stát, že virtuální počítač nebude při převzetí služeb při selhání pracovat se stejným názvem, což vede k problémům.

V následujících příkladech jsou řádky ze souborů GRUB, kde se místo požadovaných identifikátorů UUID zobrazují názvy zařízení:

- _/Boot/grub2/grub.cfg_ souboru:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Soubor: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Oprava problému

Nahraďte názvy každého zařízení odpovídajícím identifikátorem UUID:

1. Vyhledá UUID zařízení provedením příkazu `blkid <device name>` . Například:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Nahraďte název zařízení identifikátorem UUID ve formátech `root=UUID=<UUID>` a `resume=UUID=<UUID>` . Například po nahrazení by čára z _/boot/grub/menu.lst_ vypadala jako následující řádek:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Zkuste ochranu zopakovat.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>Ochrana se nezdařila, protože zařízení GRUB neexistuje (kód chyby 151124)

### <a name="possible-cause"></a>Možná příčina

Konfigurační soubory GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ nebo _/etc/default/grub_) můžou obsahovat parametry `rd.lvm.lv` nebo `rd_LVM_LV` . Tyto parametry identifikují zařízení LVM (Logical Volume Manager), která mají být zjištěna při spuštění. Pokud tato zařízení LVM neexistují, samotný chráněný systém se nespustí a zablokuje se v procesu spouštění. Stejný problém se také zobrazí u virtuálního počítače s podporou převzetí služeb při selhání. Tady je několik příkladů:

- Soubor: _/boot/grub2/grub.cfg_ na RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Soubor: _/etc/default/grub_ na RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Soubor: _/boot/grub/menu.lst_ na počítače RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

V každém příkladu musí GRUB detekovat dvě zařízení LVM s názvy `root` a `swap` ze skupiny svazků `rootvg` .

### <a name="fix-the-problem"></a>Oprava problému

Pokud zařízení LVM neexistuje, buď ho vytvořte, nebo odeberte odpovídající parametry z konfiguračních souborů GRUB. Pak zkuste ochranu zapnout znovu.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizace služby mobility byla dokončena s upozorněními (kód chyby 151083)

Služba Site Recovery mobility má mnoho komponent, z nichž jeden se nazývá ovladač filtru. Ovladač filtru je načten do systémové paměti pouze během restartování systému. Pokaždé, když aktualizace služby mobility zahrnuje změny ovladače filtru, počítač se aktualizuje, ale pořád se zobrazí upozornění, že některé opravy vyžadují restart. Upozornění se zobrazí, protože opravy ovladačů filtru se mohou projevit pouze při načtení nového ovladače filtru, který se stane pouze během restartování.

> [!NOTE]
> Toto je pouze upozornění. Existující replikace i nadále funguje i po novém aktualizace agenta. Můžete se rozhodnout, že se budete chtít restartovat kdykoli, když chcete mít výhody nového ovladače filtru, ale starý ovladač filtru bude fungovat, i když ho nerestartujete.
>
> Kromě ovladače filtru se výhody všech dalších vylepšení a oprav v rámci aktualizace služby mobility projeví bez nutnosti restartování.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Ochrana není povolená, pokud existuje spravovaný disk repliky.

K této chybě dojde, pokud se spravovaný disk repliky již nachází v cílové skupině prostředků bez očekávaných značek.

### <a name="possible-cause"></a>Možná příčina

K tomuto problému může dojít, pokud byl virtuální počítač dříve chráněn a když byla replikace zakázaná, disk repliky se neodebral.

### <a name="fix-the-problem"></a>Oprava problému

Odstraňte disk repliky identifikovaný v chybové zprávě a opakujte úlohu ochrany, která se nezdařila.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Ochranu se nepovedlo zapnout, protože instalační program nemůže najít kořenový disk (kód chyby 151137).

K této chybě dochází u počítačů se systémem Linux, kde je disk s operačním systémem zašifrovaný pomocí Azure Disk Encryption (ADE). Jedná se o platný problém pouze v agentovi verze 9,35.

### <a name="possible-causes"></a>Možné příčiny

Instalační program nemůže najít kořenový disk, který je hostitelem kořenového souborového systému.

### <a name="fix-the-problem"></a>Oprava problému

Pokud chcete tento problém vyřešit, postupujte podle následujících kroků:

1. Vyhledejte agenty v adresáři _/var/lib/waagent_ na počítačích s RHEL a CentOS pomocí příkazu níže: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Očekávaný výstup:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Vytvořte nový adresář a změňte adresář na tento nový adresář.
3. Extrahujte soubor agenta, který najdete v prvním kroku, pomocí následujícího příkazu:

    `tar -xf <Tar Ball File>`

4. Otevřete soubor _prereq_check_installer.jsna_ a odstraňte následující řádky. Soubor uložte za něj.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Pomocí příkazu volejte instalační program: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Pokud se instalační program úspěšně dokončí, zkuste znovu spustit úlohu povolit replikaci.

## <a name="next-steps"></a>Další kroky

[Replikace virtuálních počítačů Azure do jiné oblasti Azure](azure-to-azure-how-to-enable-replication.md)