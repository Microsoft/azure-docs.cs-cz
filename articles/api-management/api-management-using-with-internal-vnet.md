---
title: Jak používat Azure API Management s interní virtuální sítě | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a nakonfigurovat službu Azure API Management v interní virtuální síti
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: acaf73c2d981761b0bc57cfccbbf6c6a48e5e0c2
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446498"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Pomocí služby Azure API Management k interní virtuální síti
S virtuálními sítěmi Azure Azure API Management můžete spravovat rozhraní API není přístupný na Internetu. Řadu technologií VPN jsou k dispozici při připojování. API Management se dá nasadit v dva hlavní režimy uvnitř virtuální sítě:
* Externí
* Interní

Když API Management se nasadí v režimu interní virtuální síti, jsou viditelné ve virtuální síti, které řídí přístup k pouze všechny koncové body služby (brána, portál pro vývojáře, webu Azure portal, Přímá správa a Git). Žádný z koncových bodů služby je zaregistrovaná na veřejném serveru DNS.

Použití služby API Management v interní režimu, můžete dosáhnout následujících scénářů:

* Ujistěte se, rozhraní API hostované ve vašem privátním datacentru bezpečně je zpřístupnit třetími stranami mimo něj pomocí připojení site-to-site a připojení Azure ExpressRoute VPN.
* Povolte hybridní cloudové scénáře vystavení rozhraní API založená na cloudu a místním rozhraním API prostřednictvím společnou bránu.
* Správa rozhraní API hostovaná v několika geografických umístěních pomocí koncového bodu jednu bránu. 

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li provést postup popsaný v tomto článku, budete potřebovat:

+ **Aktivní předplatné Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Instance služby Azure API Management**. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Vytvoření API Management v interní virtuální síti
Služba API Management v interní virtuální síti je hostovaných za službou interní nástroj pro vyrovnávání zatížení (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Povolit připojení k virtuální síti pomocí webu Azure portal

1. Přejděte k vaší instanci Azure API Management v [webu Azure portal](https://portal.azure.com/).
2. Vyberte **Virtuální síť**.
3. Konfigurace instance API Management k nasazení ve virtuální síti.

    ![Nabídky pro nastavení Azure API Management v interní virtuální síti][api-management-using-internal-vnet-menu]

4. Vyberte **Uložit**.

Po úspěšném nasazení, měli byste vidět interní virtuální IP adresa služby na řídicím panelu.

![Řídicí panel pro správu rozhraní API k interní virtuální síti nakonfigurovat][api-management-internal-vnet-dashboard]

> [!NOTE]
> K dispozici na webu Azure Portal testovací konzole nebude fungovat pro **interní** virtuální sítě nasadili službu, protože adresa Url brány není registrovaný ve veřejném DNS. Je vhodné použít testovací konzole k dispozici na **portál pro vývojáře**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Povolit připojení k virtuální síti pomocí rutin prostředí PowerShell
Můžete také povolit připojení k virtuální síti pomocí rutin prostředí PowerShell.

* Vytvoření služby API Management ve virtuální síti: použijte rutinu [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) k vytvoření služby Azure API Management ve virtuální síti a nakonfigurujte ho na použití typu interní virtuální síti.

* Nasazení ve virtuální síti existující službu API Management: použijte rutinu [aktualizace AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) přesunout existující službu API Management ve virtuální síti a nakonfigurujte ho na interní použití typ virtuální sítě.

## <a name="apim-dns-configuration"></a>Konfigurace služby DNS
Když služba API Management je v režimu externí virtuální síť, DNS spravuje Azure. U režimu interní virtuální síti budete muset spravovat své vlastní směrování.

> [!NOTE]
> Služba API Management nepřijímá požadavky na požadavky přicházející z IP adresy. Pouze reaguje na požadavky na název hostitele, které jsou nakonfigurované na své koncové body služby. Tyto koncové body patří brány, na webu Azure portal a portálu pro vývojáře, koncový bod správy s přímým přístupem a Git.

### <a name="access-on-default-host-names"></a>Přístup k výchozí názvy hostitelů
Při vytváření služby API Management, například s názvem "contoso", jsou ve výchozím nastavení nakonfigurované následující koncové body služby:

   * Brána nebo proxy: contoso.azure-api.net

   * Na webu Azure portal a portálu pro vývojáře: contoso.portal.azure-api.net

   * Koncový bod správy s přímým přístupem: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

Pro přístup k tyto koncové body služby API Management, můžete vytvořit virtuální počítač v podsíti připojené k virtuální síti, ve kterém je nasazená API Management. Za předpokladu, že 10.0.0.5 je interní virtuální IP adresa pro vaši službu, můžete namapovat souboru hostitelů % SystemDrive%\drivers\etc\hosts, následujícím způsobem:

   * 10.0.0.5 contoso.azure-api.net

   * 10.0.0.5 contoso.portal.azure-api.net

   * 10.0.0.5 contoso.management.azure-api.net

   * 10.0.0.5 contoso.scm.azure-api.net

Potom můžete přistupovat všechny koncové body služby z virtuálního počítače, který jste vytvořili. Pokud používáte vlastní server DNS ve virtuální síti, můžete také vytvořit záznamy A DNS a přístup k tyto koncové body z libovolného místa ve virtuální síti. 

### <a name="access-on-custom-domain-names"></a>Přístup k vlastním názvům domén

   1. Pokud nechcete, aby pro přístup ke službě API Management s výchozími názvy hostitelů, můžete nastavit vlastní názvy domén pro všechny vaše koncové body služby jak je znázorněno na následujícím obrázku: 

   ![Nastavení vlastní domény pro službu API Management][api-management-custom-domain-name]

   2. Potom můžete vytvořit záznamy v serveru DNS pro přístup ke koncovým bodům, které jsou přístupné z v rámci vaší virtuální sítě.

## <a name="routing"> </a> Směrování
+ Skupinu s vyrovnáváním zatížení privátní virtuální IP adresu z rozsahu podsítě se měla vyhradit a používat pro přístup ke koncovým bodům služby API Management z virtuální sítě.
+ Skupinu s vyrovnáváním zatížení veřejnou IP adresu (VIP) se vyhradí také poskytnout přístup ke koncovému bodu service management jenom přes port 3443.
+ IP adresu z rozsahu podsítě protokolu IP (DIP) se použije pro přístup k prostředkům v rámci virtuální sítě a veřejné IP adresy (VIP) se použije pro přístup k prostředkům mimo virtuální síť.
+ S vyrovnáváním zatížení veřejných a privátních IP adres najdete v okně Přehled/Essentials na webu Azure Portal.

## <a name="related-content"> </a>Související obsah
Další informace naleznete v následujících článcích:
* [Běžné problémy s konfigurací sítě při nastavování Azure API Management ve virtuální síti][Common network configuration problems]
* [Nejčastější dotazy k virtuální síti](../virtual-network/virtual-networks-faq.md)
* [Vytvoření záznamu ve službě DNS](https://msdn.microsoft.com/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

