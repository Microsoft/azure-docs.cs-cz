---
title: Azure Site Recovery pro odstraňování potíží v Azure do Azure replikace problémy a chyby | Microsoft Docs
description: Řešení potíží s chyb a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: sujayt
ms.openlocfilehash: e6f694e601c506adae217e6348b4bf388cc24390
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211826"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Řešení problémů replikace virtuálního počítače Azure do Azure

Tento článek popisuje běžné problémy při Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti a vysvětluje, jak je vyřešit. Další informace o podporovaných konfiguracích najdete v tématu [matici podpory pro replikaci virtuálních počítačů Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problémy kvótu prostředků Azure (kód chyby 150097)
Vaše předplatné by měly být povoleny k vytvoření virtuálních počítačů Azure v oblasti cíl, který chcete použít jako svou oblast pro obnovení po havárii. Vaše předplatné navíc by měl mít dostatečná kvóta pro vytvoření virtuálních počítačů určité velikosti. Ve výchozím nastavení vybere Site Recovery pro cílový počítač stejnou velikost jako zdroj virtuálního počítače. Pokud odpovídající velikost není k dispozici, je automaticky vybrána nejbližší možné velikost. Pokud není k dispozici žádné odpovídající velikost, která podporuje konfiguraci zdrojového virtuálního počítače, zobrazí se tato chybová zpráva:

**Kód chyby** | **Možné příčiny** | **Doporučení**
--- | --- | ---
150097<br></br>**Zpráva**: nebylo možné povolit replikaci pro virtuální počítač VmName. | -Chcete-li vytvořit všechny virtuální počítače v cílovém umístění oblasti může být vypnutá svoje ID předplatného.</br></br>-Svoje ID předplatného nemusí být povoleno nebo nemá dostatečnou kvótu k vytvoření určité velikosti virtuálních počítačů v cílovém umístění oblasti.</br></br>-A vhodnou cílovou velikost virtuálního počítače, který odpovídá zdrojového virtuálního počítače síťový adaptér počet (2) nebyl nalezen pro ID předplatného v cílovém umístění oblasti.| Obraťte se na [Azure podporu fakturace](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) umožňující vytvoření virtuálních počítačů pro požadované velikosti virtuálních počítačů v cílovém umístění pro vaše předplatné. Když je tato funkce povolená, opakujte operaci se nezdařilo.

### <a name="fix-the-problem"></a>Opravte problém
Obraťte se na [Azure podporu fakturace](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) povolit vaše předplatné přístup k vytvoření požadovaných velikostí virtuální počítače v cílovém umístění.

Pokud cílové umístění obsahuje omezení kapacity, zakažte replikaci a povolte ho do jiného umístění, kde vaše předplatné má dostatečnou kvótu k vytvoření virtuálních počítačů pro požadované velikosti.

## <a name="trusted-root-certificates-error-code-151066"></a>Důvěryhodné kořenové certifikáty (kód chyby 151066)

Pokud nejsou k dispozici na virtuální počítač všechny nejnovější důvěryhodných kořenových certifikátů, může selhat úlohu "povolení replikace". Bez certifikáty ověřování a autorizace volání služby Site Recovery z virtuálního počítače nezdaří. Zobrazí se chybová zpráva pro nezdařené úlohy Site Recovery "povolit replikaci":

**Kód chyby** | **Možná příčina** | **Recommendations** (Doporučení)
--- | --- | ---
151066<br></br>**Zpráva**: Site Recovery konfigurace se nezdařila. | Požadované důvěryhodných kořenových certifikátů použít k ověřování a ověřování není na tomto počítači. | -Pro virtuální počítač s operačním systémem Windows zajistěte, aby byly na tomto počítači důvěryhodných kořenových certifikátů. Informace najdete v tématu [konfigurovat Důvěryhodné kořeny a zakázané certifikáty](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Pro virtuální počítač s operačním systémem Linux postupujte podle pokynů pro důvěryhodných kořenových certifikátů, které zveřejnil distributora verze operačního systému Linux.

### <a name="fix-the-problem"></a>Opravte problém
**Windows**

Nainstalujte všechny nejnovější aktualizace systému Windows ve virtuálním počítači tak, aby byly všechny důvěryhodných kořenových certifikátů na tomto počítači. Pokud jste v odpojeném prostředí, postupujte podle standardního procesu aktualizace systému Windows ve vaší organizaci získat certifikáty. Pokud požadované certifikáty nejsou k dispozici na virtuální počítač, nezdaří se volání do služby Site Recovery z bezpečnostních důvodů.

Postupujte podle typické Windows update management nebo certifikát proces správy aktualizace ve vaší organizaci získat všechny nejnovější kořenové certifikáty a seznamu odvolaných certifikátů aktualizovaný certifikát na virtuálních počítačích.

Chcete-li ověřit, že byl problém vyřešen, přejděte na login.microsoftonline.com v virtuálního počítače z prohlížeče.

**Linux**

Postupujte podle pokynů uvedených ve vaší Linux distributora, chcete-li získat nejnovější důvěryhodných kořenových certifikátů a nejnovější seznam odvolaných certifikátů ve virtuálním počítači.

Protože SuSE Linux používá symbolických odkazů k údržbě seznamu certifikátů, postupujte takto:

1.  Přihlaste se jako kořenové uživatele.

2.  Chcete-li změnit adresář tento příkaz spusťte.

      ``# cd /etc/ssl/certs``

3. Zkontrolujte, zda certifikátu Symantec kořenové certifikační Autority je přítomen.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Pokud nebyl nalezen certifikát Symantec kořenové certifikační Autority, spusťte následující příkaz ke stažení souboru. Zkontrolujte chyby a postupujte podle doporučenou akci pro selhání sítě.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Zkontrolujte, zda certifikátu Baltimore kořenové certifikační Autority je přítomen.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Pokud nebyl nalezen certifikát kořenové certifikační Autority Baltimore, stáhněte certifikát.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Zkontrolujte, jestli je certifikát DigiCert_Global_Root_CA přítomen.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Pokud DigiCert_Global_Root_CA není nalezen, spusťte následující příkazy ke stažení certifikátu.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Spuštěním skriptu rehash aktualizovat certifikát subjektu hodnoty hash pro nově stažené certifikáty.

    ``# c_rehash``

10. Zkontrolujte, pokud předmět hashuje vytvářené symbolických odkazů pro certifikáty.

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

11. Vytvořit kopii souboru VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem s filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Vytvořit kopii souboru Baltimore_CyberTrust_Root.pem s filename 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Vytvořit kopii souboru DigiCert_Global_Root_CA.pem s filename 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Zkontrolujte, zda jsou k dispozici soubory.  

    - Příkaz

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Výstup

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro rozsahy adres URL obnovení lokality nebo IP adresu (kód chyby 151037 nebo 151072)

U replikace Site Recovery k práci, odchozí připojení ke konkrétní adresy URL nebo IP adresu se rozsahy vyžaduje z virtuálního počítače. Pokud virtuální počítač je za bránou firewall nebo pravidla skupiny (NSG) zabezpečení sítě používá k řízení odchozí připojení, můžete sledovat některé z těchto chybových zpráv:

**Kódy chyb** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
151037<br></br>**Zpráva**: Nepodařilo se zaregistrovat virtuální počítač Azure Site Recovery. | -Používáte NSG k řízení odchozí přístup na virtuální počítač a požadované IP adresy rozsahů nejsou povolené pro odchozí přístup.</br></br>-Používáte nástroji brány firewall třetích stran a požadované rozsahy IP nebo URL nejsou povolené.</br>| – Pokud používáte proxy server brány firewall pro řízení odchozí síťové připojení ve virtuálním počítači, ujistěte se, jestli jsou požadované adresy URL a rozsahy IP datacenter seznam povolených adres. Informace najdete v tématu [brány firewall proxy pokyny](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>– Pokud používáte pravidla NSG k řízení odchozí síťové připojení ve virtuálním počítači, zajistěte, aby rozsahy IP požadovaných datacenter seznam povolených adres. Informace najdete v tématu [sítě doprovodné materiály zabezpečení skupiny](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Zpráva**: Site Recovery konfigurace se nezdařila. | Nelze navázat připojení ke koncovým bodům služby Site Recovery. | – Pokud používáte proxy server brány firewall pro řízení odchozí síťové připojení ve virtuálním počítači, ujistěte se, jestli jsou požadované adresy URL a rozsahy IP datacenter seznam povolených adres. Informace najdete v tématu [brány firewall proxy pokyny](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>– Pokud používáte pravidla NSG k řízení odchozí síťové připojení ve virtuálním počítači, zajistěte, aby rozsahy IP požadovaných datacenter seznam povolených adres. Informace najdete v tématu [sítě doprovodné materiály zabezpečení skupiny](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Opravte problém
Do seznamu povolených IP adres [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo [požadované rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), postupujte podle kroků v [sítě pokyny dokumentu](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nebyl nalezen v počítači (kód chyby 150039)

Nový disk připojen k virtuálnímu počítači musí být inicializován.

**Kód chyby** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
150039<br></br>**Zpráva**: Azure datový disk (DiskName) (DiskURI) se logická jednotka (LUN) (LUNValue) nebyl namapovaný na odpovídající disk z hlásí v rámci virtuálního počítače, který má stejnou hodnotu, logickou jednotku. | -Nový datový disk se připojil k virtuálnímu počítači, ale nebyl inicializován.</br></br>-Datový disk ve virtuálním počítači není správně reporting hodnota logické jednotky, kdy byla disk připojen k virtuálnímu počítači.| Zajistěte, aby se inicializují datových disků a potom operaci opakujte.</br></br>Pro Windows: [připojení a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Pro Linux: [inicializovat nový datový disk v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Opravte problém
Zajistěte, aby byly inicializovány datových disků a potom operaci opakujte:

- Pro Windows: [připojení a inicializovat nový disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Pro Linux: [přidejte nový disk dat v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Pokud potíže potrvají, obraťte se na podporu.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Nelze zobrazit virtuální počítač Azure pro výběr v "povolení replikace"

Pokud nevidíte virtuálních počítačů, které chcete povolit pro replikaci, se může být z důvodu stálou konfiguraci Site Recovery ponecháno na virtuálním počítači Azure. Stálou konfiguraci může být ponecháno na virtuální počítač Azure v následujících případech:

- Povolit replikaci pro virtuální počítač Azure pomocí Site Recovery a pak odstranit trezor Site Recovery bez výslovně zakazuje replikace na virtuálním počítači.
- Povolit replikaci pro virtuální počítač Azure pomocí Site Recovery a pak odstranit skupinu prostředků obsahující trezoru Site Recovery bez výslovně zakazuje replikace na virtuálním počítači.

### <a name="fix-the-problem"></a>Opravte problém

Můžete použít [odebrat zastaralé konfigurační skript automatické obnovení systému](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) a odebrat zastaralé konfiguraci Site Recovery na virtuálním počítači Azure. Nyní byste měli mít po odebrání stálou konfiguraci najdete v části virtuální počítač.

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Stav zřizování Virtuálního počítače je neplatný (kód chyby 150019)

Pokud chcete povolit replikaci na virtuálním počítači, musí být stav zřizování **úspěšné**. Pomocí následujícího postupu můžete zkontrolovat stav virtuálního počítače.

1.  Vyberte **Průzkumníka prostředků** z **všechny služby** na portálu Azure.
2.  Rozbalte **odběry** seznam a vyberte své předplatné.
3.  Rozbalte **Skupinyprostředků** seznam a vyberte skupinu prostředků virtuálního počítače.
4.  Rozbalte **prostředky** seznamu a vyberte virtuální počítač
5.  Zkontrolujte **provisioningState** pole v zobrazení Instance na pravé straně.

### <a name="fix-the-problem"></a>Opravte problém

- Pokud **provisioningState** je **se nezdařilo**, obraťte se na podporu s podrobnostmi o řešení.
- Pokud **provisioningState** je **aktualizace**, jiné rozšíření pro získávání může nasadit. Zkontrolujte, zda jsou všechny probíhající operace na virtuálním počítači, počkejte na jejich dokončení, a opakujte obnovení lokality se nezdařilo **povolit replikaci** úlohy.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ Chyba služby Stínová kopie svazku (kód chyby 151025)
**Kód chyby** | **Možné příčiny** | **Recommendations** (Doporučení)
--- | --- | ---
151025<br></br>**Zpráva**: Instalace rozšíření obnovení lokality se nezdařila. | -'Aplikace modelu COM + systému, služby zakázána.</br></br>-Služba Stínová kopie svazku je zakázána.| Nastavení služeb "aplikace modelu COM + systému" a Stínová kopie svazku pro automatický nebo ruční spouštění režimu.

### <a name="fix-the-problem"></a>Opravte problém

Můžete otevřít konzolu "Službami" a zajistit 'aplikace COM + systému, a Stínová kopie svazku nejsou nastavené na "Zakázáno" pro "Typ spuštění".
  ![COM – Chyba](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="next-steps"></a>Další postup
[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
