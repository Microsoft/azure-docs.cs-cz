---
title: Úlohy Red Hatu v Azure – přehled | Dokumenty společnosti Microsoft
description: Přečtěte si o produktových nabídkách Red Hat dostupných v Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970173"
---
# <a name="red-hat-workloads-on-azure"></a>Úlohy Red Hatu v Azure

Úlohy Red Hatu jsou podporované prostřednictvím různých nabídek v Azure. Image Red Hat Enterprise Linux (RHEL) jsou jádrem úloh RHEL, stejně jako red hat update infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Obrázky Red Hat Enterprise Linux

Azure nabízí širokou nabídku iniciál RHEL v Azure. Tyto obrázky jsou k dispozici prostřednictvím dvou různých licenčních modelů: průběžně zdatné platby a předplatné (BYOS). Nové ifotky RHEL v Azure se publikují, když se v průběhu jejich životního cyklu vydají a aktualizují nové verze RHEL.

### <a name="pay-as-you-go-images"></a>Obrázky s průběžným platbou

Azure nabízí celou řadu bitových kopií RHEL s průběžným platbou. Tyto obrázky mají právo na RHEL a jsou připojeny ke zdroji aktualizací (Red Hat Update Infrastructure). Tyto obrázky účtují prémiový poplatek za nárok a aktualizace RHEL. Mezi varianty obrázků s průběžným platbou rhel patří:

* Standardní RHEL.
* RHEL pro SAP.
* RHEL pro SAP s vysokou dostupností a aktualizačními službami.

Pokud se nechcete starat o samostatnou platbu za příslušný počet předplatných, můžete je použít pomocí obrázků s průběžným platbou.

### <a name="red-hat-gold-images"></a>Red Hat Gold Obrázky

Azure také nabízí obrázky`rhel-byos`Red Hat Gold ( ). Tyto image mohou být užitečné pro zákazníky, kteří mají existující předplatné Red Hat a chtějí je používat v Azure. Před použitím v Azure je musíte povolit stávající předplatná Red Hat pro Red Hat Cloud Access. Přístup k těmto bitvám se udělí automaticky, když jsou vaše předplatná Red Hat povolena pro cloudový přístup a splňují požadavky na způsobilost. Použití těchto obrázků umožňuje zákazníkovi vyhnout se dvojí fakturaci, která by mohla vzniknout při používání bitových kopií s průběžnými platbami.
* Přečtěte si, jak [povolit předplatná Red Hat pro Cloud Access s Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Zjistěte, jak [najít image Red Hat Gold na webu Azure Portal, v azure cli nebo rutině PowerShellu.](./byos.md)

> [!NOTE]
> Dvojí fakturace vzniká, když uživatel zaplatí dvakrát za předplatné RHEL. K tomuto scénáři obvykle dochází, když zákazník používá Správce předplatného Red Hat k připojení nároku na virtuální počítač s průběžným platbou RHEL. Například zákazník, který používá Subscription-Manager k připojení nároku na balíčky SAP na bitové kopii průběžných plateb RHEL, se nepřímo účtuje dvakrát, protože platí dvakrát za RHEL. Platí jednou prostřednictvím pay-as-you-go premium poplatek a jednou prostřednictvím svého předplatného SAP. Tento scénář se nestane uživatelům bitových obrázků BYOS.

### <a name="generation-2-images"></a>Generace 2 obrázky

Generace 2 virtuální počítače (VMs) poskytují některé novější funkce ve srovnání s generace 1 virtuálních počítačů. Další informace naleznete v [dokumentaci generace 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). Klíčovým rozdílem z hlediska obrazu RHEL je, že virtuální zařízení generace 2 používají rozhraní firmwaru systému BIOS místo rozhraní firmwaru systému BIOS. Používají také tabulku oddílů GUID (GPT) namísto hlavního spouštěcího záznamu (MBR) při spuštění. Použití GPT umožňuje mimo jiné velikosti disků operačního systému větší než 2 TB. Kromě toho [virtuální chodřady Mv2](../../mv2-series.md) běží jenom na bitových kopiích generace 2.

Image RHEL Generation 2 jsou dostupné na Azure Marketplace. Vyhledejte "gen2" v sku image v seznamu všech bitových kopií, které se zobrazí při použití příkazového příkazu k příkazu Konta Azure. Přejděte na kartu **Upřesnit** v procesu nasazení virtuálního montovana k nasazení virtuálního účtu generace 2.

## <a name="red-hat-update-infrastructure"></a>Infrastruktura aktualizací Red Hatu

Azure poskytuje infrastrukturu aktualizací Red Hat jenom pro virtuální počítače RHEL s průběžným platbou. RHUI je efektivně zrcadlo Red Hat CDNs, ale je přístupný jenom pro virtuální počítače Rhel Azure s průběžným platbou. Máte přístup k příslušným balíčkům v závislosti na image RHEL, kterou jste nasadili. Například obrázek RHEL pro SAP má přístup k balíčkům SAP kromě základních balíčků RHEL.

### <a name="rhui-update-behavior"></a>Chování aktualizace RHUI

Obrazy RHEL připojené k RHUI se ve výchozím nastavení `yum update` aktualizují na nejnovější dílčí verzi rhelu při spuštění. Toto chování znamená, že virtuální počítač RHEL 7.4 může získat `yum update` upgrade na RHEL 7.7, pokud je spuštěna operace na něm. Toto chování je záměrné pro RHUI. Chcete-li zmírnit toto chování upgradu, přepněte z běžných úložišť RHEL na [úložiště rozšířené podpory aktualizací](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Další kroky

* Další informace o [inacích RHEL v Azure](./redhat-images.md).
* Další informace o [infrastruktuře aktualizací Red Hat](./redhat-rhui.md).
* Další informace o [nabídce`rhel-byos`Red Hat Gold Image ( ).](./byos.md)
