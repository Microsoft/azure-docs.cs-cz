---
title: Nastavení přístupu pro více ISEs – Azure Logic Apps
description: V případě více prostředí ISEs (Integration Service Environment) můžete pro přístup k externím systémům z Azure Logic Apps nastavit jednu veřejnou odchozí IP adresu.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f35b6836012df7ac3879070f7a85fbfb21b456c0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549242"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Nastavení přístupu pro více prostředí integračních služeb v Azure Logic Apps

Při práci s Azure Logic Apps můžete nastavit [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pro hostování aplikací logiky, které potřebují přístup k prostředkům ve [službě Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Pokud máte několik instancí ISE, které potřebují přístup k jiným koncovým bodům s omezeními IP adres, nasaďte [Azure firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic) do své virtuální sítě a přesměrujte odchozí přenosy přes tuto bránu firewall nebo síťové virtuální zařízení. Pak můžete všechny instance ISE ve vaší virtuální síti používat pro komunikaci s cílovými systémy jednu, předvídatelná a veřejnou IP adresu. Tímto způsobem není nutné nastavovat další otevřená brána firewall v cílových systémech pro každý ISE. V tomto tématu se dozvíte, jak směrovat odchozí přenosy prostřednictvím Azure Firewall, ale můžete použít podobné koncepty virtuálního zařízení virtuální sítě, jako je například brána firewall jiného výrobce z Azure Marketplace.

## <a name="prerequisites"></a>Předpoklady

* Brána firewall Azure, která běží ve stejné virtuální síti jako vaše ISE. Pokud bránu firewall nemáte, přidejte do své virtuální sítě [podsíť](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) s názvem `AzureFirewallSubnet`. Pak můžete [vytvořit a nasadit bránu firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) ve vaší virtuální síti.

* [Směrovací tabulka](../virtual-network/manage-route-table.md)Azure. Pokud ho ještě nemáte, vytvořte nejprve [směrovací tabulku](../virtual-network/manage-route-table.md#create-a-route-table). Další informace o směrování najdete v tématu [směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Nastavení směrovací tabulky

1. V [Azure Portal](https://portal.azure.com)vyberte směrovací tabulku, například:

   ![Vybrat směrovací tabulku s pravidlem pro přesměrování odchozího provozu](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Chcete-li [Přidat novou trasu](../virtual-network/manage-route-table.md#create-a-route), vyberte v nabídce směrovací tabulka možnost **trasy** > **Přidat**.

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
   | **Název trasy** | <*Unique-Route-name*> | Jedinečný název trasy v tabulce směrování |
   | **Předpona adresy** | *cílová adresa <-* > | Adresa cílového systému, kam chcete přejít na provoz. Ujistěte se, že pro tuto adresu používáte [zápis CIDR (Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . |
   | **Typ dalšího segmentu směrování** | **Virtuální zařízení** | [Typ směrování](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) používaný odchozím provozem |
   | **Adresa dalšího směrování** | <*firewall-privátní IP adresa*> | Privátní IP adresa pro bránu firewall |
   |||

## <a name="set-up-network-rule"></a>Nastavit síťové pravidlo

1. V Azure Portal vyhledejte a vyberte bránu firewall. V nabídce brána firewall v části **Nastavení**vyberte **pravidla**. V podokně pravidla vyberte možnost **kolekce pravidel sítě** > **přidat kolekci pravidel sítě**.

   ![Přidat kolekci pravidel sítě do brány firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. V kolekci přidejte pravidlo, které povoluje provoz do cílového systému.

   Předpokládejme například, že máte aplikaci logiky, která běží na ISE a potřebuje komunikovat se systémem SFTP. Vytvoříte kolekci síťových pravidel s názvem `LogicApp_ISE_SFTP_Outbound`, která obsahuje síťové pravidlo s názvem `ISE_SFTP_Outbound`. Toto pravidlo povoluje přenos z IP adresy jakékoli podsítě, ve které vaše ISE běží ve vaší virtuální síti, do cílového systému SFTP pomocí privátní IP adresy brány firewall.

   ![Nastavit síťové pravidlo pro bránu firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Vlastnosti kolekce pravidel sítě**

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*Network-Rule-Collection-name*> | Název kolekce pravidel sítě |
   | **Priorita** | <> *úrovně priority* | Pořadí priority, které se má použít pro spuštění kolekce pravidel. Další informace najdete v tématu [co jsou některé Azure firewall koncepty](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Akce** | **Povolit** | Typ akce, která se má provést pro toto pravidlo |
   |||

   **Vlastnosti síťového pravidla**

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*síť – název pravidla*> | Název síťového pravidla |
   | **Protokol** | <*připojení – protokoly*> | Protokoly připojení, které se mají použít. Pokud například používáte pravidla NSG, vyberte **TCP** i **UDP**, nejen **TCP**. |
   | **Zdrojové adresy** | <*ISE-Subnet-addresss*> | IP adresy podsítě, kde vaše ISE běží a kam pochází přenos z vaší aplikace logiky |
   | **Cílové adresy** | <*cíl-IP-address*> | IP adresa pro cílový systém, kde chcete přejít do provozu |
   | **Cílové porty** | <*cílový – porty*> | Všechny porty, které cílový systém používá pro příchozí komunikaci |
   |||

   Další informace o pravidlech sítě najdete v těchto článcích:

   * [Konfigurace síťového pravidla](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika zpracování pravidla Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Nejčastější dotazy k Azure Firewall](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: AZ Network firewall Network-Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Další kroky

* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)