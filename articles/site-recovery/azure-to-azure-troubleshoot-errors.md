---
title: Azure Site Recovery pro odstraňování potíží v potíže s replikací z Azure do Azure a chyby | Dokumentace Microsoftu
description: Řešení potíží s chyb a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sujayt
ms.openlocfilehash: e0f6fcd3886ca26b51011c1d7416b942bcec5c19
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768495"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Řešení potíží s replikací virtuálních počítačů Azure do Azure

Tento článek popisuje běžné problémy ve službě Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti a vysvětluje, jak jejich řešení. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Seznam chyb
- **[Problémy s kvótou prostředků Azure (kód chyby: 150097)](#azure-resource-quota-issues-error-code-150097)** 
- **[Důvěryhodných kořenových certifikátů (kód chyby: 151066)](#trusted-root-certificates-error-code-151066)** 
- **[Odchozí připojení pro Site Recovery (kód chyby: 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)** 

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy s kvótou prostředků Azure (kód chyby: 150097)
Předplatné musí být povolené k vytvoření virtuálních počítačů Azure v cílové oblasti, kterou plánujete používat jako vaší oblasti pro zotavení po havárii. Vaše předplatné navíc by měl mít dostatečná kvóta k vytvoření virtuálních počítačů z určité velikosti. Ve výchozím nastavení služba Site Recovery vybere pro cílový virtuální počítač stejné velikosti jako zdrojový virtuální počítač. Pokud není k dispozici odpovídající velikost, se automaticky vybere nejbližší možnou velikost. Pokud neexistuje žádný odpovídající velikost, která podporuje konfiguraci zdrojového počítače, zobrazí se tato chybová zpráva:

**Kód chyby:** | **Možné příčiny** | **Doporučení**
--- | --- | ---
150097<br></br>**Zpráva**: Pro virtuální počítač VmName nebylo možné povolit replikaci. | – K vytvoření virtuálních počítačů v cílové oblasti umístění možná není povoleno ID předplatného.</br></br>– ID předplatného může být vypnutá nebo nemá dostatečnou kvótu pro vytvoření určité velikosti virtuálních počítačů v cílové oblasti umístění.</br></br>-Vhodná Cílová velikost virtuálního počítače, který odpovídá zdroji počet síťové karty virtuálního počítače (2) nebyl nalezen pro ID předplatného v umístění cílové oblasti.| Kontakt [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) umožňující vytváření virtuálních počítačů pro velikosti virtuálních počítačů v cílové umístění pro vaše předplatné. Po jeho povolení, zkuste neúspěšnou operaci.

### <a name="fix-the-problem"></a>Tento problém vyřešit
Můžete kontaktovat [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) povolit předplatné pro vytvoření virtuálních počítačů z požadovaných velikostí v cílovém umístění.

Pokud cílová lokalita obsahuje omezení kapacity, zakažte replikaci a povolte ji do jiného umístění, kde se vaše předplatné má dostatečnou kvótu pro vytvoření virtuálních počítačů z požadovaných velikostí.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodných kořenových certifikátů (kód chyby: 151066)

Pokud všechny nejnovějších důvěryhodných kořenových certifikátů nejsou k dispozici na virtuálním počítači, vaše úloha "povolit replikaci" může selhat. Bez certifikátu ověřování a autorizaci volání služby Site Recovery z virtuálního počítače selže. Zobrazí se chybová zpráva pro neúspěšnou úlohu Site Recovery "povolit replikaci":

**Kód chyby:** | **Možná příčina** | **Recommendations** (Doporučení)
--- | --- | ---
151066<br></br>**Zpráva**: Nepovedlo se nakonfigurovat službu Site Recovery. | Požadované důvěryhodné kořenové certifikáty se používají pro autorizaci a ověřování nejsou na počítači. | -Pro virtuální počítač s operačním systémem Windows Ujistěte se, že jsou důvěryhodných kořenových certifikátů na počítači. Informace najdete v tématu [konfigurovat Důvěryhodné kořeny a zakázané certifikáty](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Pro virtuální počítač s operačním systémem Linux postupujte podle pokynů pro důvěryhodné kořenové certifikáty publikovaných distributorem verze operačního systému Linux.

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

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

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

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Úkol 1: Nepovedlo se zaregistrovat virtuální počítač Azure s využitím Site Recovery (151195) </br>
- **Možná příčina** </br>
  - Nelze navázat připojení ke koncovým bodům obnovení lokality kvůli chybě rozlišení DNS.
  - To je často viděli při opětovné ochrany když budete mít převzetí služeb při selhání virtuálního počítače, ale DNS server není dostupný z oblasti pro zotavení po Havárii.
  
- **Řešení**
   - Pokud používáte vlastní DNS, ujistěte se, že DNS server přístupný z oblasti pro zotavení po havárii. Ke kontrole, pokud máte vlastní DNS, přejděte do virtuálního počítače > síť pro obnovení po havárii > servery DNS. Zkuste se na serveru DNS z virtuálního počítače. Pokud není k dispozici a pak si všechno přístupné selhání serveru DNS nebo vytvoření čáry lokality mezi sítí zotavení po Havárii a DNS.
  
    ![Chyba com](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)
 

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Úkol 2: (151196) se nepovedlo nakonfigurovat službu Site Recovery
- **Možná příčina** </br>
  - Nelze navázat připojení k Office 365 ověřování a identita IP4 koncových bodů.

- **Řešení**
  - Azure Site Recovery požadovaných pro ověřování přístupu k rozsahy IP adres Office 365.
    Pokud k řízení odchozího síťového připojení u virtuálního počítače používáte proxy server pravidla nebo brána firewall sítě Azure zabezpečení skupiny (NSG), zajistěte, aby že byla povolena komunikace s O365 rozsahy IP adres. Vytvoření [značky služby Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na základě pravidel skupiny zabezpečení sítě umožňující přístup ke všem IP adresám v odpovídající službě AAD
        - Pokud do Azure Active Directory (AAD) se přidají nové adresy v budoucnu, musíte vytvořit nová pravidla skupiny zabezpečení sítě.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Úkol 3: (151197) se nepovedlo nakonfigurovat službu Site Recovery
- **Možná příčina** </br>
  - Nelze navázat připojení ke koncovým bodům služby Azure Site Recovery.

- **Řešení**
  - Azure Site Recovery vyžaduje přístup k [rozsahy IP služby Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) v závislosti na oblasti. Ujistěte se, že požadované rozsahy ip adres jsou přístupné z virtuálního počítače.
    

### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Úkol 4: Pokud síťový provoz prochází skrz místní proxy server (151072) se nezdařila replikace A2A
 - **Možná příčina** </br>
   - Nastavení vlastní proxy server jsou neplatné a agenta služby Mobility Azure Site Recovery bez automatického rozpoznávání nastavení proxy serveru z Internet Exploreru


 - **Řešení**
   1.   Agent služby mobility zjišťuje nastavení proxy serveru z aplikace Internet Explorer ve Windows a /etc/environment v Linuxu.
   2.  Pokud chcete nastavit proxy server pouze pro služby Mobility Azure Site Recovery, můžete zadat podrobnosti o serveru proxy v ProxyInfo.conf umístění:</br>
       - ``/usr/local/InMage/config/`` na ***Linux***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` na ***Windows***
   3.   ProxyInfo.conf by měl mít nastavení proxy serveru v následujícím formátu INI.</br>
                   *proxy [Server]*</br>
                   *Adresa =http://1.2.3.4*</br>
                   *Port = 567*</br>
   4. Agenta služby Mobility Azure Site Recovery podporuje pouze ***neověřené proxy servery***.
 

### <a name="fix-the-problem"></a>Tento problém vyřešit
Na seznam povolených [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo [požadované rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), postupujte podle kroků v [sítě dokument s pokyny](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nebyl nalezen v počítači (kód chyby: 150039)

Nový disk připojen k virtuálnímu počítači musí být inicializován.

**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
150039<br></br>**Zpráva**: Azure datovým diskem (DiskName) (DiskURI) s logickou jednotkou (LUN) (LUNValue) nebyl namapován na odpovídající disk ohlášený z virtuálního počítače, který má stejnou hodnotu LUN. | -Nový datový disk byl připojen k virtuálnímu počítači, ale nebyl inicializován.</br></br>-Datový disk ve virtuálním počítači nehlásí správně hodnotu logické jednotky, ve kterém byl připojen k virtuálnímu počítači.| Zajistěte, aby se inicializují datové disky a pak zkuste operaci zopakovat.</br></br>Ve Windows: [Připojení a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Pro Linux: [Inicializovat nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Tento problém vyřešit
Ujistěte se, že datové disky jsou inicializované a pak zkuste operaci zopakovat:

- Ve Windows: [Připojení a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Pro Linux: [přidat nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Pokud se problém nevyřeší, obraťte se na podporu.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Virtuální počítač Azure pro výběr v "povolit replikaci" se nezobrazuje

 **1. příčina:  Skupinu prostředků a zdrojový virtuální počítač jsou v různém umístění** <br>
Azure Site Recovery aktuálně přidělených, že zdrojová skupina prostředků oblasti a virtuální počítače by měl být ve stejném umístění. Pokud to není případ pak jste nemohli najít virtuální počítač v době ochrany.

**2. příčina: Skupina prostředků není součástí vybrané předplatné** <br>
Není možné najít skupinu prostředků v době provedení ochrany, pokud není součástí dané předplatné. Ujistěte se, že skupina prostředků patří do předplatného, který se používá.

 **3. důvod: Zastaralou konfiguraci** <br>
Pokud chcete povolit replikaci virtuálního počítače, to může být z důvodu stálou konfiguraci Site Recovery zůstat na virtuálním počítači Azure. Zastaralou konfiguraci by mohla zůstat na Virtuálním počítači Azure v následujících případech:

- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.
- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit skupinu prostředků obsahující trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.

### <a name="fix-the-problem"></a>Tento problém vyřešit

>[!NOTE] 
>
>Ujistěte se, že modul "" azurerm.resources zavedla"" aktualizace před použitím níže uvedený skript. 

Můžete použít [odebrat zastaralé skript pro konfiguraci Azure Site Recovery](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) a odebrat zastaralou konfiguraci Site Recovery na virtuálním počítači Azure. Byste měli vidět tento virtuální počítač po odebrání zastaralou konfiguraci.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Nelze vybrat pro ochranu virtuálního počítače 
 **1. příčina:  Virtuální počítač má některá rozšíření nainstalované ve stavu selhání nebo nereaguje** <br>
 Přejděte na virtuální počítače > Nastavení > rozšíření a zkontrolujte, jestli nejsou žádná rozšíření ve stavu selhání. Odinstalovat selhalo rozšíření a zkuste ochranu virtuálního počítače.<br>
 **2. příčina:  [Stav zřizování Virtuálního počítače není platná](#vms-provisioning-state-is-not-valid-error-code-150019)**

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

**1. příčina: Pokud se váš virtuální počítač je připojený k síti, která je již namapována na cílovou síť.**
- Pokud zdrojový virtuální počítač je součástí virtuální sítě a už je namapovaný jiného virtuálního počítače ze stejné virtuální síti pomocí sítě ve skupině cílových prostředků, pak podle výběru výchozí sítě rozevírací seznam se deaktivuje.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**2. příčina: Pokud dříve chráněný virtuální počítač pomocí Azure Site Recovery a replikace zakázán.**
 - Vypíná se replikace virtuálního počítače nedojde k odstranění mapování sítě. Musí být odstraněn z trezor služby recovery Services ve kterém byl virtuální počítač chráněn. </br>
 Přejděte na trezor služby recovery Services > infrastruktura Site Recovery > mapování sítě. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Cílová síť nakonfigurovaná v průběhu instalace pro zotavení po havárii můžete změnit po počáteční nastavení, jakmile je chráněný virtuální počítač. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Všimněte si, že změna mapování sítě ovlivní všechny chráněné virtuální počítače používající konkrétní síťové mapování.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ Chyba služby Stínová kopie svazku (kód chyby: 151025)
**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
151025<br></br>**Zpráva**: Rozšíření služby Site recovery se nepodařilo nainstalovat | -Služba "aplikace modelu COM + systému" zakázána.</br></br>-Služba Stínová kopie svazku je zakázaná.| Nastavte služby "aplikace modelu COM + systému" a Stínová kopie svazku na automatický nebo ruční režim spouštění.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Můžete otevřít konzolu "Služby" a zajistit "Aplikace COM + systému" a Stínová kopie svazku nejsou nastaveny na hodnotu "Zakázáno" pro "Typ spuštění".
  ![Chyba com](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nepodporovaná spravovat velikost disku (kód chyby: 150172)


**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
150172<br></br>**Zpráva**: Nebylo možné povolit ochranu pro virtuální počítač je na něm (DiskName) s velikostí (DiskSize), která je menší než minimální podporovaná velikost 10 GB. | – Disk je menší než podporovaná velikost 1 024 MB| Zajistěte, aby velikost disku spadají do rozsahu podporovaných velikostí a zkuste operaci zopakovat. 

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Povolení ochrany se nezdařila, protože název zařízení podle konfigurace GRUB místo UUID (kód chyby: 151126)

**Možné příčiny:** </br>
Konfigurační soubory GRUB ("/ boot/grub/menu.lst", "/ boot/grub/grub.cfg", "/ boot/grub2/grub.cfg" nebo "/ etc/výchozí/grub") může obsahovat hodnotu pro parametry **kořenové** a **obnovit** jako zařízení skutečné názvy místo UUID. Site Recovery určuje UUID přístup podle názvu zařízení můžou změnit napříč restartování virtuálního počítače, protože virtuální počítač nemusí přijde up se stejným názvem na převzetí služeb při selhání, což vede k problémům. Příklad: </br>


- Následující řádek je ze souboru GRUB **/boot/grub2/grub.cfg**. <br>
*Linux /boot/vmlinuz-3.12.49-11-default **root = / dev/sda2** ${extra_cmdline} **obnovit = / dev/sda1** splash = silent bezobslužný showopts*


- Následující řádek je ze souboru GRUB **/boot/grub/menu.lst**
*jádra /boot/vmlinuz-3.0.101-63-default **root = / dev/sda2** **obnovit = / dev/sda1 ** splash = tiché crashkernel = 256M-:128M showopts vga = 0x314*

Pokud zjistíte tučné řetězec výše, GRUB má skutečné zařízení názvy parametrů "root" a "obnovit" místo UUID.
 
**Jak vyřešit:**<br>
Názvy zařízení mělo být nahrazeno s odpovídající identifikátor UUID.<br>


1. Najít identifikátor UUID zařízení spuštěním příkazu "blkid <device name>". Příklad:<br>
```
blkid /dev/sda1 
```<br>
```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
```blkid /dev/sda2```<br> 
```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
*kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device name mentioned in the GRUB configuration instead of UUID (error code 151126)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples: 

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
*linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg". 
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
 *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg". 
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
*kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
 Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  site recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:** 
Delete the mentioned replica disk in the error message and restart the failed protection job again. 

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
