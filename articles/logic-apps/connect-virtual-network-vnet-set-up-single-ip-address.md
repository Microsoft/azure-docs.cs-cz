---
title: Nastavení veřejné odchozí IP adresy pro ISEs
description: Naučte se nastavit jednu veřejnou odchozí IP adresu pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 9ee804f7bed01ca0c7f365a04e6108afd9598157
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87066386"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Nastavte jednu IP adresu pro jedno nebo více prostředí integrační služby v Azure Logic Apps

Při práci s Azure Logic Apps můžete nastavit [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pro hostování aplikací logiky, které potřebují přístup k prostředkům ve [službě Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Pokud máte více instancí ISE, které potřebují přístup k jiným koncovým bodům s omezeními IP adres, nasaďte [Azure firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic) do své virtuální sítě a přesměrujte odchozí přenosy přes tuto bránu firewall nebo síťové virtuální zařízení. Pak můžete všechny instance ISE ve vaší virtuální síti používat jednu, veřejnou, statickou a předvídatelná IP adresu ke komunikaci s cílovými systémy, které chcete. Tímto způsobem nemusíte pro každý ISE nastavovat další otevřená brána firewall v cílových systémech.

V tomto tématu se dozvíte, jak směrovat odchozí přenosy prostřednictvím Azure Firewall, ale můžete použít podobné koncepty síťového virtuálního zařízení, jako je například brána firewall jiného výrobce z Azure Marketplace. I když se toto téma zaměřuje na nastavení více instancí ISE, můžete tento přístup použít i pro jeden ISE, když váš scénář vyžaduje omezení počtu IP adres, které potřebují přístup. Zvažte, jestli další náklady na zařízení s bránou firewall nebo virtuální sítí jsou pro váš scénář smysluplné. Přečtěte si další informace o [cenách Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Požadavky

* Brána firewall Azure, která běží ve stejné virtuální síti jako vaše ISE. Pokud bránu firewall nemáte, přidejte nejprve [podsíť](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) s názvem `AzureFirewallSubnet` do vaší virtuální sítě. Pak můžete [vytvořit a nasadit bránu firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) ve vaší virtuální síti.

* [Směrovací tabulka](../virtual-network/manage-route-table.md)Azure. Pokud ho ještě nemáte, vytvořte nejprve [směrovací tabulku](../virtual-network/manage-route-table.md#create-a-route-table). Další informace o směrování najdete v tématu [směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Nastavení směrovací tabulky

1. V [Azure Portal](https://portal.azure.com)vyberte směrovací tabulku, například:

   ![Vybrat směrovací tabulku s pravidlem pro přesměrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Chcete-li [Přidat novou trasu](../virtual-network/manage-route-table.md#create-a-route), vyberte v nabídce směrovací tabulka možnost **trasy**  >  **Přidat**.

   ![Přidat trasu pro přesměrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. V podokně **Přidat trasu** [nastavte novou trasu](../virtual-network/manage-route-table.md#create-a-route) s pravidlem, které určuje, že veškerý odchozí provoz do cílového systému bude postupovat podle tohoto chování:

   * Používá [**virtuální zařízení**](../virtual-network/virtual-networks-udr-overview.md#user-defined) jako typ dalšího segmentu směrování.

   * Přejde na privátní IP adresu pro instanci brány firewall jako adresu dalšího segmentu směrování.

     Tuto IP adresu najdete tak, že v nabídce brány firewall vyberete **Přehled**a v části **privátní IP adresa**vyhledáte adresu, například:

     ![Najít soukromou IP adresu brány firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Tady je příklad, který ukazuje, jak by toto pravidlo vypadalo takto:

   ![Nastavení pravidla pro směrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název trasy** | <*jedinečný název směrování*> | Jedinečný název trasy v tabulce směrování |
   | **Předpona adresy** | <*Cílová adresa*> | Předpona adresy pro cílový systém, ve kterém chcete odchozí provoz přejít. Ujistěte se, že pro tuto adresu používáte [zápis CIDR (Inter-Domain Routing) pro třídy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . V tomto příkladu je tato předpona adresy určena pro server SFTP, který je popsaný v části [Nastavení síťového pravidla](#set-up-network-rule). |
   | **Typ dalšího segmentu směrování** | **Virtuální zařízení** | [Typ směrování](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) používaný odchozím provozem |
   | **Adresa dalšího segmentu** | <*Brána firewall – privátní IP adresa*> | Privátní IP adresa pro bránu firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Nastavit síťové pravidlo

1. V Azure Portal vyhledejte a vyberte bránu firewall. V nabídce brána firewall v části **Nastavení**vyberte **pravidla**. V podokně pravidla vyberte **kolekce pravidel sítě**  >  **přidat kolekci pravidel sítě**.

   ![Přidat kolekci pravidel sítě do brány firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. V kolekci přidejte pravidlo, které povoluje provoz do cílového systému.

   Předpokládejme například, že máte aplikaci logiky, která běží na ISE a potřebuje komunikovat se serverem SFTP. Vytvoříte kolekci síťových pravidel s názvem `LogicApp_ISE_SFTP_Outbound` , která obsahuje síťové pravidlo s názvem `ISE_SFTP_Outbound` . Toto pravidlo povoluje přenos z IP adresy jakékoli podsítě, ve které vaše ISE běží ve vaší virtuální síti, do cílového serveru SFTP pomocí privátní IP adresy brány firewall.

   ![Nastavit síťové pravidlo pro bránu firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Vlastnosti kolekce pravidel sítě**

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*Network-Rule-Collection-Name*> | Název kolekce pravidel sítě |
   | **Priorita** | <*úroveň priority*> | Pořadí priority, které se má použít pro spuštění kolekce pravidel. Další informace najdete v tématu [co jsou některé Azure firewall koncepty](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Akce** | **Povolit** | Typ akce, která se má provést pro toto pravidlo |
   |||

   **Vlastnosti síťového pravidla**

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*síťové pravidlo – název*> | Název síťového pravidla |
   | **Protokol** | <*připojení – protokoly*> | Protokoly připojení, které se mají použít. Pokud například používáte pravidla NSG, vyberte **TCP** i **UDP**, nejen **TCP**. |
   | **Zdrojové adresy** | <*ISE – adresy podsítí*> | IP adresy podsítě, kde vaše ISE běží a kam pochází přenos z vaší aplikace logiky |
   | **Cílové adresy** | <*cíl-IP adresa*> | IP adresa pro cílový systém, kde chcete odchozí provoz přejít. V tomto příkladu je tato IP adresa pro server SFTP. |
   | **Cílové porty** | <*cíl – porty*> | Všechny porty, které cílový systém používá pro příchozí komunikaci |
   |||

   Další informace o pravidlech sítě najdete v těchto článcích:

   * [Konfigurace pravidla sítě](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika zpracování pravidel Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Nejčastější dotazy ke službě Azure Firewall](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: AZ Network firewall Network-Rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
