---
title: Použití Azure API Management s interními virtuálními sítěmi
titleSuffix: Azure API Management
description: Přečtěte si, jak nastavit a nakonfigurovat Azure API Management v interní virtuální síti.
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565950"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Použití služby Azure API Management s interní virtuální sítí
S Azure Virtual Networks může Azure API Management spravovat rozhraní API, která nejsou přístupná na internetu. K vytvoření připojení je k dispozici řada technologií sítě VPN. API Management lze nasadit ve dvou hlavních režimech v rámci virtuální sítě:
* Externí
* Interní

Když API Management nasadíte do režimu interní virtuální sítě, všechny koncové body služby (Brána proxy serveru, portál pro vývojáře, přímá správa a Git) se zobrazí jenom ve virtuální síti, ke které řízení přístupu přistupuje. Žádný z koncových bodů služby není registrovaný na veřejném serveru DNS.

> [!NOTE]
> Vzhledem k tomu, že pro koncové body služby nejsou k dispozici žádné záznamy DNS, tyto koncové body nebudou dostupné, dokud není pro virtuální síť [nakonfigurována služba DNS](#apim-dns-configuration) .

Pomocí API Management v interním režimu můžete dosáhnout těchto scénářů:

* Pomocí připojení VPN typu Site-to-site nebo Azure ExpressRoute můžete nastavit, aby se rozhraní API hostovaná v privátním datacentru používala k zabezpečenému přístupu třetích stran mimo ni.
* Umožněte hybridní cloudové scénáře tím, že vystavíte cloudová rozhraní API a místní rozhraní API prostřednictvím běžné brány.
* Spravujte rozhraní API hostovaná v různých geografických umístěních pomocí jednoho koncového bodu brány.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Předpoklady

K provedení kroků popsaných v tomto článku musíte mít:

+ **Aktivní předplatné Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Instance Azure API Management**. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Když je ve virtuální síti nasazená služba API Management, použije se [seznam portů](./api-management-using-with-vnet.md#required-ports) , který je potřeba otevřít. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Vytvoření API Management v interní virtuální síti
Služba API Management v interní virtuální síti je hostovaná za [interním nástrojem pro vyrovnávání zatížení (Classic)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Toto je jediná dostupná možnost a nedá se změnit.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Povolení připojení k virtuální síti pomocí Azure Portal

1. V [Azure Portal](https://portal.azure.com/)přejděte do instance služby Azure API Management.
2. Vyberte **virtuální síť**.
3. Nakonfigurujte instanci API Management, která se má nasadit v rámci virtuální sítě.

    ![Nabídka pro nastavení API Management Azure v interní virtuální síti][api-management-using-internal-vnet-menu]

4. Vyberte **Uložit**.

Po úspěšném nasazení by se v okně Přehled měla zobrazit **privátní** virtuální IP adresa a **Veřejná** virtuální ip adresa vaší služby API Management. **Privátní** virtuální IP adresa je IP adresa s vyrovnáváním zatížení z API Management delegované podsítě, přes kterou `gateway` je `portal` `management` `scm` možné přistupovat k koncovým bodům, a. **Veřejná** virtuální IP adresa se používá **jenom** pro přenosy řídicích objektů do `management` koncového bodu přes port 3443 a je možné ji uzamknout na [ApiManagement][ServiceTags] servicetag.

![Řídicí panel API Management s nakonfigurovanou interní virtuální sítí][api-management-internal-vnet-dashboard]

> [!NOTE]
> Konzola testu dostupná na portálu Azure Portal nebude fungovat pro **interní** nasazenou službu virtuální sítě, protože adresa URL brány není zaregistrovaná na veřejném serveru DNS. Místo toho byste měli použít konzolu test, která je k dispozici na **portálu pro vývojáře**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Nasazení API Management do Virtual Network

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Připojení k virtuální síti můžete taky povolit pomocí rutin PowerShellu.

* Vytvoření služby API Management ve virtuální síti: pomocí rutiny [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) vytvoříte službu Azure API Management v rámci virtuální sítě a nakonfigurujete ji tak, aby používala interní typ virtuální sítě.

* Aktualizace stávajícího nasazení API Management služby ve virtuální síti: pomocí rutiny [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) můžete přesunout existující službu API Management v rámci virtuální sítě a nakonfigurovat ji tak, aby používala interní typ virtuální sítě.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Konfigurace DNS
Když je API Management v režimu externí virtuální sítě, služba DNS se spravuje pomocí Azure. V případě režimu interní virtuální sítě musíte spravovat vlastní DNS. Doporučuje se nakonfigurovat Azure DNS privátní zóna a jejím propojením s API Management službou Virtual Network.  Kliknutím [sem](../dns/private-dns-getstarted-portal.md) se dozvíte, jak nastavit privátní zónu v Azure DNS.

> [!NOTE]
> Služba API Management neposlouchá požadavky přicházející z IP adres. Reaguje jenom na žádosti na název hostitele nakonfigurované na svých koncových bodech služby. Mezi tyto koncové body patří brána, Azure Portal a portál pro vývojáře, přímý koncový bod správy a git.

### <a name="access-on-default-host-names"></a>Přístup k výchozím názvům hostitelů
Když vytvoříte službu API Management s názvem "contosointernalvnet", například následující koncové body služby jsou nakonfigurovány ve výchozím nastavení:

   * Brána nebo proxy: contosointernalvnet.azure-api.net

   * Portál pro vývojáře: contosointernalvnet.portal.azure-api.net

   * Nový portál pro vývojáře: contosointernalvnet.developer.azure-api.net

   * Přímý řídicí koncový bod: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Chcete-li získat přístup k těmto koncovým bodům služby API Management, můžete vytvořit virtuální počítač v podsíti připojené k virtuální síti, ve které API Management nasazeny. Za předpokladu, že interní virtuální IP adresa pro vaši službu je 10.1.0.5, můžete namapovat soubor hostitelů%SystemDrive%\drivers\etc\hosts následujícím způsobem:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Pak můžete přistupovat ke všem koncovým bodům služby z virtuálního počítače, který jste vytvořili.
Pokud ve virtuální síti používáte vlastní server DNS, můžete také vytvořit záznamy DNS a přistupovat k nim z libovolného místa ve vaší virtuální síti.

### <a name="access-on-custom-domain-names"></a>Přístup k vlastním názvům domén

1. Pokud nechcete používat službu API Management s výchozími názvy hostitelů, můžete nastavit vlastní názvy domén pro všechny vaše koncové body služby, jak je znázorněno na následujícím obrázku:

   ![Nastavení vlastní domény pro API Management][api-management-custom-domain-name]

2. Pak můžete vytvořit záznamy na serveru DNS pro přístup k koncovým bodům, které jsou přístupné jenom z vaší virtuální sítě.

## <a name="routing"></a><a name="routing"></a> Směrování

* *Privátní* virtuální IP adresa s vyrovnáváním zatížení z rozsahu podsítě bude vyhrazená a bude se používat pro přístup k koncovým bodům služby API Management v rámci virtuální sítě. Tuto *privátní* IP adresu najdete v okně Přehled pro službu v Azure Portal. Tato adresa musí být zaregistrovaná u serverů DNS, které používá virtuální síť.
* *Veřejná* IP adresa (VIP) s vyrovnáváním zatížení bude také vyhrazena pro poskytování přístupu ke koncovému bodu služby správy přes port 3443. Tuto *veřejnou* IP adresu najdete v okně Přehled pro službu v Azure Portal. *Veřejná* IP adresa se používá jenom pro provoz řídicích rovin na `management` koncový bod přes port 3443 a může být zamčená na [ApiManagement][ServiceTags] servicetag.
* IP adresy z rozsahu IP adres podsítě (DIP) se přiřadí ke každému virtuálnímu počítači ve službě a budou se používat pro přístup k prostředkům v rámci virtuální sítě. Veřejná IP adresa (VIP) se použije pro přístup k prostředkům mimo virtuální síť. Pokud se seznamy omezení IP adres používají k zabezpečení prostředků v rámci virtuální sítě, musí být zadaný celý rozsah pro podsíť, ve které je nainstalovaná služba API Management, aby bylo možné udělit nebo omezit přístup ke službě.
* Veřejné a privátní IP adresy vyrovnávání zatížení najdete v okně Přehled v Azure Portal.
* IP adresy přiřazené veřejnému a privátnímu přístupu se mohou změnit, pokud je služba odebrána z a následně přidána zpátky do virtuální sítě. Pokud k tomu dojde, může být nutné aktualizovat registrace DNS, pravidla směrování a seznamy omezení IP adres v rámci virtuální sítě.

## <a name="related-content"></a><a name="related-content"> </a>Související obsah
Další informace najdete v těchto článcích:
* [Běžné problémy s konfigurací sítě při nastavování Azure API Management ve virtuální síti][Common network configuration problems]
* [Nejčastější dotazy k virtuálním sítím](../virtual-network/virtual-networks-faq.md)
* [Vytvoření záznamu v DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
