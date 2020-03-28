---
title: Výuka – vytvoření topologie hybridní sítě rozbočovače a paprsku v Azure pomocí Terraform
description: Kurz znázorňující, jak vytvořit celou hybridní síťovou referenční architekturu v Azure pomocí Terraform
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472175"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Kurz: Vytvoření topologie hybridní sítě rozbočovače a paprsku v Azure pomocí Terraform

Tato série kurzů ukazuje, jak používat Terraform k implementaci v [Azure rozbočovača a paprskové sítě topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Topologie centra a paprsku je způsob, jak izolovat úlohy při sdílení běžných služeb. Tyto služby zahrnují identitu a zabezpečení. Rozbočovač je virtuální síť (VNet), která funguje jako centrální připojení bod k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem. Sdílené služby se nasazují v rozbočovači, zatímco jednotlivé úlohy se nasazují uvnitř paprskových sítí.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Použití hcl (HashiCorp language) k rozložení prostředků hybridní síťové referenční architektury pro rozbočovače a paprsky
> * Použití terraformu k vytvoření prostředků síťových zařízení rozbočovače
> * Použití Terraform k vytvoření centrální sítě v Azure, která bude fungovat jako společný bod pro všechny prostředky
> * Použití Terraform k vytváření jednotlivých úloh jako virtuálních sítí pro paprsky v Azure
> * Použití Terraform unavovat brány a připojení mezi místními sítěmi a sítěmi Azure
> * Použití Terraform k vytvoření partnerských sítí virtuální sítě do sítí s paprsky

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte [si bezplatný účet Azure,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.

- **Instalace a konfigurace Terraform**: Pro zřízení virtuálních počítačů a další infrastruktury v Azure, [instalace a konfigurace Terraform](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>Architektura topologie hubu a paprsku

V topologii rozbočovače a paprsku je rozbočovač virtuální sítě. Virtuální síť funguje jako centrální bod připojení k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem, které je možné použít k izolaci úloh. Přenosy mezi centrem a místním datacentrem proudí prostřednictvím připojení ExpressRoute nebo brány sítě VPN. Následující obrázek znázorní součásti topologie rozbočovače a paprsku:

![Architektura topologie hubu a paprsku v Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Výhody topologie rozbočovače a paprsku

Topologie sítě rozbočovače a paprsku je způsob, jak izolovat úlohy při sdílení běžných služeb. Tyto služby zahrnují identitu a zabezpečení. Rozbočovač je virtuální síť, která funguje jako centrální připojení k místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem. Sdílené služby se nasazují v rozbočovači, zatímco jednotlivé úlohy se nasazují uvnitř paprskových sítí. Zde jsou některé výhody topologie sítě rozbočovače a paprsku:

- **Úspory nákladů** centralizací služeb na jednom místě, které mohou být sdíleny více úlohami. Tyto úlohy zahrnují síťová virtuální zařízení a servery DNS.
- **Překonání omezení předplatných** díky vytvoření partnerského vztahu virtuálních sítí z různých předplatných s hlavním centrem
- **Oddělení oblastí zájmu** mezi centrálním IT (SecOps, InfraOps) a úlohami (DevOps)

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typické použití pro architekturu rozbočovače a paprsku

Některé z typických použití pro rozbočovač a paprskovou architekturu zahrnují:

- Mnoho zákazníků má úlohy, které jsou nasazeny v různých prostředích. Tato prostředí zahrnují vývoj, testování a výrobu. Tyto úlohy musí často sdílet služby, jako je DNS, IDS, NTP nebo AD DS. Tyto sdílené služby lze umístit do virtuální sítě rozbočovače. Tímto způsobem každé prostředí je nasazena na paprsku udržovat izolaci.
- Úlohy, které nevyžadují vzájemné připojení, ale vyžadují přístup ke sdíleným službám.
- Podniky, které vyžadují centrální kontrolu nad bezpečnostními aspekty.
- Podniky, které vyžadují oddělené řízení pro úlohy v každém paprsku.

## <a name="preview-the-demo-components"></a>Náhled ukázkových komponent

Při práci prostřednictvím každého kurzu v této sérii jsou různé součásti definovány v různých skriptech Terraform. Ukázková architektura vytvořená a nasazená se skládá z následujících součástí:

- **Místní síť**. Soukromá místní síť spuštěná s organizací. Pro referenční architekturu rozbočovače a paprsku se virtuální síť v Azure používá k simulaci místní sítě.

- **Zařízení VPN**. Zařízení nebo služba VPN poskytuje externí připojení k místní síti. Zařízení VPN může být hardwarové zařízení nebo softwarové řešení. 

- **Virtuální síť centra**. Rozbočovač je ústředním bodem připojení k místní síti a místem pro hostování služeb. Tyto služby mohou být spotřebovány různými úlohami hostovanými ve virtuálních sítích s paprsky.

- **Podsíť brány**. Brány virtuální sítě jsou podržené ve stejné podsíti.

- **Virtuální sítě paprsků**. Paprsky se dají použít k izolování úloh v jejich vlastních virtuálních sítích, které se spravují odděleně od ostatních paprsků. Každá úloha může obsahovat několik vrstev s několika podsítěmi připojenými prostřednictvím nástrojů pro vyrovnávání zatížení Azure. 

- **Partnerský vztah virtuální sítě**. Dvě virtuální sítě lze připojit pomocí připojení partnerského vztahu. Připojení s partnerským vztahem jsou nepřenositelná připojení s nízkou latencí mezi virtuálními sítěmi. Po partnerské síti si virtuální sítě vyměňují provoz pomocí páteřní sítě Azure, aniž by potřebovaly směrovač. V topologii sítě rozbočovače a paprsku se partnerský vztah virtuální sítě používá k připojení rozbočovače ke každému paprsku. Virtuální sítě můžete předsuzovat ve stejné oblasti nebo v různých oblastech.

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Vytvořte adresář, který obsahuje konfigurační soubory Terraform pro ukázku.

1. Přejděte na [portál Azure](https://portal.azure.com).

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

1. V prostředí Cloud Shell otevřete nový soubor s názvem `main.tf`.

    ```bash
    code main.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Uložte tento soubor a ukončete editor.

## <a name="create-the-variables-file"></a>Vytvoření souboru proměnných

Vytvořte konfigurační soubor Terraform pro běžné proměnné, které se používají v různých skriptech.

1. V prostředí Cloud Shell otevřete nový soubor s názvem `variables.tf`.

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

1. Uložte tento soubor a ukončete editor.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Vytvoření místní virtuální sítě pomocí Terraform v Azure](./terraform-hub-spoke-on-prem.md)