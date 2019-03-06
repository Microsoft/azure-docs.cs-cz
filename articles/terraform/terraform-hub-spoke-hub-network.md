---
title: Vytvoření virtuální sítě centra s využitím Terraformu v Azure
description: Kurz ukazující vytvoření virtuální sítě v Azure, která funguje jako společný bod připojení mezi jinými sítěmi
services: terraform
ms.service: terraform
keywords: terraform, střed a paprsek, sítí, hybridní sítě, devops, virtuální počítač, azure, VNet peering, centra s paprsky, rozbočovače.
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 11603a2863ea9d9a2f5e790a64b1cafef5fc1087
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411400"
---
# <a name="tutorial-create-a-hub-virtual-network-with-terraform-in-azure"></a>Kurz: Vytvoření virtuální sítě centra s využitím Terraformu v Azure

Centrální virtuální síti (VNet) funguje jako ústřední bod připojení k místní síti. Virtuální sítě hostitelů sdílené služby používané úlohami hostovanými ve virtuálních sítích paprsků. Pro účely ukázky jsou implementovány žádné sdílené služby v tomto kurzu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Implementace virtuální síti centra v topologii centra s paprsky pomocí HCL (HashiCorp Language)
> * Použití Terraformu k vytvoření centra Jump box virtuálního počítače
> * Použití Terraformu k vytvoření brány virtuální privátní sítě rozbočovače
> * Použití Terraformu k vytvoření připojení rozbočovače a na místní brána

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření centra hvězdicové hybridní topologie sítě s využitím Terraformu v Azure](./terraform-hub-spoke-introduction.md).
1. [Vytvořit místní virtuální sítě s využitím Terraformu v Azure](./terraform-hub-spoke-on-prem.md).

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Centrální síti se skládá z následujících součástí:

- Centrální virtuální síti
- Brána virtuální sítě centra
- Brána připojení rozbočovače 

Následující konfigurační soubor Terraformu definuje prostředky:

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

## <a name="declare-the-hub-vnet"></a>Deklarovat virtuální síti centra

Vytvoření konfiguračního souboru Terraform, který deklaruje centrální virtuální síti.

1. Ve Cloud Shellu vytvořte soubor s názvem `hub-vnet.tf`.

    ```bash
    code hub-vnet.tf
    ```

1. Do editoru vložte následující kód:

    ```JSON
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = "${local.hub-resource-group}"
      location = "${local.hub-location}"
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub}"
        subnet_id                     = "${azurerm_subnet.hub-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = "${local.prefix-hub}"
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nic.id}"]
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
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub}"
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.hub-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.hub-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.hub-vnet-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"

      shared_key = "${local.shared-key}"
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.hub-vnet-gateway.id}"

      shared_key = "${local.shared-key}"
    }
    ```
    
1. Uložte soubor a ukončete editor.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [S využitím Terraformu v Azure vytvořit virtuální síťové zařízení centra](./terraform-hub-spoke-hub-nva.md))