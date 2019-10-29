---
title: Kurz – vytvoření hybridní síťové topologie centra a paprsků v Azure pomocí Terraformu
description: Kurz ilustrující postup vytvoření celé hybridní síťové referenční architektury v Azure pomocí Terraformu
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 8d85163e746f1d2d0713a9a4f247a2061e0029b8
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969398"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Kurz: Vytvoření hybridní síťové topologie centra a paprsků v Azure pomocí Terraformu

V této sérii kurzů se dozvíte, jak používat Terraformu k implementaci v Azure a hvězdicové [síťové topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Topologie rozbočovače a paprsků je způsob, jak izolovat úlohy při sdílení běžných služeb. Mezi tyto služby patří identita a zabezpečení. Hub je virtuální síť (VNet), která funguje jako centrální bod připojení k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem. Sdílené služby se nasazují v centru, zatímco jednotlivé úlohy se nasazují v sítích paprsků.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Použití HCL (HashiCorp Language) k rozložení prostředků referenčních architekturou hybridní sítě rozbočovače a paprsků
> * Použití Terraformu k vytváření prostředků síťových zařízení centra
> * Použití Terraformu k vytvoření sítě rozbočovače v Azure, která bude fungovat jako běžný bod pro všechny prostředky
> * Použití Terraformu k vytváření jednotlivých úloh jako paprskových virtuální sítě v Azure
> * Použití Terraformu k navázání bran a připojení mezi místním prostředím a sítěmi Azure
> * Použití Terraformu k vytváření partnerských vztahů virtuálních sítí pro sítě paprsků

## <a name="prerequisites"></a>Předpoklady

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Instalace a konfigurace terraformu**: zřizování virtuálních počítačů a jiné infrastruktury v Azure, [instalace a konfigurace terraformu](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Architektura topologie rozbočovače a paprsků

V topologii centra a paprsků je centrum virtuální síť. Virtuální síť funguje jako centrální bod připojení k vaší místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem, které je možné použít k izolaci úloh. Přenosy mezi centrem a místním datacentrem proudí prostřednictvím připojení ExpressRoute nebo brány sítě VPN. Následující obrázek znázorňuje komponenty v hvězdicové topologii:

![Architektura topologie rozbočovače a paprsků v Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Výhody topologie centra a paprsků

Topologie sítě rozbočovače a paprsků je způsob, jak izolovat úlohy při sdílení běžných služeb. Mezi tyto služby patří identita a zabezpečení. Centrum je virtuální síť, která funguje jako centrální bod připojení k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem. Sdílené služby se nasazují v centru, zatímco jednotlivé úlohy se nasazují v sítích paprsků. Tady jsou některé výhody síťové topologie centra a paprsků:

- **Úspora nákladů** díky centralizaci služeb v jednom umístění, které může sdílet více úloh. Mezi tyto úlohy patří síťová virtuální zařízení a servery DNS.
- **Překonání omezení předplatných** díky vytvoření partnerského vztahu virtuálních sítí z různých předplatných s hlavním centrem
- **Oddělení oblastí zájmu** mezi centrálním IT (SecOps, InfraOps) a úlohami (DevOps)

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typická použití pro architekturu centra a paprsků

Mezi Typická použití pro architekturu hub a paprsku patří:

- Mnoho zákazníků má úlohy, které jsou nasazeny v různých prostředích. Mezi tato prostředí patří vývoj, testování a produkce. V mnoha případech je potřeba, aby tyto úlohy sdílely služby, jako jsou DNS, identifikátory, NTP nebo služba AD DS. Tyto sdílené služby můžou být umístěné ve virtuální síti centra. V takovém případě se každé prostředí nasadí do paprsku, aby se zachovala izolace.
- Úlohy, které nevyžadují připojení navzájem, ale vyžadují přístup ke sdíleným službám.
- Podniky, které vyžadují centrální kontrolu nad aspekty zabezpečení.
- Podniky, které vyžadují oddělené řízení pro úlohy v každém paprsku.

## <a name="preview-the-demo-components"></a>Náhled ukázkových součástí

Při práci v každém kurzu v této sérii jsou různé komponenty definovány v samostatných Terraformu skriptech. Ukázková architektura vytvořená a nasazená se skládá z následujících součástí:

- **Místní síť**: Privátní místní síť běžící s organizací. V případě referenční architektury hub a paprsků se pro simulaci místní sítě používá virtuální síť v Azure.

- **Zařízení VPN**. Zařízení nebo služba sítě VPN poskytují externí připojení k místní síti. Zařízení VPN může být hardwarové zařízení nebo softwarové řešení. 

- **Virtuální síť centra**. Centrum je centrální bod připojení k vaší místní síti a místo pro hostování služeb. Tyto služby mohou být spotřebovány různými úlohami hostovanými v virtuální sítě paprsků.

- **Podsíť brány**. Brány virtuální sítě se nacházejí ve stejné podsíti.

- **Virtuální sítě paprsků**. Paprsky se dají použít k izolování úloh v jejich vlastních virtuálních sítích, které se spravují odděleně od ostatních paprsků. Každá úloha může obsahovat několik vrstev s několika podsítěmi připojenými prostřednictvím nástrojů pro vyrovnávání zatížení Azure. 

- **Partnerské vztahy virtuálních sítí**. Dva virtuální sítě se dají připojit pomocí partnerského připojení. Připojení s partnerským vztahem jsou nepřenositelná připojení s nízkou latencí mezi virtuálními sítěmi. Po navázání partnerského vztahu virtuální sítě provoz Exchange pomocí páteře Azure, aniž by bylo potřeba směrovač. V hvězdicové síťové topologii se pro připojení centra k jednotlivým paprskům používá VNet peering. Peer virtuální sítě můžete vytvořit ve stejné oblasti nebo v různých oblastech.

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Vytvořte adresář, který obsahuje konfigurační soubory Terraformu pro ukázku.

1. Přejděte na web [Azure Portal](https://portal.azure.com).

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

1. V Cloud Shell otevřete nový soubor s názvem `main.tf`.

    ```bash
    code main.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Uložte soubor a ukončete Editor.

## <a name="create-the-variables-file"></a>Vytvoření souboru proměnných

Vytvořte konfigurační soubor Terraformu pro běžné proměnné, které se používají v různých skriptech.

1. V Cloud Shell otevřete nový soubor s názvem `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
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

1. Uložte soubor a ukončete Editor.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Vytvoření místní virtuální sítě pomocí Terraformu v Azure](./terraform-hub-spoke-on-prem.md)