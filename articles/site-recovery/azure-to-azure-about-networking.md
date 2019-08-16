---
title: O sítích v Azure do Azure – zotavení po havárii pomocí Azure Site Recovery | Microsoft Docs
description: Poskytuje přehled o sítích pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: c642640d590e1f568fb6f6c5072decd75575ab2d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543648"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>O sítích v Azure do replikace Azure



Tento článek poskytuje pokyny k síti při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Přečtěte si, jak Site Recovery poskytuje zotavení po havárii pro [Tento scénář](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typická síťová infrastruktura

Následující diagram znázorňuje typické prostředí Azure pro aplikace běžící na virtuálních počítačích Azure:

![zákazník – prostředí](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pokud používáte Azure ExpressRoute nebo připojení VPN z vaší místní sítě do Azure, prostředí je následující:

![zákazník – prostředí](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Sítě jsou obvykle chráněné pomocí bran firewall a skupin zabezpečení sítě (skupin zabezpečení sítě). Brány firewall používají k řízení připojení k síti adresu URL nebo přidávání do seznamu povolených IP adres. Skupin zabezpečení sítě poskytují pravidla, která používají rozsahy IP adres k řízení síťového připojení.

>[!IMPORTANT]
> Použití ověřeného proxy serveru k řízení připojení k síti není v Site Recovery podporováno a replikaci nelze povolit.


## <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte tyto adresy URL Site Recovery:


**Adresa URL** | **Podrobnosti**  
--- | ---
*.blob.core.windows.net | Vyžaduje se, aby se data mohla zapsat do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního počítače. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete místo *. blob.core.windows.net v seznamu povolit konkrétní adresy URL účtu úložiště (např.: cache1.blob.core.windows.net a cache2.blob.core.windows.net).
login.microsoftonline.com | Vyžaduje se pro autorizaci a ověřování adres URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Vyžaduje se, aby na virtuálním počítači mohla probíhat komunikace služby Site Recovery. Pokud proxy server firewall podporuje IP adresy, můžete použít odpovídající Site Recovery IP adresu.
*.servicebus.windows.net | Požadováno, aby se z virtuálního počítače mohla zapisovat data monitorování Site Recovery a diagnostická data. V případě, že proxy server brány firewall podporuje IP adresy, můžete použít odpovídající "Site Recovery monitorování IP adres".

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud používáte proxy server brány firewall založený na protokolu IP nebo NSG pravidla pro řízení odchozího připojení, tyto rozsahy IP adres musí být povoleny.

- Všechny rozsahy IP adres, které odpovídají účtům úložiště ve zdrojové oblasti
    - Vytvořte pravidlo NSG založené na [značce služby úložiště](../virtual-network/security-overview.md#service-tags) pro zdrojovou oblast.
    - Povolte tyto adresy, aby bylo možné do účtu úložiště mezipaměti zapsat data z virtuálního počítače.
- Vytvoření [značky služby Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na základě pravidel skupiny zabezpečení sítě umožňující přístup ke všem IP adresám v odpovídající službě AAD
    - Pokud do Azure Active Directory (AAD) se přidají nové adresy v budoucnu, musíte vytvořit nová pravidla skupiny zabezpečení sítě.
- Site Recovery IP adresy koncového bodu služby – k dispozici v [souboru XML](https://aka.ms/site-recovery-public-ips) a závisí na cílovém umístění.
- Doporučujeme, abyste vytvořili požadovaná pravidla NSG na NSG testu a ověřili, že neexistují žádné problémy předtím, než vytvoříte pravidla na produkčním NSG.


Site Recovery rozsahy IP adres jsou následující:

   **Cílové** | **Site Recovery IP** |  **Site Recovery monitorování IP adresy**
   --- | --- | ---
   Východní Asie | 52.175.17.132 | 13.94.47.61
   Jihovýchodní Asie | 52.187.58.193 | 13.76.179.223
   Střed Indie | 52.172.187.37 | 104.211.98.185
   Jižní Indie | 52.172.46.220 | 104.211.224.190
   Střed USA – sever | 23.96.195.247 | 168.62.249.226
   Severní Evropa | 40.69.212.238 | 52.169.18.8
   Západní Evropa | 52.166.13.64 | 40.68.93.145
   East US | 13.82.88.226 | 104.45.147.24
   USA – západ | 40.83.179.48 | 104.40.26.199
   Střed USA – jih | 13.84.148.14 | 104.210.146.250
   Střed USA | 40.69.144.231 | 52.165.34.144
   Východní USA 2 | 52.184.158.163 | 40.79.44.59
   Japonsko – východ | 52.185.150.140 | 138.91.1.105
   Japonsko – západ | 52.175.146.69 | 138.91.17.38
   Brazílie – jih | 191.234.185.172 | 23.97.97.36
   Austrálie – východ | 104.210.113.114 | 191.239.64.144
   Austrálie – jihovýchod | 13.70.159.158 | 191.239.160.45
   Kanada – střed | 52.228.36.192 | 40.85.226.62
   Kanada – východ | 52.229.125.98 | 40.86.225.142
   Západní střed USA | 52.161.20.168 | 13.78.149.209
   USA – západ 2 | 52.183.45.166 | 13.66.228.204
   Spojené království – západ | 51.141.3.203 | 51.141.14.113
   Velká Británie – jih | 51.140.43.158 | 51.140.189.52
   Spojené království – jih 2 | 13.87.37.4| 13.87.34.139
   Spojené království – sever | 51.142.209.167 | 13.87.102.68
   Jižní Korea – střed | 52.231.28.253 | 52.231.32.85
   Jižní Korea – jih | 52.231.198.185 | 52.231.200.144
   Francie – střed | 52.143.138.106 | 52.143.136.55
   Francie – jih | 52.136.139.227 |52.136.136.62
   Austrálie – střed| 20.36.34.70 | 20.36.46.142
   Austrálie – střed 2| 20.36.69.62 | 20.36.74.130
   Jižní Afrika – západ | 102.133.72.51 | 102.133.26.128
   Jižní Afrika – sever | 102.133.160.44 | 102.133.154.128
   USA (Gov) – Virginia | 52.227.178.114 | 23.97.0.197
   US Gov – Iowa | 13.72.184.23 | 23.97.16.186
   USA (Gov) – Arizona | 52.244.205.45 | 52.244.48.85
   USA (Gov) – Texas | 52.238.119.218 | 52.238.116.60
   US DoD – východ | 52.181.164.103 | 52.181.162.129
   US DoD – střed | 52.182.95.237 | 52.182.90.133
   Čína – sever | 40.125.202.254 | 42.159.4.151
   Čína – sever 2 | 40.73.35.193 | 40.73.33.230
   Čína – východ | 42.159.205.45 | 42.159.132.40
   Čína – východ 2 | 40.73.118.52| 40.73.100.125
  
## <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje, jak nakonfigurovat NSG pravidla pro replikaci virtuálního počítače.

- Pokud používáte pravidla NSG k řízení odchozího připojení, použijte pravidla "povolení odchozího přenosu HTTPS" na port: 443 pro všechny požadované rozsahy IP adres.
- V příkladu se předpokládá, že umístění zdroje virtuálního počítače je "Východní USA" a cílové umístění je "Střed USA".

### <a name="nsg-rules---east-us"></a>Pravidla NSG – Východní USA

1. Vytvořte pravidlo zabezpečení odchozího HTTPS (443) pro Storage. EastUS na NSG, jak je znázorněno na snímku obrazovky níže.

      ![úložiště – značka](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvořte pravidlo zabezpečení odchozího HTTPS (443) pro "Azureactivedirectory selhala" na NSG, jak je znázorněno na snímku obrazovky níže.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Vytvořit odchozí pravidla HTTPS (443) pro Site Recovery IP adresy, které odpovídají cílovému umístění:

   **Location** | **Site Recovery IP adresa** |  **IP adresa monitorování Site Recovery**
    --- | --- | ---
   Střed USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Pravidla NSG – Střed USA

Tato pravidla jsou nutná, aby bylo možné replikaci z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání povolit.

1. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro Storage. CentralUS na NSG.

2. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro Azureactivedirectory selhala na NSG.

3. Vytvořit odchozí pravidla HTTPS (443) pro Site Recovery IP adresy, které odpovídají zdrojovému umístění:

   **Location** | **Site Recovery IP adresa** |  **IP adresa monitorování Site Recovery**
    --- | --- | ---
   Střed USA | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Konfigurace síťového virtuálního zařízení

Pokud k řízení odchozího síťového provozu z virtuálních počítačů používáte síťová virtuální zařízení (síťová virtuální zařízení), může se zařízení omezovat, pokud se veškerý provoz replikace projde přes síťové virtuální zařízení. Doporučujeme vytvořit koncový bod síťové služby ve virtuální síti pro úložiště, aby provoz replikace nepřešel do síťové virtuální zařízení.

### <a name="create-network-service-endpoint-for-storage"></a>Vytvoření koncového bodu síťové služby pro úložiště
V rámci virtuální sítě můžete vytvořit koncový bod síťové služby pro úložiště, aby provoz replikace neopouští hranice Azure.

- Vyberte svou virtuální síť Azure a klikněte na koncové body služby.

    ![úložiště – koncový bod](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Otevře se karta přidat a přidat koncové body služby.
- Vyberte Microsoft. Storage v části služba a požadované podsítě v poli podsítě a klikněte na Přidat.

>[!NOTE]
>Neomezovat přístup k virtuální síti pro účty úložiště používané pro ASR. Měli byste mít povolený přístup ze všech sítí.

### <a name="forced-tunneling"></a>Vynucené tunelování

Výchozí systémovou trasu Azure pro předponu adresy 0.0.0.0/0 můžete přepsat [vlastní trasou](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrováním provozu virtuálního počítače do místního síťového virtuálního zařízení (síťové virtuální zařízení), ale tato konfigurace se nedoporučuje pro Site Recovery replikaci. Pokud používáte vlastní trasy, měli byste ve virtuální síti [vytvořit koncový bod služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) pro úložiště, aby provoz replikace neopouští hranice Azure.

## <a name="next-steps"></a>Další postup
- Začněte chránit své úlohy [replikací virtuálních počítačů Azure](site-recovery-azure-to-azure.md).
- Další informace o [uchovávání IP adres](site-recovery-retain-ip-azure-vm-failover.md) pro převzetí služeb při selhání virtuálního počítače Azure
- Přečtěte si další informace o zotavení po havárii [virtuálních počítačů Azure pomocí ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
