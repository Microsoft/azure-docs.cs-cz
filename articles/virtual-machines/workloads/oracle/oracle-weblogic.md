---
title: Oracle WebLogic Server Azure Applications | Microsoft Docs
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
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83665212"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Oracle WebLogic Server Azure – aplikace

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server je škálovatelný server aplikace Java EE připravený pro podniky.

Oracle WebLogic Server je první cloudový nativní cloudový aplikační server pro vývoj a nasazování vícevrstvých distribuovaných podnikových aplikací. Azure WebLogic Server nabízí možnost využít cloud computing tím, že poskytuje větší možnosti a flexibilitu migrace WebLogic, včetně zvedání a posunování aplikací Java EE do cloudu Azure s nejmenším úsilím a největším dopadem. Nabídky umožňují rychle spustit obchodní aplikace tím, že automaticky zřídí prostředky virtuální sítě, úložiště a Linux, nainstalujete server WebLogic, nastavíte zabezpečení pomocí skupiny zabezpečení sítě, Vyrovnávání zatížení pomocí služby Azure App Gateway, ověřování pomocí Azure Active Directory a usnadnění připojení databáze.

Existují čtyři nabídky k dispozici pro splnění různých scénářů: jeden uzel bez serveru pro správu, jeden uzel se serverem pro správu, cluster a dynamický cluster.  Měli byste si být jisti, že nabídky jsou bezplatné, takže nabídky jsou dostupné zdarma.

_Tyto nabídky jsou vlastními licencemi_. Předpokládají, že jste už získali příslušné licence pro Oracle a máte řádně licenci ke spuštění nabídek v Microsoft Azure.

_Pokud chcete úzce pracovat na scénářích migrace s technickým týmem, který vyvíjí tyto nabídky, stačí tlačítko [kontaktovat mi](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ v [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Vedoucí programu, architekti a technici vám budou brzy kontaktovat a zahájit spolupráci.

### <a name="oracle-weblogic-server-single-node"></a>Jeden uzel Oracle WebLogic Server

Tato nabídka zřídí jeden virtuální počítač a na něj nainstaluje Oracle WebLogic Server. Není vytvořena doména ani nemůže spustit server pro správu. To je užitečné pro scénáře s vysoce přizpůsobenou konfigurací domény.

### <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server se serverem pro správu

Tato nabídka zřídí jeden virtuální počítač a na něj nainstaluje Oracle WebLogic Server. Vytvoří doménu a spustí server pro správu, který umožňuje správu domény.

### <a name="oracle-weblogic-server-cluster"></a>Serverový cluster Oracle WebLogic Server

Tato nabídka vytvoří cluster virtuálních počítačů Oracle WebLogic Server s vysokou dostupností. Server pro správu a všechny spravované servery jsou spouštěny ve výchozím nastavení, což umožňuje spravovat doménu.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamický cluster serveru Oracle WebLogic Server

Tato nabídka vytvoří vysoce dostupný a škálovatelný dynamický cluster virtuálních počítačů Oracle WebLogic Server. Server pro správu a všechny spravované servery jsou spouštěny ve výchozím nastavení, což umožňuje spravovat doménu.

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
