---
title: Připojení Azure ExpressRoute s Cloudovou infrastrukturou Oracle | Dokumentace Microsoftu
description: Připojení Azure ExpressRoute s FastConnect Oracle cloudové infrastruktury (OCI) umožňující řešení aplikace Oracle cloudu
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: ce1f86f7594692c797aaca5008b211b96ba81fbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453148"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Nastavit přímé propojení mezi Azure a Oracle cloudové infrastruktury  

Vytvoření [integrované prostředí multicloudové](oracle-oci-overview.md) (preview), Microsoft a Oracle nabízí přímé propojení mezi Azure a Oracle cloudu infrastruktury (OCI) prostřednictvím [ExpressRoute](../../../expressroute/expressroute-introduction.md) a [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Prostřednictvím ExpressRoute a FastConnect propojení může docházet k zákazníkům nízkou latenci, vysokou propustnost, privátní přímé připojení mezi dva cloudy.

> [!IMPORTANT]
> Ve fázi preview se připojení mezi Microsoft Azure a OCI. Umožňuje připojení s nízkou latencí mezi Azure a OCI nutné OCI tenantů a předplatného Azure na seznamu povolených pro tuto funkci.

Následující obrázek znázorňuje přehled propojení:

![Připojení k síti cloudu](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Požadavky

* K navázání připojení mezi Azure a OCI, musíte mít aktivní předplatné Azure a aktivní tenantů OCI.

* Připojení je možné pouze pokud je umístění partnerského vztahu Azure ExpressRoute v blízkosti nebo ve stejném umístění partnerského vztahu jako OCI FastConnect. Zobrazit [omezení verze preview](oracle-oci-overview.md#preview-limitations).

* Vaše předplatné Azure musí být povolené pro této funkce ve verzi preview. Kontaktujte zástupce Microsoftu k povolení této funkce v rámci předplatného.

* Vašem tenantovi OCI musí být povolené pro této funkce ve verzi preview. Podrobnosti získáte od zástupce Oracle.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurace přímé připojení mezi FastConnect a ExpressRoute

1. Vytvořte standardní okruh ExpressRoute v rámci předplatného Azure v rámci skupiny prostředků. 
    * Při vytváření ExpressRoute, zvolte **Oracle cloudu FastConnect** jako poskytovatele služeb. Vytvořit okruh ExpressRoute, přečtěte si článek [podrobného průvodce](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Okruh Azure ExpressRoute poskytuje možnosti podrobné šířky pásma, zatímco FastConnect podporuje 1, 2, 5 nebo 10 GB/s. Proto doporučujeme zvolit jednu z těchto možností odpovídající šířky pásma v rámci ExpressRoute.

    ![Vytvoření okruhu ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Poznamenejte si přes ExpressRoute **klíč služby**. Musíte zadat klíč při konfiguraci FastConnect okruh.

    ![Klíč služby ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Bude se vám účtovat poplatky za ExpressRoute ihned poté, co je okruh ExpressRoute zřízený (i v případě **stav poskytovatele** je **Nezřízeno**).

1. Vyčlenit dva privátní adresní prostory IP /30 každé, které se nepřekrývají s virtuální sítí Azure nebo OCI cloud pro virtuální síť adresní prostor IP adres. Jsme vrátíme se k první adresní prostor IP adres jako primární adresní prostor a druhý adresní prostor IP adres jako sekundární adresní prostor. Poznamenejte si adresy, protože je budete potřebovat při konfiguraci FastConnect okruh.
1. Vytvoření brány dynamického směrování (DRG). Po vytvoření okruhu FastConnect ho budete potřebovat. Další informace najdete v tématu [Dynamic Routing Gateway](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) dokumentaci.
1. Vytvoření okruhu FastConnect v rámci vašeho tenanta Oracle. Další informace najdete v tématu [dokumentaci Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * V části Konfigurace FastConnect vyberte **Microsoft Azure: ExpressRoute** jako zprostředkovatel.
    * Vyberte Dynamic Routing Gateway, kterou jste zřídili v předchozím kroku.
    * Vyberte šířku pásma, které se mají zřídit. Pro optimální výkon musí odpovídat šířku pásma šířky pásma, které vybrali při vytváření okruh ExpressRoute.
    * V **klíč služby poskytovatele**, vložte klíč služby ExpressRoute.
    * Použijte první/30 ubírat privátní adresní prostor IP adres v předchozím kroku pro **primární IP adresu BGP** a druhý/30 privátní adresní prostor IP adres pro **sekundární IP protokolu BGP** adresu.
        * Přiřaďte první nedodržíte adresu dvou oblastí pro Oracle BGP IP adresu (primární i sekundární) a druhou adresu zákazníka IP adresu BGP (z hlediska FastConnect). První nedodržíte IP adresa je druhou IP adresu v/30 adresní prostor (první IP adresa je vyhrazená microsoftem).
    * Klikněte na možnost **Vytvořit**.
1. Dokončete propojení FastConnect cloudové virtuální sítě v rámci vašeho tenanta Oracle prostřednictvím Dynamic Routing Gateway, použití směrovací tabulky.
1. Přejděte do Azure a ujistěte se, že **stav poskytovatele** pro ExpressRoute okruh se změnila na **zřízená** a partnerský vztah typu **Azure privátní** bylo zřízené. Toto je nezbytné pro následující kroky.

    ![Stav poskytovatele služby ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klikněte na **Azure privátní** partnerský vztah. Uvidíte, že podrobností partnerského vztahu jsou automaticky nakonfigurované na základě informací, které jste zadali při nastavení si váš okruh FastConnect.

    ![Nastavení soukromého partnerského vztahu](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Propojení virtuální sítě pro ExpressRoute

1. Vytvoření virtuální sítě a brány virtuální sítě, pokud jste tak již neučinili. Podrobnosti najdete v tématu [podrobného průvodce](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Nastavení připojení mezi bránou virtuální sítě a váš okruh ExpressRoute pomocí provádí [Terraformu skript](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) nebo spuštěním příkazu Powershellu [konfigurace ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po dokončení konfigurace sítě, můžete ověřit správnost konfigurace po kliknutí na **získat záznamy ARP** a **Get směrovací tabulka** pod oknem ExpressRoute soukromého partnerského vztahu v na webu Azure portal.

## <a name="automation"></a>Automation

Společnost Microsoft vytvořila skripty Terraformu umožňuje automatizované nasazení síťové propojení. Terraform skripty muset provádět ověřování pomocí Azure před spuštěním, protože vyžadují odpovídající oprávnění u předplatného Azure. Je možné provádět ověřování pomocí [instanční objekt Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) nebo pomocí rozhraní příkazového řádku Azure. Další informace najdete v tématu [Terraformu dokumentaci](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Terraform skripty a související dokumentace k nasazení inter-connect najdete v tomto [úložiště GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorování

Instalovat agenty na oba cloudy, můžete využít Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) ke sledování výkonu sítě začátku do konce. NPM pomáhá snadno identifikovat problémy se sítí a pomáhá eliminovat konflikty je.

## <a name="delete-the-interconnect-link"></a>Odstranit odkaz propojení

Pokud chcete odstranit propojení, musí být zadán následující kroky v daném konkrétním pořadí. Pokud tak neučiníte způsobí okruh ExpressRoute "stavu selhání".

1. Odstraňte připojení ExpressRoute. Odstranit připojení kliknutím **odstranit** ikonu na stránce pro připojení. Další informace najdete v tématu [dokumentace ke službě ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Odstraňte Oracle FastConnect od Oracle Cloud Console.
1. Jakmile okruh Oracle FastConnect byla odstraněna, můžete odstranit okruh Azure ExpressRoute.

V tomto okamžiku odstranění a zrušení zřizování procesu je dokončena.

## <a name="next-steps"></a>Další postup

* Další informace o připojení cloudu mezi OCI a Azure, najdete v článku [dokumentaci Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Použití [Terraformu skripty](https://aka.ms/azureociinterconnecttf) nasazení infrastruktury pro cílové aplikace Oracle v Azure a nakonfigurujte síťové propojení. 