---
title: Připojení k virtuálním sítím Azure pomocí ISE
description: Vytvoření prostředí ISE (Integration Service Environment), které má přístup k virtuálním sítím Azure (virtuální sítě) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 315de18539bf083515658b40fa70f3c214d7c909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739735"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps pomocí prostředí integrační služby (ISE)

Pro scénáře, ve kterých aplikace logiky a účty pro integraci potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md), vytvořte [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Prostředí integrační služby (ISE) je vyhrazené prostředí, které využívá vyhrazené úložiště a další prostředky oddělené od globální služby Logic Apps pro více tenantů. Toto oddělení také snižuje vliv na výkon, který můžou mít jiní klienti Azure na výkon vašich aplikací. Prostředí integrační služby vám také poskytuje vlastní statickou IP adresu. Tyto IP adresy jsou oddělené od statických IP adres, které jsou sdílené pomocí Logic Apps ve veřejné víceklientské službě.

Při vytváření ISE Azure *vloží* do vaší virtuální sítě Azure, které ISE, nasadí službu Logic Apps do vaší virtuální sítě. Když vytvoříte aplikaci logiky nebo účet pro integraci, vyberte ISE jako své umístění. Vaše aplikace logiky nebo účet pro integraci pak můžou přímo přistupovat k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby, ve vaší virtuální síti.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Aby Logic Apps a integrační účty fungovaly společně v ISE, musí jako své umístění používat *stejný ISE* .

ISE má vyšší omezení doby trvání spuštění, uchovávání úložiště, propustnosti, požadavků HTTP a časových limitů odpovědí, velikostí zpráv a požadavků vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Další informace o ISEs najdete v tématu [přístup k prostředkům Azure Virtual Network z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

V tomto článku se dozvíte, jak tyto úlohy dokončit pomocí Azure Portal:

* Povolte přístup k vašemu ISE.
* Vytvořte své ISE.
* Přidejte do svého ISEu další kapacitu.

Můžete také vytvořit ISE pomocí [ukázkové Azure Resource Manager šablony pro rychlé](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) zprovoznění nebo pomocí REST API Logic Apps, včetně nastavení klíčů spravovaných zákazníkem:

* [Vytvoření prostředí integrační služby (ISE) s využitím rozhraní REST API služby Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md)
* [Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém umístění pro ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logic Apps, integrované triggery, integrované akce a konektory spouštěné ve vašem ISE používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](../logic-apps/logic-apps-pricing.md).

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md) se čtyřmi *prázdnými* podsítěmi, které jsou potřeba pro vytváření a nasazování prostředků v ISE a používané těmito interními a skrytými součástmi:

  * Logic Apps COMPUTE
  * Interní App Service Environment (konektory)
  * Interní API Management (konektory)
  * Interní Redis pro ukládání do mezipaměti a výkon
  
  Podsítě můžete vytvořit předem nebo při vytváření ISE, abyste mohli vytvořit podsítě ve stejnou dobu. Před vytvořením podsítí ale nezapomeňte zkontrolovat [požadavky podsítě](#create-subnet).

  * Ujistěte se, že vaše virtuální síť [umožňuje přístup k vašemu ISE](#enable-access) , takže vaše ISE může správně fungovat a zůstat přístupná.

  * Pokud používáte nebo chcete používat [ExpressRoute](../expressroute/expressroute-introduction.md) spolu s [vynuceným tunelovým propojením](../firewall/forced-tunneling.md), musíte [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) s následujícím konkrétním směrováním a propojit směrovací tabulku s každou podsítí, kterou používá vaše ISE:

    **Název**: <*trasa-Name*><br>
    **Předpona adresy**: 0.0.0.0/0<br>
    **Další segment směrování**: Internet
    
    Tato konkrétní směrovací tabulka je povinná, aby Logic Apps komponenty mohly komunikovat s dalšími závislými službami Azure, jako jsou Azure Storage a Azure SQL DB. Další informace o této trase najdete v tématu [předpona adresy 0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Pokud nepoužíváte vynucené tunelování s ExpressRoute, nepotřebujete tuto konkrétní směrovací tabulku.
    
    ExpressRoute umožňuje rozmístit vaše místní sítě do cloudu Microsoftu a připojit se ke cloudovým službám Microsoftu přes soukromé připojení, které usnadňuje poskytovatel připojení. Konkrétně ExpressRoute je virtuální privátní síť, která směruje provoz přes soukromou síť, nikoli prostřednictvím veřejného Internetu. Vaše aplikace logiky se můžou připojit k místním prostředkům, které jsou ve stejné virtuální síti, když se připojují prostřednictvím ExpressRoute nebo virtuální privátní sítě.
   
  * Pokud používáte [síťové virtuální zařízení (síťové virtuální zařízení)](../virtual-network/virtual-networks-udr-overview.md#user-defined), ujistěte se, že nepovolíte ukončení protokolu TLS/SSL nebo změníte odchozí přenosy TLS/SSL. Také se ujistěte, že nepovolíte kontrolu provozu, který pochází z podsítě ISE. Další informace najdete v tématu [směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

  * Pokud chcete pro službu Azure Virtual Network používat vlastní servery DNS, [nastavte tyto servery pomocí následujících kroků](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ještě před nasazením ISE do virtuální sítě. Další informace o správě nastavení serveru DNS najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > Pokud změníte nastavení serveru DNS nebo serveru DNS, musíte restartovat ISE, aby ISE mohl tyto změny vyzvednout. Další informace najdete v tématu [restart vaší ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Povolení přístupu pro prostředí integrační služby (ISE)

Pokud používáte ISE s virtuální sítí Azure, běžný problém instalace je jeden nebo více blokovaných portů. Konektory používané pro vytváření připojení mezi ISE a cílovými systémy mohou mít také vlastní požadavky na porty. Pokud například komunikujete se systémem FTP pomocí konektoru FTP, musí být port, který používáte v systému FTP, k dispozici, například port 21 pro odesílání příkazů.

Abyste se ujistili, že je váš ISE přístupný a že aplikace logiky v této ISE můžou komunikovat napříč všemi podsítěmi ve vaší virtuální síti, [otevřete porty popsané v této tabulce pro každou podsíť](#network-ports-for-ise). Pokud některé požadované porty nejsou k dispozici, vaše ISE nebude fungovat správně.

* Pokud máte několik instancí ISE, které potřebují přístup k jiným koncovým bodům s omezeními IP adres, nasaďte [Azure firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic) do své virtuální sítě a přesměrujte odchozí přenosy přes tuto bránu firewall nebo síťové virtuální zařízení. Pak můžete [nastavit jednu, odchozí, veřejnou, statickou a předvídatelná IP adresu](connect-virtual-network-vnet-set-up-single-ip-address.md) , kterou můžou všechny instance ISE ve vaší virtuální síti používat ke komunikaci s cílovými systémy. Tímto způsobem není nutné nastavovat další otevřená brána firewall v těchto cílových systémech pro každý ISE.

   > [!NOTE]
   > Tento postup můžete použít pro jeden ISE v případě, že váš scénář vyžaduje omezení počtu IP adres, které potřebují přístup. Zvažte, jestli další náklady na zařízení s bránou firewall nebo virtuální sítí jsou pro váš scénář smysluplné. Přečtěte si další informace o [cenách Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Pokud jste vytvořili novou virtuální síť Azure a podsítě bez jakýchkoli omezení, nemusíte ve virtuální síti nastavovat [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/network-security-groups-overview.md#network-security-groups) , abyste mohli řídit provoz napříč podsítěmi.

* Pro existující virtuální síť můžete *volitelně* nastavit [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/network-security-groups-overview.md#network-security-groups) pro [filtrování síťového provozu napříč podsítěmi](../virtual-network/tutorial-filter-network-traffic.md). Pokud chcete přejít k této trase, nebo pokud už používáte skupin zabezpečení sítě, ujistěte se, že jste pro tyto skupin zabezpečení sítě [otevřeli porty popsané v této tabulce](#network-ports-for-ise) .

  Při nastavování [pravidel zabezpečení NSG](../virtual-network/network-security-groups-overview.md#security-rules)je potřeba použít *jak* protokoly **TCP** , **UDP** , nebo můžete vybrat **libovolnou** , takže nemusíte vytvářet samostatná pravidla pro každý protokol. Pravidla zabezpečení NSG popisují porty, které je třeba otevřít pro IP adresy, které potřebují přístup k těmto portům. Ujistěte se, že všechny brány firewall, směrovače nebo jiné položky, které existují mezi těmito koncovými body, také udržují tyto porty dostupné pro tyto IP adresy.

* Pokud nastavíte vynucené tunelové propojení přes bránu firewall pro přesměrování internetového provozu, přečtěte si [Další požadavky na vynucené tunelování](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Síťové porty používané prostředím integrační služby (ISE)

V této tabulce jsou popsány porty, které vaše ISE vyžaduje k přístupu a účelu pro tyto porty. Tabulka používá [značky služeb](../virtual-network/service-tags-overview.md) , které reprezentují skupiny předpon IP adres pro konkrétní službu Azure, aby při nastavování pravidel zabezpečení lépe omezila složitost. Pokud je uvedeno jinak, *interní ISE* a *externí ISE* odkazují na [koncový bod přístupu, který je vybraný při vytváření ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Další informace najdete v tématu [přístup ke koncovému bodu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> U všech pravidel nezapomeňte nastavit zdrojové porty na, `*` protože zdrojové porty jsou dočasné.

#### <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| Účel | Značka zdrojové služby nebo IP adresy | Zdrojové porty | Značka nebo IP adresa cílové služby | Cílové porty | Poznámky |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Komunikace mezi podsítí v rámci virtuální sítě | Adresní prostor virtuální sítě s ISEmi podsítěmi | * | Adresní prostor virtuální sítě s ISEmi podsítěmi | * | Vyžaduje se pro tok provozu *mezi* podsítěmi ve vaší virtuální síti. <p><p>**Důležité**: Pokud chcete provoz směrovat mezi *součásti* v každé podsíti, ujistěte se, že jste otevřeli všechny porty v každé podsíti. |
| Protokoly <p>Komunikace s aplikací logiky <p><p>Historie spuštění aplikace logiky| Interní ISE: <br>**VirtualNetwork** <p><p>Externí ISE: **Internet** nebo zobrazit **poznámky** | * | **VirtualNetwork** | 443 | Místo toho, abyste mohli používat značku **internetové** služby, můžete zadat zdrojovou IP adresu pro tyto položky: <p><p>– Počítač nebo služba, které ve vaší aplikaci logiky volají jakékoli triggery žádostí nebo Webhooky <p>– Počítač nebo služba, ze kterých chcete získat přístup k historii spuštění aplikace logiky <p><p>**Důležité**: zavření nebo blokování tohoto portu brání volání aplikací logiky, které mají triggery požadavků nebo Webhooky. Nebudete také mít přístup k vstupům a výstupům pro každý krok v historii spuštění. Nebudete se však bránit v přístupu k historii spuštění aplikace logiky.|
| Návrhář Logic Apps – dynamické vlastnosti | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Požadavky pocházejí z [příchozích IP adres](../logic-apps/logic-apps-limits-and-config.md#inbound) koncového bodu přístupu Logic Apps pro tuto oblast. |
| Nasazení konektoru | **AzureConnectors** | * | **VirtualNetwork** | 454 | Vyžaduje se pro nasazení a aktualizaci konektorů. Zavřením nebo blokováním tohoto portu dojde k selhání nasazení ISE a zabráníte aktualizaci konektoru a opravám. |
| Kontrolu stavu sítě | **LogicApps** | * | **VirtualNetwork** | 454 | Požadavky pocházejí z [IP adresy](../logic-apps/logic-apps-limits-and-config.md#inbound) koncového bodu přístupu Logic Apps a [odchozích IP adres](../logic-apps/logic-apps-limits-and-config.md#outbound) pro tuto oblast. |
| Závislost správy App Service | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Komunikace z Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Interní ISE: 454 <p><p>Externí ISE: 443 ||
| Protokoly <p>Nasazení zásad konektoru <p>Koncový bod správy API Management | **APIManagement** | * | **VirtualNetwork** | 3443 | Pro nasazení zásad konektoru se pro nasazení a aktualizaci konektorů vyžaduje přístup k portu. Zavřením nebo blokováním tohoto portu dojde k selhání nasazení ISE a zabráníte aktualizaci konektoru a opravám. |
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi rolí | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383 a navíc zobrazit **poznámky**| Aby ISE mohl pracovat s Azure cache pro Redis, musíte otevřít tyto [odchozí a příchozí porty popsané v mezipaměti Azure pro Redis Nejčastější dotazy](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| Účel | Značka zdrojové služby nebo IP adresy | Zdrojové porty | Značka nebo IP adresa cílové služby | Cílové porty | Poznámky |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Komunikace mezi podsítí v rámci virtuální sítě | Adresní prostor virtuální sítě s ISEmi podsítěmi | * | Adresní prostor virtuální sítě s ISEmi podsítěmi | * | Vyžaduje se pro tok provozu *mezi* podsítěmi ve vaší virtuální síti. <p><p>**Důležité**: Pokud chcete provoz směrovat mezi *součásti* v každé podsíti, ujistěte se, že jste otevřeli všechny porty v každé podsíti. |
| Komunikace z aplikace logiky | **VirtualNetwork** | * | Liší se v závislosti na cíli. | 80, 443 | Cíl se liší v závislosti na koncových bodech externí služby, se kterou vaše aplikace logiky potřebuje komunikovat. |
| Azure Active Directory | **VirtualNetwork** | * | **Azureactivedirectory selhala** | 80, 443 ||
| Azure Storage závislost | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Správa připojení | **VirtualNetwork** | * | **AppService** | 443 ||
| Publikování diagnostických protokolů & metriky | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Závislost Azure SQL | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Požadováno pro publikování stavu Resource Health. |
| Závislost z protokolu k zásadám centra událostí a agentům monitorování | **VirtualNetwork** | * | **Centrum událostí** | 5672 ||
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi rolí | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383 a navíc zobrazit **poznámky**| Aby ISE mohl pracovat s Azure cache pro Redis, musíte otevřít tyto [odchozí a příchozí porty popsané v mezipaměti Azure pro Redis Nejčastější dotazy](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Překlad názvů DNS | **VirtualNetwork** | * | IP adresy pro všechny vlastní servery DNS (Domain Name System) ve virtuální síti | 53 | Vyžadováno jenom v případě, že ve virtuální síti používáte vlastní servery DNS |
|||||||

Kromě toho je potřeba přidat odchozí pravidla pro [App Service Environment (POmocného mechanismu)](../app-service/environment/intro.md):

* Pokud používáte Azure Firewall, musíte bránu firewall nastavit pomocí [značky plně kvalifikovaného názvu domény (FQDN](../firewall/fqdn-tags.md#current-fqdn-tags)) App Service Environment (pomocného programu), která umožňuje odchozí přístup k provozu platformy pomocného mechanismu.

* Pokud používáte jiné zařízení brány firewall než Azure Firewall, je třeba nastavit bránu firewall se *všemi* pravidly uvedenými v [integračních závislostech brány firewall](../app-service/environment/firewall-integration.md#dependencies) , které jsou požadovány pro App Service Environment.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Vynucené tunelování – požadavky

Pokud nastavíte nebo použijete [vynucené tunelování](../firewall/forced-tunneling.md) přes bránu firewall, budete muset povolit další externí závislosti pro ISE. Vynucené tunelové propojení umožňuje přesměrovat provoz vázaný na Internet na určené další segmenty směrování, jako je vaše virtuální privátní síť (VPN) nebo virtuální zařízení, a ne na Internet, abyste mohli kontrolovat a auditovat odchozí síťový provoz.

Pokud pro tyto závislosti nepovolíte přístup, nasazení ISE se nepovede a vaše nasazené ISE přestane fungovat.

* Trasy definované uživatelem

  Chcete-li zabránit asymetrickému směrování, je nutné definovat trasu pro každou a každou IP adresu, která je uvedena níže, s **internetem** jako s dalším segmentem směrování.
  
  * [Adresy pro správu App Service Environment](../app-service/environment/management-addresses.md)
  * [IP adresy Azure pro konektory v oblasti ISE, které jsou k dispozici v tomto souboru ke stažení](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Adresy pro správu Azure Traffic Manager](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps příchozích a odchozích adres pro oblast ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [IP adresy Azure pro konektory v oblasti ISE, které jsou v tomto souboru ke stažení](https://www.microsoft.com/download/details.aspx?id=56519)

* Koncové body služby

  Je potřeba povolit koncové body služby pro Azure SQL, Storage, Service Bus, Trezor klíčů a Event Hubs, protože do těchto služeb nemůžete odesílat přenosy přes bránu firewall.

*  Další příchozí a odchozí závislosti

   Brána firewall *musí* umožňovat následující příchozí a odchozí závislosti:
   
   * [Azure App Service závislosti](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Závislosti Azure Cache Service](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Závislosti Azure API Management](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření prostředí integrační služby (ISE)

1. V [Azure Portal](https://portal.azure.com)do hlavního pole Azure Search zadejte `integration service environments` jako filtr a vyberte **prostředí integrační služby**.

   ![Najde a vybere "prostředí integrační služby".](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. V podokně **prostředí integrační služby** vyberte **Přidat**.

   ![Pokud chcete vytvořit prostředí integrační služby, vyberte Přidat.](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Zadejte tyto údaje pro vaše prostředí a pak vyberte **zkontrolovat + vytvořit**, například:

   ![Zadání podrobností prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Yes | <*Azure – předplatné – název*> | Předplatné Azure, které se má použít pro vaše prostředí |
   | **Skupina prostředků** | Yes | <*Azure-Resource-Group-Name*> | Nová nebo existující skupina prostředků Azure, ve které chcete vytvořit prostředí. |
   | **Název prostředí integrační služby** | Yes | <*Název prostředí*> | Název ISE, který může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ) a tečky ( `.` ). |
   | **Umístění** | Yes | <*Azure – Datacenter – oblast*> | Oblast datacenter Azure, kde se má vaše prostředí nasadit |
   | **SKU** | Yes | **Premium** nebo **Developer (bez smlouvy SLA)** | SKU ISE, která se má vytvořit a použít. Rozdíly mezi těmito SKU najdete v tématu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Důležité**: Tato možnost je k dispozici pouze při vytváření ISE a nedá se změnit později. |
   | **Další kapacita** | Premium: <br>Yes <p><p>Maximalizac <br>Neuvedeno | Premium: <br>0 až 10 <p><p>Maximalizac <br>Neuvedeno | Počet dalších jednotek zpracování, které se mají použít pro tento prostředek ISE. Pokud chcete přidat kapacitu po vytvoření, přečtěte si téma [Přidání kapacity ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Koncový bod přístupu** | Yes | **Interní** nebo **externí** | Typ koncových bodů přístupu, které se mají použít pro ISE. Tyto koncové body určují, jestli triggery Request nebo Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě. <p><p>Pokud například chcete použít následující triggery založené na webhookech, ujistěte se, že jste vybrali možnost **externí**: <p><p>– Azure DevOps <br>-Azure Event Grid <br>-Common Data Service <br>– Office 365 <br>-SAP (verze ISE) <p><p>Váš výběr také ovlivňuje způsob zobrazení a přístupu ke vstupům a výstupům v historii spuštění aplikace logiky. Další informace najdete v tématu [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Důležité**: během vytváření ISE můžete vybrat koncový bod přístupu a tuto možnost nemůžete později změnit. |
   | **Virtuální síť** | Yes | <*Azure – Virtual-Network-Name*> | Virtuální síť Azure, do které chcete vložit své prostředí, aby měly aplikace logiky v tomto prostředí přístup k vaší virtuální síti. Pokud nemáte síť, [vytvořte nejdřív virtuální síť Azure](../virtual-network/quick-create-portal.md). <p><p>**Důležité**: Toto vkládání můžete provést *jenom* při vytváření ISE. |
   | **Podsítě** | Yes | <*podsíť-Resource-list*> | ISE vyžaduje čtyři *prázdné* podsítě, které jsou potřeba pro vytváření a nasazování prostředků v ISE a používají je interní Logic Apps komponenty, jako jsou konektory a ukládání do mezipaměti pro výkon. <p>**Důležité**: [před pokračováním v provádění těchto kroků, abyste mohli vytvořit podsítě](#create-subnet), zkontrolujte, že jste zkontrolovali požadavky podsítě. |
   |||||

   <a name="create-subnet"></a>

   **Vytvoření podsítí**

   Vaše ISE vyžaduje čtyři *prázdné* podsítě, které jsou potřeba k vytváření a nasazování prostředků ve vašich ISE a jsou používané interními součástmi Logic Apps, jako jsou konektory a ukládání do mezipaměti pro výkon. Po vytvoření prostředí *nemůžete* tyto adresy podsítě změnit. Pokud ISE vytvoříte a nasadíte prostřednictvím Azure Portal, ujistěte se, že tyto podsítě nedelegujete na žádné služby Azure. Pokud však vytvoříte a nasadíte své ISE prostřednictvím šablony REST API, Azure PowerShell nebo Azure Resource Manager, je nutné [delegovat](../virtual-network/manage-subnet-delegation.md) jednu prázdnou podsíť na `Microsoft.integrationServiceEnvironment` . Další informace najdete v tématu [Přidání delegování podsítě](../virtual-network/manage-subnet-delegation.md).

   Každá podsíť musí splňovat tyto požadavky:

   * Používá název, který začíná abecedním znakem nebo podtržítkem (bez čísel), a nepoužívá tyto znaky: `<` , `>` , `%` , `&` , `\\` , `?` , `/` .

   * Používá [Formát směrování Inter-Domain ve třídě (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
   
     > [!IMPORTANT]
     >
     > Pro virtuální síť nebo podsítě nepoužívejte následující adresní prostory IP adres, protože je nelze přeložit pomocí Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Používá `/27` v adresním prostoru, protože každá podsíť vyžaduje 32 adres. Například `10.0.0.0/27` má 32 adres, protože 2<sup>(32-27)</sup> je 2<sup>5</sup> nebo 32. Další adresy neposkytují další výhody. Další informace o výpočtu adres najdete v tématu [bloky CIDR protokolu IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), musíte [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) , která má následující trasu a propojit ji s každou podsítí, kterou používá vaše ISE:

     **Název**: <*trasa-Name*><br>
     **Předpona adresy**: 0.0.0.0/0<br>
     **Další segment směrování**: Internet

   1. V seznamu **podsítě** vyberte **spravovat konfiguraci podsítě**.

      ![Správa konfigurace podsítě](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. V podokně **podsítě** vyberte **podsíť**.

      ![Přidat čtyři prázdné podsítě](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. V podokně **Přidat podsíť** zadejte tyto informace.

      * **Název**: název vaší podsítě.
      * **Rozsah adres (blok CIDR)**: rozsah vaší podsítě ve virtuální síti a ve formátu CIDR

      ![Přidat podrobnosti podsítě](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Až to bude hotové, vyberte **OK**.

   1. Opakujte tyto kroky pro tři další podsítě.

      > [!NOTE]
      > Pokud podsítě, které se pokoušíte vytvořit, nejsou platné, Azure Portal zobrazí zprávu, ale neblokuje průběh.

   Další informace o vytváření podsítí najdete v tématu [Přidání podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

1. Až Azure úspěšně ověří vaše ISE informace, vyberte **vytvořit**, například:

   ![Po úspěšném ověření vyberte vytvořit.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure začne nasazovat vaše prostředí, které obvykle trvá do dvou hodin, než se dokončí. V některých případech může nasazení trvat až čtyři hodiny. Pokud chcete zjistit stav nasazení, na panelu nástrojů Azure vyberte ikonu oznámení, která otevře podokno oznámení.

   ![Zkontroluje stav nasazení.](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Pokud se nasazení úspěšně dokončí, Azure zobrazí toto oznámení:

   ![Nasazení bylo úspěšné.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Jinak postupujte podle pokynů Azure Portal pro řešení potíží s nasazením.

   > [!NOTE]
   > Pokud se nasazení nepovede nebo odstraníte ISE, může Azure ve výjimečných případech trvat až hodinu nebo déle, než se vaše podsítě uvolní. Proto může být nutné počkat, než budete moci tyto podsítě znovu použít v jiném ISE.
   >
   > Pokud virtuální síť odstraníte, Azure obvykle trvá až dvě hodiny, než se uvolní vaše podsítě, ale tato operace může trvat delší dobu. 
   > Při odstraňování virtuálních sítí se ujistěte, že nejsou připojené žádné prostředky. 
   > Viz [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Pokud chcete zobrazit své prostředí, vyberte **Přejít k prostředku** , pokud Azure po dokončení nasazení automaticky nepřejde do vašeho prostředí.

1. Pro ISE, který má přístup k *externímu* koncovému bodu, je potřeba vytvořit skupinu zabezpečení sítě, pokud ji ještě nemáte, a přidat příchozí pravidlo zabezpečení, které povolí přenos z odchozích IP adres spravovaného konektoru. Chcete-li nastavit toto pravidlo, postupujte podle následujících kroků:

   1. V nabídce ISE v části **Nastavení** vyberte **vlastnosti**.

   1. V části **konektor odchozí IP adresy** zkopírujte rozsahy veřejných IP adres, které se také zobrazí v tomto článku, [omezení a konfigurace – odchozí IP adresy](../logic-apps/logic-apps-limits-and-config.md#outbound).

   1. Vytvořte skupinu zabezpečení sítě, pokud ji ještě nemáte.
   
   1. Na základě následujících informací přidejte příchozí pravidlo zabezpečení pro veřejné odchozí IP adresy, které jste zkopírovali. Další informace najdete v tématu [kurz: filtrování síťového provozu pomocí skupiny zabezpečení sítě pomocí Azure Portal](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Účel | Značka zdrojové služby nebo IP adresy | Zdrojové porty | Značka nebo IP adresa cílové služby | Cílové porty | Poznámky |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Povolit přenosy z odchozích IP adres konektoru | <*Connector – Public-Outbound-IP-addresss*> | * | Adresní prostor virtuální sítě s ISEmi podsítěmi | * | |
      |||||||

1. Pokud chcete zjistit stav sítě pro svůj ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > Pokud se vaše síť ISE stane ve špatném stavu, může se stát, že se interní App Service Environment (pomocného mechanismu), který používá váš ISE, stane také špatným. Pokud není stav pomocného mechanismu v pořádku po dobu delší než sedm dnů, pozastaví se pomocného mechanismu. Chcete-li tento stav vyřešit, ověřte nastavení virtuální sítě. Vyřešte všechny problémy, které najdete, a pak restartujte ISE. V opačném případě se po 90 dnech odstraní pozastavený pomocného mechanismu řízení a vaše ISE se stane nepoužitelnou. Ujistěte se, že udržujete ISE v dobrém stavu, abyste umožnili potřebný provoz.
   > 
   > Další informace najdete v těchto tématech:
   >
   > * [Přehled diagnostiky Azure App Service](../app-service/overview-diagnostics.md)
   > * [Protokolování zpráv pro Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

1. Pokud chcete začít vytvářet aplikace logiky a další artefakty v ISE, přečtěte si téma [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Po vytvoření ISE budou spravované konektory ISE k dispozici pro použití, ale nejsou automaticky zobrazeny v ovládacím prvku pro výběr konektoru v návrháři aplikace logiky. Než budete moct používat tyto konektory ISE, musíte je ručně [Přidat a nasadit do ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) , aby se zobrazily v návrháři aplikace logiky.

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
