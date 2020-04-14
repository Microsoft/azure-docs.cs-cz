---
title: Propojení služby Azure ExpressRoute s infrastrukturou Oracle Cloud | Dokumenty společnosti Microsoft
description: Propojte Azure ExpressRoute s řešením Oracle Cloud Infrastructure (OCI) FastConnect a povolte řešení aplikací Oracle napříč cloudy
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: d85c0fc8986adfa00559eab1c49a79daacdeb33f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263178"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Nastavení přímého propojení mezi Azure a infrastrukturou Oracle Cloud Infrastructure  

Pro vytvoření [integrovaného prostředí pro více cloudů](oracle-oci-overview.md)nabízejí společnosti Microsoft a Oracle přímé propojení mezi Azure a Oracle Cloud Infrastructure (OCI) prostřednictvím [aplikací ExpressRoute](../../../expressroute/expressroute-introduction.md) a [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Prostřednictvím propojení ExpressRoute a FastConnect mohou zákazníci zaznamenat nízké latence, vysokou propustnost, soukromé přímé připojení mezi těmito dvěma cloudy.

> [!IMPORTANT]
> Společnost Oracle bude tyto aplikace certifikovat tak, aby se v Azure spouštěla do května 2020 při používání řešení Propojení Azure / Oracle Cloud.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * Peoplesoft
> * Maloobchodní aplikace Oracle
> * Oracle Hyperion Financial Management

Následující obrázek znázorňuje přehled na vysoké úrovni o propojení:

![Síťové připojení mezi cloudy](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Požadavky

* Chcete-li vytvořit připojení mezi Azure a OCI, musíte mít aktivní předplatné Azure a aktivní nájem OCI.

* Připojení je možné jenom v případě, že umístění partnerského vztahu Azure ExpressRoute je v blízkosti nebo ve stejném umístění partnerského vztahu jako OCI FastConnect. Viz [Dostupnost oblasti](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurace přímého připojení mezi technologiemi ExpressRoute a FastConnect

1. Vytvořte standardní okruh ExpressRoute v předplatném Azure v rámci skupiny prostředků. 
    * Při vytváření ExpressRoute zvolte jako poskytovatele služeb **službu Oracle Cloud FastConnect.** Chcete-li vytvořit okruh ExpressRoute, podívejte se na [podrobný návod](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Okruh Azure ExpressRoute poskytuje podrobné možnosti šířky pásma, zatímco FastConnect podporuje 1, 2, 5 nebo 10 Gb/s. Proto se doporučuje zvolit jednu z těchto odpovídajících možností šířky pásma v části ExpressRoute.

    ![Vytvořit okruh ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Poznamenejte si **klíč služby**ExpressRoute Service . Při konfiguraci okruhu FastConnect je třeba zadat klíč.

    ![Klíč služby ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Poplatky za ExpressRoute se vám budou účtovat, jakmile se zřídí okruh ExpressRoute (i když není **zřízen** **stav poskytovatele).**

1. Vyřízněte dva privátní IP adresní prostory o částce /30, které se nepřekrývají s vaší virtuální sítí Azure nebo s IP adresním prostorem virtuální cloudové sítě OCI. První adresní prostor IP budeme označovat jako primární adresní prostor a druhý adresní prostor IP jako sekundární adresní prostor. Poznamenejte si adresy, které potřebujete při konfiguraci okruhu FastConnect.
1. Vytvořte dynamickou směrovací bránu (DRG). Budete to potřebovat při vytváření okruhu FastConnect. Další informace naleznete v dokumentaci [k dynamické bráně směrování.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Vytvořte okruh FastConnect pod tenantem Oracle. Další informace naleznete v [dokumentaci](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)k řešení Oracle .
  
    * V části Konfigurace FastConnect vyberte **jako zprostředkovatele Microsoft Azure: ExpressRoute.**
    * Vyberte dynamickou bránu směrování, kterou jste zříditi v předchozím kroku.
    * Vyberte šířku pásma, která má být zřízena. Pro optimální výkon musí šířka pásma odpovídat šířce pásma vybrané při vytváření okruhu ExpressRoute.
    * V **klíči služby zprostředkovatele**vložte klíč služby ExpressRoute.
    * Použijte první /30 privátní IP adresní prostor vytesaný v předchozím kroku pro **primární adresu IP protokolu BGP** a druhý privátní adresní prostor /30 pro sekundární adresu IP protokolu **BGP.**
        * Přiřaďte první použitelnou adresu obou rozsahů pro adresu IP protokolu Oracle BGP (primární a sekundární) a druhou adresu zákazníkovi ip adresu BGP (z hlediska rychlého připojení). První použitelná adresa IP je druhá adresa IP v adresním prostoru /30 (první adresu IP je rezervována společností Microsoft).
    * Klikněte na **Vytvořit**.
1. Dokončete propojení fastconnectu s virtuální cloudovou sítí pod klientem Oracle prostřednictvím dynamické brány směrování pomocí směrovací tabulky.
1. Přejděte do Azure a ujistěte se, že **stav zprostředkovatele** pro okruh ExpressRoute se změnil na **Zřízeno** a že byl zřízen partnerský vztah typu **Azure private.** Toto je předpokladem pro následující kroky.

    ![Stav zprostředkovatele ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klikněte na **privátní** partnerský vztah Azure. Podrobnosti partnerského vztahu byly automaticky nakonfigurovány na základě informací, které jste zadali při nastavování okruhu FastConnect.

    ![Nastavení soukromého partnerského vztahu](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Připojení virtuální sítě k ExpressRoute

1. Pokud jste tak ještě neučinili, vytvořte virtuální síť a bránu virtuální sítě. Podrobnosti naleznete v [podrobnépříručce](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Nastavte připojení mezi bránou virtuální sítě a okruhem ExpressRoute spuštěním [skriptu Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) nebo spuštěním příkazu PowerShell [pro konfiguraci expressroute fastpath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po dokončení konfigurace sítě můžete ověřit platnost konfigurace kliknutím na **získat záznamy ARP** a **získat trasovací tabulku** pod okno soukromého partnerského vztahu ExpressRoute na webu Azure Portal.

## <a name="automation"></a>Automation

Společnost Microsoft vytvořila skripty Terraform, které umožňují automatické nasazení síťového propojení. Skripty Terraform je třeba ověřit pomocí Azure před spuštěním, protože vyžadují odpovídající oprávnění k předplatnému Azure. Ověřování lze provést pomocí [objektu zabezpečení služby Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) nebo pomocí rozhraní příkazového příkazu Azure. Další informace naleznete v [dokumentaci terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Skripty Terraform a související dokumentaci k nasazení propojení lze nalézt v tomto [úložišti GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorování

Instalace agentů v obou cloudech, můžete využít Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) ke sledování výkonu sítě koncového konce. NPM vám pomůže snadno identifikovat problémy se sítí a pomáhá je eliminovat.

## <a name="delete-the-interconnect-link"></a>Odstranění propojení

Chcete-li odstranit propojení, je třeba dodržovat následující kroky v uvedeném pořadí. Pokud tak neučiníte, bude mít za následek "selhání stavu" ExpressRoute okruhu.

1. Odstraňte připojení ExpressRoute. Smažte připojení kliknutím na ikonu **Odstranit** na stránce připojení. Další informace naleznete v [dokumentaci ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Odstraňte řešení Oracle FastConnect ze služby Oracle Cloud Console.
1. Po odstranění okruhu Oracle FastConnect můžete odstranit okruh Azure ExpressRoute.

V tomto okamžiku odstranění a zrušení zřízení procesu je dokončena.

## <a name="next-steps"></a>Další kroky

* Další informace o propojení mezi cloudy mezi OCI a Azure najdete v [dokumentaci k oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Pomocí [skriptů Terraform](https://aka.ms/azureociinterconnecttf) nasazujte infrastrukturu pro cílené aplikace Oracle přes Azure a nakonfigurujte propojení sítě. 
