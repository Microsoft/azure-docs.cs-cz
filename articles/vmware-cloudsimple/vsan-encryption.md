---
title: Řešení Azure VMware (AVS) – konfigurace šifrování síti vSAN pro privátní cloud služby AVS
description: Popisuje, jak nakonfigurovat funkci síti vSAN software Encryption, aby váš privátní cloud služby AVS mohl pracovat s klíčovým management server spuštěným ve službě Azure Virtual Network.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 056c05701a3915610fb17a7e8c04feb743e38286
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020637"
---
# <a name="configure-vsan-encryption-for-avs-private-cloud"></a>Konfigurace šifrování síti vSAN pro privátní cloud služby AVS

Můžete nakonfigurovat funkci síti vSAN software Encryption, aby váš privátní cloud služby AVS mohl pracovat s klíčovým management server spuštěným ve službě Azure Virtual Network.

Při použití šifrování síti vSAN vyžaduje VMware použití externího nástroje management server Key 1,1 KMIP (KMS) třetí strany. Můžete využít veškerou podporovanou službu správy klíčů, která je certifikována VMware a je dostupná pro Azure.

V této příručce se dozvíte, jak používat službu správy klíčů HyTrust, která běží ve službě Azure Virtual Network. Podobný přístup lze použít pro jakékoli jiné certifikované řešení služby správy klíčů třetí strany pro síti vSAN.

Toto řešení služby správy klíčů vyžaduje:

* Instalace, konfigurace a Správa nástroje pro správu služby správy klíčů, který je certifikovaný VMware, ve službě Azure Virtual Network.
* Poskytněte vlastní licence pro nástroj služby správy klíčů.
* Nakonfigurujte a spravujte šifrování síti vSAN v privátním cloudu služby AVS pomocí nástroje služby správy klíčů od jiného výrobce, který běží ve službě Azure Virtual Network.

## <a name="kms-deployment-scenario"></a>Scénář nasazení služby správy klíčů

Cluster serverů služby správy klíčů běží ve vaší virtuální síti Azure a IP adresa je dostupná z privátního cloudu služby AVS přes nakonfigurované připojení Azure ExpressRoute.

![.. cluster/media/KMS ve službě Azure Virtual Network](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Proces nasazení má následující kroky:

1. [Ověření splnění požadavků](#verify-prerequisites-are-met)
2. [Portál pro funkci AVS: získání informací o partnerském vztahu ExpressRoute](#avs-portal-obtain-expressroute-peering-information)
3. [Azure Portal: připojení virtuální sítě k privátnímu cloudu služby AVS](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
4. [Azure Portal: nasazení clusteru HyTrust Control ve vaší virtuální síti](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Konfigurace serveru KMIP](#hytrust-webui-configure-the-kmip-server)
6. [uživatelské rozhraní vCenter: Konfigurace šifrování síti vSAN pro použití clusteru služby správy klíčů ve službě Azure Virtual Network](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Ověřte splnění požadavků.

Před nasazením ověřte následující:

* Vybraný dodavatel, nástroj a verze služby správy klíčů se nachází v seznamu kompatibilního s síti vSAN.
* Vybraný dodavatel podporuje verzi nástroje, která se má spustit v Azure.
* Verze nástroje služby správy klíčů pro Azure je kompatibilní s KMIP 1,1.
* Azure Resource Manager a virtuální síť jsou již vytvořeny.
* Privátní cloud pro funkci AVS je již vytvořen.

### <a name="avs-portal-obtain-expressroute-peering-information"></a>Portál pro funkci AVS: získání informací o partnerském vztahu ExpressRoute

Abyste mohli pokračovat v instalaci, budete potřebovat autorizační klíč a identifikátor URI partnerského okruhu pro ExpressRoute plus přístup k vašemu předplatnému Azure. Tyto informace jsou k dispozici na stránce Virtual Network připojení na portálu AVS. Pokyny najdete v tématu [nastavení připojení k virtuální síti k privátnímu cloudu služby AVS](virtual-network-connection.md). Pokud máte potíže s získáním informací, otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-the-avs-private-cloud"></a>Azure Portal: připojení virtuální sítě k privátnímu cloudu služby AVS

1. Vytvořte bránu virtuální sítě pro virtuální síť podle pokynů v tématu [Konfigurace brány virtuální sítě pro ExpressRoute pomocí Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Podle pokynů v tématu [připojení virtuální sítě k okruhu ExpressRoute pomocí portálu připojte](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)virtuální síť k okruhu EXPRESSROUTE služby AVS.
3. Pomocí informací o okruhu AVS ExpressRoute přijatých v uvítacím e-mailu ze služby AVS můžete propojit vaši virtuální síť s okruhem služby AVS ExpressRoute v Azure.
4. Zadejte autorizační klíč a identifikátor URI partnerského okruhu, zadejte název připojení a klikněte na **OK**.

![Při vytváření virtuální sítě zadat identifikátor URI partnerského okruhu CS](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure Portal: nasazení clusteru HyTrust Control v Azure Resource Manager ve vaší virtuální síti

K nasazení clusteru HyTrust Control v Azure Resource Manager ve vaší virtuální síti proveďte následující úlohy. Podrobnosti najdete v [dokumentaci k HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) .

1. Podle pokynů v dokumentaci HyTrust vytvořte skupinu zabezpečení sítě Azure (NSG-hytrust) se zadanými příchozími pravidly.
2. Vygenerujte pár klíčů SSH v Azure.
3. Nasaďte počáteční uzel ovládacího prvku z obrázku v Azure Marketplace. Použijte veřejný klíč páru klíčů, který se vygeneroval, a jako skupinu zabezpečení sítě pro uzel ovládacího prvku vyberte **NSG-hytrust** .
4. Převeďte soukromou IP adresu ovládacího prvku na statickou IP adresu.
5. SSH k virtuálnímu počítači s ovládacím prvkem klíčů pomocí jeho veřejné IP adresy a privátního klíče dřív zmíněného páru klíčů.
6. Po zobrazení výzvy v prostředí SSH vyberte `No`, aby se uzel nastavil jako počáteční uzel ovládacího prvku.
7. Přidejte další uzly řídicích prvků zopakováním kroků 3-5 tohoto postupu a výběrem `Yes` po zobrazení výzvy k přidání do existujícího clusteru.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Konfigurace serveru KMIP

Přejít na https://*Public-IP*, kde *Public-IP* je veřejná IP adresa virtuálního počítače uzlu ovládacího prvku. Postupujte podle těchto kroků v [dokumentaci k HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Konfigurace serveru KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Vytvoření sady certifikátů pro šifrování VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>uživatelské rozhraní vCenter: Konfigurace šifrování síti vSAN pro použití clusteru služby správy klíčů ve službě Azure Virtual Network

Postupujte podle pokynů HyTrust a [vytvořte cluster služby správy klíčů v vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Přidání podrobností o clusteru služby správy klíčů v vCenter](media/vsan-config01.png)

V vCenter přejít na **Cluster > nakonfigurovat** a vyberte **obecnou** možnost pro síti vSAN. Povolte šifrování a vyberte cluster služby správy klíčů, který jste dříve přidali do vCenter.

![Povolení šifrování síti vSAN a konfigurace clusteru služby správy klíčů v vCenter](media/vsan-config02.png)

## <a name="references"></a>Odkazy

### <a name="azure"></a>Azure

[Konfigurace brány virtuální sítě pro ExpressRoute pomocí Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl a Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Konfigurace serveru KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Vytvoření sady certifikátů pro šifrování VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Vytvoření clusteru služby správy klíčů v vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
