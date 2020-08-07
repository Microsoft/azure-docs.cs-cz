---
title: Co je Oracle WebLogic Server v Azure?
description: Naučte se, jak spustit Oracle WebLogic Server na Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851850"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Co je Oracle WebLogic Server v Azure?

Tato stránka popisuje řešení pro spuštění WebLogic serveru (WLS) na Azure Virtual Machines.  Tato řešení jsou společně vyvinutá Oracle a Microsoftem.

Oracle WebLogic Server je první cloudový nativní cloudový aplikační server pro vývoj a nasazování vícevrstvých distribuovaných podnikových aplikací. Azure WebLogic Server nabízí možnost využít cloud computing.  Získáte větší možnosti a flexibilitu migrace WebLogic, včetně zvedání a Shift vašich aplikací Java EE do cloudu Azure.   Na platformě Azure se s malým úsilím poskytuje velký dopad. Nabídky vám umožní rychle spustit obchodní aplikace (LOB).  Každá nabídka automaticky zřídí prostředky virtuální sítě, úložiště a Linux.  S nulovým úsilím se nainstaluje WebLogic Server.  Služba WLS v Azure nastavuje zabezpečení pomocí skupiny zabezpečení sítě, Vyrovnávání zatížení s využitím služby Azure App Gateway, ověřování pomocí Azure Active Directory a automaticky se připojuje k vaší stávající databázi.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="K nasazení WebLogic serveru v Azure můžete použít Azure Portal.":::

Existují čtyři nabídky k dispozici pro splnění různých scénářů: jeden uzel bez serveru pro správu, jeden uzel se serverem pro správu, clusterem a dynamickým clusterem.  Vyzkoušejte si nabídky, které jsou k dispozici zdarma.

_Tyto nabídky jsou vlastními licencemi_. Předpokládají, že už máte příslušné licence pro Oracle a máte řádně licenci ke spuštění nabídek v Microsoft Azure.

_Pokud chcete úzce pracovat na scénářích migrace pomocí technického týmu, který vyvíjí tyto nabídky, vyberte na Azure Marketplace tlačítko [kontaktovat](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ . [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) Správci programu, architekti a technici vám budou brzy kontaktovat a začít spolupracovat.

## <a name="oracle-weblogic-server-single-node"></a>Jeden uzel Oracle WebLogic Server

Tato nabídka zřídí jeden virtuální počítač a nainstaluje na něj službu WLS. Nevytváří doménu ani nespouští Server pro správu. Jeden uzel je vhodný pro scénáře s vysoce přizpůsobenou konfigurací domény.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server se serverem pro správu

Tato nabídka zřídí jeden virtuální počítač a nainstaluje na něj službu WLS. Vytvoří doménu a spustí server pro správu, který umožňuje správu domény.

## <a name="oracle-weblogic-server-cluster"></a>Serverový cluster Oracle WebLogic Server

Tato nabídka vytvoří cluster virtuálních počítačů na serveru WLS s vysokou dostupností. Server pro správu a všechny spravované servery jsou spouštěny ve výchozím nastavení, což umožňuje spravovat doménu.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamický cluster serveru Oracle WebLogic Server

Tato nabídka vytvoří vysoce dostupný a škálovatelný dynamický cluster virtuálních počítačů na serveru WLS. Server pro správu a všechny spravované servery jsou spouštěny ve výchozím nastavení, což umožňuje spravovat doménu.

## <a name="next-steps"></a>Další kroky

Prozkoumejte nabídky Azure Marketplace.

> [!div class="nextstepaction"]
> [Jeden uzel Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server se serverem pro správu](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Serverový cluster Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamický cluster serveru Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
