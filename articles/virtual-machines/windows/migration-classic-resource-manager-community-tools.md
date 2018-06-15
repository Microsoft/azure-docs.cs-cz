---
title: Komunita nástroje – klasické prostředky přesunout do Azure Resource Manageru | Microsoft Docs
description: Tento článek obsahuje nástroje, které poskytly pomohou migrovat prostředky IaaS z klasického modelu nasazení Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: cce1906e75646f2fb9ea30842e968d14830f3497
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913932"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Nástroje komunity pro migraci prostředků IaaS z modelu Classic na Azure Resource Manager
Tento článek obsahuje nástroje, které byly zadány komunitou jako pomoc s migrací prostředky infrastruktury z klasického modelu nasazení Azure Resource Manager.

> [!NOTE]
> Tyto nástroje nejsou oficiálně podporována Microsoft Support. Proto jsou open source na Githubu a máme radostí tak, aby přijímal PRs opravy nebo další scénáře. Chcete-li ohlásit problém, použijte funkci problémy Githubu.
> 
> Migrace s těmito nástroji způsobí, že výpadek pro klasické virtuální počítač. Pokud hledáte platformy podporované migrace, navštivte 
> 
>   * [Platforma podporovaná migrace prostředky infrastruktury z klasického správce Azure Resource Manager zásobníku](migration-classic-resource-manager-overview.md)
>   * [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md)
>   * [Migrace prostředků IaaS z klasického do správce Azure Resource Manager pomocí Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Toto je kolekce pomocné nástroje, které jsou vytvořené jako součást enterprise migrace z Azure Service Management do Azure Resource Manageru. Tento nástroj umožňuje replikovat do jiné předplatné, které lze použít pro testování migrace a železa na všechny problémy před spuštěním migrace na vaše předplatné produkční vaší infrastruktury.

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz je další možnost pro migraci kompletní sadu klasické prostředky IaaS k prostředkům Azure Resource Manager IaaS. Migrace může dojít v rámci stejného předplatného nebo mezi různých předplatných a typy předplatného (např: CSP odběry).

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Další kroky

* [Přehled platformy podporované migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Azure Resource Manageru pomocí prostředí PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Pomocí rozhraní příkazového řádku můžete migrovat prostředky infrastruktury z classic do Azure Resource Manageru](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy o migraci prostředky infrastruktury jako služby z klasického do Azure Resource Manageru](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

