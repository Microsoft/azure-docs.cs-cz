---
title: Poradce při potížích s připojením pro zotavení po havárii Azure azure pomocí Azure Site Recovery
description: Řešení problémů s připojením v zotavení po havárii virtuálního počítače Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 49d2d3d3e8829198a57aaf2feb40e89f105667bd
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804856"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Poradce při potížích s připojením k síti virtuálních zařízení Azure-to-Azure

Tento článek popisuje běžné problémy související s připojením k síti při replikaci a obnovení virtuálních počítačů Azure (VM) z jedné oblasti do jiné oblasti. Další informace o požadavcích na síť najdete v [tématu požadavky na připojení pro replikaci virtuálních počítačů Azure](azure-to-azure-about-networking.md).

Aby replikace obnovení lokality fungovala, je z virtuálního počítačů vyžadováno odchozí připojení k určitým adresám URL nebo rozsahům IP adres. Pokud váš virtuální počítač je za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozí připojení, může čelit jeden z těchto problémů.

| **Adresa URL** | **Podrobnosti** |
| --- | --- |
| `*.blob.core.windows.net` | Povinné, aby data mohla být zapsána do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního účtu. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete použít seznam povolených adres pro konkrétní adresy URL účtu úložiště. Například `cache1.blob.core.windows.net` a `cache2.blob.core.windows.net` místo `*.blob.core.windows.net`. |
| `login.microsoftonline.com` | Vyžadováno pro autorizaci a autentizaci adres URL služby Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Povinné tak, aby služba obnovení webu komunikace může dojít z virtuálního provozu. Pokud proxy firewall podporuje IP adresy, můžete použít odpovídající ip adresu _site recovery._ |
| `*.servicebus.windows.net` | Povinné tak, aby monitorování webu obnovení a diagnostická data mohou být zapsány z virtuálního zařízení. Pokud proxy firewall podporuje IP adresy, můžete použít odpovídající ip adresu _sledování obnovení webu._ |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro adresy URL obnovení lokality nebo rozsahy IP adres (kód chyby 151037 nebo 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problém 1: Nepodařilo se zaregistrovat virtuální počítač Azure pomocí site recovery (151195)

#### <a name="possible-cause"></a>Možná příčina

Připojení nelze navázat na koncové body obnovení lokality z důvodu selhání překladu dns (Domain Name System). Tento problém je častější při opětovném protekci, když jste selhali přes virtuální hod, ale dns server není dosažitelný z oblasti zotavení po havárii (DR).

#### <a name="resolution"></a>Řešení

Pokud používáte vlastní DNS, ujistěte se, že dns server je přístupný z oblasti zotavení po havárii.

Chcete-li zkontrolovat, jestli virtuální hod používá vlastní nastavení DNS:

1. Otevřete **virtuální počítače** a vyberte virtuální počítač.
1. Přejděte na **nastavení** virtuálních počítače a vyberte **síť .**
1. Ve **virtuální síti nebo podsíti**vyberte odkaz pro otevření stránky prostředků virtuální sítě.
1. Přejděte na **Nastavení** a vyberte **servery DNS**.

Pokuste se získat přístup k serveru DNS z virtuálního počítače. Pokud server DNS není přístupný, zpřístupníte jej buď selháním přes server DNS, nebo vytvořením řádku sítě mezi sítí Zotavení po havárii a službou DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-chyba":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace obnovení lokality se nezdařila (151196)

> [!NOTE]
> Pokud virtuální aplikace jsou za **standardní** interní vyrovnávání zatížení, ve výchozím nastavení, nebude mít přístup `login.microsoftonline.com`k IP adresy Office 365, jako je například . Změňte jej na **základní** typ interního vyvažovače zatížení nebo vytvořte odchozí přístup, jak je uvedeno v článku [Konfigurace vyrovnávání zatížení a odchozích pravidel ve standardním vyvyřiču zatížení pomocí rozhraní příkazového příkazu Azure CLI](/azure/load-balancer/configure-load-balancer-outbound-cli).

#### <a name="possible-cause"></a>Možná příčina

Připojení nelze navázat na koncové body office 365 ověřování a identity IP4.

#### <a name="resolution"></a>Řešení

- Azure Site Recovery vyžaduje přístup k rozsahům IP adres Office 365 pro ověřování.
- Pokud používáte pravidla skupiny zabezpečení Azure Network (NSG) nebo proxy firewall k řízení odchozího připojení k síti na virtuálním počítači, ujistěte se, že povolíte komunikaci s rozsahy IP adres Office 365. Vytvořte pravidlo nsg založené na [značce služby Azure Active Directory (Azure AD),](/azure/virtual-network/security-overview#service-tags) které umožňuje přístup ke všem IP adresám odpovídajícím Azure AD.
- Pokud nové adresy jsou přidány do Služby Azure AD v budoucnu, je třeba vytvořit nová pravidla nsg.

### <a name="example-nsg-configuration"></a>Příklad konfigurace nsg

Tento příklad ukazuje, jak nakonfigurovat pravidla sítě zabezpečení sítě pro virtuální počítače replikovat.

- Pokud používáte pravidla nsg k řízení odchozí připojení, použijte **Povolit https odchozí** pravidla na port 443 pro všechny požadované rozsahy IP adres.
- Příklad předpokládá, že umístění zdroje virtuálního soudu je **východní USA** a cílové umístění je **centrální USA**.

#### <a name="nsg-rules---east-us"></a>Pravidla nsg - Východní USA

1. Vytvořte pravidlo odchozího zabezpečení HTTPS pro skupinu zabezpečení zabezpečení, jak je znázorněno na následujícím snímku obrazovky. Tento příklad používá **značku cílové služby**: **Rozsahy portů** _Storage.EastUS_ a Destination : _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="značka úložiště":::

1. Vytvořte pravidlo odchozího zabezpečení HTTPS pro skupinu zabezpečení zabezpečení, jak je znázorněno na následujícím snímku obrazovky. Tento příklad používá **značku cílové služby**: **Rozsahy portů** _AzureActiveDirectory_ a Cílové porty : _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Vytvořte odchozí pravidla portu HTTPS 443 pro IP adresy obnovení webu, které odpovídají cílovému umístění:

   | **Umístění** | **IP adresa pro obnovení webu** |  **IP adresa monitorování obnovení lokality** |
   | --- | --- | --- |
   | USA – střed | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>Pravidla nsg - Centrální USA

V tomto příkladu jsou tato pravidla skupiny sítě pro sítě požadována, aby replikace mohla být povolena z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání:

1. Vytvořte pravidlo odchozího zabezpečení protokolu HTTPS pro _službu Storage.CentralUS_:

   - **Cílová značka služby:** _Storage.CentralUS_
   - **Rozsahy cílových přístavů**: _443_

1. Vytvořte pravidlo odchozího zabezpečení https pro _AzureActiveDirectory_.

   - **Cílová značka služby**: _AzureActiveDirectory_
   - **Rozsahy cílových přístavů**: _443_

1. Vytvořte odchozí pravidla portu HTTPS 443 pro IP adresy obnovení webu, které odpovídají zdrojovému umístění:

   |**Umístění** | **IP adresa pro obnovení webu** |  **IP adresa monitorování obnovení lokality** |
   | --- | --- | --- |
   | USA – východ | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace obnovení lokality se nezdařila (151197)

#### <a name="possible-cause"></a>Možná příčina

Připojení nelze navázat na koncové body služby Azure Site Recovery.

#### <a name="resolution"></a>Řešení

Azure Site Recovery vyžaduje přístup k [rozsahům IP adres služby Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) v závislosti na konkrétní oblasti. Ujistěte se, že požadované rozsahy IP jsou přístupné z virtuálního počítačů.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problém 4: Replikace Azure se nezdařila, když síťový provoz prochází místním proxy serverem (151072)

#### <a name="possible-cause"></a>Možná příčina

Vlastní nastavení proxy serveru je neplatné a agent služby Mobility azure site recovery nezjistil automatické rozpoznání nastavení proxy serveru z aplikace Internet Explorer (IE).

#### <a name="resolution"></a>Řešení

1. Agent služby Mobility detekuje nastavení proxy serveru `/etc/environment` z aplikace IE v systému Windows a v systému Linux.
1. Pokud dáváte přednost nastavení proxy pouze pro službu Azure Site Recovery Mobility, můžete poskytnout podrobnosti proxy serveru v _ProxyInfo.conf_ umístěném na adrese:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ by měl mít nastavení proxy serveru v následujícím formátu _INI:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

1. Agent služby Azure Site Recovery Mobility podporuje pouze **neověřené proxy servery**.

### <a name="fix-the-problem"></a>Oprava problému

Chcete-li povolit [požadované adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo požadované [rozsahy IP adres](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), postupujte podle pokynů pro [síť](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="next-steps"></a>Další kroky

[Replikace virtuálních počítačů Azure](site-recovery-replicate-azure-to-azure.md)
