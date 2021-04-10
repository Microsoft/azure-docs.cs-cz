---
title: Co jsou řešení pro provoz Oracle WebLogic serveru v Azure Virtual Machines
description: Naučte se, jak spustit Oracle WebLogic Server na Microsoft Azure Virtual Machines.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/23/2021
ms.author: rezar
ms.openlocfilehash: 59bae8bfea29ee458288751209ffc860b5b9ce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955991"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Jaká jsou řešení pro spouštění Oracle WebLogic Serveru na virtuálních počítačích Azure?

Tato stránka popisuje řešení pro spouštění Oracle WebLogic serveru (WLS) na virtuálních počítačích Azure. Tato řešení jsou společně vyvinutá a podporovaná Oracle a Microsoftem.

Službu WLS můžete spustit také ve službě Azure Kubernetes. Tato řešení jsou popsaná v [tomto článku společnosti Microsoft](./weblogic-aks.md).

Server WLS je předním aplikačním serverem Java, který provozuje nejdůležitější podnikové aplikace v jazyce Java po celém světě. Na serveru WLS je základem middlewaru pro sadu Oracle Software Suite. Oracle a Microsoft se zavazují přispívat zákazníkům ze serveru WLS a pružně spouštět úlohy v Azure jako špičkovou cloudovou platformu.

Řešení Azure WLS se zaměřuje na to, aby bylo možné snadno migrovat aplikace Java na virtuální počítače Azure. Řešení to uděláte tak, že vygenerujete nasazené prostředky pro většinu běžných scénářů zřizování cloudu. Řešení automaticky zřídí prostředky virtuální sítě, úložiště, jazyků Java, serveru WLS a Linux. S minimálním úsilím se nainstaluje WebLogic Server. Řešení můžou nastavovat zabezpečení pomocí skupiny zabezpečení sítě, Vyrovnávání zatížení s Azure App Gateway, ověřování pomocí Azure Active Directory, centralizované protokolování pomocí ELK a distribuované ukládání do mezipaměti s koprovázaností Oracle. Můžete se také automaticky připojit k vaší stávající databázi, včetně Azure PostgreSQL, Azure SQL a Oracle DB v cloudu Oracle nebo v Azure. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="K nasazení WebLogic serveru v Azure můžete použít Azure Portal.":::

Existují čtyři nabídky k dispozici pro splnění různých scénářů: [jeden uzel bez serveru pro správu](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [jeden uzel se serverem pro správu](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [clusterem](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)a [dynamickým clusterem](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). Nabídky jsou k dispozici bezplatně. Tyto nabídky jsou popsány a propojeny níže.

_Tyto nabídky jsou vlastními licencemi_. Předpokládají, že už máte příslušné licence pro Oracle a máte řádně licenci na spuštění nabídek v Azure.

Nabízí podporu řady operačních systémů, jazyků Java a serveru WLS prostřednictvím základních imagí (například WebLogic Server 14 a JDK 11 v Oracle Linux 7,6). Tyto základní image jsou k dispozici také na Azure sami. Základní image jsou vhodné pro zákazníky, kteří vyžadují složitá a přizpůsobená nasazení Azure. Aktuální sada základních imagí je k dispozici [zde](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Pokud vás zajímá, jak úzce pracovat na scénářích migrace pomocí technického týmu, který vyvíjí tyto nabídky, [](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) vyberte_ na [stránce Přehled nabídky Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)tlačítko kontaktovat. Správci programu, architekti a technici budou brzy k dispozici a zahájí se uzavírání spolupráce. Možnost spolupracovat na scénáři migrace je zadarmo, zatímco nabídky jsou v rámci aktivního vývoje.

## <a name="oracle-weblogic-server-single-node"></a>Jeden uzel Oracle WebLogic Server

[Tato nabídka](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) zřídí jeden virtuální počítač a nainstaluje na něj službu WLS. Nevytváří doménu ani nespouští Server pro správu. Nabídka s jedním uzlem je užitečná pro scénáře s vysoce přizpůsobenou konfigurací domény.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server se serverem pro správu

[Tato nabídka](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) zřídí jeden virtuální počítač a nainstaluje na něj službu WLS. Vytvoří doménu a spustí server pro správu. Můžete spravovat doménu a hned začít s nasazením aplikací.

## <a name="oracle-weblogic-server-cluster"></a>Serverový cluster Oracle WebLogic Server

[Tato nabídka](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) vytvoří cluster virtuálních počítačů na serveru WLS s vysokou dostupností. Server pro správu a všechny spravované servery jsou spouštěny ve výchozím nastavení. Cluster můžete spravovat a hned začít s aplikacemi s vysokou dostupností.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamický cluster serveru Oracle WebLogic Server

[Tato nabídka](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) vytvoří vysoce dostupný a škálovatelný dynamický cluster virtuálních počítačů na serveru WLS. Server pro správu a všechny spravované servery jsou spouštěny ve výchozím nastavení.

Řešení umožní široké spektrum architektur pro nasazení připravené pro produkční prostředí s relativním usnadněním. Většinu případů migrace můžete vyhovět co nejefektivnějším způsobem, a to díky tomu, že se zaměříte na vývoj obchodních aplikací.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="V Azure jsou povolená složitá nasazení serveru WebLogic.":::

Kromě toho, co jsou řešení automaticky zřízené, mají zákazníci ještě plnou flexibilitu pro přizpůsobení jejich nasazení. Zákazníci s nasazováním aplikací nejspíš budou integrovat další prostředky Azure s jejich nasazeními. Zákazníkům se doporučuje [připojit se k vývojovému týmu](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) a poskytnout zpětnou vazbu k dalšímu vylepšení řešení.

## <a name="next-steps"></a>Další kroky

Prozkoumejte nabídky Azure.

> [!div class="nextstepaction"]
> [Jeden uzel Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server se serverem pro správu](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Serverový cluster Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamický cluster serveru Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
