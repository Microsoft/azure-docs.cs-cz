---
title: O možnostech sítě v zotavení po havárii Azure do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Poskytuje přehled sítě pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: sujayt
ms.openlocfilehash: 55eed1afa9d9dbf2c16643cf9186a7e6dafa847e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>O možnostech sítě v Azure do Azure replikace

>[!NOTE]
> Replikace obnovení lokality pro virtuální počítače Azure je aktuálně ve verzi preview.

Tento článek obsahuje síťové pokyny, když jste replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné, pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Zjistěte, jak Site Recovery poskytuje zotavení po havárii pro [tento scénář](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typickou síťovou infrastrukturu

Následující diagram znázorňuje typické prostředí Azure pro aplikace spuštěné na virtuálních počítačích Azure:

![prostředí zákazníka](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pokud používáte Azure ExpressRoute nebo VPN připojení z vaší místní sítě do Azure, prostředí je následující:

![prostředí zákazníka](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Obvykle sítě jsou chráněny pomocí brány firewall a skupiny zabezpečení sítě (Nsg). Brány firewall pomocí adresy URL nebo povolených založenou na protokolu IP můžete řídit připojení k síti. Skupiny Nsg zadejte pravidla, která používají rozsahy IP adres k řízení připojení k síti.

>[!IMPORTANT]
> Pomocí ověřené proxy pro připojení k síti ovládací prvek není podporována službou Site Recovery a replikaci nejde povolit.


## <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud používáte proxy server brány firewall založená na adresu URL k řízení odchozí připojení, povolte tyto adresy URL obnovení lokality:


**Adresa URL** | **Podrobnosti**  
--- | ---
*.blob.core.windows.net | Vyžaduje, aby data je možné zapsat do mezipaměti účet úložiště v oblasti zdroje z virtuálního počítače.
login.microsoftonline.com | Vyžaduje se pro autorizaci a ověřování na adresy URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Vyžaduje, aby komunikace služby Site Recovery může dojít z virtuálního počítače.
*.servicebus.windows.net | Vyžaduje, aby data monitorování a Diagnostika Site Recovery je možné zapsat z virtuálního počítače.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud používáte proxy služby založenou na protokolu IP brány firewall nebo pravidla NSG k řízení odchozí připojení, musí být povoleny tyto rozsahy IP.

- Všechny rozsahy IP adres, které odpovídají na účtech úložiště v oblasti zdroje
    - Vytvoření [úložiště výrobní číslo](../virtual-network/security-overview.md#service-tags) na základě pravidla NSG pro oblast zdroje.
    - Povolit tyto adresy tak, aby data je možné zapsat do mezipaměti účet úložiště, z virtuálního počítače.
- Všechny rozsahy IP adres, které odpovídají Office 365 [ověřování a identita koncové body IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
    - Pokud rozsahy Office 365 se přidají nové adresy v budoucnu, musíte vytvořit nová pravidla NSG.
- Obnovení služby koncový bod IP adresy - k dispozici v lokalitě [souboru XML](https://aka.ms/site-recovery-public-ips) a závisí na cílové umístění.
-  Můžete [stáhnout a použít tento skript](https://aka.ms/nsg-rule-script), aby automaticky vytvoří požadované pravidla v této skupině.
- Doporučujeme vytvořit požadované pravidla NSG na testovací skupina NSG a ověřte, že před vytvořením pravidel u produkčních NSG neexistují žádné problémy.


Rozsahy adres IP pro obnovení lokality jsou následující:

   **cíl** | **Obnovení lokality IP** |  **Site Recovery monitorování IP**
   --- | --- | ---
   Východní Asie | 52.175.17.132 | 13.94.47.61
   Jihovýchodní Asie | 52.187.58.193 | 13.76.179.223
   Střed Indie | 52.172.187.37 | 104.211.98.185
   Indie – jih | 52.172.46.220 | 104.211.224.190
   Střed USA – sever | 23.96.195.247 | 168.62.249.226
   Severní Evropa | 40.69.212.238 | 52.169.18.8
   Západní Evropa | 52.166.13.64 | 40.68.93.145
   Východ USA | 13.82.88.226 | 104.45.147.24
   Západní USA | 40.83.179.48 | 104.40.26.199
   Střed USA – jih | 13.84.148.14 | 104.210.146.250
   Střed USA | 40.69.144.231 | 52.165.34.144
   Východní USA 2 | 52.184.158.163 | 40.79.44.59
   Japonsko – východ | 52.185.150.140 | 138.91.1.105
   Japonsko – západ | 52.175.146.69 | 138.91.17.38
   Brazílie – jih | 191.234.185.172 | 23.97.97.36
   Austrálie – východ | 104.210.113.114 | 191.239.64.144
   Austrálie – jihovýchod | 13.70.159.158 | 191.239.160.45
   Střední Kanada | 52.228.36.192 | 40.85.226.62
   Východní Kanada | 52.229.125.98 | 40.86.225.142
   Západní střed USA | 52.161.20.168 | 13.78.149.209
   Západní USA 2 | 52.183.45.166 | 13.66.228.204
   Spojené království – západ | 51.141.3.203 | 51.141.14.113
   Spojené království – jih | 51.140.43.158 | 51.140.189.52
   Spojené království – jih 2 | 13.87.37.4| 13.87.34.139
   Spojené království – sever | 51.142.209.167 | 13.87.102.68
   Korea – střed | 52.231.28.253 | 52.231.32.85
   Korea – jih | 52.231.298.185 | 52.231.200.144
   Francie – střed | 52.143.138.106 | 52.143.136.55
   Francie – jih | 52.136.139.227 |52.136.136.62


## <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje postup konfigurace pravidla NSG pro virtuální počítač k replikaci.

- Pokud používáte pravidla NSG k řízení odchozí připojení, použijte "Povolit HTTPS odchozí" pravidel portu: 443 pro všechny požadované rozsahy IP adres.
- V příkladu se předpokládá, že umístění zdrojového virtuálního počítače je "Východ USA" a cílové umístění je "Střední USA".

### <a name="nsg-rules---east-us"></a>Pravidla NSG - východní USA

1. Vytvoření odchozí pravidla zabezpečení HTTPS (443) pro "Storage.EastUS" na NSG, jak je vidět na tomto snímku obrazovky.

      ![úložiště – značka](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvářet odchozí pravidla HTTPS (443) pro všechny rozsahy IP adres, které odpovídají Office 365 [ověřování a identita koncové body IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Vytvořte odchozí pravidla HTTPS (443) pro IP adresy obnovení lokality, která odpovídají do cílového umístění:

   **Umístění** | **Obnovení IP adresu serveru** |  **Obnovení monitorování IP adresa**
    --- | --- | ---
   Střed USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Pravidla NSG - střed USA

Tato pravidla jsou vyžadována, takže je možné povolit replikaci z oblasti target zdrojovém oblast post-převzetí služeb při selhání:

1. Pro "Storage.CentralUS" v této skupině vytvořte odchozí pravidlo zabezpečení HTTPS (443).

2. Vytvářet odchozí pravidla HTTPS (443) pro všechny rozsahy IP adres, které odpovídají Office 365 [ověřování a identita koncové body IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

3. Vytvořte odchozí pravidla HTTPS (443) pro obnovení lokality IP adresy, které odpovídají umístění zdroje:

   **Umístění** | **Obnovení IP adresu serveru** |  **Obnovení monitorování IP adresa**
    --- | --- | ---
   Střed USA | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Konfigurace sítě virtuálního zařízení

Pokud používáte virtuální zařízení sítě (NVAs) řídit odchozí síťový provoz z virtuálních počítačů, zařízení může získat omezena Pokud postupuje veškerý provoz replikace do hodnocení chyb zabezpečení. Doporučujeme, aby provoz replikace do hodnocení chyb zabezpečení nepřekračuje vytvoření koncového bodu sítě služby ve virtuální síti pro "Úložiště".

### <a name="create-network-service-endpoint-for-storage"></a>Vytvoření koncového bodu sítě služby pro úložiště
Koncový bod sítě služby ve virtuální síti můžete vytvořit pro "Úložiště" tak, aby provoz replikace, nenechává Azure hranic.

- Vyberte virtuální sítě Azure a kliknutím na koncové body služby

    ![koncový bod úložiště](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klikněte na tlačítko "Přidat" a 'Přidat koncové body služby' kartě otevře
- Vyberte 'Microsoft.Storage' v části "Služba" a požadované podsítí v rámci pole, podsítě a klikněte na tlačítko "přidat.

>[!NOTE]
>Účty úložiště použít pro automatické obnovení systému neomezují přístup k virtuální síti. By měly umožnit přístup z 'všechny sítě.

### <a name="forced-tunneling"></a>Vynucené tunelování

Můžete přepsat Azure výchozí systému trasu pro předpony adres 0.0.0.0/0 s [vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat přenosy virtuálních počítačů pro virtuální zařízení místní síti (hodnocení chyb zabezpečení), ale tato konfigurace se nedoporučuje pro obnovení lokality replikace. Pokud používáte vlastní trasy, měli byste [vytvoření koncového bodu služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává Azure hranic.

## <a name="next-steps"></a>Další postup
- Začněte chránit vaše úlohy [replikovat virtuální počítače Azure](site-recovery-azure-to-azure.md).
- Další informace o [IP adresu uchování](site-recovery-retain-ip-azure-vm-failover.md) převzetí služeb při selhání virtuálního počítače Azure.
- Další informace o zotavení po havárii [virtuální počítače Azure prostřednictvím ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
