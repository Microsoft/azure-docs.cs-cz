---
title: Úlohy Red Hat na Azure – přehled | Microsoft Docs
description: Seznamte se s nabídkami produktů Red Hat dostupnými v Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 09612ff3d86c669801f9e824d367a2b9a5bb10ac
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830645"
---
# <a name="red-hat-workloads-on-azure"></a>Úlohy Red Hat v Azure

Úlohy Red Hat se podporují prostřednictvím nejrůznějších nabídek v Azure. Bitové kopie Red Hat Enterprise Linux (RHEL) jsou v jádru úloh RHEL, jako je například infrastruktura Red Hat Update (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux image

Azure nabízí rozsáhlou nabídku imagí RHEL v Azure. Tyto image jsou zpřístupněny prostřednictvím dvou různých modelů licencování: průběžné platby a vlastní předplatné (BYOS). Nové image RHEL v Azure se publikují, až budou nové verze RHEL vydané a aktualizované v celém životním cyklu, podle potřeby.

### <a name="pay-as-you-go-images"></a>Obrázky s průběžnými platbami

Azure nabízí celou řadu RHELch imagí s průběžnými platbami. Tyto image jsou správně způsobilé pro RHEL a jsou připojené ke zdroji aktualizací (infrastruktura Red Hat Update). Tyto image účtují poplatek za prémi za RHEL nárok a aktualizace. Varianty imagí s průběžnými platbami RHEL zahrnují:

* RHEL úrovně Standard
* RHEL pro SAP.
* RHEL pro SAP s vysokou dostupností a aktualizačními službami.

Pokud se nechcete zabývat nezávisle na placení za příslušný počet předplatných, můžete použít image s průběžnými platbami.

### <a name="red-hat-gold-images"></a>Image Red Hat Gold

Azure nabízí také Red Hat Gold images ( `rhel-byos` ). Tyto obrázky můžou být užitečné pro zákazníky, kteří mají existující předplatné Red Hat a chtějí je používat v Azure. Abyste mohli povolit cloudový přístup Red Hat do Azure, budete si muset povolit svoje stávající předplatné Red Hat. Přístup k těmto obrázkům se uděluje automaticky, pokud jsou pro cloudový přístup povolená předplatná Red Hat a splňují požadavky na způsobilost. Použití těchto imagí umožňuje zákazníkovi vyhnout se dvojímu vyúčtování, které by mohlo vést k používání imagí s průběžnými platbami.
* Naučte se, jak [Povolit předplatná Red Hat pro cloudový přístup pomocí Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access).
* Přečtěte si [, jak najít obrázky Red Hat Gold v rutinách Azure Portal, Azure CLI nebo PowerShellu](./byos.md).

> [!NOTE]
> Pokud uživatel zaplatí dvakrát pro předplatná RHEL, účtuje se dvojitá fakturace. K tomuto scénáři obvykle dochází, když zákazník používá Subscription-Manager Red Hat k připojení nároku na RHEL virtuální počítač s průběžnými platbami. Například zákazník, který používá Subscription-Manager k připojení oprávnění k balíčkům SAP na RHEL s průběžnými platbami, se nepřímo zdvojnásobí, protože platí dvakrát pro RHEL. Platíte jednou přes poplatek za průběžné platby za vás a jednou prostřednictvím předplatného SAP. Tento scénář se nestane BYOSým uživatelům obrazu.

### <a name="generation-2-images"></a>Image 2. generace

Virtuální počítače generace 2 poskytují několik novějších funkcí v porovnání s virtuálními počítači 1. generace. Další informace najdete v dokumentaci k [generaci 2](../../generation-2.md). Klíčovým rozdílem z hlediska image RHEL je, že virtuální počítače 2. generace používají rozhraní UEFI místo rozhraní firmware systému BIOS. Místo hlavního spouštěcího záznamu (MBR) v době spuštění používají také tabulku oddílů GUID (GPT). Použití GPT umožňuje mimo jiné velikost disků s operačním systémem větší než 2 TB. [Virtuální počítače řady Mv2](../../mv2-series.md) se navíc spouštějí jenom na imagí generace 2.

RHEL generace 2 jsou k dispozici v Azure Marketplace. V části SKU image vyhledejte "Gen2" v seznamu všech imagí, které se zobrazí při použití rozhraní příkazového řádku Azure CLI. Pokud chcete nasadit virtuální počítač 2. generace, v procesu nasazení virtuálního počítače přejít na kartu **Upřesnit** .

## <a name="red-hat-update-infrastructure"></a>Infrastruktura aktualizací Red Hatu

Azure poskytuje infrastrukturu aktualizací Red Hat jenom pro virtuální počítače RHEL s průběžnými platbami. RHUI je v podstatě zrcadlový obraz sítě CDN Red Hat, ale je dostupný jenom pro virtuální počítače RHEL s průběžnými platbami za Azure. Máte přístup k příslušným balíčkům v závislosti na tom, jakou bitovou kopii RHEL jste nasadili. Například RHEL pro Image SAP má kromě základních balíčků RHEL přístup k balíčkům SAP.

### <a name="rhui-update-behavior"></a>Chování aktualizace RHUI

RHEL image připojené k RHUI aktualizací ve výchozím nastavení na nejnovější podverzi RHEL při `yum update` spuštění. Toto chování znamená, že virtuální počítač s RHEL 7,4 se může upgradovat na RHEL 7,7, pokud `yum update` se na něm spustí operace. Toto chování je záměrné pro RHUI. Pokud chcete toto chování upgradu zmírnit, přepínejte z běžných úložišť RHEL do [úložišť podpory rozšířené aktualizace](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [RHEL imagí v Azure](./redhat-images.md).
* Přečtěte si další informace o [infrastruktuře aktualizací Red Hat](./redhat-rhui.md).
* Přečtěte si další informace o [nabídce Red Hat Gold Image ( `rhel-byos` )](./byos.md).