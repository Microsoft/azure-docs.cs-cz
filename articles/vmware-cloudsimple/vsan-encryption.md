---
title: Řešení Azure VMware od CloudSimple – konfigurace šifrování vSAN pro privátní cloud
description: Popisuje, jak nakonfigurovat funkci šifrování softwaru vSAN tak, aby váš CloudSimple Private Cloud mohl pracovat se serverem pro správu klíčů spuštěným ve vaší virtuální síti Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020637"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Konfigurace šifrování vSAN pro cloudový privátní cloud

Funkci šifrování softwaru vSAN můžete nakonfigurovat tak, aby váš CloudSimple Private Cloud mohl pracovat se serverem pro správu klíčů spuštěným ve vaší virtuální síti Azure.

Společnost VMware vyžaduje při použití šifrování vSAN použití externího nástroje pro správu klíčů (KMS) kompatibilního s rozhraním KMIP 1.1. Můžete využít všechny podporované KMS, který je certifikovaný společností VMware a je k dispozici pro Azure.

Tato příručka popisuje, jak používat HyTrust KeyControl KMS spuštěné ve virtuální síti Azure. Podobný přístup lze použít pro jakékoli jiné certifikované řešení KMS třetích stran pro vSAN.

Toto řešení KMS vyžaduje:

* Nainstalujte, nakonfigurujte a spravujte nástroj KMS s certifikací v mware třetí strany ve vaší virtuální síti Azure.
* Poskytněte vlastní licence pro nástroj KMS.
* Konfigurace a správa šifrování vSAN v privátním cloudu pomocí nástroje KMS jiného výrobce spuštěného ve vaší virtuální síti Azure.

## <a name="kms-deployment-scenario"></a>Scénář nasazení služby KMS

Cluster serverů KMS běží ve vaší virtuální síti Azure a je IP dosažitelný z privátního cloudu vCenter přes nakonfigurované připojení Azure ExpressRoute.

![.. Cluster /media/KMS ve virtuální síti Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Proces nasazení má následující kroky:

1. [Ověření, zda jsou splněny požadavky](#verify-prerequisites-are-met)
2. [Portál CloudSimple: Získání informací o partnerskému vztahu ExpressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portál Azure: Připojení virtuální sítě k privátnímu cloudu](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portál Azure: Nasazení clusteru HyTrust KeyControl ve virtuální síti](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Konfigurace serveru KMIP](#hytrust-webui-configure-the-kmip-server)
6. [UI vCenter: Konfigurace šifrování vSAN pro použití clusteru KMS ve virtuální síti Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Ověření splnění požadavků

Před nasazením ověřte následující:

* Vybraný dodavatel, nástroj a verze služby KMS jsou uvedeny v seznamu kompatibility vSAN.
* Vybraný dodavatel podporuje verzi nástroje, který se má spustit v Azure.
* Verze nástroje AZURE je kompatibilní s KMIP 1.1.
* Azure Resource Manager a virtuální síť jsou už vytvořeny.
* CloudSimple Privátní cloud je již vytvořen.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portál CloudSimple: Získání informací o partnerskému vztahu ExpressRoute

Chcete-li pokračovat v instalaci, potřebujete autorizační klíč a identifikátor URI okruhu partnera pro ExpressRoute plus přístup k vašemu předplatnému Azure. Tyto informace jsou k dispozici na stránce Připojení k virtuální síti na portálu CloudSimple. Pokyny najdete [v tématu Nastavení připojení k virtuální síti k privátnímu cloudu](virtual-network-connection.md). Máte-li potíže se získáním informací, otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portál Azure: Připojení virtuální sítě k privátnímu cloudu

1. Vytvořte bránu virtuální sítě pro vaši virtuální síť podle pokynů v [části Konfigurace brány virtuální sítě pro ExpressRoute pomocí portálu Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Propojte virtuální síť s okruhem CloudSimple ExpressRoute podle pokynů v [části Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. K propojení virtuální sítě s okruhem CloudSimple ExpressRoute v Azure použijte informace o okruhu CloudSimple ExpressRoute přijaté v uvítacím e-mailu z CloudSimple.
4. Zadejte autorizační klíč a identifikátor URI okruhu rovnocenných zprostředkovatelů, pojmenujte připojení a klepněte na tlačítko **OK**.

![Poskytnutí identifikátoru URI síťového okruhu CS při vytváření virtuální sítě](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Portál Azure: Nasazení clusteru HyTrust KeyControl ve Správci prostředků Azure ve vaší virtuální síti

Chcete-li nasadit cluster HyTrust KeyControl ve Správci prostředků Azure ve vaší virtuální síti, proveďte následující úkoly. Podrobnosti naleznete v [dokumentaci hytrustu.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. Vytvořte skupinu zabezpečení sítě Azure (nsg-hytrust) se zadanými příchozími pravidly podle pokynů v dokumentaci HyTrust.
2. Vygenerujte pár klíčů SSH v Azure.
3. Nasaďte počáteční uzel KeyControl z image na Azure Marketplace.  Použijte veřejný klíč dvojice klíčů, která byla generována, a vyberte **nsg-hytrust** jako skupinu zabezpečení sítě pro uzel KeyControl.
4. Převeďte privátní IP adresu KeyControl na statickou ADRESU IP.
5. SSH do virtuálního počítačů KeyControl pomocí své veřejné IP adresy a privátníklíč výše uvedené dvojice klíčů.
6. Po zobrazení výzvy v prostředí `No` SSH vyberte, chcete-li nastavit uzel jako počáteční uzel KeyControl.
7. Přidejte další uzly KeyControl opakováním kroků 3-5 tohoto postupu a výběrem `Yes` po zobrazení výzvy k přidání do existujícího clusteru.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Konfigurace serveru KMIP

Přejděte na https://*public-ip*, kde *public-ip* je veřejná IP adresa virtuálního počítačů uzlu KeyControl. Postupujte podle následujících kroků z [dokumentace hytrust .](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. [Konfigurace serveru KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Vytvoření sady certifikátů pro šifrování VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>UI vCenter: Konfigurace šifrování vSAN pro použití clusteru KMS ve virtuální síti Azure

Podle pokynů [hytrustu vytvořte cluster SLUŽBY SPRÁVY a služeb v centru společnosti](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Přidání podrobností o clusteru SLUŽBY SPRÁVY KLÍČŮ v programu vCenter](media/vsan-config01.png)

V aplikaci vCenter přejděte na **možnost Konfigurace > clusteru** a vyberte **možnost Obecné** pro síť vSAN. Povolte šifrování a vyberte cluster SLUŽBY SPRÁVY klíčů, který byl dříve přidán do programu vCenter.

![Povolení šifrování vSAN a konfigurace clusteru SPRÁVY klíčů v programu vCenter](media/vsan-config02.png)

## <a name="references"></a>Odkazy

### <a name="azure"></a>Azure

[Konfigurace brány virtuální sítě pro ExpressRoute pomocí portálu Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Připojení virtuální sítě k okruhu ExpressRoute pomocí portálu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl a Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Konfigurace serveru KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Vytvoření sady certifikátů pro šifrování VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Vytvoření clusteru KMS ve službě vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
