---
title: Řešení potíží s replikací virtuálních počítačů Azure v Azure Site Recovery
description: Řešení chyb při replikaci virtuálních počítačů Azure na zotavení po havárii.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: b3c459c0eaac98a1cb704b4346153f77ec974188
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084927"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Řešení chyb replikace virtuálních počítačů z Azure do Azure

Tento článek popisuje, jak řešit běžné chyby v Azure Site Recovery během replikace a obnovení virtuálních počítačů Azure z jedné oblasti do druhé. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy s kvótou prostředků Azure (kód chyby 150097)

Ujistěte se, že je ve vašem předplatném povolené vytváření virtuálních počítačů Azure v cílové oblasti, kterou plánujete použít jako oblast zotavení po havárii. Také se ujistěte, že vaše předplatné má dostatečnou kvótu pro vytvoření virtuálních počítačů nezbytných velikostí. Ve výchozím nastavení Site Recovery zvolí cílovou velikost virtuálního počítače, která je stejná jako velikost zdrojového virtuálního počítače. Pokud není odpovídající velikost k dispozici, Site Recovery automaticky zvolí nejbližší dostupnou velikost.

Pokud neexistují žádné velikosti, která podporuje konfiguraci zdrojového virtuálního počítače, zobrazí se následující zpráva:

> "Replikaci nešlo povolit pro virtuální počítač *VmName*."

### <a name="possible-causes"></a>Možné příčiny

- Vaše ID předplatného nemá povoleno vytvářet žádné virtuální počítače v umístění cílové oblasti.
- ID vašeho předplatného není povolené nebo nemá dostatečnou kvótu, aby bylo možné vytvořit konkrétní velikosti virtuálních počítačů v umístění cílové oblasti.
- Nenašla se žádná vhodná Cílová velikost virtuálního počítače, která by odpovídala počtu síťových rozhraní (2) zdrojového virtuálního počítače pro ID předplatného v umístění cílové oblasti.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Obraťte se na [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a umožněte vašemu předplatnému vytváření virtuálních počítačů požadovaných velikostí v cílovém umístění. Pak zkuste neúspěšnou operaci zopakovat.

Pokud má cílové umístění omezení kapacity, zakažte na něj replikaci. Potom povolte replikaci do jiného umístění, kde má vaše předplatné dostatečnou kvótu pro vytvoření virtuálních počítačů požadovaných velikostí.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodné kořenové certifikáty (kód chyby 151066)

Pokud na virtuálním počítači neexistují všechny nejnovější důvěryhodné kořenové certifikáty, může se Site Recovery úlohy povolit replikaci zdařit. Ověřování a autorizace volání služby Site Recovery z virtuálního počítače selžou bez těchto certifikátů. 

Pokud úloha "Povolit replikaci" neproběhne úspěšně, zobrazí se následující zpráva:

> Site Recovery konfigurace se nezdařila.

### <a name="possible-cause"></a>Možná příčina

Na virtuálním počítači nejsou k dispozici důvěryhodné kořenové certifikáty, které jsou vyžadovány k autorizaci a ověřování.

### <a name="fix-the-problem"></a>Tento problém vyřešit

#### <a name="windows"></a>Windows

V případě virtuálního počítače s operačním systémem Windows nainstalujte na virtuální počítač nejnovější aktualizace Windows, aby se na tomto počítači nacházely všechny důvěryhodné kořenové certifikáty. Pokud chcete získat nejnovější kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů na virtuálních počítačích, postupujte podle obvyklého procesu správy aktualizací Windows nebo aktualizace certifikátů ve vaší organizaci.

Pokud jste v odpojeném prostředí, postupujte podle standardního procesu aktualizace Windows ve vaší organizaci získat certifikáty. Pokud požadované certifikáty není k dispozici na virtuálním počítači, volání služby Site Recovery se nezdaří z důvodu zabezpečení.

Pokud chcete ověřit, že byl problém vyřešen, přejděte na login.microsoftonline.com z prohlížeče ve virtuálním počítači.

Další informace najdete v tématu [Konfigurace důvěryhodných kořenových adresářů a zakázaných certifikátů](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Pokud chcete získat nejnovější důvěryhodné kořenové certifikáty a nejnovější seznam odvolaných certifikátů na virtuálním počítači, postupujte podle pokynů, které poskytuje distributor vaší verze operačního systému Linux.

Vzhledem k tomu, že SuSE Linux používá k údržbě seznamu certifikátů symbolické odkazy (nebo *symbolických odkazů*), postupujte podle následujících kroků:

1. Přihlaste se jako uživatel root.

1. Spuštěním tohoto příkazu změňte adresář:

    **# CD/etc/SSL/certs**

1. Ověřte, zda je k dispozici certifikát kořenové certifikační autority Symantec:

    **# LS VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Pokud se certifikát kořenové certifikační autority Symantec nenajde, spusťte následující příkaz a Stáhněte soubor. Vyhledejte případné chyby a proveďte doporučené akce při selhání sítě.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem-O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Ověřte, jestli je přítomný certifikát kořenové certifikační autority Baltimore:

    **# LS Baltimore_CyberTrust_Root. pem**

1. Pokud se nenalezne certifikát kořenové certifikační autority Baltimore, spusťte tento příkaz a Stáhněte certifikát:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem-O Baltimore_CyberTrust_Root. pem**

1. Ověřte, zda je k dispozici DigiCert_Global_Root_CA certifikát:

    **# LS DigiCert_Global_Root_CA. pem**

1. Pokud se DigiCert_Global_Root_CA nenajde, spusťte následující příkazy a Stáhněte certifikát:

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# OpenSSL x509-in DigiCertGlobalRootCA. CRT – informování formátu der – PEM-out DigiCert_Global_Root_CA. pem**

1. Spusťte skript znovu hash, který aktualizuje hodnoty hash subjektu certifikátu pro nově stažené certifikáty:

    **# c_rehash**

1. Spusťte tyto příkazy a ověřte, zda byly pro certifikáty vytvořeny hodnoty hash předmětu jako symbolických odkazů:

    - Systému

        **# ls-l | Baltimore grep**

    - Výstup:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Systému

        **# ls-l | VeriSign_Class_3_Public_Primary_Certification_Authority_G5 grep**

    - Výstup:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Systému

        **# ls-l | DigiCert_Global_Root grep**

    - Výstup:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Vytvořte kopii souboru VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem s názvem filename b204d74a. 0:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Vytvořte kopii souboru Baltimore_CyberTrust_Root. pem s názvem filename 653b494a. 0:

    **# CP Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. Vytvořte kopii souboru DigiCert_Global_Root_CA. pem s názvem filename 3513523f. 0:

    **# CP DigiCert_Global_Root_CA. pem 3513523f. 0**

1. Ověřte, zda jsou soubory k dispozici:

    - Systému

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Výstup

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro rozsahy adresám URL služby Site Recovery nebo adresy IP (kód chyby 151037 nebo 151072)

Aby mohla replikace Site Recovery fungovat, vyžaduje se odchozí připojení z virtuálního počítače na konkrétní adresy URL nebo rozsahy IP adres. Pokud se váš virtuální počítač nachází za bránou firewall nebo používá síť pravidla skupiny zabezpečení (NSG) k řízení odchozího připojení, může setkat jednu z těchto problémů.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problém 1: Nepodařilo se zaregistrovat virtuální počítač Azure s Site Recovery (kód chyby 151195).

#### <a name="possible-cause"></a>Možná příčina 

Připojení k koncovým bodům Site Recovery nelze navázat z důvodu chyby překladu DNS.

K tomuto problému dochází nejčastěji během opětovné ochrany, Pokud převezmete služby při selhání virtuálního počítače, ale server DNS není dosažitelný z oblasti zotavení po havárii (DR).

#### <a name="fix-the-problem"></a>Tento problém vyřešit

Pokud používáte vlastní server DNS, ujistěte se, že je server DNS přístupný z oblasti zotavení po havárii. Pokud chcete zjistit, jestli máte vlastní DNS, na virtuálním počítači přejdete do části *zotavení po havárii sítě* **servery DNS** > .

![Seznam vlastních serverů DNS](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Zkuste získat přístup k serveru DNS z virtuálního počítače. Pokud server není dostupný, zpřístupněte ho buď při selhání prostřednictvím serveru DNS, nebo vytvořením řádku lokality mezi sítí DR a DNS.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Problém 2: Konfigurace Site Recovery se nezdařila (kód chyby 151196)

#### <a name="possible-cause"></a>Možná příčina

Nejde navázat připojení k ověřování Office 365 a koncovým bodům IP4 identity.

#### <a name="fix-the-problem"></a>Tento problém vyřešit

Site Recovery vyžaduje přístup k rozsahům IP adres Office 365 pro ověřování.
Pokud k řízení odchozího připojení k síti na virtuálním počítači používáte pravidla Azure NSG nebo proxy serveru brány firewall, ujistěte se, že jste povolili komunikaci s rozsahy IP adres Office 365. Vytvořte pravidlo NSG založené na [značce služby Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags)a umožněte přístup ke všem IP adresám, které odpovídají službě Azure AD. Pokud v budoucnu přidáte do Azure AD nové adresy, musíte vytvořit nová pravidla NSG.

> [!NOTE]
> Pokud jsou virtuální počítače za *standardním* interním nástrojem pro vyrovnávání zatížení, nemá nástroj pro vyrovnávání zatížení ve výchozím nastavení přístup k ROZSAHŮM IP adres Office 365 (tj. Login.microsoftonline.com). Buď změňte typ interního nástroje pro vyrovnávání zatížení na *Basic* , nebo vytvořte odchozí přístup, jak je popsáno v článku [konfigurace vyrovnávání zatížení a odchozích pravidel](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Problém 3: Konfigurace Site Recovery se nezdařila (kód chyby 151197)

#### <a name="possible-cause"></a>Možná příčina

Nepovedlo se navázat připojení k Site Recovery koncovým bodům služby.

#### <a name="fix-the-problem"></a>Tento problém vyřešit

Site Recovery vyžaduje přístup k [rozsahům IP adres Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)v závislosti na oblasti. Ujistěte se, že virtuální počítač má přístup k požadovaným rozsahům IP adres.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Problém 4: replikace z Azure do Azure selhala, když síťový provoz projde místními proxy server (kód chyby 151072).

#### <a name="possible-cause"></a>Možná příčina

Vlastní nastavení proxy serveru je neplatné a agent služby Site Recovery mobility automaticky nerozpoznal nastavení proxy serveru z Internet Exploreru.

#### <a name="fix-the-problem"></a>Tento problém vyřešit

Agent služby mobility detekuje nastavení proxy serveru z Internet Exploreru ve Windows a z/etc/Environment v systému Linux.

Pokud dáváte přednost nastavení proxy serveru jenom pro službu mobility, můžete zadat podrobnosti o proxy serveru v souboru ProxyInfo. conf v těchto umístěních:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure Site Recovery\Config

V ProxyInfo. conf zadejte nastavení proxy serveru v následujícím formátu inicializačního souboru:

> [*proxy*]

> Adresa = *http://1.2.3.4*

> Port =*567*


> [!NOTE]
> Agent služby mobility Site Recovery podporuje jenom *neověřené proxy servery*.

### <a name="more-information"></a>Další informace

Pokud chcete zadat [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo [požadované rozsahy IP](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)adres, postupujte podle pokynů v článku [o sítích v Azure do replikace Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nebyl nalezen v počítači (kód chyby: 150039)

Nový disk připojen k virtuálnímu počítači musí být inicializován. Pokud se disk nenajde, zobrazí se následující zpráva:

> " *Datový disk Azure* *DiskURI* s logickou jednotkou *LUN* *LUNValue* nebyl namapován na odpovídající disk hlášený z virtuálního počítače, který má stejnou hodnotu logické jednotky (LUN).

### <a name="possible-causes"></a>Možné příčiny

- K virtuálnímu počítači byl připojen nový datový disk, ale nebyl inicializován.
- Datový disk ve virtuálním počítači nehlásí správně hodnotu logické jednotky (LUN), při které byl disk připojen k virtuálnímu počítači.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Zajistěte, aby byly datové disky inicializovány, a potom operaci opakujte.

- **Windows**: [připojte a inicializujte nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [inicializujte nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Pokud se problém nevyřeší, obraťte se na podporu.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Pro ochranu je k dispozici jeden nebo více disků (kód chyby 153039)

### <a name="possible-causes"></a>Možné příčiny

- Jeden nebo více disků byl po ochraně nedávno přidán do virtuálního počítače.
- Po ochraně virtuálního počítače se inicializoval minimálně jeden disk.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Chcete-li znovu nastavit stav replikace virtuálního počítače, můžete zvolit, že chcete chránit disky, nebo zrušit upozornění.

#### <a name="to-protect-the-disks"></a>Ochrana disků

1. Přejít na **replikované položky** > *název virtuálního počítače* > **disky**.
1. Vyberte nechráněný disk a pak vyberte **Povolit replikaci**:

    ![Povolení replikace na discích virtuálních počítačů](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Zavření upozornění

1. Přejít na **replikované položky** > *název virtuálního počítače*.
1. V části **Přehled** vyberte upozornění a pak vyberte **OK**.

    ![Zavřít upozornění na nový disk](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Odebrání virtuálního počítače z trezoru bylo dokončeno s informacemi (kód chyby 150225).

Při ochraně virtuálního počítače Site Recovery vytvoří některé odkazy na zdrojovém virtuálním počítači. Když ochranu odeberete nebo zakážete, Site Recovery tyto odkazy odebrat jako součást úlohy čištění. Pokud má virtuální počítač zámek prostředků, úloha vyčištění se dokončí s informacemi. Tyto informace říká, že virtuální počítač byl odebrán z trezoru Recovery Services, ale některé ze zastaralých propojení nebylo na zdrojovém počítači možné vyčistit.

Toto upozornění můžete ignorovat, pokud už nemáte v úmyslu chránit tento virtuální počítač znovu. Pokud ale budete chtít tento virtuální počítač chránit později, postupujte podle kroků v části "řešení problému" pro vyčištění odkazů.

> [!WARNING]
> Pokud toto čištění neprovedete:
>
> - Pokud povolíte replikaci pomocí trezoru Recovery Services, virtuální počítač se nebude zobrazovat v seznamu.
> - Pokud se pokusíte chránit virtuální počítač pomocí > **Nastavení** **virtuálního počítače** > **zotavení po havárii**, operace se nezdaří a zobrazí se zpráva "replikaci nelze povolit z důvodu existujících propojení zastaralých prostředků na virtuálním počítači".

### <a name="fix-the-problem"></a>Tento problém vyřešit

> [!NOTE]
> Při provádění těchto kroků Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.

1. Odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Například na následujícím obrázku je nutné odstranit zámek prostředků na virtuálním počítači s názvem "MoveDemo":

    ![Odebrat zámek z virtuálního počítače](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Stáhněte si skript, kterým [odeberete starou konfiguraci Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript, který se nazývá Cleanup-stale-asr-config-Azure-VM. ps1. Jako parametry zadejte ID předplatného, skupinu prostředků virtuálního počítače a název virtuálního počítače.
1. Pokud budete požádáni o zadání přihlašovacích údajů Azure, poskytněte je. Pak ověřte, že se skript spouští bez jakýchkoli selhání.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replikaci nejde povolit kvůli zastaralým odkazům na prostředky na virtuálním počítači (kód chyby 150226).

### <a name="possible-cause"></a>Možná příčina

Virtuální počítač má nestarou konfiguraci z předchozí ochrany Site Recovery.

Pokud jste povolili replikaci pro virtuální počítač Azure pomocí Site Recovery, může dojít k zastaralým konfiguracím na virtuálním počítači Azure.

- Zakázali jste replikaci, ale zdrojový virtuální počítač měl zámek prostředků.
- Odstranili jste Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.
- Odstranili jste skupinu prostředků obsahující Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.

### <a name="fix-the-problem"></a>Tento problém vyřešit

> [!NOTE]
> Při provádění těchto kroků Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.

1. Odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Například na následujícím obrázku je nutné odstranit zámek prostředků na virtuálním počítači s názvem "MoveDemo":

    ![Odebrat zámek z virtuálního počítače](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Stáhněte si skript, kterým [odeberete starou konfiguraci Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript, který se nazývá Cleanup-stale-asr-config-Azure-VM. ps1. Jako parametry zadejte ID předplatného, skupinu prostředků virtuálního počítače a název virtuálního počítače.
1. Pokud budete požádáni o zadání přihlašovacích údajů Azure, poskytněte je. Pak ověřte, že se skript spouští bez jakýchkoli selhání.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Nepovedlo se zobrazit virtuální počítač nebo skupinu prostředků Azure pro výběr v úloze povolit replikaci.

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Příčina 1: Skupina prostředků a zdrojový virtuální počítač jsou v různých umístěních

Site Recovery aktuálně vyžaduje, aby byla skupina prostředků zdrojové oblasti a virtuální počítače ve stejném umístění. Pokud ne, nebudete při pokusu o použití ochrany moci najít virtuální počítač nebo skupinu prostředků.

Jako alternativní řešení můžete místo Recovery Services trezoru povolit replikaci z virtuálního počítače. Přejít na **zdrojový virtuální počítač** > **vlastnosti** > **zotavení po havárii** a povolení replikace.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Příčina 2: Skupina prostředků není součástí vybraného předplatného.

Pokud skupina prostředků není součástí vybraného předplatného, možná nebudete schopni najít skupinu prostředků v době ochrany. Ujistěte se, že skupina prostředků patří k předplatnému, které používáte.

### <a name="cause-3-stale-configuration"></a>Příčina 3: zastaralá konfigurace

Pokud na virtuálním počítači Azure zůstala zastaralá konfigurace Site Recovery, nemusíte mít virtuální počítač, který chcete povolit pro replikaci. K tomuto stavu může dojít, pokud jste povolili replikaci pro virtuální počítač Azure pomocí Site Recovery a pak:

- Odstranili jste Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.
- Odstranili jste skupinu prostředků obsahující Site Recovery trezor bez explicitního zákazu replikace na virtuálním počítači.
- Zakázali jste replikaci, ale zdrojový virtuální počítač měl zámek prostředků.

### <a name="fix-the-problem"></a>Tento problém vyřešit

> [!NOTE]
> Před použitím skriptu uvedeného v této části nezapomeňte aktualizovat modul AzureRM. Resources.  Při provádění těchto kroků Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.

1. Odeberte zámek (pokud existuje) z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Například na následujícím obrázku je nutné odstranit zámek prostředků na virtuálním počítači s názvem "MoveDemo":

    ![Odebrat zámek z virtuálního počítače](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Stáhněte si skript, kterým [odeberete starou konfiguraci Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Spusťte skript, který se nazývá Cleanup-stale-asr-config-Azure-VM. ps1. Jako parametry zadejte ID předplatného, skupinu prostředků virtuálního počítače a název virtuálního počítače.
1. Pokud budete požádáni o zadání přihlašovacích údajů Azure, poskytněte je. Pak ověřte, že se skript spouští bez jakýchkoli selhání.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Nepovedlo se vybrat virtuální počítač pro ochranu.

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Příčina 1: virtuální počítač má rozšíření nainstalované v neúspěšném nebo nereagující stavu.

Přejít na **virtuální počítače** > **Nastavení** > **rozšíření** a vyhledat jakákoli rozšíření ve stavu selhání. Odinstalujte všechna neúspěšná rozšíření a pak to zkuste znovu, abyste virtuální počítač chránili.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Příčina 2: stav zřizování virtuálního počítače není platný.

Postup řešení potíží ve [stavu zřizování virtuálního počítače není platný](#the-vms-provisioning-state-is-not-valid-error-code-150019)dále v tomto článku.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>Stav zřizování virtuálního počítače není platný (kód chyby 150019).

Aby bylo možné povolit replikaci na virtuálním počítači, musí být jeho stav zřizování **úspěšný**. Chcete-li zjistit stav zřizování, postupujte podle následujících kroků:

1. V Azure Portal vyberte **Průzkumník prostředků** ze **všech služeb**.
1. Rozbalte **předplatná** seznam a vyberte své předplatné.
1. Rozbalte **ResourceGroups** seznam a vyberte skupinu prostředků virtuálního počítače.
1. Rozbalte seznam **prostředky** a vyberte svůj virtuální počítač.
1. Zkontrolujte pole **provisioningState** v zobrazení instance na pravé straně.

### <a name="fix-the-problem"></a>Tento problém vyřešit

- Pokud **provisioningState** je **neúspěšné**, obraťte se na podporu s podrobnostmi řešení.
- Pokud se provisioningState **aktualizuje**, může se nasadit další rozšíření. Zkontrolujte, jestli virtuální počítač neobsahuje nějaké probíhající operace, počkejte, až se dokončí, a pak zkuste neúspěšnou Site Recovery úlohy povolit replikaci.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Nejde vybrat cílový virtuální počítač (karta výběr sítě není k dispozici).

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Příčina 1: váš virtuální počítač je připojený k síti, která už je namapovaná na cílovou síť.

Pokud je zdrojový virtuální počítač součástí virtuální sítě a v cílové skupině prostředků už je namapovaný jiný virtuální počítač ze stejné virtuální sítě, rozevírací seznam pro výběr sítě není ve výchozím nastavení k dispozici (zobrazuje se šedě).

![Seznam výběru sítě není dostupný.](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Příčina 2: předtím jste virtuální počítač chránili pomocí Site Recovery a pak jste zakázali replikaci.

Vypnutím replikace virtuálního počítače se neodstraní mapování sítě. Mapování je nutné odstranit z Recovery Services trezoru, ve kterém byl virtuální počítač chráněn. Přejít na *Recovery Services trezor* > **Site Recovery infrastruktura** > **mapování sítě**.

![Odstranit mapování sítě](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Cílová síť, která byla nakonfigurovaná během instalace pro zotavení po havárii, se dá po počáteční instalaci změnit, jakmile je virtuální počítač chráněný:

![Upravit mapování sítě](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Všimněte si, že změna mapování sítě má vliv na všechny chráněné virtuální počítače, které používají stejné mapování sítě.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Chyba služby COM+ nebo Stínová kopie svazku (kód chyby 151025)

Při výskytu této chyby se zobrazí následující zpráva:

> Nepovedlo se nainstalovat rozšíření Site Recovery.

### <a name="possible-causes"></a>Možné příčiny

- Služba systémová aplikace modelu COM+ je zakázána.
- Služba Stínová kopie svazku je zakázána.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Nastavte systémové aplikace modelu COM+ a služby stínové kopie svazku na automatický nebo ruční režim spouštění.

1. Otevřete konzolu služby v systému Windows.
1. Ujistěte se, že systémová aplikace modelu COM+ a služba Stínová kopie svazku nejsou **nastaveny jako** **Typ spouštění**.

    ![Kontrolovat typ spouštění systémové aplikace modelu COM+ a služby Stínová kopie svazku](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nepodporovaná velikost spravovaného disku (kód chyby 150172)

Při výskytu této chyby se zobrazí následující zpráva:

> "Pro virtuální počítač se nepovedlo povolit ochranu, protože má *disk* s velikostí *DiskSize*) *, která je menší než minimální podporovaná velikost 1024 MB.

### <a name="possible-cause"></a>Možná příčina

Disk je menší než podporovaná velikost 1024 MB.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Ujistěte se, že velikost disku je v rozsahu podporované velikosti, a potom operaci opakujte.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Ochrana není povolená, protože konfigurace GRUB zahrnuje místo identifikátoru UUID název zařízení (kód chyby 151126).

### <a name="possible-cause"></a>Možná příčina

Konfigurační soubory pro Linux GRUB (/boot/grub/menu.lst,/boot/grub/grub.cfg,/Boot/grub2/grub.cfg nebo/etc/default/grub) můžou místo hodnot UUID pro parametry *root* a *Resume* zadat skutečné názvy zařízení. Site Recovery vyžaduje identifikátory UUID, protože se můžou změnit názvy zařízení. Po restartování se může stát, že virtuální počítač nebude při převzetí služeb při selhání pracovat se stejným názvem, což vede k problémům.

V následujících příkladech jsou řádky ze souborů GRUB, kde se místo požadovaných identifikátorů UUID zobrazují názvy zařízení (zobrazují se tučně):

- /Boot/grub2/grub.cfg souboru

  > Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** Úvod = tiché tiché showopts

- Soubor:/boot/grub/menu.lst

  > jádro/Boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **Resume =/dev/sda1** Úvod = Silent crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>Tento problém vyřešit

Nahraďte názvy každého zařízení odpovídajícím identifikátorem UUID:

1. Vyhledejte identifikátor UUID zařízení spuštěním příkazu **blkid** ***název zařízení***. Příklad:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Název zařízení nahraďte identifikátorem UUID, ve formátech **root = uuid**=*UUID* a **Resume = UUID**=*UUID*. Například po nahrazení by čára z/boot/grub/menu.lst (popsaná výše) vypadala takto:

    > jádro/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** úvodní = tichá crashkernel = 256M-: 128M showopts VGA = 0x314

1. Zkuste ochranu zopakovat.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Ochranu se nepovedlo zapnout, protože zařízení uvedené v konfiguraci GRUB neexistuje (kód chyby 151124).

### <a name="possible-cause"></a>Možná příčina

Konfigurační soubory GRUB (/boot/grub/menu.lst,/boot/grub/grub.cfg,/Boot/grub2/grub.cfg nebo/etc/default/grub) můžou obsahovat parametry *Rd.LVM.lv* nebo *rd_LVM_LV*. Tyto parametry identifikují zařízení LVM (Logical Volume Manager), která mají být zjištěna při spuštění. Pokud tato zařízení LVM neexistují, samotný chráněný systém se nespustí a zablokuje se v procesu spouštění. Stejný problém se také zobrazí u virtuálního počítače s podporou převzetí služeb při selhání. Tady je několik příkladů:

- Soubor:/Boot/grub2/grub.cfg na RHEL7:

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/dev/Mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** RHGB quiet LANG = en_US. UTF-8

- Soubor:/etc/default/grub na RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = auto **Rd. LVM. Lotyšsko = rootvg/root Rd. LVM. Lotyšsko = rootvg/swap** rhgb quiet"

- Soubor:/boot/grub/menu.lst na počítače RHEL6:

    > jádro/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = PC = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

V každém příkladu ukazuje část tučného písma, že GRUB musí detekovat dvě zařízení LVM s názvy "root" a "swap" ze skupiny svazků "rootvg".

### <a name="fix-the-problem"></a>Tento problém vyřešit

Pokud zařízení LVM neexistuje, buď ho vytvořte, nebo odeberte odpovídající parametry z konfiguračních souborů GRUB. Pak zkuste ochranu zapnout znovu.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizace služby mobility Site Recovery se dokončila s upozorněními (kód chyby 151083).

Služba Site Recovery mobility má mnoho komponent, z nichž jeden se nazývá ovladač filtru. Ovladač filtru je načten do systémové paměti pouze během restartování systému. Pokaždé, když aktualizace služby mobility zahrnuje změny ovladače filtru, počítač se aktualizuje, ale pořád se zobrazí upozornění, že některé opravy vyžadují restart. Upozornění se zobrazí, protože opravy ovladačů filtru se mohou projevit pouze při načtení nového ovladače filtru, který se stane pouze během restartování.

> [!NOTE]
> Toto je pouze upozornění. Existující replikace i nadále funguje i po novém aktualizace agenta. Můžete se rozhodnout, že se budete chtít restartovat kdykoli, když chcete mít výhody nového ovladače filtru, ale starý ovladač filtru bude fungovat, i když ho nerestartujete.
>
> Kromě ovladače filtru se výhody všech dalších vylepšení a oprav v rámci aktualizace služby mobility projeví bez nutnosti restartování.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Ochranu nelze povolit, protože spravovaný disk repliky již existuje bez očekávaných značek v cílové skupině prostředků (kód chyby 150161).

### <a name="possible-cause"></a>Možná příčina

K tomuto problému může dojít, pokud byl virtuální počítač dříve chráněn a když byla replikace zakázaná, disk repliky se nevyčistí.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Odstraňte disk repliky identifikovaný v chybové zprávě a opakujte úlohu ochrany, která se nezdařila.

## <a name="next-steps"></a>Další kroky

[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
