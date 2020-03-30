---
title: Nastavení veřejné odchozí IP adresy pro ises
description: Zjistěte, jak nastavit jednu veřejnou odchozí IP adresu pro prostředí integračních služeb (ISEs) v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191515"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Nastavení jedné IP adresy pro jedno nebo více prostředí integračních služeb v Aplikacích Azure Logic Apps

Když pracujete s Azure Logic Apps, můžete nastavit [ *prostředí služby integrace* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pro hostování aplikací logiky, které potřebují přístup k prostředkům ve [virtuální síti Azure](../virtual-network/virtual-networks-overview.md). Pokud máte více instancí ISE, které potřebují přístup k jiným koncovým bodům, které mají omezení IP, nasaďte [bránu Azure Firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic) do virtuální sítě a směrujte odchozí provoz přes tuto bránu firewall nebo síťové virtuální zařízení. Potom můžete mít všechny instance ISE ve vaší virtuální síti používat jednu, veřejnou, statickou a předvídatelnou IP adresu pro komunikaci s cílovými systémy. Tímto způsobem není třeba nastavit další otvory brány firewall v těchto cílových systémech pro každou ISE.

Toto téma ukazuje, jak směrovat odchozí provoz přes Azure Firewall, ale můžete použít podobné koncepty na síťové virtuální zařízení, jako je brána firewall jiného výrobce z Azure Marketplace. Zatímco toto téma se zaměřuje na nastavení pro více instancí ISE, můžete také použít tento přístup pro jednu ise, pokud váš scénář vyžaduje omezení počtu IP adres, které potřebují přístup. Zvažte, zda dodatečné náklady na bránu firewall nebo zařízení virtuální sítě smysl pro váš scénář. Přečtěte si další informace o [cenách azure firewallu](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Požadavky

* Brána firewall Azure, která běží ve stejné virtuální síti jako vaše ISE. Pokud nemáte bránu firewall, nejprve [přidejte podsíť](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) s názvem `AzureFirewallSubnet` do vaší virtuální sítě. Potom můžete [vytvořit a nasadit bránu firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) ve vaší virtuální síti.

* Tabulka [tras](../virtual-network/manage-route-table.md)Azure . Pokud ji nemáte, [nejprve vytvořte směrovací tabulku](../virtual-network/manage-route-table.md#create-a-route-table). Další informace o směrování naleznete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Nastavit tabulku tras

1. Na [webu Azure Portal](https://portal.azure.com)vyberte tabulku tras, například:

   ![Vybrat směrovací tabulku s pravidlem pro přesměrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Chcete-li [přidat novou trasu](../virtual-network/manage-route-table.md#create-a-route), vyberte v nabídce tabulky trasy **možnost Přidat** > **trasy**.

   ![Přidání trasy pro přesměrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. V podokně **Přidat trasu** [nastavte novou trasu](../virtual-network/manage-route-table.md#create-a-route) s pravidlem, které určuje, že veškerý odchozí provoz do cílového systému se řídí tímto chováním:

   * Používá [**virtual zařízení**](../virtual-network/virtual-networks-udr-overview.md#user-defined) jako další typ směrování.

   * Přejde na privátní IP adresu instance brány firewall jako další adresu směrování.

     Chcete-li najít tuto adresu IP, vyberte v nabídce brány firewall **možnost Přehled**, vyhledejte adresu v části **Soukromá ADRESA IP**, například:

     ![Najít privátní IP adresu firewallu](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Zde je příklad, který ukazuje, jak by takové pravidlo mohlo vypadat:

   ![Nastavit pravidlo pro přesměrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název trasy** | <*jedinečný název trasy*> | Jedinečný název trasy v tabulce tras |
   | **Předpona adresy** | <*cílová adresa*> | Adresa cílového systému, kam chcete přejít provoz. Ujistěte se, že pro tuto adresu používáte [zápis cidr (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) |
   | **Typ dalšího segmentu** | **Virtuální zařízení** | [Typ směrování](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) používaný odchozím provozem |
   | **Adresa dalšího segmentu** | <*firewall-private-IP-adresa*> | Privátní IP adresa brány firewall |
   |||

## <a name="set-up-network-rule"></a>Nastavit síťové pravidlo

1. Na webu Azure Portal najděte a vyberte bránu firewall. V nabídce brány firewall vyberte v části **Nastavení** **položku Pravidla**. V podokně pravidel vyberte **kolekce síťových** > pravidel**Přidat kolekci síťových pravidel**.

   ![Přidání kolekce síťových pravidel do brány firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Do kolekce přidejte pravidlo, které povoluje provoz do cílového systému.

   Předpokládejme například, že máte aplikaci logiky, která běží v ISE a potřebuje komunikovat se systémem SFTP. Vytvoříte kolekci síťových pravidel `LogicApp_ISE_SFTP_Outbound`s názvem , `ISE_SFTP_Outbound`která obsahuje síťové pravidlo s názvem . Toto pravidlo povoluje přenosy z ip adresy libovolné podsítě, ve které je služba ISE spuštěna ve vaší virtuální síti, do cílového systému SFTP pomocí privátní IP adresy brány firewall.

   ![Nastavení síťového pravidla pro bránu firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Vlastnosti kolekce síťových pravidel**

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*název kolekce pravidel sítě*> | Název kolekce síťových pravidel |
   | **Priorita** | <*úroveň priority*> | Pořadí priority pro spuštění kolekce pravidel. Další informace najdete v tématu [Jaké jsou některé koncepty azure firewall?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Akce** | **Povolit** | Typ akce, který má být pro toto pravidlo naplánováno |
   |||

   **Vlastnosti pravidla sítě**

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*název pravidla sítě*> | Název pravidla sítě |
   | **Protokol** | <*protokoly připojení*> | Protokoly připojení, které chcete použít. Pokud například používáte pravidla souboru nsg, vyberte **protokoly TCP** i **UDP**, nikoli pouze **protokol TCP**. |
   | **Zdrojové adresy** | <*Adresy podsítí ISE*> | IP adresy podsítě, kde je vaše služba ISE spuštěna a odkud pochází provoz z aplikace logiky |
   | **Cílové adresy** | <*cílová IP adresa*> | IP adresa cílového systému, kam chcete, aby byl provoz |
   | **Cílové porty** | <*cílové přístavy*> | Všechny porty, které cílový systém používá pro příchozí komunikaci |
   |||

   Další informace o pravidlech sítě naleznete v těchto článcích:

   * [Konfigurace pravidla sítě](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika zpracování pravidel Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Nejčastější dotazy k Azure Firewall](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: síťové pravidlo síťové brány az](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)