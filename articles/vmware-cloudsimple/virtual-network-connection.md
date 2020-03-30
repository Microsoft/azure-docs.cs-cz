---
title: Připojení virtuální sítě Azure ke CloudSimple pomocí ExpressRoute – řešení Azure VMware by CloudSimple
description: Popisuje, jak získat informace o partnerské síti pro připojení mezi virtuální sítí Azure a prostředím CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563991"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Připojení virtuální sítě Azure ke CloudSimple pomocí ExpressRoute

Privátní cloudovou síť můžete rozšířit na virtuální síť Azure a prostředky Azure. Připojení ExpressRoute umožňuje přístup k prostředkům spuštěným ve vašem předplatném Azure z vašeho privátního cloudu.

## <a name="request-authorization-key"></a>Požádat o autorizační klíč

Pro připojení ExpressRoute mezi privátním cloudem a virtuální sítí Azure je vyžadován autorizační klíč. Chcete-li získat klíč, soubor lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a>.  V žádosti použijte následující informace:

* Typ problému: **Technické**
* Předplatné: **Vyberte předplatné, ve kterém se používá služba CloudSimple.**
* Služba: **Řešení VMware od CloudSimple**
* Typ problému: **Požadavek na službu**
* Podtyp problému: **Autorizační klíč pro připojení virtuální sítě Azure**
* Předmět: **Žádost o autorizační klíč pro připojení virtuální sítě Azure**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Získání informací o partnerské společnosti z portálu CloudSimple

Chcete-li nastavit připojení, musíte navázat připojení mezi virtuální sítí Azure a prostředí CloudSimple.  V rámci postupu je nutné zadat identifikátor URI okruhu druhé strany a autorizační klíč. Získejte identifikátor URI a autorizační klíč z [portálu CloudSimple](access-cloudsimple-portal.md).  V boční nabídce vyberte **Síť** a pak vyberte **Azure Network Connection**. Nebo vyberte **Účet** v boční nabídce a pak vyberte **síťové připojení Azure**.

Zkopírujte identifikátor URI okruhu rovnocenných dat a pro autorizační klíč pro každou oblast pomocí ikony *kopírování.* Pro každou oblast CloudSimple, kterou chcete připojit:

1. Chcete-li zkopírovat identifikátor URI, klepněte na tlačítko **Kopírovat.** Vložte ji do souboru, kde může být k dispozici pro přidání na portál Azure.  
2. Chcete-li zkopírovat autorizační klíč a vložit jej do souboru, klepněte na tlačítko **Kopírovat.**

Zkopírujte autorizační klíč a identifikátor URI okruhu rovnocenných účastníků, který je ve stavu **K dispozici.**  **Stav použitý** označuje, že klíč již byl použit k vytvoření připojení k virtuální síti.

![Stránka Připojení k virtuální síti](media/virtual-network-connection.png)

Podrobnosti o nastavení virtuální sítě Azure na propojení CloudSimple najdete v [tématu Připojení privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure k privátnímu cloudu](azure-expressroute-connection.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
