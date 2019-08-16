---
title: Připojení virtuální sítě Azure k CloudSimple pomocí ExpressRoute
description: Popisuje, jak získat informace o partnerském vztahu pro připojení mezi virtuální sítí Azure a prostředím CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536596"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Připojení virtuální sítě Azure k CloudSimple pomocí ExpressRoute

Privátní cloudovou síť můžete roztáhnout do vaší virtuální sítě Azure a prostředků Azure. Připojení ExpressRoute umožňuje přístup k prostředkům běžícím v předplatném Azure z vašeho privátního cloudu.

## <a name="request-authorization-key"></a>Požádat o autorizační klíč

Pro připojení ExpressRoute mezi Vaším privátním cloudem a virtuální sítí Azure se vyžaduje autorizační klíč. Pokud chcete získat klíč, zařiďte lístek s <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">podporou</a>.  V žádosti použijte následující informace:

* Typ problému: **Odbornou**
* Předplatné: Vyberte předplatné, ve kterém je nasazená služba CloudSimple * *.
* Služba: **Řešení VMware podle CloudSimple**
* Typ problému: **Žádost o službu**
* Podtyp problému: **Autorizační klíč pro připojení virtuální sítě Azure**
* Předmět: **Požadavek na autorizační klíč pro připojení virtuální sítě Azure**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Získání informací o partnerském vztahu pro virtuální síť Azure a připojení CloudSimple

Pokud chcete nastavit připojení, musíte vytvořit propojení mezi virtuální sítí Azure a prostředím CloudSimple.  V rámci tohoto postupu je nutné dodat identifikátor URI a autorizační klíč partnerského okruhu. Získejte identifikátor URI a autorizační klíč z [portálu CloudSimple](access-cloudsimple-portal.md).  V nabídce na straně vyberte **síť** a pak vyberte **připojení k síti Azure**. Nebo v nabídce na straně vyberte **účet** a pak vyberte **připojení k síti Azure**.

Všimněte si ikon kopírování identifikátoru URI partnerského okruhu a autorizačního klíče pro každou z oblastí. Pro každý privátní cloud, který chcete připojit:

* Kliknutím na **Kopírovat** zkopírujte identifikátor URI. Vložte je do souboru, kde může být k dispozici pro přidání do Azure Portal.  
* Kliknutím na **Kopírovat** zkopírujte autorizační klíč a vložte ho do souboru taky.

![Stránka Virtual Network připojení](media/network-virt-conn-page.png)

Podrobnosti o nastavení propojení Azure Virtual Network na CloudSimple najdete v tématu [připojení vašeho privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute](azure-expressroute-connection.md).
