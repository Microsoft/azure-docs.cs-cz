---
title: Připojení virtuální sítě Azure ke službě AVS pomocí ExpressRoute
description: Popisuje, jak získat informace o partnerském vztahu pro připojení mezi virtuální sítí Azure a prostředím služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014398"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Připojení virtuální sítě Azure ke službě AVS pomocí ExpressRoute

Privátní cloudovou síť služby AVS můžete roztáhnout do vaší virtuální sítě Azure a prostředků Azure. Připojení ExpressRoute umožňuje přístup k prostředkům běžícím v předplatném Azure z vašeho privátního cloudu služby AVS.

## <a name="request-authorization-key"></a>Požádat o autorizační klíč

Pro připojení ExpressRoute mezi privátním cloudem služby AVS a službou Azure Virtual Network je vyžadován autorizační klíč. Pokud chcete získat klíč, zařiďte lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a>. V žádosti použijte následující informace:

* Typ problému: **technický**
* Předplatné: **Vyberte předplatné, ve kterém je nasazená služba AVS** .
* Služba: **řešení VMware (AVS)**
* Typ problému: **žádost o službu**
* Podtyp problému: **autorizační klíč pro připojení virtuální sítě Azure**
* Subject: **požadavek na autorizační klíč pro připojení virtuální sítě Azure**

## <a name="get-peering-information-from-avs-portal"></a>Získání informací o partnerském vztahu z portálu pro funkci AVS

Pokud chcete nastavit připojení, musíte navázat spojení mezi virtuální sítí Azure a prostředím služby AVS. V rámci tohoto postupu je nutné dodat identifikátor URI a autorizační klíč partnerského okruhu. Získejte identifikátor URI a autorizační klíč z [portálu AVS](access-cloudsimple-portal.md). V nabídce na straně vyberte **síť** a pak vyberte **připojení k síti Azure**. Nebo v nabídce na straně vyberte **účet** a pak vyberte **připojení k síti Azure**.

Zkopírujte identifikátor URI partnerského okruhu a pro každý z oblastí použijte ikonu *kopírování* . Pro každou oblast služby AVS, kterou chcete připojit:

1. Kliknutím na **Kopírovat** zkopírujte identifikátor URI. Vložte je do souboru, kde může být k dispozici pro přidání do Azure Portal. 
2. Kliknutím na **Kopírovat** zkopírujte autorizační klíč a vložte ho do souboru taky.

Zkopírujte autorizační klíč a identifikátor URI partnerského okruhu, který je v **dostupném** stavu. **Použitý** stav označuje, že klíč již byl použit k vytvoření připojení k virtuální síti.

![Stránka Virtual Network připojení](media/virtual-network-connection.png)

Podrobnosti o nastavení propojení Azure Virtual Network na funkci AVS najdete v tématu [připojení vašeho prostředí privátního cloudu služby AVS ke službě Azure Virtual Network pomocí ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure k privátnímu cloudu služby AVS](azure-expressroute-connection.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
