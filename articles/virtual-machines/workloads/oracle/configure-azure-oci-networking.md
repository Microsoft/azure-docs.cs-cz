---
title: Připojení Azure ExpressRoute pomocí cloudové infrastruktury Oracle | Microsoft Docs
description: Připojení Azure ExpressRoute pomocí Oracle Cloud Infrastructure (OCI) FastConnect pro povolení mezicloudových řešení Oracle pro aplikace
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: rogardle
ms.openlocfilehash: 5bb26a21317401ddbd0d9b8f8a9a501c78153842
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776574"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Nastavení přímého vzájemného propojení mezi cloudovou infrastrukturou Azure a Oracle  

K vytvoření [integrovaného prostředí pro více cloudů](oracle-oci-overview.md)Microsoft a Oracle nabízí přímé propojení mezi Azure a Oracle cloudovou infrastrukturou (OCI) prostřednictvím [ExpressRoute](../../../expressroute/expressroute-introduction.md) a [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Prostřednictvím propojení ExpressRoute a FastConnect můžou zákazníci zaznamenat nízkou latenci, vysokou propustnost a privátní přímé připojení mezi dvěma cloudy.

> [!IMPORTANT]
> Oracle tyto aplikace osvědčí, aby běžely v Azure při použití řešení Cloud Interconnect Azure/Oracle od května 2020.
> * Sada E-Business
> * ŘEŠENÍ JD Edwards EnterpriseOne
> * PeopleSoft spouštěných místně
> * Maloobchodní aplikace Oracle
> * Hyperion finanční správa Oracle

Následující obrázek ukazuje podrobný přehled propojení:

![Připojení k síti mezi cloudy](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Požadavky

* K navázání připojení mezi Azure a OCI musíte mít aktivní předplatné Azure a aktivní nájem architektury OCI.

* Možnost připojení je možná jenom v případě, že umístění partnerského vztahu Azure ExpressRoute je v blízkosti nebo ve stejném umístění partnerského vztahu jako rozhraní OCI FastConnect. Podívejte se na téma [dostupnost oblasti](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurace přímého připojení mezi ExpressRoute a FastConnect

1. Vytvořte ve svém předplatném Azure standardní okruh ExpressRoute v rámci skupiny prostředků. 
    * Při vytváření ExpressRoute vyberte **Oracle Cloud FastConnect** jako poskytovatele služeb. Pokud chcete vytvořit okruh ExpressRoute, přečtěte si [podrobný průvodce](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Okruh Azure ExpressRoute poskytuje podrobné možnosti šířky pásma, zatímco FastConnect podporuje 1, 2, 5 nebo 10 GB/s. Proto se doporučuje zvolit jednu z těchto možností pro porovnání šířky pásma v rámci ExpressRoute.

    ![Vytvořit okruh ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Poznamenejte si **klíč služby**ExpressRoute. Klíč je potřeba zadat při konfiguraci okruhu FastConnect.

    ![Klíč služby ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Poplatky za ExpressRoute se vám budou účtovat ihned po zřízení okruhu ExpressRoute (i v případě, že se **nezřizuje** **stav poskytovatele** ).

1. Vyčlenit dvě privátní adresní prostory IP adres/30, které se nepřekrývají s adresním prostorem IP adres sítě virtuálních cloudů Azure Virtual Network nebo OCI. Na první adresní prostor IP adres se odkazuje jako na primární adresní prostor a druhý adresní prostor IP adres jako na sekundární adresní prostor. Poznamenejte si adresy, které potřebujete při konfiguraci okruhu FastConnect.
1. Vytvořte bránu dynamického směrování (DRG). Budete ho potřebovat při vytváření okruhu FastConnect. Další informace najdete v dokumentaci k [bráně dynamického směrování](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) .
1. V tenantovi Oracle vytvořte okruh FastConnect. Další informace najdete v [dokumentaci Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * V části Konfigurace FastConnect jako zprostředkovatel vyberte **Microsoft Azure: ExpressRoute** .
    * Vyberte bránu dynamického směrování, kterou jste zřídili v předchozím kroku.
    * Vyberte šířku pásma, která se má zřídit. Pro zajištění optimálního výkonu musí šířka pásma odpovídat šířce pásma vybrané při vytváření okruhu ExpressRoute.
    * Do pole **klíč služby poskytovatele**vložte klíč služby ExpressRoute.
    * Použijte první/30 privátního prostoru IP adres Carved v předchozím kroku pro **primární IP adresu protokolu BGP** a druhý/30 privátní adresní prostor IP adres pro **sekundární IP adresu protokolu BGP** .
        * Přiřaďte první IP adresu dvou rozsahů pro IP adresu protokolu BGP Oracle (primární a sekundární) a druhou adresu IP adrese protokolu BGP zákazníka (z perspektivy FastConnect). První dostupná IP adresa je druhá IP adresa v adresním prostoru/30 (první IP adresa je vyhrazená Microsoftem).
    * Klikněte na **Vytvořit**.
1. Dokončete propojení FastConnect s virtuální cloudovou sítí v rámci vašeho tenanta Oracle přes bránu dynamického směrování pomocí směrovací tabulky.
1. Přejděte do Azure a ujistěte se, že se **stav poskytovatele** pro váš okruh ExpressRoute změnil na **zřízený** a že se zřídil partnerský vztah typu **Private Azure** . Tento postup je nezbytný pro následující kroky.

    ![Stav poskytovatele ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klikněte na soukromý partnerský vztah **Azure** . Na základě informací, které jste zadali při nastavování okruhu FastConnect, uvidíte podrobnosti partnerského vztahu automaticky nastavené.

    ![Nastavení privátního partnerského vztahu](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Připojení virtuální sítě k ExpressRoute

1. Vytvořte virtuální síť a bránu virtuální sítě, pokud jste to ještě neudělali. Podrobnosti najdete v [podrobné příručce](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Nastavte připojení mezi bránou virtuální sítě a okruhem ExpressRoute spuštěním [skriptu terraformu](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) nebo spuštěním příkazu PowerShellu pro [konfiguraci ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po dokončení konfigurace sítě můžete ověřit platnost konfigurace kliknutím na **získat záznamy ARP** a **získat tabulku směrování** pod oknem privátní partnerský vztah ExpressRoute v Azure Portal.

## <a name="automation"></a>Automation

Společnost Microsoft vytvořila skripty Terraformu, které umožňují automatizované nasazení síťového propojení. Skripty Terraformu se musí před spuštěním ověřit u Azure, protože vyžadují adekvátní oprávnění k předplatnému Azure. Ověřování se dá provádět pomocí [Azure Active Directory instančního objektu](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) nebo pomocí Azure CLI. Další informace najdete v [dokumentaci k terraformu](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

V tomto [úložišti GitHubu](https://aka.ms/azureociinterconnecttf)najdete skripty terraformu a související dokumentace k nasazení připojení mezi lokalitami.

## <a name="monitoring"></a>Monitorování

Instalace agentů v cloudech vám umožní využít Azure [Network Performance Monitor (npm)](../../../expressroute/how-to-npm.md) a monitorovat výkon komplexní sítě. NPM pomáhá snadno identifikovat problémy se sítí a pomáhá je eliminovat.

## <a name="delete-the-interconnect-link"></a>Odstranit odkaz propojení

Aby bylo možné propojení odstranit, musí být v zadaném pořadí dodrženy následující kroky. K tomuto selhání dojde v důsledku neúspěšného okruhu ExpressRouteho stavu.

1. Odstraňte připojení ExpressRoute. Odstraňte připojení kliknutím na ikonu **Odstranit** na stránce pro vaše připojení. Další informace najdete v [dokumentaci k ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources).
1. Odstraňte Oracle FastConnect z cloudové konzoly Oracle.
1. Po odstranění okruhu Oracle FastConnect můžete odstranit okruh Azure ExpressRoute.

V tuto chvíli je proces odstranění a zrušení zřízení dokončený.

## <a name="next-steps"></a>Další kroky

* Další informace o propojení mezi platformami OCI a Azure najdete v [dokumentaci Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Pomocí [skriptů terraformu](https://aka.ms/azureociinterconnecttf) nasaďte infrastrukturu pro cílové aplikace Oracle přes Azure a nakonfigurujte síťové propojení. 
