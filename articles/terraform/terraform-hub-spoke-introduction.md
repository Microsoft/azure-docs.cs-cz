---
title: Vytvoření centra hvězdicové hybridní topologie sítě s využitím Terraformu v Azure
description: Kurz ilustrující vytvoření referenční architektury celý hybridní sítě v Azure pomocí Terraformu
services: terraform
ms.service: terraform
keywords: terraform, střed a paprsek, sítí, hybridní sítě, devops, virtuální počítač, azure, partnerský vztah virtuální sítě, síťové virtuální zařízení
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 25ba7016c10fc13357b403efae6e4996de59b624
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411500"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Kurz: Vytvoření centra hvězdicové hybridní topologie sítě s využitím Terraformu v Azure

V této sérii kurzů ukazuje, jak implementovat v Azure pomocí Terraformu [rozbočovač a uvedenou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Hvězdicová topologie je způsob, jak izolovat úlohy při sdílení společných služeb. Mezi tyto služby patří, identity a zabezpečení. Centrum je virtuální síť (VNet), která funguje jako centrální spojovací bod k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem. Sdílené služby jsou nasazené v rozbočovači, jednotlivé úlohy se nasadí v sítích paprsků.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Rozložení hvězdicové hybridní sítě referenční zdroje informací o architektuře pomocí HCL (HashiCorp Language)
> * Použití Terraformu k vytvoření centrální síti prostředky zařízení
> * Použití Terraformu k vytvoření centrální síti v Azure tak, aby fungoval jako společný bod pro všechny prostředky
> * Použití Terraformu k vytvoření jednotlivé úlohy jako paprsky virtuálních sítí v Azure
> * Použití Terraformu k vytvoření brány a připojení mezi na místě a sítě Azure
> * Použití Terraformu k vytvoření partnerské vztahy virtuálních sítí paprsků sítě

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

- **Instalace a konfigurace Terraformu**: Ke zřízení virtuálních počítačů a další infrastrukturu v Azure, [instalace a konfigurace Terraformu](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Hvězdicová topologie architektury

Centrum je v hvězdicové topologii virtuální sítě. Virtuální sítě funguje jako ústřední bod připojení k vaší místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem, které je možné použít k izolaci úloh. Přenosy mezi centrem a místním datacentrem proudí prostřednictvím připojení ExpressRoute nebo brány sítě VPN. Následující obrázek znázorňuje komponenty v hvězdicové topologii:

![Hvězdicová topologie architektury v Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Výhody hvězdicové topologii

Rozbočovač a uvedenou síťovou topologii je způsob, jak izolovat úlohy při sdílení společných služeb. Mezi tyto služby patří, identity a zabezpečení. Centrum je virtuální síť, která funguje jako centrální spojovací bod k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem. Sdílené služby jsou nasazené v rozbočovači, jednotlivé úlohy se nasadí v sítích paprsků. Tady je několik výhod rozbočovač a uvedenou síťovou topologii:

- **Úspory nákladů** tím, že centralizují služby na jednom místě, který může být sdílen více úloh. Mezi tyto úlohy patří síťových virtuálních zařízení a servery DNS.
- **Překonání omezení předplatných** díky vytvoření partnerského vztahu virtuálních sítí z různých předplatných s hlavním centrem
- **Oddělení oblastí zájmu** mezi centrálním IT (SecOps, InfraOps) a úlohami (DevOps)

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typické použití hvězdicové architektury

Mezi typická použití hvězdicové architektury patří:

- Řada zákazníků má úlohy, které jsou nasazené v různých prostředích. Tato prostředí patří vývoj, testování a provoz. V mnoha případech tyto úlohy potřebují sdílet služby jako DNS, identifikátory, NTP nebo službě AD DS. Tyto sdílené služby mohou být umístěny ve virtuální síti centra. Tímto způsobem každé prostředí je nasazené do paprsku, aby se zachovala izolace.
- Úlohy, které nevyžadují připojení k sobě navzájem, ale vyžadují přístup ke sdíleným službám.
- Podniky, které vyžadují centrální kontrolu nad aspekty zabezpečení.
- Podniky, které vyžadují oddělenou správu pro úlohy v každém paprsku.

## <a name="preview-the-demo-components"></a>Ve verzi Preview součásti demo

Při práci prostřednictvím jednotlivých kurzu této série, různé součásti jsou definovány v různých Terraformu skripty. Architektura ukázka vytvoří a nasadí se skládá z následujících součástí:

- **Místní síť**: Privátní místní síti se spuštěnou s organizací. Pro rozbočovač a uvedenou referenční architekturu virtuální sítě v Azure umožňuje simulovat v místní síti.

- **Zařízení VPN**. Zařízení VPN ani služby obsahuje externí připojení k místní síti. Zařízení VPN může být hardwarové zařízení nebo softwarové řešení. 

- **Virtuální síť centra**. Centrum je ústřední bod připojení k vaší místní síti a místo pro hostování služeb. Tyto služby mohou být spotřebovány různými úlohami hostovanými ve virtuálních sítích paprsků.

- **Podsíť brány**. Brány virtuální sítě jsou uloženy ve stejné podsíti.

- **Virtuální sítě paprsků**. Paprsky se dají použít k izolování úloh v jejich vlastních virtuálních sítích, které se spravují odděleně od ostatních paprsků. Každá úloha může obsahovat několik vrstev s několika podsítěmi připojenými prostřednictvím nástrojů pro vyrovnávání zatížení Azure. 

- **Partnerské vztahy virtuálních sítí**. Dvě virtuální sítě můžou být připojené pomocí připojení s partnerským vztahem. Připojení s partnerským vztahem jsou nepřenositelná připojení s nízkou latencí mezi virtuálními sítěmi. Po navázání partnerského vztahu, virtuální sítě vyměňují přenosy pomocí páteřní síť Azure, aniž by bylo směrovač. V hvězdicové síťové topologie VNet peering se používá připojuje Centrum ke každému paprsku. Po vytvoření partnerského vztahu virtuálních sítí ve stejné oblasti nebo v různých oblastech.

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Vytvoření adresáře, který obsahuje konfigurační soubory Terraformu pro tuto ukázku.

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Deklarování zprostředkovatele Azure

Vytvořte konfigurační soubor Terraformu, který deklaruje zprostředkovatele Azure.

1. Ve službě Cloud Shell, otevřete nový soubor s názvem `main.tf`.

    ```bash
    code main.tf
    ```

1. Do editoru vložte následující kód:

    ```JSON
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Uložte soubor a ukončete editor.

## <a name="create-the-variables-file"></a>Vytvoření souboru proměnných

Vytvořte konfigurační soubor Terraformu pro společné proměnné, které se používají na různých skriptů.

1. Ve službě Cloud Shell, otevřete nový soubor s názvem `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Do editoru vložte následující kód:

    ```JSON
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Uložte soubor a ukončete editor.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Vytvořit místní virtuální sítě s využitím Terraformu v Azure](./terraform-hub-spoke-on-prem.md)
