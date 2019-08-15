---
title: Azure Site Recovery pro odstraňování potíží v potíže s replikací z Azure do Azure a chyby | Dokumentace Microsoftu
description: Řešení potíží s chyb a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034778"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Řešení potíží s replikací virtuálních počítačů Azure do Azure

Tento článek popisuje běžné problémy ve službě Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti a vysvětluje, jak jejich řešení. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Seznam chyb
- **[Problémy s kvótou prostředků Azure (kód chyby 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Důvěryhodné kořenové certifikáty (kód chyby 151066)](#trusted-root-certificates-error-code-151066)**
- **[Odchozí připojení pro Site Recovery (kód chyby 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy s kvótou prostředků Azure (kód chyby 150097)
Předplatné musí být povolené k vytvoření virtuálních počítačů Azure v cílové oblasti, kterou plánujete používat jako vaší oblasti pro zotavení po havárii. Vaše předplatné navíc by měl mít dostatečná kvóta k vytvoření virtuálních počítačů z určité velikosti. Ve výchozím nastavení služba Site Recovery vybere pro cílový virtuální počítač stejné velikosti jako zdrojový virtuální počítač. Pokud není k dispozici odpovídající velikost, se automaticky vybere nejbližší možnou velikost. Pokud neexistuje žádný odpovídající velikost, která podporuje konfiguraci zdrojového počítače, zobrazí se tato chybová zpráva:

**Kód chyby:** | **Možné příčiny** | **Doporučení**
--- | --- | ---
150097<br></br>**Zpráva**: Pro virtuální počítač se nepovedlo povolit replikaci VmName. | – K vytvoření virtuálních počítačů v cílové oblasti umístění možná není povoleno ID předplatného.</br></br>– ID předplatného může být vypnutá nebo nemá dostatečnou kvótu pro vytvoření určité velikosti virtuálních počítačů v cílové oblasti umístění.</br></br>-Vhodná Cílová velikost virtuálního počítače, který odpovídá zdroji počet síťové karty virtuálního počítače (2) nebyl nalezen pro ID předplatného v umístění cílové oblasti.| Kontakt [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) umožňující vytváření virtuálních počítačů pro velikosti virtuálních počítačů v cílové umístění pro vaše předplatné. Po jeho povolení, zkuste neúspěšnou operaci.

### <a name="fix-the-problem"></a>Tento problém vyřešit
Můžete kontaktovat [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) povolit předplatné pro vytvoření virtuálních počítačů z požadovaných velikostí v cílovém umístění.

Pokud cílová lokalita obsahuje omezení kapacity, zakažte replikaci a povolte ji do jiného umístění, kde se vaše předplatné má dostatečnou kvótu pro vytvoření virtuálních počítačů z požadovaných velikostí.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodné kořenové certifikáty (kód chyby 151066)

Pokud všechny nejnovějších důvěryhodných kořenových certifikátů nejsou k dispozici na virtuálním počítači, vaše úloha "povolit replikaci" může selhat. Bez certifikátu ověřování a autorizaci volání služby Site Recovery z virtuálního počítače selže. Zobrazí se chybová zpráva pro neúspěšnou úlohu Site Recovery "povolit replikaci":

**Kód chyby:** | **Možná příčina** | **Recommendations** (Doporučení)
--- | --- | ---
151066<br></br>**Zpráva**: Konfigurace Site Recovery se nezdařila. | Požadované důvěryhodné kořenové certifikáty se používají pro autorizaci a ověřování nejsou na počítači. | -Pro virtuální počítač s operačním systémem Windows Ujistěte se, že jsou důvěryhodných kořenových certifikátů na počítači. Informace najdete v tématu [konfigurovat Důvěryhodné kořeny a zakázané certifikáty](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Pro virtuální počítač s operačním systémem Linux postupujte podle pokynů pro důvěryhodné kořenové certifikáty publikovaných distributorem verze operačního systému Linux.

### <a name="fix-the-problem"></a>Tento problém vyřešit
**Windows**

Nainstalujte všechny nejnovější aktualizace Windows na virtuálním počítači tak, aby důvěryhodných kořenových certifikátů na počítači. Pokud jste v odpojeném prostředí, postupujte podle standardního procesu aktualizace Windows ve vaší organizaci získat certifikáty. Pokud požadované certifikáty není k dispozici na virtuálním počítači, volání služby Site Recovery se nezdaří z důvodu zabezpečení.

Postupujte podle typické Windows update management nebo certifikát proces správy aktualizace ve vaší organizaci můžete získat všechny nejnovější kořenové certifikáty a seznamu odvolaných certifikátů aktualizovaný certifikát na virtuálních počítačích.

Pokud chcete ověřit, že byl problém vyřešen, přejděte na login.microsoftonline.com z prohlížeče ve virtuálním počítači.

**Linux**

Postupujte podle pokynů, které jste dostali od distributora Linuxu k získání nejnovějších důvěryhodných kořenových certifikátů a nejnovější seznam odvolaných certifikátů na virtuálním počítači.

Vzhledem k tomu operačním systémem SuSE Linux používá symbolických odkazů udržovat seznam certifikátů, postupujte podle těchto kroků:

1.  Přihlaste se jako uživatel root.

2.  Spuštěním tohoto příkazu změňte adresář.

      ``# cd /etc/ssl/certs``

1. Zkontrolujte, jestli je k dispozici kořenový certifikát certifikační Autority Symantec.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Pokud není nalezen kořenový certifikát certifikační Autority Symantec, spusťte následující příkaz ke stažení souboru. Zkontrolujte chyby a postupujte podle pokynů doporučené akce pro selhání sítě.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Zkontrolujte, jestli certifikát Baltimore kořenové certifikační Autority je k dispozici.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Pokud nebyl nalezen certifikát kořenové certifikační Autority Baltimore, stáhněte si certifikát.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Zkontrolujte, jestli certifikát DigiCert_Global_Root_CA je k dispozici.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Pokud se nenajde DigiCert_Global_Root_CA, spusťte následující příkazy ke stažení certifikátu.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Spusťte rehash skript pro aktualizaci certifikátu subjektu hodnoty hash pro nově staženým certifikáty.

    ``# c_rehash``

8.  Zaškrtněte, pokud předmět hashuje vytvářené symbolických odkazů pro certifikáty.

    - Příkaz

      ``# ls -l | grep Baltimore``

    - Výstup

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Příkaz

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Výstup

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Příkaz

      ``# ls -l | grep DigiCert_Global_Root``

    - Výstup

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Vytvoření kopie souboru VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem s b204d74a.0 název souboru

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Vytvoření kopie souboru Baltimore_CyberTrust_Root.pem s 653b494a.0 název souboru

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Vytvoření kopie souboru DigiCert_Global_Root_CA.pem s 3513523f.0 název souboru

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Zkontrolujte, jestli jsou soubory k dispozici.  

    - Příkaz

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Výstup

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro rozsahy adresám URL služby Site Recovery nebo adresy IP (kód chyby 151037 nebo 151072)

U replikace Site Recovery pro práci, odchozí připojení ke konkrétní adresy URL nebo IP rozsahy se vyžaduje z virtuálního počítače. Pokud se váš virtuální počítač nachází za bránou firewall nebo používá síť pravidla skupiny zabezpečení (NSG) k řízení odchozího připojení, může setkat jednu z těchto problémů.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problém 1: Nepovedlo se zaregistrovat virtuální počítač Azure s Site Recovery (151195) </br>
- **Možná příčina** </br>
  - Nelze navázat připojení k Site Recovery koncovým bodům z důvodu chyby překladu názvů DNS.
  - To je často viděli při opětovné ochrany když budete mít převzetí služeb při selhání virtuálního počítače, ale DNS server není dostupný z oblasti pro zotavení po Havárii.

- **Řešení**
   - Pokud používáte vlastní DNS, ujistěte se, že DNS server přístupný z oblasti pro zotavení po havárii. Ke kontrole, pokud máte vlastní DNS, přejděte do virtuálního počítače > síť pro obnovení po havárii > servery DNS. Zkuste se na serveru DNS z virtuálního počítače. Pokud není k dispozici a pak si všechno přístupné selhání serveru DNS nebo vytvoření čáry lokality mezi sítí zotavení po Havárii a DNS.

    ![Chyba com](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace Site Recovery se nezdařila (151196)
- **Možná příčina** </br>
  - Nelze navázat připojení k Office 365 ověřování a identita IP4 koncových bodů.

- **Řešení**
  - Azure Site Recovery požadovaných pro ověřování přístupu k rozsahy IP adres Office 365.
    Pokud k řízení odchozího síťového připojení u virtuálního počítače používáte proxy server pravidla nebo brána firewall sítě Azure zabezpečení skupiny (NSG), zajistěte, aby že byla povolena komunikace s O365 rozsahy IP adres. Vytvoření [značky služby Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na základě pravidel skupiny zabezpečení sítě umožňující přístup ke všem IP adresám v odpovídající službě AAD
      - Pokud do Azure Active Directory (AAD) se přidají nové adresy v budoucnu, musíte vytvořit nová pravidla skupiny zabezpečení sítě.

> [!NOTE]
> Pokud jsou virtuální počítače za **standardním** interním nástrojem pro vyrovnávání zatížení, neměli by mít přístup k IP adresám O365, tj. login.microsoftonline.com ve výchozím nastavení. Buď změňte na **základní** typ interního nástroje pro vyrovnávání zatížení, nebo vytvořte vázaný přístup, jak je uvedeno v [článku](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace Site Recovery se nezdařila (151197)
- **Možná příčina** </br>
  - Nelze navázat připojení ke koncovým bodům služby Azure Site Recovery.

- **Řešení**
  - Azure Site Recovery vyžaduje přístup k [rozsahy IP služby Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) v závislosti na oblasti. Ujistěte se, že požadované rozsahy ip adres jsou přístupné z virtuálního počítače.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problém 4: Replikace A2A selhala, když se síťový provoz přechází prostřednictvím místních proxy server (151072)
- **Možná příčina** </br>
  - Vlastní nastavení proxy serveru je neplatné a agent služby mobility Azure Site Recovery nerozpoznal nastavení proxy automaticky z IE.


- **Řešení**
  1. Agent služby mobility zjišťuje nastavení proxy serveru z aplikace Internet Explorer ve Windows a /etc/environment v Linuxu.
  2. Pokud dáváte přednost nastavení proxy serveru jenom pro Azure Site Recovery službu mobility, můžete zadat podrobnosti o proxy serveru v souboru ProxyInfo. conf, který najdete na adrese:</br>
     - ``/usr/local/InMage/config/`` na ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` na ***Windows***
  3. ProxyInfo.conf by měl mít nastavení proxy serveru v následujícím formátu INI.</br>
                *proxy [Server]*</br>
                *Adresa =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Agent služby mobility Azure Site Recovery podporuje jenom neověřené ***proxy servery***.


### <a name="fix-the-problem"></a>Tento problém vyřešit
Pokud chcete povolené [adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo požadované [rozsahy IP](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)adres, postupujte podle kroků v [dokumentu s pokyny k síti](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nebyl nalezen v počítači (kód chyby: 150039)

Nový disk připojen k virtuálnímu počítači musí být inicializován.

**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
150039<br></br>**Zpráva**: Datový disk Azure (DiskURI) s logickou jednotkou (LUN) (LUNValue) nebyl namapován na odpovídající disk, který je hlášený z virtuálního počítače, který má stejnou hodnotu logické jednotky (LUN). | -Nový datový disk byl připojen k virtuálnímu počítači, ale nebyl inicializován.</br></br>-Datový disk ve virtuálním počítači nehlásí správně hodnotu logické jednotky, ve kterém byl připojen k virtuálnímu počítači.| Zajistěte, aby se inicializují datové disky a pak zkuste operaci zopakovat.</br></br>Ve Windows: [Připojit a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Pro Linux: [Inicializujte nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Tento problém vyřešit
Ujistěte se, že datové disky jsou inicializované a pak zkuste operaci zopakovat:

- Ve Windows: [Připojit a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Pro Linux: [přidat nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Pokud se problém nevyřeší, obraťte se na podporu.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Pro ochranu je k dispozici jeden nebo více disků (kód chyby 153039)
- **Možná příčina** </br>
  - v případě, že jeden nebo více disků byl po ochraně nedávno přidán do virtuálního počítače. 
  - v případě, že jeden nebo více disků bylo inicializováno později po ochraně virtuálního počítače.

### <a name="fix-the-problem"></a>Tento problém vyřešit
Můžete buď vybrat možnost chránit disky, nebo ignorovat upozornění, aby se stav replikace virtuálního počítače znovu provedl.</br>
1. K ochraně disků. Přejděte na replikované položky > > disky virtuálních počítačů > klikněte na nechráněný disk > Povolit replikaci.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. K zavření upozornění. Přejděte na replikované položky > virtuálního počítače > v části Přehled klikněte na Zavřít výstrahu.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Odebrání virtuálního počítače z trezoru bylo dokončeno s informacemi (kód chyby 150225).
V době ochrany virtuálního počítače Azure Site Recovery vytvoří některé odkazy na zdrojovém virtuálním počítači. Po odebrání ochrany nebo zakázání replikace Azure Site Recovery odebrat tyto odkazy jako součást úlohy čištění. V případě, že virtuální počítač má zámek prostředku, úloha se dokončí s informacemi. Informuje o tom, že se virtuální počítač odebral z trezoru služby Recovery Services, ale některé ze zastaralých odkazů nešlo vyčistit ze zdrojového počítače.

Toto upozornění můžete ignorovat, pokud už nechcete v budoucnu chránit tento virtuální počítač znovu. Pokud ale budete chtít virtuální počítač chránit později, měli byste vyčistit odkazy, jak je uvedeno v následujících krocích. 

**Pokud neprovedete čištění, proveďte následující:**

1.  Během povolování replikace prostřednictvím trezoru služby Recovery Services nebude virtuální počítač v seznamu uveden. 
2.  Pokud se pokusíte chránit virtuální počítač pomocí **> nastavení virtuálního počítače > zotavení po havárii** , nepodaří se mu*Povolit replikaci kvůli existujícím zastaralým propojením prostředků na virtuálním*počítači.


### <a name="fix-the-problem"></a>Tento problém vyřešit

>[!NOTE]
>
>Při provádění níže uvedených kroků Azure Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.
>

1. Odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. Příklad: Níže uvedený název virtuálního počítače "MoveDemo" má zámek prostředků, který je nutné odstranit.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Stáhněte si skript [Odebrat zastaralou konfiguraci Azure Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Spusťte skript *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Zadejte ID předplatného, skupinu prostředků virtuálního počítače a název virtuálního počítače jako parametr.
5. Pokud se zobrazí výzva k zadání přihlašovacích údajů Azure, poskytněte prosím, že se skript spustí bez jakýchkoli selhání. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Replikaci nejde povolit, protože na virtuálním počítači existují existující odkazy na zastaralé prostředky (kód chyby 150226).

**Příčina: Virtuální počítač má nestarou konfiguraci nalevo od předchozí ochrany Site Recovery.**

Zastaralou konfiguraci by mohla zůstat na Virtuálním počítači Azure v následujících případech:

- Replikaci pro virtuální počítač Azure jste povolili pomocí Site Recovery a pak zakážete replikaci, ale **zdrojový virtuální počítač měl zámek prostředků**.
- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.
- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit skupinu prostředků obsahující trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.

### <a name="fix-the-problem"></a>Tento problém vyřešit

>[!NOTE]
>
>Při provádění níže uvedených kroků Azure Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.


1. Pokud existují, odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. *Příklad:* Níže uvedený název virtuálního počítače "MoveDemo" má zámek prostředků, který je nutné odstranit.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Stáhněte si skript [Odebrat zastaralou konfiguraci Azure Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Spusťte skript *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Zadejte ID předplatného, skupinu prostředků virtuálního počítače a název virtuálního počítače jako parametr.
5. Pokud se zobrazí výzva k zadání přihlašovacích údajů Azure, poskytněte prosím, že se skript spustí bez jakýchkoli selhání.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Nepovedlo se zobrazit virtuální počítač nebo skupinu prostředků Azure pro výběr v možnosti Povolit replikaci.

 **1. příčina:  Skupina prostředků a zdrojový virtuální počítač jsou v jiném umístění.**
 
Azure Site Recovery aktuálně má za následek, že skupina prostředků zdrojové oblasti a virtuální počítače by měly být ve stejném umístění. Pokud tomu tak není, nebudete moci najít virtuální počítač nebo skupinu prostředků v době ochrany. 

**Jako alternativní řešení**můžete místo trezoru služby Recovery Services povolit replikaci z virtuálního počítače. Přejít na zdrojový virtuální počítač > Vlastnosti > zotavení po havárii a povolení replikace.

**2. příčina: Skupina prostředků není součástí vybraného předplatného.**

Není možné najít skupinu prostředků v době provedení ochrany, pokud není součástí dané předplatné. Ujistěte se, že skupina prostředků patří do předplatného, který se používá.

 **Příčina 3: Stará konfigurace**
 
Pokud chcete povolit replikaci virtuálního počítače, to může být z důvodu stálou konfiguraci Site Recovery zůstat na virtuálním počítači Azure. Zastaralou konfiguraci by mohla zůstat na Virtuálním počítači Azure v následujících případech:

- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.
- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit skupinu prostředků obsahující trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.

- Replikaci pro virtuální počítač Azure jste povolili pomocí Site Recovery a pak zakážete replikaci, ale zdrojový virtuální počítač měl zámek prostředků.

### <a name="fix-the-problem"></a>Tento problém vyřešit

> [!NOTE]
>
> Před použitím skriptu uvedeného níže nezapomeňte aktualizovat modul AzureRM. Resources. Při provádění níže uvedených kroků Azure Site Recovery neodstraní zdrojový virtuální počítač nebo ho nijak neovlivní.
>

1. Pokud existují, odeberte zámek z virtuálního počítače nebo skupiny prostředků virtuálního počítače. *Příklad:* Níže uvedený název virtuálního počítače "MoveDemo" má zámek prostředků, který je nutné odstranit.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Stáhnout skript [Odebrat starou konfiguraci](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Spusťte skript *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Zadejte ID předplatného, skupinu prostředků virtuálního počítače a název virtuálního počítače jako parametr.
5. Pokud se zobrazí výzva k zadání přihlašovacích údajů Azure, poskytněte prosím, že se skript spustí bez jakýchkoli selhání.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Nelze vybrat pro ochranu virtuálního počítače
 **1. příčina:  Virtuální počítač má některá rozšíření nainstalovaná v neúspěšném nebo nereagující stavu.** <br>
 Přejděte na virtuální počítače > Nastavení > rozšíření a zkontrolujte, jestli nejsou žádná rozšíření ve stavu selhání. Odinstalovat selhalo rozšíření a zkuste ochranu virtuálního počítače.<br>
 **2. příčina:  [Stav zřizování virtuálního počítače není platný.](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Stav zřizování Virtuálního počítače není platný (kód chyby: 150019)

Pokud chcete povolit replikaci na virtuálním počítači, by měla být stav zřizování **Succeeded**. Stav virtuálního počítače můžete zkontrolovat pomocí následujících kroků.

1.  Vyberte **Průzkumníka prostředků** z **všechny služby** na webu Azure portal.
2.  Rozbalte **předplatná** seznam a vyberte své předplatné.
3.  Rozbalte **ResourceGroups** seznam a vyberte skupinu prostředků virtuálního počítače.
4.  Rozbalte **prostředky** seznam a vyberte virtuální počítač
5.  Zkontrolujte **provisioningState** v zobrazení Instance na pravé straně.

### <a name="fix-the-problem"></a>Tento problém vyřešit

- Pokud **provisioningState** je **neúspěšné**, obraťte se na podporu s podrobnostmi řešení.
- Pokud **provisioningState** je **aktualizace**, jiné rozšíření může být získání nasazený. Zkontrolujte, jestli jsou všechny probíhající operace na virtuálním počítači, počkejte na jejich dokončení a opakujte neúspěšné Site Recovery **povolit replikaci** úlohy.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Nelze vybrat cílové virtuální sítě – výběr kartu Síť je zobrazena šedě.

**1. příčina: Pokud je váš virtuální počítač připojený k síti, která už je namapovaná na cílovou síť.**
- Pokud zdrojový virtuální počítač je součástí virtuální sítě a už je namapovaný jiného virtuálního počítače ze stejné virtuální síti pomocí sítě ve skupině cílových prostředků, pak podle výběru výchozí sítě rozevírací seznam se deaktivuje.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**2. příčina: Pokud jste virtuální počítač dříve chránili pomocí Azure Site Recovery a zakázali replikaci.**
 - Vypíná se replikace virtuálního počítače nedojde k odstranění mapování sítě. Musí být odstraněn z trezor služby recovery Services ve kterém byl virtuální počítač chráněn. </br>
 Přejděte na trezor služby recovery Services > infrastruktura Site Recovery > mapování sítě. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Cílová síť nakonfigurovaná v průběhu instalace pro zotavení po havárii můžete změnit po počáteční nastavení, jakmile je chráněný virtuální počítač. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Všimněte si, že změna mapování sítě ovlivní všechny chráněné virtuální počítače používající konkrétní síťové mapování.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ Chyba služby Stínová kopie svazku (kód chyby: 151025)

**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
151025<br></br>**Zpráva**: Nepovedlo se nainstalovat rozšíření Site Recovery. | -Služba "aplikace modelu COM + systému" zakázána.</br></br>-Služba Stínová kopie svazku je zakázaná.| Nastavte služby "aplikace modelu COM + systému" a Stínová kopie svazku na automatický nebo ruční režim spouštění.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Můžete otevřít konzolu "Služby" a zajistit "Aplikace COM + systému" a Stínová kopie svazku nejsou nastaveny na hodnotu "Zakázáno" pro "Typ spuštění".
  ![Chyba com](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nepodporovaná velikost spravovaného disku (kód chyby 150172)


**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
150172<br></br>**Zpráva**: Pro virtuální počítač se nepovedlo povolit ochranu, protože má (DiskSize) s velikostí (), která je menší než minimální podporovaná velikost 1024 MB. | -Disk je menší než podporovaná velikost 1024 MB.| Zajistěte, aby velikosti disků byly v rozsahu podporované velikosti, a operaci opakujte.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Ochranu se nepovedlo zapnout, protože název zařízení uvedený v konfiguraci GRUB místo UUID (kód chyby 151126).

**Možná příčina:** </br>
Konfigurační soubory GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/GRUB") můžou obsahovat hodnotu pro **kořen** parametrů a **obnovit** je jako skutečné názvy zařízení namísto UUID. Site Recovery přístup k identifikátorům UUID, protože název zařízení se může změnit v průběhu restartování virtuálního počítače, protože virtuální počítač nemusí při převzetí služeb při selhání přijít se stejným názvem, což způsobí problémy. Příklad: </br>


- Následující řádek je ze souboru GRUB **/boot/grub2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** Úvod = tiché tiché showopts*


- Následující řádek je ze souboru GRUB **/boot/grub/menu.lst** 
  *jádra /boot/vmlinuz-3.0.101-63-default **root = / dev/sda2** **obnovit = / dev/sda1** splash = tiché crashkernel = 256M-:128M showopts vga = 0x314*

Pokud si vyberete tučný řetězec uvedený výše, GRUB má skutečné názvy zařízení pro parametry "root" a "Resume" místo UUID.

**Jak opravit:**<br>
Názvy zařízení by měly být nahrazeny odpovídajícím identifikátorem UUID.<br>


1. Vyhledejte UUID zařízení spuštěním příkazu "blkid \<Device Name >". Příklad:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. Nyní nahraďte název zařízení identifikátorem UUID ve formátu jako "root = UUID =\<UUID >". Pokud například nahradíte názvy zařízení identifikátorem UUID pro kořen a parametr Resume uvedené výše v souborech "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/grub: řádky v souborech vypadají jako. <br>
   *jádro/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** úvodní = tichá crashkernel = 256M-: 128M showopts VGA = 0x314*
1. Znovu znovu spusťte ochranu.

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>Ochranu se nepovedlo zapnout, protože zařízení zmíněné v konfiguraci GRUB neexistuje (kód chyby 151124).
**Možná příčina:** </br>
Konfigurační soubory GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/GRUB") mohou obsahovat parametry "rd.lvm.lv" nebo "rd_LVM_LV", které označují zařízení LVM, které by mělo být zjištěno v době spuštění. Pokud tato zařízení LVM neexistují, samotný chráněný systém se nespustí a zablokuje se v procesu spouštění. I když se u virtuálního počítače s podporou převzetí služeb při selhání bude dodržovat i stejné. Níže jsou uvedeny některé příklady:

Několik příkladů: </br>

1. Následující řádek je ze souboru GRUB **"/boot/grub2/grub.cfg"** na RHEL7. </br>
   *linux16/vmlinuz-3.10.0-957.el7.x86_64 root =/dev/Mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. Lotyšsko = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet lang = en_US. UTF-8*</br>
   Na zvýrazněné části se dozvíte, že GRUB musí detekovat dvě zařízení LVM s názvy **"root"** a **"swap"** ze skupiny svazků "rootvg".
1. Následující řádek je ze souboru GRUB **"/etc/default/GRUB"** na RHEL7 </br>
   *GRUB_CMDLINE_LINUX = "crashkernel = auto **Rd. LVM. Lotyšsko = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet"*</br>
   Na zvýrazněné části se dozvíte, že GRUB musí detekovat dvě zařízení LVM s názvy **"root"** a **"swap"** ze skupiny svazků "rootvg".
1. Následující řádek je ze souboru GRUB **"/boot/grub/menu.lst"** na počítače RHEL6 </br>
   */vmlinuz-2.6.32-754.el6.x86_64 jádra ro = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto rd_LVM_LV = rootvg/lv_root KEYBOARDTYPE = PC TABLE = US rd_LVM_LV = rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Na zvýrazněné části se dozvíte, že GRUB musí detekovat dvě zařízení LVM s názvy **"root"** a **"swap"** ze skupiny svazků "rootvg".<br>

**Jak opravit:**<br>

Pokud zařízení LVM neexistuje, opravte ho tak, že ho vytvoříte, nebo odeberte parametr pro stejný z konfiguračních souborů GRUB a pak zkuste povolit ochranu znovu. </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>Aktualizace služby mobility Site Recovery se dokončila s upozorněními (kód chyby 151083).
Služba Site Recovery mobility má mnoho komponent, z nichž jedna se nazývá ovladač filtru. Ovladač filtru se načte do systémové paměti pouze v době restartování systému. Pokaždé, když jsou dostupné aktualizace služby mobility Site Recovery s ovladačem filtru, aktualizujeme počítač, ale pořád vám pošle upozornění na to, že některé opravy vyžadují restart. To znamená, že opravy ovladačů filtru je možné realizovat pouze při načtení nového ovladače filtru, ke kterému může dojít pouze v době restartování systému.<br>
**Všimněte si** , že toto je pouze upozornění a stávající replikace funguje i po nové aktualizaci agenta. Můžete se rozhodnout, že budete chtít kdykoli restartovat, když chcete získat výhody nového ovladače filtru, ale pokud ho nerestartujete, než bude i nadále fungovat starý ovladač filtru. Kromě ovladače filtru jsou **výhody jakékoli jiné vylepšení a oprav v rámci služby mobility realizované bez nutnosti jakéhokoli restartování, když se Agent aktualizuje.**  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Ochranu se nepovedlo povolit, protože v cílové skupině prostředků už existuje replika, která má spravovaný disk repliky, a to bez očekávaných značek (kód chyby 150161.

**Příčina:** Může k tomu dojít, pokud byl virtuální počítač chráněn dříve v minulosti a během zakazování replikace nebyl z nějakého důvodu vyčištěný disk repliky.</br>
**Jak opravit:** Odstraňte uvedený disk repliky v chybové zprávě a znovu spusťte úlohu ochrany, která se nezdařila.

## <a name="next-steps"></a>Další postup
[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
