---
title: Poradce při potížích s připojením pro zotavení po havárii Azure azure pomocí Azure Site Recovery
description: Řešení problémů s připojením v zotavení po havárii virtuálního počítače Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292013"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Poradce při potížích s připojením k síti virtuálních zařízení Azure-to-Azure

Tento článek popisuje běžné problémy související s připojením k síti při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Další informace o požadavcích na síť najdete v [tématu požadavky na připojení pro replikaci virtuálních počítačů Azure](azure-to-azure-about-networking.md).

Aby replikace obnovení lokality fungovala, je z virtuálního počítačů vyžadováno odchozí připojení k určitým adresám URL nebo rozsahům IP adres. Pokud váš virtuální počítač je za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozí připojení, může čelit jeden z těchto problémů.

**Adresa URL** | **Podrobnosti**  
--- | ---
*.blob.core.windows.net | Povinné, aby data mohla být zapsána do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního účtu. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete povolit seznam konkrétních adres URL účtu úložiště (například cache1.blob.core.windows.net a cache2.blob.core.windows.net) namísto *.blob.core.windows.net
login.microsoftonline.com | Vyžadováno pro autorizaci a autentizaci adres URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Povinné tak, aby služba obnovení webu komunikace může dojít z virtuálního provozu. Pokud proxy firewall podporuje IP adresy, můžete použít odpovídající IP adresu site recovery.
*.servicebus.windows.net | Povinné tak, aby monitorování webu obnovení a diagnostická data mohou být zapsány z virtuálního zařízení. Pokud proxy firewall podporuje IP adresy, můžete použít odpovídající IP adresu site recovery monitoring ip.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro adresy URL obnovení lokality nebo rozsahy IP adres (kód chyby 151037 nebo 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problém 1: Nepodařilo se zaregistrovat virtuální počítač Azure pomocí site recovery (151195) </br>
- **Možná příčina** </br>
  - Nelze navázat připojení ke koncovým bodům obnovení sítě z důvodu selhání překladu DNS.
  - Častěji k tomu dochází při opětovném nastavování ochrany po převzetí služeb při selhání virtuálního počítače, kdy server DNS není dostupný z oblasti pro zotavení po havárii.

- **Rozlišení**
   - Pokud používáte vlastní DNS, ujistěte se, že dns server je přístupný z oblasti zotavení po havárii. Chcete-li zkontrolovat, zda máte vlastní DNS, přejděte na virtuální mon> sítě zotavení po havárii> servery DNS. Zkuste získat přístup k serveru DNS z virtuálního počítače. Pokud není přístupná, zpřístupní ji buď převzetím služeb při selhání přes server DNS, nebo vytvořením řádku sítě mezi sítí ZOTAVENÍ po havárii a službou DNS.

    ![com-chyba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace obnovení lokality se nezdařila (151196)

> [!NOTE]
> Pokud jsou virtuální počítače za **standardní matný** nástroj pro vyrovnávání zatížení, nebude mít ve výchozím nastavení přístup k IP adresy O365 (to znamená login.microsoftonline.com). Změňte jej na **základní** typ interního vykladače zatížení nebo vytvořte odchozí přístup, jak je uvedeno v [článku](https://aka.ms/lboutboundrulescli).

- **Možná příčina** </br>
  - Nelze navázat připojení k koncovým bodům IP4 služby Office 365 a identity.

- **Rozlišení**
  - Azure Site Recovery vyžaduje přístup k rozsahům IP stránek Office 365 pro ověřování.
    Pokud používáte pravidla skupiny zabezpečení Azure Network (NSG) nebo proxy firewall k řízení odchozípřipojení k síti na virtuálním počítači, ujistěte se, že povolíte komunikaci s O365 IPranges. Vytvoření pravidla nsg na [základě značky služby Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags) pro povolení přístupu ke všem IP adresám odpovídajícím Azure AD
      - Pokud nové adresy jsou přidány do Služby Azure AD v budoucnu, je třeba vytvořit nová pravidla nsg.

### <a name="example-nsg-configuration"></a>Příklad konfigurace nsg

Tento příklad ukazuje, jak nakonfigurovat pravidla sítě zabezpečení sítě pro virtuální počítače replikovat.

- Pokud používáte pravidla nsg k řízení odchozí připojení, použijte "Povolit odchozí HTTPS" pravidla port:443 pro všechny požadované rozsahy IP adres.
- Příklad předpokládá, že umístění zdroje virtuálního soudu je "Východní USA" a cílové umístění je "Střední USA".

### <a name="nsg-rules---east-us"></a>Pravidla nsg - Východní USA

1. Vytvořte odchozí https (443) pravidlo zabezpečení pro "Storage.EastUS" na nsg, jak je znázorněno na snímku obrazovky níže.

      ![značka úložiště](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvořte odchozí https (443) pravidlo zabezpečení pro "AzureActiveDirectory" na souboru zabezpečení, jak je znázorněno na snímku obrazovky níže.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Vytvořte odchozí pravidla HTTPS (443) pro IP adresy obnovení webu, které odpovídají cílovému umístění:

   **Umístění** | **IP adresa pro obnovení webu** |  **IP adresa monitorování obnovení lokality**
    --- | --- | ---
   USA – střed | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Pravidla nsg - Centrální USA

Tato pravidla jsou vyžadována tak, aby replikace mohla být povolena z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání:

1. Vytvořte odchozí https (443) pravidlo zabezpečení pro "Storage.CentralUS" na skupinu zabezpečení.

2. Vytvořte odchozí https (443) pravidlo zabezpečení pro "AzureActiveDirectory" na skupinu zabezpečení.

3. Vytvořte odchozí pravidla HTTPS (443) pro IP adresy obnovení webu, které odpovídají zdrojovému umístění:

   **Umístění** | **IP adresa pro obnovení webu** |  **IP adresa monitorování obnovení lokality**
    --- | --- | ---
   USA – střed | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace obnovení lokality se nezdařila (151197)
- **Možná příčina** </br>
  - Připojení nelze navázat na koncové body služby Azure Site Recovery.

- **Rozlišení**
  - Azure Site Recovery vyžaduje přístup k [rozsahům IP adres služby Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags) v závislosti na konkrétní oblasti. Ujistěte se, že virtuální počítač má přístup k požadovaným rozsahům IP adres.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problém 4: Replikace A2A se nezdařila při průchodu síťového provozu místním proxy serverem (151072)
- **Možná příčina** </br>
  - Vlastní nastavení proxy serveru je neplatné a agent služby Mobility Service azure site recovery nezjistil automaticky nastavení proxy serveru z aplikace IE.


- **Rozlišení**
  1. Agent služby Mobility Service detekuje nastavení proxy serveru z aplikace IE v systému Windows a prostředí /etc/v systému Linux.
  2. Pokud dáváte přednost nastavení proxy pouze pro službu Azure Site Recovery Mobility Service, můžete poskytnout podrobnosti proxy serveru v ProxyInfo.conf na adrese:</br>
     - ``/usr/local/InMage/config/``na ***Linuxu***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``ve ***Windows***
  3. Soubor ProxyInfo.conf by měl obsahovat nastavení proxy v následujícím formátu INI:</br>
                *[proxy]*</br>
                *Adresa=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Agent služby Mobility Service Azure Site Recovery service podporuje pouze ***neověřené proxy servery***.

### <a name="fix-the-problem"></a>Oprava problému
Chcete-li povolit [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo požadované [rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), postupujte podle pokynů pro [síť](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Další kroky
[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
