---
title: O sítích v zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Obsahuje přehled sítí pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: sutalasi
ms.openlocfilehash: 58348c9aed14a5cc9126be780fe01817274a0b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283255"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>O sítích v zotavení po havárii virtuálního počítače Azure



Tento článek poskytuje pokyny k síti, když replikujete a obnovujete virtuální počítače Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Zjistěte, jak site recovery poskytuje zotavení po havárii pro [tento scénář](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typická síťová infrastruktura

Následující diagram znázorňuje typické prostředí Azure pro aplikace spuštěné na virtuálních počítačích Azure:

![zákaznické prostředí](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pokud používáte Azure ExpressRoute nebo připojení VPN z místní sítě do Azure, je prostředí následující:

![zákaznické prostředí](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Sítě jsou obvykle chráněny pomocí bran firewall a skupin zabezpečení sítě (NSGs). Brány firewall používají k řízení připojení k síti adresu URL nebo seznam povolených adres WHITE. Soubory zabezpečení sítě poskytují pravidla, která používají rozsahy IP adres k řízení připojení k síti.

>[!IMPORTANT]
> Obnovení lokality nepodporuje použití ověřeného proxy serveru pro řízení připojení k síti a replikace nelze povolit.


## <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založeného na adrese URL, povolte tyto adresy URL obnovení webu:


**Adresa URL** | **Podrobnosti**
--- | ---
*.blob.core.windows.net | Povinné, aby data mohla být zapsána do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního účtu. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete povolit přístup ke konkrétním adresám URL účtu úložiště (například cache1.blob.core.windows.net a cache2.blob.core.windows.net) namísto *.blob.core.windows.net
login.microsoftonline.com | Vyžadováno pro autorizaci a autentizaci adres URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Povinné tak, aby služba obnovení webu komunikace může dojít z virtuálního provozu.
*.servicebus.windows.net | Povinné tak, aby monitorování webu obnovení a diagnostická data mohou být zapsány z virtuálního zařízení.
*.vault.azure.net | Umožňuje přístup k povolení replikace pro virtuální počítače s podporou ADE prostřednictvím portálu.
*.automation.ext.azure.com | Umožňuje automatické upgradování agenta mobility pro replikovanou položku prostřednictvím portálu

## <a name="outbound-connectivity-using-service-tags"></a>Odchozí připojení pomocí značek služeb

Pokud používáte nsg k řízení odchozí připojení, tyto značky služeb musí být povolena.

- Pro účty úložiště ve zdrojové oblasti:
    - Vytvořte pravidlo nsg založené na [značce služby Úložiště](../virtual-network/security-overview.md#service-tags) pro zdrojovou oblast.
    - Povolte tyto adresy, aby data mohla být zapsána do účtu úložiště mezipaměti z virtuálního účtu.
- Vytvoření pravidla nsg na [základě značky služby Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) pro povolení přístupu ke všem IP adresám odpovídajícím AAD
- Vytvořte pravidlo skupiny nsg na základě značky služby EventsHub pro cílovou oblast, což umožňuje přístup k monitorování obnovení webu.
- Vytvořte pravidlo nsg na základě značky služby AzureSiteRecovery, které umožňuje přístup ke službě Site Recovery v libovolné oblasti.
- Vytvořte pravidlo nsg založené na značce služby AzureKeyVault. To je vyžadováno pouze pro povolení replikace virtuálních počítačů s podporou ADE prostřednictvím portálu.
- Vytvořte pravidlo nsg na základě značky služby GuestAndHybridManagement. To je vyžadováno pouze pro povolení automatického upgradu agenta mobility pro replikovanou položku prostřednictvím portálu.
- Doporučujeme vytvořit požadovaná pravidla nsg na testovací soubor nsg a ověřit, že neexistují žádné problémy před vytvořením pravidel pro výrobu nsg.

## <a name="example-nsg-configuration"></a>Příklad konfigurace nsg

Tento příklad ukazuje, jak nakonfigurovat pravidla sítě zabezpečení sítě pro virtuální počítače replikovat.

- Pokud používáte pravidla nsg k řízení odchozí připojení, použijte "Povolit odchozí HTTPS" pravidla port:443 pro všechny požadované rozsahy IP adres.
- Příklad předpokládá, že umístění zdroje virtuálního soudu je "Východní USA" a cílové umístění je "Střední USA".

### <a name="nsg-rules---east-us"></a>Pravidla nsg - Východní USA

1. Vytvořte odchozí https (443) pravidlo zabezpečení pro "Storage.EastUS" na nsg, jak je znázorněno na snímku obrazovky níže.

      ![značka úložiště](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvořte odchozí https (443) pravidlo zabezpečení pro "AzureActiveDirectory" na souboru zabezpečení, jak je znázorněno na snímku obrazovky níže.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Podobně jako výše uvedená pravidla zabezpečení vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "EventHub.CentralUS" na souboru zabezpečení, které odpovídají cílovému umístění. To umožňuje přístup k monitorování site recovery.

4. Vytvořte odchozí https (443) pravidlo zabezpečení pro "AzureSiteRecovery" na skupinu zabezpečení sítě. To umožňuje přístup ke službě site recovery service v libovolné oblasti.

### <a name="nsg-rules---central-us"></a>Pravidla nsg - Centrální USA

Tato pravidla jsou vyžadována tak, aby replikace mohla být povolena z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání:

1. Vytvořte odchozí https (443) pravidlo zabezpečení pro "Storage.CentralUS" na skupinu zabezpečení.

2. Vytvořte odchozí https (443) pravidlo zabezpečení pro "AzureActiveDirectory" na skupinu zabezpečení.

3. Podobně jako výše uvedená pravidla zabezpečení vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "EventHub.EastUS" na souboru zabezpečení, které odpovídají zdrojovému umístění. To umožňuje přístup k monitorování site recovery.

4. Vytvořte odchozí https (443) pravidlo zabezpečení pro "AzureSiteRecovery" na skupinu zabezpečení sítě. To umožňuje přístup ke službě site recovery service v libovolné oblasti.

## <a name="network-virtual-appliance-configuration"></a>Konfigurace síťového virtuálního zařízení

Pokud používáte síťová virtuální zařízení (NVA) k řízení odchozího síťového provozu z virtuálních počítačů, zařízení může být omezeno, pokud veškerý replikační provoz prochází síťovým virtuálním zařízením. Doporučujeme vytvořit koncový bod síťové služby ve virtuální síti pro "úložiště", aby provoz replikace nepřešel na síťové virtuální zařízení.

### <a name="create-network-service-endpoint-for-storage"></a>Vytvoření koncového bodu síťové služby pro úložiště
Můžete vytvořit koncový bod síťové služby ve virtuální síti pro "úložiště", takže provoz replikace neopustí hranice Azure.

- Vyberte virtuální síť Azure a klikněte na "Koncové body služby"

    ![koncový bod úložiště](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klikněte na "Přidat" a otevře se záložka Přidat koncové body služby.
- V části Služba a požadované podsítě vyberte možnost Microsoft.Storage a požadované podsítě v poli Podsítě a klikněte na tlačítko Přidat.

>[!NOTE]
>Neomezujte přístup virtuální sítě k účtům úložiště používaným pro asr. Měli byste povolit přístup ze všech sítí

### <a name="forced-tunneling"></a>Vynucené tunelování

Můžete přepsat výchozí systémovou trasu Azure pro předponu adresy 0.0.0.0/0 s [vlastní trasou](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat provoz virtuálních počítačů na místní síťové virtuální zařízení (NVA), ale tato konfigurace se nedoporučuje pro replikaci obnovení lokality. Pokud používáte vlastní trasy, měli byste [vytvořit koncový bod virtuální síťové služby](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve virtuální síti pro "úložiště", aby provoz replikace neopustil hranici Azure.

## <a name="next-steps"></a>Další kroky
- Začněte chránit své úlohy [replikací virtuálních počítačů Azure](site-recovery-azure-to-azure.md).
- Přečtěte si další informace o [uchovávání IP adres](site-recovery-retain-ip-azure-vm-failover.md) pro převzetí služeb při selhání virtuálního počítače Azure.
- Další informace o zotavení po havárii [virtuálních počítačů Azure s ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
