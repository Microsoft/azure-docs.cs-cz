---
title: Vytvoření clusteru Azure Data Explorer & DB ve vaší virtuální síti
description: V tomto článku se dozvíte, jak vytvořit cluster Průzkumník a dat Azure ve vaší virtuální síti.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548898"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Vytvoření clusteru Průzkumníka dat Azure ve virtuální síti

Azure Data Explorer podporuje nasazení clusteru do podsítě ve vaší virtuální síti (Virtuální síť). Tato funkce umožňuje přístup ke clusteru soukromě z vaší virtuální sítě Azure nebo místní, přístup k prostředkům, jako je centrum událostí a úložiště uvnitř vaší virtuální sítě a omezit příchozí a odchozí provoz.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Vytvořit skupinu zabezpečení sítě (NSG)

[Skupiny zabezpečení sítě (NSG)](/azure/virtual-network/security-overview) poskytují možnost řídit přístup k síti v rámci virtuální sítě. K Průzkumníku dat Azure se dá přistupovat pomocí dvou koncových bodů HTTP (443) a TDS (1433). Následující pravidla skupiny nsg musí být nakonfigurována tak, aby umožňovala přístup k těmto koncovým bodům pro správu, monitorování a správnou funkci clusteru.

Vytvoření skupiny zabezpečení sítě:

1. V levém horním rohu portálu vyberte tlačítko **+ Vytvořit zdroj.**
1. Vyhledejte *skupinu zabezpečení sítě*.
1. V části **Skupina zabezpečení sítě**vyberte v dolní části obrazovky možnost **Vytvořit**.
1. V okně **Vytvořit skupinu zabezpečení sítě** vyplňte následující informace.

   ![Vytvořit formulář NSG](media/vnet-create-cluster-portal/network-security-group.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj cluster.|
    | Skupina prostředků | Vaše skupina prostředků | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Name (Název) | AzureDataExplorerNsg | Zvolte název, který identifikuje skupinu zabezpečení sítě (NSG) ve skupině prostředků.  |
    | Region (Oblast) | *USA – západ* | Vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | | | |

1. Vyberte **Zkontrolovat + vytvořit,** chcete-li zkontrolovat podrobnosti clusteru, a **vytvořit** pro zřízení clusteru.

1. Po dokončení nasazení vyberte **Přejít na prostředek**.

    ![Přejít k prostředku](media/vnet-create-cluster-portal/notification-nsg.png)

1. Na kartě **Příchozí pravidla zabezpečení** vyberte možnost **+Přidat**.
1. V okně **Přidat příchozí pravidlo zabezpečení** vyplňte následující informace.

    ![Vytvořit formulář příchozího pravidla nsg](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Nastavení** | **Navrhovaná hodnota** 
    |---|---|
    | Zdroj | ServiceTag
    | Zdrojová značka servisu | AzureDataExplorerManagement
    | Rozsahy zdrojových portů | *
    | Cíl | VirtualNetwork
    | Rozsahy cílových portů | *
    | Protocol (Protokol) | TCP
    | Akce | Povolit
    | Priorita | 100
    | Name (Název) | Povolit AzureDataExplorerManagement
    | | |
    
1. Opakujte předchozí dva kroky pro všechny příchozí a odchozí závislosti podle [závislostí pro nasazení virtuální sítě](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment). Odchozí pravidla mohou být případně nahrazena jediným pravidlem, které umožňuje *Internet* pro porty 443 a 80.
    
    Pravidla souboru nsg pro příchozí a odchozí závislosti by měla vypadat takto:

    ![Pravidla NSG](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Vytvoření veřejných IP adres

Vytvoření dotazu (Engine) veřejné IP adresy:

1. V levém horním rohu portálu vyberte tlačítko **+ Vytvořit zdroj.**
1. Vyhledejte *skupinu zabezpečení sítě*.
1. V části **Veřejná IP adresa**vyberte v dolní části obrazovky **možnost Vytvořit**.
1. V podokně **Vytvořit veřejnou adresu IP** vyplňte následující informace.
   
   ![Vytvořit formulář veřejné IP adresy](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Verze IP | IPv4 | Vyberte verzi IP. Podporujeme pouze IPv4.|
    | Skladová jednotka (SKU) | Standard | Požadujeme **standardní** pro dotaz (Engine) URI koncový bod. |
    | Name (Název) | motor-pip | Zvolte název, který identifikuje vaši veřejnou IP adresu ve skupině prostředků.
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro veřejnou IP adresu.|
    | Skupina prostředků | Vaše skupina prostředků | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Umístění | *USA – západ* | Vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | | | |

1. Vyberte **Vytvořit,** chcete-li vytvořit veřejnou IP adresu.

1. Chcete-li vytvořit veřejnou IP adresu "Ingestion) (Data Management), postupujte podle stejných pokynů a vyberte 
    * **Sku**: Základní
    * **Přiřazení IP adresy**: Statické

## <a name="create-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Vytvoření virtuální sítě a podsítě:

1. V levém horním rohu portálu vyberte tlačítko **+ Vytvořit zdroj.**
1. Vyhledejte *virtuální síť*.
1. V části **Virtuální síť**vyberte v dolní části obrazovky **možnost Vytvořit**.
1. V okně **Vytvořit virtuální síť** vyplňte následující informace.

   ![Vytvoření formuláře virtuální sítě](media/vnet-create-cluster-portal/vnet-blade.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj cluster.|
    | Skupina prostředků | Vaše skupina prostředků | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Name (Název) | AzureDataExplorerVnet | Zvolte název, který identifikuje vaši virtuální síť ve skupině prostředků.
    | Region (Oblast) | *USA – západ* | Vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | | | |

    > [!NOTE]
    > Pro produkční úlohy naplánujte velikost podsítě podle [velikosti podsítě plánu ve virtuální síti.](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. Vyberte **Zkontrolovat + vytvořit,** chcete-li zkontrolovat podrobnosti clusteru, a **vytvořit** pro zřízení clusteru.

1. Po dokončení nasazení vyberte **Přejít na prostředek**.
1. Přejděte do okna **Podsítě** a vyberte **výchozí** podsíť.
    
    ![Okno podsítí](media/vnet-create-cluster-portal/subnets.png)

1. Ve **výchozím** okně podsítě:
    1. V rozevírací nabídce vyberte **skupinu zabezpečení sítě.** 
    1. Vyberte název skupiny zabezpečení sítě, v tomto případě **AzureDataExplorerNsg**. 
    1. **Uložit**

    ![Konfigurace podsítě](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Vytvoření clusteru

Vytvořte cluster Průzkumníka dat Azure s definovanou sadou výpočetních prostředků a prostředků úložiště ve skupině prostředků Azure, jak je popsáno v [článku vytvoření clusteru](create-cluster-database-portal.md#create-a-cluster).

1. Před dokončením vytváření clusteru vyberte v okně **Vytvořit cluster Průzkumník dat Azure** kartu **Síť,** která poskytuje podrobnosti o virtuální síti pomocí prostředků vytvořených na předchozích kartách:

   ![Vytvoření formuláře virtuální sítě clusteru](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro síťové prostředky.|
    | Virtual Network | AzureDataExplorerVnet | Zvolte virtuální síť vytvořenou v předchozích krocích.
    | Podsíť | default | Zvolte podsíť vytvořenou v předchozích krocích.
    | Dotaz veřejné IP | motor-pip | Zvolte veřejnou IP adresu dotazu vytvořenou v předchozích krocích.
    | Veřejná IP adresa při požití dat | dm-pip | Zvolte ingestování veřejné IP adresy vytvořené v předchozích krocích.
    | | | |

1. Chcete-li vytvořit cluster, vyberte **Zkontrolovat + vytvořit.**
1. Po dokončení nasazení vyberte **Přejít na prostředek**.

Pokud chcete nasadit cluster Azure Data Explorer do virtuální sítě, použijte [cluster Deploy Azure Data Explorer do](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) šablony Správce prostředků Virtuální sítě Azure.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení Průzkumníka dat Azure do virtuální sítě](vnet-create-cluster-portal.md)