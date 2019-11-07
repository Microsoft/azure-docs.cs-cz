---
title: Řešení potíží s připojením pro zotavení po havárii Azure do Azure pomocí Azure Site Recovery
description: Řešení chyb a problémů při replikaci virtuálních počítačů Azure pro zotavení po havárii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 4995a4086c471a06fe859febfd2d1af7fbb22a76
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622451"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Řešení potíží s připojením k síti virtuálních počítačů z Azure do Azure

Tento článek popisuje běžné problémy související s připojením k síti při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Další informace o požadavcích na síť najdete v tématu [požadavky na připojení pro replikaci virtuálních počítačů Azure](azure-to-azure-about-networking.md).

Aby mohla replikace Site Recovery fungovat, z virtuálního počítače se vyžaduje odchozí připojení ke konkrétním adresám URL nebo rozsahům IP adres. Pokud je váš virtuální počítač za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozího připojení, můžete se setkat s jedním z těchto problémů.

**Adresa URL** | **Podrobnosti**  
--- | ---
*.blob.core.windows.net | Vyžaduje se, aby se data mohla zapsat do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního počítače. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete místo *. blob.core.windows.net vypsat konkrétní adresy URL účtu úložiště (například cache1.blob.core.windows.net a cache2.blob.core.windows.net).
login.microsoftonline.com | Vyžaduje se pro autorizaci a ověřování adres URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Vyžaduje se, aby na virtuálním počítači mohla probíhat komunikace služby Site Recovery. Pokud proxy server firewall podporuje IP adresy, můžete použít odpovídající Site Recovery IP adresu.
*.servicebus.windows.net | Požadováno, aby se z virtuálního počítače mohla zapisovat data monitorování Site Recovery a diagnostická data. V případě, že proxy server brány firewall podporuje IP adresy, můžete použít odpovídající "Site Recovery monitorování IP adres".

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro adresy URL Site Recovery nebo rozsahy IP adres (kód chyby 151037 nebo 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problém 1: registrace virtuálního počítače Azure pomocí Site Recovery se nezdařila (151195) </br>
- **Možná příčina** </br>
  - Nelze navázat připojení k Site Recovery koncovým bodům z důvodu chyby překladu názvů DNS.
  - Častěji k tomu dochází při opětovném nastavování ochrany po převzetí služeb při selhání virtuálního počítače, kdy server DNS není dostupný z oblasti pro zotavení po havárii.

- **Rozhodnutí**
   - Pokud používáte vlastní server DNS, ujistěte se, že je server DNS přístupný z oblasti zotavení po havárii. Pokud chcete zjistit, jestli máte vlastní DNS, přečtěte si tento virtuální počítač > > servery DNS na síť VM pro zotavení po havárii. Zkuste získat přístup k serveru DNS z virtuálního počítače. Pokud k ní nelze získat přístup, zpřístupněte ji buď při selhání přes server DNS, nebo při vytváření sítě mezi sítí DR a DNS.

    ![com – chyba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace Site Recovery se nezdařila (151196)

> [!NOTE]
> Pokud jsou virtuální počítače za **standardním** interním nástrojem pro vyrovnávání zatížení, nebude mít ve výchozím nastavení přístup k IP adresám O365 (tj. Login.microsoftonline.com). Buď změňte na **základní** typ interního nástroje pro vyrovnávání zatížení, nebo vytvořte odchozí přístup, jak je uvedeno v [článku](https://aka.ms/lboutboundrulescli).

- **Možná příčina** </br>
  - Nejde navázat připojení k ověřování Office 365 a koncovým bodům IP4 identity.

- **Rozhodnutí**
  - Azure Site Recovery požadovaný přístup k rozsahům IP adres Office 365 pro ověřování.
    Pokud k řízení odchozího připojení k síti na virtuálním počítači používáte pravidla skupiny zabezpečení sítě Azure (NSG) nebo proxy server brány firewall, ujistěte se, že jste povolili komunikaci s rozhraními O365 IPrange. Azure Active Directory vytvoření NSG pravidla pro [poskytování služeb na základě značky služby (Azure AD)](../virtual-network/security-overview.md#service-tags) pro povolení přístupu ke všem IP adresám, které odpovídají službě Azure AD
      - Pokud v budoucnu přidáte do Azure AD nové adresy, budete muset vytvořit nová pravidla NSG.

### <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje, jak nakonfigurovat NSG pravidla pro replikaci virtuálního počítače.

- Pokud používáte pravidla NSG k řízení odchozího připojení, použijte pravidla "povolení odchozího přenosu HTTPS" na port: 443 pro všechny požadované rozsahy IP adres.
- V příkladu se předpokládá, že umístění zdroje virtuálního počítače je "Východní USA" a cílové umístění je "Střed USA".

### <a name="nsg-rules---east-us"></a>Pravidla NSG – Východní USA

1. Vytvořte pravidlo zabezpečení odchozího HTTPS (443) pro Storage. EastUS na NSG, jak je znázorněno na snímku obrazovky níže.

      ![úložiště – značka](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvořte pravidlo zabezpečení odchozího HTTPS (443) pro "Azureactivedirectory selhala" na NSG, jak je znázorněno na snímku obrazovky níže.

      ![AAD – značka](./media/azure-to-azure-about-networking/aad-tag.png)

3. Vytvořit odchozí pravidla HTTPS (443) pro Site Recovery IP adresy, které odpovídají cílovému umístění:

   **Umístění** | **Site Recovery IP adresa** |  **IP adresa monitorování Site Recovery**
    --- | --- | ---
   Střední USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Pravidla NSG – Střed USA

Tato pravidla jsou nutná, aby bylo možné replikaci z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání povolit.

1. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro Storage. CentralUS na NSG.

2. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro Azureactivedirectory selhala na NSG.

3. Vytvořit odchozí pravidla HTTPS (443) pro Site Recovery IP adresy, které odpovídají zdrojovému umístění:

   **Umístění** | **Site Recovery IP adresa** |  **IP adresa monitorování Site Recovery**
    --- | --- | ---
   Střední USA | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace Site Recovery se nezdařila (151197)
- **Možná příčina** </br>
  - Nelze navázat připojení k Azure Site Recovery koncovým bodům služby.

- **Rozhodnutí**
  - Azure Site Recovery vyžaduje přístup k [rozsahům IP adres služby Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) v závislosti na konkrétní oblasti. Ujistěte se, že virtuální počítač má přístup k požadovaným rozsahům IP adres.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problém 4: replikace A2A selhala, když se síťový provoz přechází prostřednictvím místních proxy server (151072).
- **Možná příčina** </br>
  - Vlastní nastavení proxy serveru je neplatné a Azure Site Recovery agent služby mobility automaticky nerozpoznal nastavení proxy serveru z IE.


- **Rozhodnutí**
  1. Agent služby mobility detekuje nastavení proxy serveru z IE ve Windows a/etc/Environment v systému Linux.
  2. Pokud dáváte přednost nastavení proxy serveru jenom pro Azure Site Recovery služby mobility, můžete zadat podrobnosti o proxy serveru v souboru ProxyInfo. conf, který najdete na adrese:</br>
     - ``/usr/local/InMage/config/`` v systému ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` ve ***Windows***
  3. Soubor ProxyInfo.conf by měl obsahovat nastavení proxy v následujícím formátu INI:</br>
                *soubory*</br>
                *Adresa =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Agent služby mobility Azure Site Recovery podporuje jenom ***neověřené proxy servery***.

### <a name="fix-the-problem"></a>Opravit problém
Pokud chcete povolené [adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo požadované [rozsahy IP](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)adres, postupujte podle kroků v [dokumentu s pokyny k síti](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Další kroky
[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
