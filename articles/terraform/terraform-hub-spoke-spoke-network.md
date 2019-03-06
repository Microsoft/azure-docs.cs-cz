---
title: Vytvoření sítě paprsků s využitím Terraformu v Azure
description: Zjistěte, jak implementovat dvě paprsku, virtuální sítě připojené k centrum v topologii centra s paprsky
services: terraform
ms.service: terraform
keywords: terraform, střed a paprsek, sítí, hybridní sítě, devops, virtuální počítač, azure, VNet peering, paprsku, centra s paprsky
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 71e8c9f50c4540308380e3f29acfc7bed4b427d7
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411404"
---
# <a name="tutorial-create-a-spoke-virtual-network-with-terraform-in-azure"></a>Kurz: Vytvoření virtuální sítě paprsků s využitím Terraformu v Azure

V tomto kurzu implementujete dvě sítě samostatné paprsku k předvedení oddělení úloh. Sítě sdílení společných prostředků pomocí centrální virtuální síti. Paprsky se dají použít k izolování úloh v jejich vlastních virtuálních sítích, které se spravují odděleně od ostatních paprsků. Každá úloha může obsahovat několik vrstev s několika podsítěmi připojenými prostřednictvím nástrojů pro vyrovnávání zatížení Azure.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Implementace virtuálních sítí paprsků v topologii centra s paprsky pomocí HCL (HashiCorp Language)
> * Použití Terraformu k vytvoření virtuálního počítače v sítích paprsků
> * Použití Terraformu k vytvoření partnerské vztahy virtuálních sítí pomocí sítí centra

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření centra hvězdicové hybridní topologie sítě s využitím Terraformu v Azure](./terraform-hub-spoke-introduction.md).
1. [Vytvořit místní virtuální sítě s využitím Terraformu v Azure](./terraform-hub-spoke-on-prem.md).
1. [S využitím Terraformu v Azure vytvořit virtuální síť centra](./terraform-hub-spoke-hub-network.md).
1. [S využitím Terraformu v Azure vytvořit virtuální síťové zařízení centra](./terraform-hub-spoke-hub-nva.md).

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

V této části jsou vytvořeny dva skripty paprsku. Každý skript definuje virtuální sítě paprsků a virtuálního počítače pro pracovní vytížení. Partnerské virtuální síti z centra do paprsku se pak vytvoří.

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

## <a name="declare-the-two-spoke-networks"></a>Deklarovat dvě paprsku sítě

1. Ve službě Cloud Shell, otevřete nový soubor s názvem `spoke1.tf`.

    ```bash
    code spoke1.tf
    ```

1. Do editoru vložte následující kód:

    ```JSON
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = "${local.spoke1-resource-group}"
      location = "${local.spoke1-location}"
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      address_space       = ["10.1.0.0/16"]

      tags {
        environment = "${local.prefix-spoke1 }"
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke1-vnet.name}"
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke1-vnet.name}"
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.spoke1-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.hub-vnet.id}"

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet" , "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-spoke1}"
        subnet_id                     = "${azurerm_subnet.spoke1-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.spoke1-nic.id}"]
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
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-spoke1}"
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.hub-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.spoke1-vnet.id}"
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```

1. Uložte soubor a ukončete editor.

1. Vytvořte nový soubor s názvem `spoke2.tf`.

      ```bash
      code spoke2.tf
      ```
    
1. Do editoru vložte následující kód:
    
    ```JSON
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = "${local.spoke2-resource-group}"
      location = "${local.spoke2-location}"
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      address_space       = ["10.2.0.0/16"]

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke2-vnet.name}"
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke2-vnet.name}"
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.spoke2-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.hub-vnet.id}"

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-spoke2}"
        subnet_id                     = "${azurerm_subnet.spoke2-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.spoke2-nic.id}"]
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
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.hub-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.spoke2-vnet.id}"
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```
     
1. Uložte soubor a ukončete editor.
  
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ověření hvězdicové sítě s využitím Terraformu v Azure](./terraform-hub-spoke-validation.md)