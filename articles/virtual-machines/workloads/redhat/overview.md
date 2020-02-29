---
title: Úlohy Red Hat na Azure – přehled | Microsoft Docs
description: Seznamte se s nabídkami produktů Red Hat dostupnými v Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 424ef37885d685829a11d1864a72b043a562231c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920548"
---
# <a name="red-hat-workloads-on-azure"></a>Úlohy Red Hat v Azure
Úlohy Red Hat se podporují prostřednictvím nejrůznějších nabídek v Azure. Bitové kopie Red Hat Enterprise Linux (RHEL) jsou v jádru úloh RHEL, jako je například infrastruktura Red Hat Update (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Bitové kopie Red Hat Enterprise Linux (RHEL)
Azure nabízí rozsáhlou nabídku imagí RHEL v Azure. Tyto image jsou dostupné prostřednictvím dvou různých modelů licencování: průběžné platby (PAYG) a přineste si vlastní předplatné (BYOS). Nové image RHEL v Azure se zveřejňují po vydání nových verzí RHEL a jejich aktualizaci v celém životním cyklu podle potřeby.

### <a name="pay-as-you-go-payg-images"></a>Image s průběžnými platbami (PAYG)
Azure nabízí celou řadu imagí RHEL PAYG. Tyto image jsou správně způsobilé pro RHEL a jsou připojené ke zdroji aktualizací (infrastruktura Red Hat Update). Tyto image budou účtovat poplatek za prémi na RHEL nárok a aktualizace. Varianty imagí RHEL PAYG zahrnují:
* RHEL úrovně Standard
* RHEL pro SAP
* RHEL pro SAP s vysokou dostupností a aktualizačními službami.

Pokud se nechcete zabývat nezávisle na placení za příslušný počet předplatných, možná budete chtít použít image PAYG.

### <a name="red-hat-gold-images-rhel-byos"></a>Image Red Hat Gold (`rhel-byos`)
Azure nabízí také obrázky Red Hat Gold. Tyto obrázky můžou být užitečné pro zákazníky, kteří mají existující předplatné Red Hat a chtějí je používat v Azure. Abyste mohli povolit cloudový přístup Red Hat do Azure, musíte pro něj Povolit svoje stávající předplatné Red Hat. Přístup k těmto obrázkům se uděluje automaticky, pokud jsou pro cloudový přístup povolená předplatná Red Hat a splňují požadavky na způsobilost. Pomocí těchto imagí můžete zákazníkovi zabránit dvojímu vyúčtování, které by mohlo vycházet z použití PAYG imagí.
* [Naučte se, jak povolit předplatná Red Hat pro cloudový přístup pomocí Azure.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Přečtěte si, jak najít obrázky Red Hat Gold v rutině Azure Portal, CLI nebo PowerShellu.](./byos.md)

> [!NOTE]
> Poznámka: při dvojím účtování se účtuje dvojitá fakturace, pokud uživatel za předplatných RHEL zaplatí dvakrát. K tomu obvykle dochází v případě, že zákazník používá správce předplatného k připojení nároku na virtuální počítač s RHEL PAYG. Například zákazník, který používá předplatné (Správce předplatného) k připojení nároku na balíčky SAP na RHEL imagi PAYG, se nebude nepřímo fakturovat, protože se za RHEL-jedenkrát účtuje přes poplatek za PAYG Premium a jednou prostřednictvím jejich předplatného SAP. Nestane se tak uživatelům BYOS image.

### <a name="generation-2-images"></a>Image 2. generace
Virtuální počítače generace 2 poskytují několik novějších funkcí v porovnání s virtuálními počítači 1. generace. Podrobnosti jsou uvedeny v [dokumentaci generace 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). Klíčovým rozdílem z hlediska image RHEL je, že virtuální počítače 2. generace používají rozhraní UEFI místo rozhraní firmware systému BIOS a místo hlavního spouštěcího záznamu (MBR) v době spuštění použijí tabulku oddílů GUID (GPT). To mimo jiné umožňuje i velikosti disků s operačním systémem větší než 2 TB. [Virtuální počítače řady Mv2](../../mv2-series.md) se navíc spouštějí jenom na imagí generace 2.

Image RHEL generace 2 jsou k dispozici na webu Marketplace. Při výpisu všech imagí pomocí rozhraní příkazového řádku Azure vyhledejte "Gen2" a v procesu nasazení virtuálního počítače v části "Upřesnit" nasaďte virtuální počítač 2. generace.

## <a name="red-hat-update-infrastructure-rhui"></a>Infrastruktura aktualizace Red Hat (RHUI)
Azure poskytuje infrastrukturu aktualizací Red Hat jenom pro virtuální počítače s PAYG RHEL. RHUI je ve skutečnosti zrcadlem červené Hat sítě CDN, ale je dostupné jenom pro virtuální počítače Azure PAYG RHEL. V závislosti na RHEL imagi, kterou jste nasadili, budete mít přístup k příslušným balíčkům. Například RHEL pro Image SAP bude mít kromě základních balíčků RHEL přístup k balíčkům SAP.

### <a name="rhui-update-behavior"></a>Chování aktualizace RHUI
Image RHEL připojené k RHUI se ve výchozím nastavení aktualizují na nejnovější podverzi RHEL při spuštění `yum update`. Toto chování znamená, že virtuální počítač s RHEL 7,4 se může upgradovat na RHEL 7,7, pokud se na něm spustí operace `yum update`. Jedná se o návrh RHUI. Toto chování upgradu se ale dá zmírnit přepnutím z běžných úložišť RHEL do [úložišť EUS (Extended Update support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Další kroky
* Další informace o [RHEL imagí v Azure](./redhat-images.md)
* Další informace o [infrastruktuře aktualizací Red Hat](./redhat-rhui.md)
* Další informace o [nabídce Red Hat Gold Image (`rhel-byos`)](./byos.md)
