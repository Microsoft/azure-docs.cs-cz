---
title: Jak používat Azure API Management s interní virtuální sítě | Microsoft Docs
description: Zjistěte, jak připravit a nakonfigurovat Azure API Management na interní virtuální sítě
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
ms.openlocfilehash: be4aa45c919f579355c6ac6c5682c1537e9cc0cb
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Interní virtuální sítě pomocí služby Azure API Management
S virtuálními sítěmi Azure Azure API Management můžete spravovat rozhraní API není dostupný na Internetu. Počet technologie VPN jsou k dispozici pro připojení. API Management se dá nasadit v dva hlavní režimy uvnitř virtuální sítě:
* Externí
* Interní


Když API Management nasadí v režimu interní virtuální síť, jsou viditelné ve virtuální síti, která můžete řídit přístup ke pouze všechny služby na koncové body (brány, portál pro vývojáře, portálu Azure, přímou správu a Git). Žádné koncové body služby jsou registrované na veřejném serveru DNS.

Použití služby API Management v interní režimu, můžete dosáhnout následujících scénářů:
* Ujistěte se, rozhraní API hostovaných ve vašem privátním datacentru bezpečně přístupné třetími stranami mimo pomocí site-to-site nebo připojení k síti VPN Azure ExpressRoute.
* Povolte hybridní cloudové scénáře vystavení rozhraní API pro cloudové a místní rozhraní API prostřednictvím brány běžné.
* Spravujte vaše rozhraní API hostovaných ve více zeměpisných oblastí pomocí koncový bod jedna brána. 


## <a name="prerequisites"></a>Požadavky

K provedení kroků popsaných v tomto článku, musíte mít:

+ **Aktivní předplatné Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Instance služby Azure API Management**. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Vytvoření API Management v interní virtuální sítě
Služby API Management je interní virtuální síť je hostován za vyrovnávání interní zatížení (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Povolit připojení virtuální sítě pomocí portálu Azure

1. Přejděte k vaší instanci Azure API Management v [portál Azure](https://portal.azure.com/).
2. Vyberte **virtuální síť**.
3. Nakonfigurujte instanci služby API Management k nasazení ve virtuální síti.

    ![Nabídky pro nastavení Azure API Management v interní virtuální sítě][api-management-using-internal-vnet-menu]

4. Vyberte **Uložit**.

Po úspěšné nasazení, měli byste vidět interní virtuální IP adresu služby na řídicím panelu.

![Řídicí panel správy rozhraní API s interní virtuální síť nakonfigurované][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Povolit připojení k virtuální síti pomocí rutin prostředí PowerShell
Můžete také povolit připojení k virtuální síti pomocí rutin prostředí PowerShell.

* Vytvoření služby API Management uvnitř virtuální sítě: použijte rutinu [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) k vytvoření služby Azure API Management uvnitř virtuální sítě a nakonfigurujte ji chcete použít typ interní virtuální síť.

* Nasazení služby API Management existující uvnitř virtuální sítě: použijte rutinu [aktualizace AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) přesunout existující službu API Management uvnitř virtuální sítě a nakonfigurujte ho na použití interní typ virtuální sítě.

## <a name="apim-dns-configuration"></a>Konfigurace služby DNS
Když API Management je v režimu externí virtuální síť, DNS spravuje Azure. Pro režim interní virtuální sítě budete muset spravovat vlastní směrování.

> [!NOTE]
> Služba API Management nepřijímá požadavky na požadavky pocházejících z IP adresy. Pouze reaguje na požadavky na název hostitele, který je nakonfigurovaný na jeho koncové body služby. Tyto koncové body patří brány, portálu Azure a portál pro vývojáře, koncový bod přímé správy a Git.

### <a name="access-on-default-host-names"></a>Přístup na výchozí názvy hostitelů
Při vytváření služby API Management, například s názvem "contoso", jsou ve výchozím nastavení nakonfigurované následující služby na koncové body:

   * Brána nebo proxy server: contoso.azure api.net

   * portál Azure a portál pro vývojáře: contoso.portal.azure api.net

   * Direct management endpoint: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

Pro přístup k tyto koncové body služby API Management, můžete vytvořit virtuální počítač v podsíti, připojený k virtuální síti, ve kterém je nasazený API Management. Za předpokladu, že je interní virtuální IP adresa pro vaši službu 10.0.0.5, můžete namapovat k souboru hostitele, % SystemDrive%\drivers\etc\hosts, následujícím způsobem:

   * 10.0.0.5 contoso.azure-api.net

   * 10.0.0.5 contoso.portal.azure-api.net

   * 10.0.0.5 contoso.management.azure-api.net

   * 10.0.0.5 contoso.scm.azure-api.net

Všechny koncové body služby můžete poté přistoupit z virtuálního počítače, kterou jste vytvořili. Pokud používáte vlastního serveru DNS ve virtuální síti, můžete také vytvořit záznamy A DNS a využít těchto koncových bodů z libovolného místa ve virtuální síti. 

### <a name="access-on-custom-domain-names"></a>Přístup na vlastních názvů domén

   1. Pokud nechcete, aby přístup ke službě API Management s výchozí názvy hostitelů, můžete nastavit vlastní názvy domén pro všechny vaše koncové body služby jak je znázorněno na následujícím obrázku: 

   ![Nastavení vlastní doménu pro API Management][api-management-custom-domain-name]

   2. Potom můžete vytvořit záznamy v serveru DNS pro přístup koncových bodů, které jsou pouze k dispozici v rámci virtuální sítě.

## <a name="routing"> </a> Směrování
+ Skupinu s vyrovnáváním zatížení privátní virtuální IP adresu z rozsahu podsítě vyhrazené se použije pro přístup koncových bodů služby API Management z v rámci virtuální sítě.
+ Skupinu s vyrovnáváním zatížení veřejnou IP adresu (VIP) se také vyhrazena pro poskytování přístupu k koncový bod služby správy pouze přes port 3443.
+ IP adresu z rozsahu podsítě IP (DIP) se použije pro přístup k prostředkům v rámci virtuální sítě a veřejnou IP adresu (VIP) se použije pro přístup k prostředkům mimo síť vnet.
+ S vyrovnáváním zatížení se veřejné a soukromé IP adresy najdete v okně Přehled/Essentials na webu Azure portal.

## <a name="related-content"> </a>Související obsah
Další informace naleznete v následujících článcích:
* [Běžné problémy s konfigurací sítě při nastavení Azure API Management ve virtuální síti][Common network configuration problems]
* [Nejčastější dotazy k virtuální síti](../virtual-network/virtual-networks-faq.md)
* [Vytvoření záznamu DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

