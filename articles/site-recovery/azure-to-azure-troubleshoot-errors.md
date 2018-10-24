---
title: Azure Site Recovery pro odstraňování potíží v potíže s replikací z Azure do Azure a chyby | Dokumentace Microsoftu
description: Řešení potíží s chyb a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: sujayt
ms.openlocfilehash: 040ace1eab4062c011ed82a59e7f5bfb789c256b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945735"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Řešení potíží s replikací virtuálních počítačů Azure do Azure

Tento článek popisuje běžné problémy ve službě Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti a vysvětluje, jak jejich řešení. Další informace o podporovaných konfiguracích najdete v tématu [matice podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy s kvótou prostředků Azure (kód chyby: 150097)
Předplatné musí být povolené k vytvoření virtuálních počítačů Azure v cílové oblasti, kterou plánujete používat jako vaší oblasti pro zotavení po havárii. Vaše předplatné navíc by měl mít dostatečná kvóta k vytvoření virtuálních počítačů z určité velikosti. Ve výchozím nastavení služba Site Recovery vybere pro cílový virtuální počítač stejné velikosti jako zdrojový virtuální počítač. Pokud není k dispozici odpovídající velikost, se automaticky vybere nejbližší možnou velikost. Pokud neexistuje žádný odpovídající velikost, která podporuje konfiguraci zdrojového počítače, zobrazí se tato chybová zpráva:

**Kód chyby:** | **Možné příčiny** | **Doporučení**
--- | --- | ---
150097<br></br>**Zpráva**: nebylo možné povolit replikaci pro virtuální počítač VmName. | – K vytvoření virtuálních počítačů v cílové oblasti umístění možná není povoleno ID předplatného.</br></br>– ID předplatného může být vypnutá nebo nemá dostatečnou kvótu pro vytvoření určité velikosti virtuálních počítačů v cílové oblasti umístění.</br></br>-Vhodná Cílová velikost virtuálního počítače, který odpovídá zdroji počet síťové karty virtuálního počítače (2) nebyl nalezen pro ID předplatného v umístění cílové oblasti.| Kontakt [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) umožňující vytváření virtuálních počítačů pro velikosti virtuálních počítačů v cílové umístění pro vaše předplatné. Po jeho povolení, zkuste neúspěšnou operaci.

### <a name="fix-the-problem"></a>Tento problém vyřešit
Můžete kontaktovat [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) povolit předplatné pro vytvoření virtuálních počítačů z požadovaných velikostí v cílovém umístění.

Pokud cílová lokalita obsahuje omezení kapacity, zakažte replikaci a povolte ji do jiného umístění, kde se vaše předplatné má dostatečnou kvótu pro vytvoření virtuálních počítačů z požadovaných velikostí.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodných kořenových certifikátů (kód chyby: 151066)

Pokud všechny nejnovějších důvěryhodných kořenových certifikátů nejsou k dispozici na virtuálním počítači, vaše úloha "povolit replikaci" může selhat. Bez certifikátu ověřování a autorizaci volání služby Site Recovery z virtuálního počítače selže. Zobrazí se chybová zpráva pro neúspěšnou úlohu Site Recovery "povolit replikaci":

**Kód chyby:** | **Možná příčina** | **Recommendations** (Doporučení)
--- | --- | ---
151066<br></br>**Zpráva**: Site Recovery konfigurace se nezdařila. | Požadované důvěryhodné kořenové certifikáty se používají pro autorizaci a ověřování nejsou na počítači. | -Pro virtuální počítač s operačním systémem Windows Ujistěte se, že jsou důvěryhodných kořenových certifikátů na počítači. Informace najdete v tématu [konfigurovat Důvěryhodné kořeny a zakázané certifikáty](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Pro virtuální počítač s operačním systémem Linux postupujte podle pokynů pro důvěryhodné kořenové certifikáty publikovaných distributorem verze operačního systému Linux.

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

3. Zkontrolujte, jestli je k dispozici kořenový certifikát certifikační Autority Symantec.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Pokud není nalezen kořenový certifikát certifikační Autority Symantec, spusťte následující příkaz ke stažení souboru. Zkontrolujte chyby a postupujte podle pokynů doporučené akce pro selhání sítě.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Zkontrolujte, jestli certifikát Baltimore kořenové certifikační Autority je k dispozici.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Pokud nebyl nalezen certifikát kořenové certifikační Autority Baltimore, stáhněte si certifikát.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Zkontrolujte, jestli certifikát DigiCert_Global_Root_CA je k dispozici.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Pokud se nenajde DigiCert_Global_Root_CA, spusťte následující příkazy ke stažení certifikátu.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Spusťte rehash skript pro aktualizaci certifikátu subjektu hodnoty hash pro nově staženým certifikáty.

    ``# c_rehash``

10. Zaškrtněte, pokud předmět hashuje vytvářené symbolických odkazů pro certifikáty.

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

11. Vytvoření kopie souboru VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem s b204d74a.0 název souboru

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Vytvoření kopie souboru Baltimore_CyberTrust_Root.pem s 653b494a.0 název souboru

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

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151037-br"></a>Úkol 1: Nepovedlo se zaregistrovat virtuální počítač Azure s využitím Site Recovery (151037) </br>
- **Možná příčina** </br>
  - K řízení odchozího přístupu používáte skupiny zabezpečení sítě na virtuálním počítači a požadované IP rozsahy nejsou na seznamu povolených pro odchozí přístup.
  - Při použití brány firewall třetích stran nástrojů a požadované rozsahy IP/URL nejsou na seznamu povolených.


- **Řešení**
   - Pokud k řízení odchozího síťového připojení u virtuálního počítače používáte proxy server brány firewall, ujistěte se, že požadované adresy URL nebo rozsahy IP adres datacentra jsou povolené. Informace najdete v tématu [proxy pokyny s branou firewall](https://aka.ms/a2a-firewall-proxy-guidance).
   - Pokud k řízení odchozího síťového připojení u virtuálního počítače používáte pravidla skupiny zabezpečení sítě, ujistěte se, že rozsahy IP adres požadavků datacentra jsou povolené. Informace najdete v tématu [pokyny pro skupiny zabezpečení sítě](azure-to-azure-about-networking.md).
   - Na seznam povolených [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo [požadované rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), postupujte podle kroků v [sítě dokument s pokyny](azure-to-azure-about-networking.md).

### <a name="issue-2-site-recovery-configuration-failed-151072"></a>Úkol 2: (151072) se nepovedlo nakonfigurovat službu Site Recovery
- **Možná příčina** </br>
  - Nelze navázat připojení ke koncovým bodům služby Site Recovery


- **Řešení**
   - Pokud k řízení odchozího síťového připojení u virtuálního počítače používáte proxy server brány firewall, ujistěte se, že požadované adresy URL nebo rozsahy IP adres datacentra jsou povolené. Informace najdete v tématu [proxy pokyny s branou firewall](https://aka.ms/a2a-firewall-proxy-guidance).
   - Pokud k řízení odchozího síťového připojení u virtuálního počítače používáte pravidla skupiny zabezpečení sítě, ujistěte se, že rozsahy IP adres požadavků datacentra jsou povolené. Informace najdete v tématu [pokyny pro skupiny zabezpečení sítě](https://aka.ms/a2a-nsg-guidance).
   - Na seznam povolených [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo [požadované rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), postupujte podle kroků v [sítě dokument s pokyny](site-recovery-azure-to-azure-networking-guidance.md).

### <a name="issue-3-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Úkol 3: Replikace A2A selhala při síťový provoz prochází skrz místní proxy server (151072)
 - **Možná příčina** </br>
   - Nastavení vlastní proxy server jsou neplatné a agenta služby Mobility Azure Site Recovery bez automatického rozpoznávání nastavení proxy serveru z Internet Exploreru


 - **Řešení**
  1.    Agent služby mobility zjišťuje nastavení proxy serveru z aplikace Internet Explorer ve Windows a /etc/environment v Linuxu.
  2.  Pokud chcete nastavit proxy server pouze pro služby Mobility Azure Site Recovery, můžete zadat podrobnosti o serveru proxy v ProxyInfo.conf umístění:</br>
      - ``/usr/local/InMage/config/`` na ***Linux***
      - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` na ***Windows***
  3.    ProxyInfo.conf by měl mít nastavení proxy serveru v následujícím formátu INI. </br>
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
150039<br></br>**Zpráva**: Azure datovým diskem (DiskName) (DiskURI) s logickou jednotkou (LUN) (LUNValue) nebyl namapován na odpovídající disk ohlášený z virtuálního počítače, který má stejnou hodnotu LUN. | -Nový datový disk byl připojen k virtuálnímu počítači, ale nebyl inicializován.</br></br>-Datový disk ve virtuálním počítači nehlásí správně hodnotu logické jednotky, ve kterém byl připojen k virtuálnímu počítači.| Zajistěte, aby se inicializují datové disky a pak zkuste operaci zopakovat.</br></br>Pro Windows: [připojení a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Pro Linux: [inicializovat nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Tento problém vyřešit
Ujistěte se, že datové disky jsou inicializované a pak zkuste operaci zopakovat:

- Pro Windows: [připojení a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Pro Linux: [přidat nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Pokud se problém nevyřeší, obraťte se na podporu.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Virtuální počítač Azure pro výběr v "povolit replikaci" se nezobrazuje

 **1 příčina: Skupina prostředků a zdrojový virtuální počítač jsou v různém umístění** <br>
Azure Site Recovery aktuálně přidělených, že zdrojová skupina prostředků oblasti a virtuální počítače by měl být ve stejném umístění. Pokud to není případ pak jste nemohli najít virtuální počítač v době ochrany.

**2. příčina: Skupina prostředků není součástí vybrané předplatné** <br>
Není možné najít skupinu prostředků v době provedení ochrany, pokud není součástí dané předplatné. Ujistěte se, že skupina prostředků patří do předplatného, který se používá.

 **3 příčina: Konfigurace zastaralé** <br>
Pokud chcete povolit replikaci virtuálního počítače, to může být z důvodu stálou konfiguraci Site Recovery zůstat na virtuálním počítači Azure. Zastaralou konfiguraci by mohla zůstat na Virtuálním počítači Azure v následujících případech:

- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.
- Povolení replikace pro virtuální počítač Azure s využitím Site Recovery a pak odstranit skupinu prostředků obsahující trezor Site Recovery bez explicitně zakázat replikaci na virtuálním počítači.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Můžete použít [odebrat zastaralé skript pro konfiguraci Azure Site Recovery](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) a odebrat zastaralou konfiguraci Site Recovery na virtuálním počítači Azure. Byste měli vidět tento virtuální počítač po odebrání zastaralou konfiguraci.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Nelze vybrat pro ochranu virtuálního počítače 
 **1 příčina: má virtuální počítač některé rozšíření nainstalované ve stavu selhání nebo nereaguje** <br>
 Přejděte na virtuální počítače > Nastavení > rozšíření a zkontrolujte, jestli nejsou žádná rozšíření ve stavu selhání. Odinstalovat selhalo rozšíření a zkuste ochranu virtuálního počítače.<br>
 **2. příčina: [Stav zřizování Virtuálního počítače není platná](#vms-provisioning-state-is-not-valid-error-code-150019)**

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

**1. příčina: Pokud váš virtuální počítač je připojen k síti, která je již namapována na cílovou síť.**
- Pokud zdrojový virtuální počítač je součástí virtuální sítě a už je namapovaný jiného virtuálního počítače ze stejné virtuální síti pomocí sítě ve skupině cílových prostředků, pak podle výběru výchozí sítě rozevírací seznam se deaktivuje.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**2. příčina: Pokud jste dříve chráněných virtuálních počítačů pomocí Azure Site Recovery a zakázané replikace.**
 - Vypíná se replikace virtuálního počítače nedojde k odstranění mapování sítě. Musí být odstraněn z trezor služby recovery Services ve kterém byl virtuální počítač chráněn. </br>
 Přejděte na trezor služby recovery Services > infrastruktura Site Recovery > mapování sítě. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Cílová síť nakonfigurovaná v průběhu instalace pro zotavení po havárii můžete změnit po počáteční nastavení, jakmile je chráněný virtuální počítač. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Všimněte si, že změna mapování sítě ovlivní všechny chráněné virtuální počítače používající konkrétní síťové mapování.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ Chyba služby Stínová kopie svazku (kód chyby: 151025)
**Kód chyby:** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
151025<br></br>**Zpráva**: rozšíření služby Site recovery se nepodařilo nainstalovat | -Služba "aplikace modelu COM + systému" zakázána.</br></br>-Služba Stínová kopie svazku je zakázaná.| Nastavte služby "aplikace modelu COM + systému" a Stínová kopie svazku na automatický nebo ruční režim spouštění.

### <a name="fix-the-problem"></a>Tento problém vyřešit

Můžete otevřít konzolu "Služby" a zajistit "Aplikace COM + systému" a Stínová kopie svazku nejsou nastaveny na hodnotu "Zakázáno" pro "Typ spuštění".
  ![Chyba com](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="next-steps"></a>Další postup
[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
