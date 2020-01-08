---
title: Úlohy Red Hat na Azure – přehled | Microsoft Docs
description: Seznamte se s nabídkami produktů Red Hat dostupnými v Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473161"
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

### <a name="bring-your-own-subscription-byos-images"></a>Image s vlastními předplatnými (BYOS)
Azure také nabízí RHEL image BYOS. Tyto obrázky můžou být užitečné pro zákazníky, kteří mají existující předplatné Red Hat a chtějí je používat v Azure. Abyste mohli svoje stávající odběry použít v Azure, musíte je převést na předplatné Cloud Accessu. Přístup k těmto obrázkům je udělen pouze v případě, že Red Hat ověřuje, zda máte dostatečný odběr cloudového přístupu. Pomocí těchto imagí můžete zákazníkovi zabránit dvojímu vyúčtování, které by mohlo vycházet z použití PAYG imagí. Můžete požádat o přístup k imagí BYOS [zde](https://aka.ms/rhel-byos).

> [!NOTE]
> Poznámka: při dvojím účtování se účtuje dvojitá fakturace, pokud uživatel za předplatných RHEL zaplatí dvakrát. K tomu obvykle dochází v případě, že zákazník používá správce předplatného k připojení nároku na virtuální počítač s RHEL PAYG. Například zákazník, který používá předplatné (Správce předplatného) k připojení nároku na balíčky SAP na RHEL imagi PAYG, se nebude nepřímo fakturovat, protože se za RHEL-jedenkrát účtuje přes poplatek za PAYG Premium a jednou prostřednictvím jejich předplatného SAP. Nestane se tak uživatelům BYOS image.

## <a name="red-hat-update-infrastructure-rhui"></a>Infrastruktura aktualizace Red Hat (RHUI)
Azure poskytuje infrastrukturu aktualizací Red Hat jenom pro virtuální počítače s PAYG RHEL. RHUI je ve skutečnosti zrcadlem červené Hat sítě CDN, ale je dostupné jenom pro virtuální počítače Azure PAYG RHEL. V závislosti na RHEL imagi, kterou jste nasadili, budete mít přístup k příslušným balíčkům. Například RHEL pro Image SAP bude mít kromě základních balíčků RHEL přístup k balíčkům SAP.

### <a name="rhui-update-behavior"></a>Chování aktualizace RHUI
Image RHEL připojené k RHUI se ve výchozím nastavení aktualizují na nejnovější podverzi RHEL při spuštění `yum update`. Toto chování znamená, že virtuální počítač s RHEL 7,4 se může upgradovat na RHEL 7,7, pokud se na něm spustí operace `yum update`. Jedná se o návrh RHUI. Toto chování upgradu se ale dá zmírnit přepnutím z běžných úložišť RHEL do [úložišť EUS (Extended Update support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Další kroky
* Další informace o [RHEL imagí v Azure](./redhat-images.md)
* Další informace o [infrastruktuře aktualizací Red Hat](./redhat-rhui.md)
* Další informace o [nabídce RHEL BYOS](./redhat-byos.md)