---
title: 'Terraformu: nasazení webové aplikace front-endu a back-end webové aplikace s bezpečným připojením ke službě VNet Integration a privátnímu'
description: Naučte se používat poskytovatele terraformu k App Service k nasazení dvou webových aplikací zabezpečených pomocí privátního koncového bodu a integrace virtuální sítě.
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91739829"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Bezpečné vytvoření dvou webových aplikací s privátním koncovým bodem a integrací virtuální sítě

Tento článek ukazuje příklad použití [privátního koncového bodu](../networking/private-endpoint.md) a regionální [Integrace virtuální](../web-sites-integrate-with-vnet.md) sítě k bezpečnému připojení dvou webových aplikací (front-endu a back-endu) pomocí následujících kroků:
- Nasazení virtuální sítě
- Vytvoření první podsítě pro integraci
- Vytvořte druhou podsíť pro soukromý koncový bod, musíte nastavit konkrétní parametr pro zakázání zásad sítě.
- Nasazení jednoho App Service plánu typu PremiumV2 nebo PremiumV3, který je vyžadován pro funkci privátního koncového bodu
- Vytvoření webové aplikace front-end se specifickým nastavením aplikace pro využívání privátní zóny DNS, [Další podrobnosti](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Připojení webové aplikace front-end k podsíti integrace
- Vytvoření back-end webové aplikace
- Vytvoření privátní zóny DNS s názvem zóny privátního propojení pro webovou aplikaci privatelink.azurewebsites.net
- Propojit tuto zónu s virtuální sítí
- Vytvořte privátní koncový bod pro back-end webovou aplikaci v podsíti koncového bodu a zaregistrujte názvy DNS (Web a SCM) v dříve vytvořené privátní zóně DNS.

## <a name="how-to-use-terraform-in-azure"></a>Jak používat terraformu v Azure

Přejděte k [dokumentaci Azure](/azure/developer/terraform/) , kde se dozvíte, jak používat Terraformu s Azure.

## <a name="the-complete-terraform-file"></a>Úplný soubor terraformu

Chcete-li použít tento soubor, musíte změnit vlastnost Name pro prostředky frontwebapp a backwebapp (název WebApp musí být jedinečný název DNS celosvětový). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Další kroky


> [Další informace o používání Terraformu v Azure](/azure/developer/terraform/)