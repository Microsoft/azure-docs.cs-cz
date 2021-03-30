---
title: O sítích v zotavení po havárii virtuálních počítačů Azure pomocí Azure Site Recovery
description: Poskytuje přehled o sítích pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: b9fdaf8a0791570ecee402442c5faefe2f70a22b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370436"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>O sítích v zotavení po havárii virtuálního počítače Azure



Tento článek poskytuje pokyny k síti při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Přečtěte si, jak Site Recovery poskytuje zotavení po havárii pro [Tento scénář](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typická síťová infrastruktura

Následující diagram znázorňuje typické prostředí Azure pro aplikace běžící na virtuálních počítačích Azure:

![Diagram, který znázorňuje typické prostředí Azure pro aplikace běžící na virtuálních počítačích Azure.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pokud používáte Azure ExpressRoute nebo připojení VPN z vaší místní sítě do Azure, prostředí je následující:

![zákazník – prostředí](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Sítě jsou obvykle chráněné pomocí bran firewall a skupin zabezpečení sítě (skupin zabezpečení sítě). Pro řízení připojení k síti byste měli použít značky služby. Skupin zabezpečení sítě by mělo umožňovat řízení odchozího připojení pro několik značek služeb.

>[!IMPORTANT]
> Použití ověřeného proxy serveru k řízení připojení k síti není v Site Recovery podporováno a replikaci nelze povolit.

>[!NOTE]
>- Pro řízení odchozího připojení by se nemělo provádět filtrování na základě IP adresy.
>- Pro řízení odchozího připojení by se neměly přidávat IP adresy Azure Site Recovery do směrovací tabulky Azure.

## <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte tyto adresy URL Site Recovery:

**Adresa URL** | **Podrobnosti**
--- | ---
*.blob.core.windows.net | Vyžaduje se, aby se data mohla zapsat do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního počítače. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete přístup k určitým adresám URL účtu úložiště (např.: cache1.blob.core.windows.net a cache2.blob.core.windows.net) zpřístupnit místo *. blob.core.windows.net
login.microsoftonline.com | Vyžaduje se pro autorizaci a ověřování adres URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Vyžaduje se, aby na virtuálním počítači mohla probíhat komunikace služby Site Recovery.
*.servicebus.windows.net | Požadováno, aby se z virtuálního počítače mohla zapisovat data monitorování Site Recovery a diagnostická data.
*.vault.azure.net | Umožňuje přístup k povolení replikace pro virtuální počítače s podporou ADE přes portál.
*. automation.ext.azure.com | Umožňuje povolit automatický upgrade agenta mobility pro replikovanou položku prostřednictvím portálu.

## <a name="outbound-connectivity-using-service-tags"></a>Odchozí připojení pomocí značek služeb

Při použití NSG k řízení odchozího připojení je potřeba tyto značky služeb povolit.

- Pro účty úložiště ve zdrojové oblasti:
    - Vytvořte pravidlo NSG založené na [značce služby úložiště](../virtual-network/network-security-groups-overview.md#service-tags) pro zdrojovou oblast.
    - Povolte tyto adresy, aby bylo možné do účtu úložiště mezipaměti zapsat data z virtuálního počítače.
- Vytvořit pravidlo NSG založené na [značce služby pro Azure Active Directory (AAD)](../virtual-network/network-security-groups-overview.md#service-tags) pro povolení přístupu ke všem IP adresám, které odpovídají AAD
- Vytvořte pravidlo NSG na základě značky služby EventsHub pro cílovou oblast a umožněte přístup Site Recovery monitorování.
- Vytvořte pravidlo NSG na základě značek služby AzureSiteRecovery, které umožní přístup k Site Recovery službě v libovolné oblasti.
- Vytvořte pravidlo NSG na základě značek služby AzureKeyVault. To se vyžaduje jenom pro povolení replikace virtuálních počítačů s podporou ADE přes portál.
- Vytvořte pravidlo NSG na základě značek služby GuestAndHybridManagement. To se vyžaduje jenom pro povolení automatického upgradu agenta mobility pro replikovanou položku prostřednictvím portálu.
- Doporučujeme, abyste vytvořili požadovaná pravidla NSG na NSG testu a ověřili, že neexistují žádné problémy předtím, než vytvoříte pravidla na produkčním NSG.

## <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje, jak nakonfigurovat NSG pravidla pro replikaci virtuálního počítače.

- Pokud používáte pravidla NSG k řízení odchozího připojení, použijte pravidla "povolení odchozího přenosu HTTPS" na port: 443 pro všechny požadované rozsahy IP adres.
- V příkladu se předpokládá, že umístění zdroje virtuálního počítače je "Východní USA" a cílové umístění je "Střed USA".

### <a name="nsg-rules---east-us"></a>Pravidla NSG – Východní USA

1. Vytvořte pravidlo zabezpečení odchozího HTTPS (443) pro Storage. EastUS na NSG, jak je znázorněno na snímku obrazovky níže.

      ![Snímek obrazovky s informacemi o přidání odchozího pravidla zabezpečení pro skupinu zabezpečení sítě pro Storage s tečkou U S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvořte pravidlo zabezpečení odchozího HTTPS (443) pro "Azureactivedirectory selhala" na NSG, jak je znázorněno na snímku obrazovky níže.

      ![Snímek obrazovky ukazuje přidat odchozí pravidlo zabezpečení pro skupinu zabezpečení sítě pro Azure A D.](./media/azure-to-azure-about-networking/aad-tag.png)

3. Podobně jako u výše uvedených pravidel zabezpečení vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "EventHub. CentralUS" v NSG, které odpovídá cílovému umístění. To umožňuje přístup k Site Recovery monitorování.

4. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro AzureSiteRecovery na NSG. To umožňuje přístup ke službě Site Recovery v libovolné oblasti.

### <a name="nsg-rules---central-us"></a>Pravidla NSG – Střed USA

Tato pravidla jsou nutná, aby bylo možné replikaci z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání povolit.

1. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro Storage. CentralUS na NSG.

2. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro Azureactivedirectory selhala na NSG.

3. Podobně jako u výše uvedených pravidel zabezpečení vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "EventHub. EastUS" v NSG, které odpovídá umístění zdroje. To umožňuje přístup k Site Recovery monitorování.

4. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro AzureSiteRecovery na NSG. To umožňuje přístup ke službě Site Recovery v libovolné oblasti.

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

## <a name="next-steps"></a>Další kroky
- Začněte chránit své úlohy [replikací virtuálních počítačů Azure](./azure-to-azure-quickstart.md).
- Další informace o [uchovávání IP adres](site-recovery-retain-ip-azure-vm-failover.md) pro převzetí služeb při selhání virtuálního počítače Azure
- Přečtěte si další informace o zotavení po havárii [virtuálních počítačů Azure pomocí ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).