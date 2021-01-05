---
title: Připojení služby Azure Virtual Network k CloudSimple pomocí řešení ExpressRoute – Azure VMware od CloudSimple
description: Popisuje, jak získat informace o partnerském vztahu pro připojení mezi virtuální sítí Azure a prostředím CloudSimple.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3fedfbe55fd8ea3d2b4cc910df631e40bc74e210
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899061"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Připojení virtuální sítě Azure k CloudSimple pomocí ExpressRoute

Privátní cloudovou síť můžete roztáhnout do vaší virtuální sítě Azure a prostředků Azure. Připojení ExpressRoute umožňuje přístup k prostředkům běžícím v předplatném Azure z vašeho privátního cloudu.

## <a name="request-authorization-key"></a>Požádat o autorizační klíč

Pro připojení ExpressRoute mezi Vaším privátním cloudem a virtuální sítí Azure se vyžaduje autorizační klíč. Pokud chcete získat klíč, zařiďte lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a>.  V žádosti použijte následující informace:

* Typ problému: **technický**
* Předplatné: **Vyberte předplatné, ve kterém je nasazená služba CloudSimple** .
* Služba: **řešení VMware podle CloudSimple**
* Typ problému: **žádost o službu**
* Podtyp problému: **autorizační klíč pro připojení virtuální sítě Azure**
* Subject: **požadavek na autorizační klíč pro připojení virtuální sítě Azure**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Získání informací o partnerském vztahu z portálu CloudSimple

Pokud chcete nastavit připojení, musíte navázat připojení mezi virtuální sítí Azure a prostředím CloudSimple.  V rámci tohoto postupu je nutné dodat identifikátor URI a autorizační klíč partnerského okruhu. Získejte identifikátor URI a autorizační klíč z [portálu CloudSimple](access-cloudsimple-portal.md).  V nabídce na straně vyberte **síť** a pak vyberte **připojení k síti Azure**. Nebo v nabídce na straně vyberte **účet** a pak vyberte **připojení k síti Azure**.

Zkopírujte identifikátor URI partnerského okruhu a pro každý z oblastí použijte ikonu *kopírování* . Pro každou oblast CloudSimple, kterou chcete připojit:

1. Kliknutím na **Kopírovat** zkopírujte identifikátor URI. Vložte je do souboru, kde může být k dispozici pro přidání do Azure Portal.  
2. Kliknutím na **Kopírovat** zkopírujte autorizační klíč a vložte ho do souboru taky.

Zkopírujte autorizační klíč a identifikátor URI partnerského okruhu, který je v **dostupném** stavu.  **Použitý** stav označuje, že klíč již byl použit k vytvoření připojení k virtuální síti.

![Stránka Virtual Network připojení](media/virtual-network-connection.png)

Podrobnosti o nastavení propojení Azure Virtual Network na CloudSimple najdete v tématu [připojení vašeho privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure k privátnímu cloudu](azure-expressroute-connection.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
