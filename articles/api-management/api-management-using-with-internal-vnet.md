---
title: Použití azure api managementu s interními virtuálními sítěmi
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
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841859"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Použití služby Azure API Management s interní virtuální sítí
Pomocí virtuálních sítí Azure může správa rozhraní API Spravovat rozhraní API, která nejsou přístupná na internetu. Pro připojení je k dispozici řada technologií VPN. Api Management lze nasadit ve dvou hlavních režimech uvnitř virtuální sítě:
* Externí
* Interní

Když se správa rozhraní API nasadí v režimu interní virtuální sítě, všechny koncové body služby (brána proxy, portál pro vývojáře, přímá správa a Git) jsou viditelné pouze ve virtuální síti, ke které řídíte přístup. Žádný z koncových bodů služby není registrován na veřejném serveru DNS.

> [!NOTE]
> Vzhledem k tomu, že pro koncové body služby neexistují žádné položky DNS, nebudou tyto koncové body přístupné, dokud [nebude služba DNS nakonfigurována](#apim-dns-configuration) pro virtuální síť.

Pomocí správy rozhraní API v interním režimu můžete dosáhnout následujících scénářů:

* Pomocí připojení site-to-site nebo Azure ExpressRoute VPN můžete pomocí připojení site-to-site nebo Azure ExpressRoute VPN zabezpečit api hostovaná ve vašem soukromém datovém centru bezpečně třetími stranami mimo něj.
* Povolte scénáře hybridního cloudu vystavením cloudových api a místních api prostřednictvím společné brány.
* Spravujte svá api hostovaná ve více geografických umístěních pomocí jednoho koncového bodu brány.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto článku, musíte mít:

+ **Aktivní předplatné Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Instance Azure API Management**. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Když je služba správy rozhraní API nasazená ve virtuální síti, používá se [seznam portů,](./api-management-using-with-vnet.md#required-ports) které je potřeba otevřít. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Vytvoření správy rozhraní API v interní virtuální síti
Služba API Management v interní virtuální síti je hostována za [interním nástrojem pro vyrovnávání zatížení (klasickým).](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud) Toto je jediná dostupná možnost, kterou nelze změnit.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Povolení připojení k virtuální síti pomocí portálu Azure

1. Přejděte na instanci Azure API Management na [webu Azure Portal](https://portal.azure.com/).
2. Vyberte **Virtuální síť**.
3. Nakonfigurujte instanci správy rozhraní API, která má být nasazena uvnitř virtuální sítě.

    ![Nabídka pro nastavení správy rozhraní API Azure v interní virtuální síti][api-management-using-internal-vnet-menu]

4. Vyberte **Uložit**.

Po úspěšném nasazení byste měli v okně přehledu vidět **privátní** virtuální IP adresu a **veřejnou** virtuální IP adresu vaší služby API Management. **Privátní** virtuální IP adresa je IP adresa s vyrovnáváním `gateway` `portal`zatížení `management` `scm` z delegované podsítě správy rozhraní API, přes kterou lze přistupovat k koncovým bodům , a koncovými body. **Veřejná** virtuální IP **only** adresa se používá `management` pouze pro řízení provozu roviny do koncového bodu přes port 3443 a může být uzamčena na značku služby [ApiManagement.][ServiceTags]

![Řídicí panel správy rozhraní API s nakonfigurovanou interní virtuální sítí][api-management-internal-vnet-dashboard]

> [!NOTE]
> Testovací konzola dostupná na webu Azure Portal nebude fungovat pro **interní** nasazenou virtuální síť, protože adresa URL brány není registrovaná ve veřejném DNS. Místo toho byste měli použít testovací konzolu poskytnutou na **portálu pro vývojáře**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Povolení připojení k virtuální síti pomocí rutin prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Připojení virtuální sítě můžete také povolit pomocí rutin prostředí PowerShell.

* Vytvoření služby správy rozhraní API uvnitř virtuální sítě: Pomocí rutiny [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) vytvořte službu Azure API Management uvnitř virtuální sítě a nakonfigurujte ji tak, aby používala typ interní virtuální sítě.

* Aktualizace existujícího nasazení služby api management uvnitř virtuální sítě: Pomocí rutiny [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) přesuňte existující službu správy rozhraní API do virtuální sítě a nakonfigurujte ji tak, aby používala typ interní virtuální sítě.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Konfigurace DNS
Když je správa rozhraní API v režimu externí virtuální sítě, dns spravuje Azure. Pro režim interní virtuální sítě je třeba spravovat vlastní směrování.

> [!NOTE]
> Služba API Management nenaslouchá požadavkům přicházejícím z IP adres. Reaguje pouze na požadavky na název hostitele nakonfigurované na koncových bodech služby. Mezi tyto koncové body patří brána, portál Azure a portál pro vývojáře, koncový bod přímé správy a Git.

### <a name="access-on-default-host-names"></a>Přístup k výchozím názvům hostitelů
Při vytváření služby správy rozhraní API s názvem "contosointernalvnet" jsou ve výchozím nastavení nakonfigurovány následující koncové body služby:

   * Brána nebo proxy server: contosointernalvnet.azure-api.net

   * Vývojářský portál: contosointernalvnet.portal.azure-api.net

   * Nový vývojářský portál: contosointernalvnet.developer.azure-api.net

   * Koncový bod přímé správy: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Chcete-li získat přístup k těmto koncovým bodům služby Správa rozhraní API, můžete vytvořit virtuální počítač v podsíti připojené k virtuální síti, ve které se nasazuje správa rozhraní API. Za předpokladu, že interní virtuální IP adresa vaší služby je 10.1.0.5, můžete mapovat soubor hosts, %SystemDrive%\drivers\etc\hosts, a to následovně:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Potom můžete přistupovat ke všem koncovým bodům služby z virtuálního počítače, který jste vytvořili.
Pokud používáte vlastní server DNS ve virtuální síti, můžete také vytvořit záznamy DNS a přistupovat k těmto koncovým bodům odkudkoli ve virtuální síti.

### <a name="access-on-custom-domain-names"></a>Přístup k vlastním názvům domén

1. Pokud nechcete získat přístup ke službě API Management s výchozími názvy hostitelů, můžete nastavit vlastní názvy domén pro všechny koncové body služby, jak je znázorněno na následujícím obrázku:

   ![Nastavení vlastní domény pro správu rozhraní API][api-management-custom-domain-name]

2. Pak můžete vytvořit záznamy na serveru DNS pro přístup ke koncovým bodům, které jsou přístupné pouze z vaší virtuální sítě.

## <a name="routing"></a><a name="routing"> </a> Směrování

* *Privátní* virtuální IP adresa s vyrovnáváním zatížení z rozsahu podsítě bude vyhrazena a použita pro přístup k koncovým bodům služby Správa rozhraní API z virtuální sítě. Tuto *privátní* IP adresu najdete na okně Přehled pro službu na webu Azure Portal. Tato adresa musí být registrována u serverů DNS používaných virtuální sítí.
* *Veřejná* IP adresa (VIP) s vyrovnáváním zatížení bude také vyhrazena pro poskytování přístupu ke koncovému bodu služby pro správu přes port 3443. Tuto *veřejnou* IP adresu najdete v okně Přehled pro službu na webu Azure Portal. *Veřejná* IP adresa se používá pouze `management` pro řízení provozu roviny na koncový bod přes port 3443 a může být uzamčen a [apimanagement][ServiceTags] servicetag.
* IP adresy z rozsahu IP podsítě (DIP) budou přiřazeny ke každému virtuálnímu počítači ve službě a budou použity pro přístup k prostředkům v rámci virtuální sítě. Veřejná IP adresa (VIP) bude použita pro přístup k prostředkům mimo virtuální síť. Pokud se seznamy omezení IP používají k zabezpečení prostředků v rámci virtuální sítě, musí být určen celý rozsah podsítě, ve které je nasazena služba Api Management, aby byl udělován nebo omezen přístup ze služby.
* Veřejné a soukromé IP adresy s vyrovnáváním zatížení najdete na okně Přehled na webu Azure Portal.
* IP adresy přiřazené pro veřejný a soukromý přístup se mohou změnit, pokud je služba odebrána a přidána zpět do virtuální sítě. Pokud k tomu dojde, může být nutné aktualizovat registrace DNS, pravidla směrování a seznamy omezení IP v rámci virtuální sítě.

## <a name="related-content"></a><a name="related-content"> </a>Související obsah
Další informace naleznete v následujících článcích:
* [Běžné problémy s konfigurací sítě při nastavování Azure API Management ve virtuální síti][Common network configuration problems]
* [Nejčastější dotazy k virtuální síti](../virtual-network/virtual-networks-faq.md)
* [Vytvoření záznamu ve službě DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

