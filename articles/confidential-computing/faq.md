---
title: Nejčastější dotazy k Azure důvěrného zpracování
description: Odpovědi na nejčastější dotazy týkající se důvěrného výpočetního prostředí Azure
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 2bd9430fc6f48d72faa2c1850af0bb8432a7c5f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149499"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Nejčastější dotazy týkající se důvěrného výpočetního prostředí Azure

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se spouštění [důvěrných výpočetních úloh v Azure](overview.md).

Pokud váš problém s Azure není v tomto článku řešen, navštivte fóra Azure na [webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Svůj problém můžete vystavit na těchto fórech nebo odeslat [ @AzureSupport na Twitter](https://twitter.com/AzureSupport). Můžete také odeslat žádost o podporu Azure. Pokud chcete odeslat žádost o podporu, vyberte na [stránce podpory Azure](https://azure.microsoft.com/support/options/)možnost získat podporu.

## <a name="confidential-computing-virtual-machines"></a>Důvěrné výpočetní Virtual Machines<a id="vm-faq"></a>

1. **Jak můžete začít nasazovat virtuální počítače řady DCsv2?**

   Tady je několik způsobů, jak můžete nasadit virtuální počítač s DCsv2:
   - Použití [šablony Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - Z [Azure Portal](https://portal.azure.com/#create/hub)
   - V šabloně řešení [Azure důvěrné COMPUTE (virtuální počítač)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace. Šablona řešení Marketplace vám pomůže omezit zákazníky na podporované scénáře (oblasti, obrázky, dostupnost, šifrování disků). 

1. **Budou všechny image operačních systémů fungovat s důvěrným výpočetním prostředím Azure?**

   Ne. Virtuální počítače se dají nasadit jenom na virtuální počítače generace 2. Nabízíme podporu generace 2 pro Ubuntu Server 18,04, Ubuntu Server 16,04 a Windows Server 2016 Datacenter. Další informace o virtuálních počítačích 2. generace v systémech [Linux](../virtual-machines/linux/generation-2.md) a [Windows](../virtual-machines/windows/generation-2.md)

1. **Virtuální počítače s DCsv2 jsou na portálu zobrazeny šedě a nelze je vybrat.**

   Na základě informačního bublinového rámečku u virtuálního počítače je potřeba vzít v úvahu různé akce:
    -   **UnsupportedGeneration**: Změňte generaci image virtuálního počítače na "Gen2".
    -   **NotAvailableForSubscription** : oblast zatím není pro vaše předplatné k dispozici. Vyberte dostupnou oblast.
    -   **InsufficientQuota**: [vytvořte žádost o podporu, která zvýší vaši kvótu](../azure-portal/supportability/per-vm-quota-requests.md). Bezplatné zkušební předplatné nemají kvótu pro virtuální počítače s důvěrnými výpočetními prostředími. 

1. **Virtuální počítače s DCsv2 se při pokusu o jejich hledání v selektoru velikosti portálu nezobrazí.**

   Ujistěte se, že jste vybrali oblast, která je k dispozici. Také se ujistěte, že v selektoru velikosti vyberete možnost zrušit všechny filtry. 

1. **Jaký je rozdíl mezi virtuálními počítači řady DCsv2-Series a DC-Series?**

   Virtuální počítače DC-Series běží na starších 6 procesorech Intel s SGX. Mají méně celkovou paměť, méně paměti EPC (enklávy Page klec) a jsou dostupné v méně oblastech. Tyto virtuální počítače jsou dostupné jenom v USA – východ a Evropa – západ jsou k dispozici ve dvou velikostech: Standard_DC2s a Standard_DC4s. Nebudou jít o GA a dají se nasadit jenom v instanci Marketplace [Cloud COMPUTE DC-Series VM [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) .

1. **Jsou virtuální počítače DCsv2 k dispozici globálně?**

   Ne. Tyto virtuální počítače jsou dostupné jenom ve vybraných oblastech. Poslední dostupné oblasti najdete na [stránce produkty podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) . 

1. **Návody nainstalovat sadu SDK Open enklávy?**
   
   Pokyny k instalaci sady OE SDK na počítač bez ohledu na to, jestli jsou v Azure nebo v místním nasazení, postupujte podle pokynů v článku [Open ENKLÁVY SDK GitHub](https://github.com/openenclave/openenclave).
     
   Můžete také přejít na otevřený enklávy SDK GitHub pro pokyny k instalaci specifické pro konkrétní operační systém:
     - [Instalace rozhraní OE SDK ve Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Instalace sady OE SDK na Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Instalace sady OE SDK na Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
