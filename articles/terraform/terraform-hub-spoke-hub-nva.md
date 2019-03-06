---
title: Vytvoření zařízení virtuální sítě centra s využitím Terraformu v Azure
description: Implementuje kurz vytvoření virtuální sítě rozbočovače, který funguje jako společný bod připojení mezi všech ostatních sítí
services: terraform
ms.service: terraform
keywords: terraform, střed a paprsek, sítí, hybridní sítě, devops, virtuální počítač, azure, VNet peering, centra s paprsky, rozbočovače.
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: ec729a1a4ca63149b4060046ea1206b7a0e520c7
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411432"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-with-terraform-in-azure"></a>Kurz: Vytvoření zařízení virtuální sítě centra s využitím Terraformu v Azure

A **zařízení VPN** je zařízení, která poskytuje externí připojení k místní síti. Zařízení VPN může být hardwarové zařízení nebo softwarové řešení. Příkladem řešení softwaru je směrování a vzdálený přístup (RRAS) ve Windows serveru 2012. Další informace o zařízení VPN najdete v tématu [informace o zařízeních VPN pro připojení typu Site-to-Site VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Azure podporuje širokou řadu síťových virtuálních zařízení, ze kterého chcete vybrat. V tomto kurzu se používá Ubuntu image. Další informace o širokou řadu řešení zařízení podporované v Azure, najdete v článku [síťová zařízení domovskou stránku](https://azure.microsoft.com/solutions/network-appliances/).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Můžete implementovat virtuální síť centra v topologii centra s paprsky HCL (HashiCorp Language)
> * Použití Terraformu k vytvoření centra sítě virtuálního počítače, který bude fungovat jako zařízení
> * Použití Terraformu k povolení tras pomocí rozšíření CustomScript
> * Použití Terraformu k vytvoření hvězdicové brány směrovací tabulky

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření centra hvězdicové hybridní topologie sítě s využitím Terraformu v Azure](./terraform-hub-spoke-introduction.md).
1. [Vytvořit místní virtuální sítě s využitím Terraformu v Azure](./terraform-hub-spoke-on-prem.md).
1. [S využitím Terraformu v Azure vytvořit virtuální síť centra](./terraform-hub-spoke-hub-network.md).

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-network-appliance"></a>Deklarovat síťové zařízení centra

Vytvoření konfiguračního souboru Terraform, který deklaruje virtuální místní sítě.

1. Ve službě Cloud Shell, vytvořte nový soubor s názvem `hub-nva.tf`.

    ```bash
    code hub-nva.tf
    ```

1. Do editoru vložte následující kód:
    
    ```JSON
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "CentralUS"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = "${local.hub-nva-location}"

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub-nva}"
        subnet_id                     = "${azurerm_subnet.hub-dmz.id}"
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-nva-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nva-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      virtual_machine_name = "${azurerm_virtual_machine.hub-nva-vm.name}"
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"

      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = "${azurerm_subnet.hub-gateway-subnet.id}"
      route_table_id = "${azurerm_route_table.hub-gateway-rt.id}"
      depends_on = ["azurerm_subnet.hub-gateway-subnet"]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke1-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke1-workload.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-workload"]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke2-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke2-workload.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-workload"]
    }

    ```

1. Uložte soubor a ukončete editor.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednoho paprsku virtuálních sítí s využitím Terraformu v Azure](./terraform-hub-spoke-spoke-network.md)