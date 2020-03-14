---
title: Připojení k virtuálním sítím Azure pomocí ISE
description: Vytvoření prostředí ISE (Integration Service Environment), které má přístup k virtuálním sítím Azure (virtuální sítě) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fedc1f6ce8fbaeaf0d2cae3a1b04169192868e61
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270690"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps pomocí prostředí integrační služby (ISE)

Pro scénáře, ve kterých aplikace logiky a účty pro integraci potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md), vytvořte [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE je izolované prostředí, které využívá vyhrazené úložiště a další prostředky, které jsou udržovány odděleně od "globálního" víceklientské Logic Apps služby. Toto oddělení také snižuje vliv na výkon, který můžou mít jiní klienti Azure na výkon vašich aplikací. ISE také poskytuje vlastní statické IP adresy. Tyto IP adresy jsou oddělené od statických IP adres, které jsou sdílené pomocí Logic Apps ve veřejné víceklientské službě.

Při vytváření ISE Azure *vloží* do vaší virtuální sítě Azure, které ISE, nasadí službu Logic Apps do vaší virtuální sítě. Když vytvoříte aplikaci logiky nebo účet pro integraci, vyberte ISE jako své umístění. Vaše aplikace logiky nebo účet pro integraci pak můžou přímo přistupovat k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby, ve vaší virtuální síti.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Aby Logic Apps a integrační účty fungovaly společně v ISE, musí jako své umístění používat *stejný ISE* .

ISE má vyšší omezení doby trvání spuštění, uchovávání úložiště, propustnosti, požadavků HTTP a časových limitů odpovědí, velikostí zpráv a požadavků vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Další informace o ISEs najdete v tématu [přístup k prostředkům Azure Virtual Network z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

V tomto článku se dozvíte, jak tyto úlohy dokončit pomocí Azure Portal:

* Povolte přístup k vašemu ISE.
* Vytvořte své ISE.
* Přidejte do svého ISEu další kapacitu.

Můžete také vytvořit ISE pomocí REST API Logic Apps, včetně nastavení klíčů spravovaných zákazníkem:

* [Vytvoření prostředí ISE (Integration Service Environment) pomocí Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém umístění pro ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logic Apps, integrované triggery, integrované akce a konektory spouštěné ve vašem ISE používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](../logic-apps/logic-apps-pricing.md).

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Pokud nemáte virtuální síť, přečtěte si, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md).

  * Vaše virtuální síť musí mít čtyři *prázdné* podsítě pro vytváření a nasazování prostředků v ISE. Každá podsíť podporuje jinou komponentu Logic Apps, která se používá ve vašem ISE. Tyto podsítě můžete vytvořit předem nebo můžete počkat, dokud nevytvoříte ISE, kde můžete vytvářet podsítě ve stejnou dobu. Přečtěte si další informace o [požadavcích na podsíť](#create-subnet).

  * Názvy podsítí musí začínat znakem abecedy nebo podtržítkem a nesmí tyto znaky používat: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Pokud chcete nasadit ISE pomocí šablony Azure Resource Manager, nejprve se ujistěte, že delegujete jednu prázdnou podsíť do Microsoft. Logic/integrationServiceEnvironment. Toto delegování nemusíte dělat při nasazení prostřednictvím Azure Portal.

  * Ujistěte se, že vaše virtuální síť [umožňuje přístup k vašemu ISE](#enable-access) , takže vaše ISE může správně fungovat a zůstat přístupná.

  * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), který poskytuje privátní připojení ke cloudovým službám Microsoftu, které usnadňuje poskytovatel připojení, musíte [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) , která má následující trasu a propojit ji s každou podsítí, kterou používá vaše ISE:

    **Název**: <*trasa-Name*><br>
    **Předpona adresy**: 0.0.0.0/0<br>
    **Další segment směrování**: Internet

* Pokud chcete pro službu Azure Virtual Network používat vlastní servery DNS, [nastavte tyto servery pomocí následujících kroků](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ještě před nasazením ISE do virtuální sítě. Další informace o správě nastavení serveru DNS najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Pokud změníte nastavení serveru DNS nebo serveru DNS, musíte restartovat ISE, aby ISE mohl tyto změny vyzvednout. Další informace najdete v tématu [restart vaší ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Povolit přístup pro ISE

Pokud používáte ISE s virtuální sítí Azure, běžný problém instalace je jeden nebo více blokovaných portů. Konektory používané pro vytváření připojení mezi ISE a cílovými systémy mohou mít také vlastní požadavky na porty. Pokud například komunikujete se systémem FTP pomocí konektoru FTP, musí být port, který používáte v systému FTP, k dispozici, například port 21 pro odesílání příkazů.

Abyste se ujistili, že je váš ISE přístupný a že aplikace logiky v této ISE můžou komunikovat napříč všemi podsítěmi ve vaší virtuální síti, [otevřete porty popsané v této tabulce pro každou podsíť](#network-ports-for-ise). Pokud některé požadované porty nejsou k dispozici, vaše ISE nebude fungovat správně.

* Pokud máte několik instancí ISE, které potřebují přístup k jiným koncovým bodům s omezeními IP adres, nasaďte [Azure firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic) do své virtuální sítě a přesměrujte odchozí přenosy přes tuto bránu firewall nebo síťové virtuální zařízení. Pak můžete [nastavit jednu, odchozí, veřejnou, statickou a předvídatelná IP adresu](connect-virtual-network-vnet-set-up-single-ip-address.md) , kterou můžou všechny instance ISE ve vaší virtuální síti používat ke komunikaci s cílovými systémy. Tímto způsobem není nutné nastavovat další otevřená brána firewall v těchto cílových systémech pro každý ISE.

   > [!NOTE]
   > Tento postup můžete použít pro jeden ISE v případě, že váš scénář vyžaduje omezení počtu IP adres, které potřebují přístup. Zvažte, jestli další náklady na zařízení s bránou firewall nebo virtuální sítí jsou pro váš scénář smysluplné. Přečtěte si další informace o [cenách Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Pokud jste vytvořili novou virtuální síť Azure a podsítě bez jakýchkoli omezení, nemusíte ve virtuální síti nastavovat [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/security-overview.md#network-security-groups) , abyste mohli řídit provoz napříč podsítěmi.

* V existující virtuální síti můžete *volitelně* nastavit skupin zabezpečení sítě [filtrováním síťového provozu mezi podsítěmi](../virtual-network/tutorial-filter-network-traffic.md). Pokud chcete přejít k této trase nebo pokud již používáte skupin zabezpečení sítě, ujistěte se, že jste [v této tabulce otevřeli porty](#network-ports-for-ise) ve virtuální síti, ve které máte skupin zabezpečení sítě nebo chcete nastavit skupin zabezpečení sítě.

  > [!NOTE]
  > Pokud používáte [pravidla zabezpečení NSG](../virtual-network/security-overview.md#security-rules), *musíte použít protokoly TCP i UDP* . Pravidla zabezpečení NSG popisují porty, které je třeba otevřít pro IP adresy, které potřebují přístup k těmto portům. Ujistěte se, že všechny brány firewall, směrovače nebo jiné položky, které existují mezi těmito koncovými body, také udržují tyto porty dostupné pro tyto IP adresy.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Síťové porty používané vaším ISE

Tato tabulka popisuje porty ve vaší virtuální síti Azure, kterou používá ISE, a kde se tyto porty používají. V zájmu snížení složitosti při vytváření pravidel zabezpečení představuje [značka služby](../virtual-network/service-tags-overview.md) v tabulce skupiny předpon IP adres pro konkrétní službu Azure.

> [!IMPORTANT]
> Zdrojové porty jsou dočasné, takže se ujistěte, že jste je nastavili tak, aby `*` pro všechna pravidla. Pokud je uvedeno jinak, interní ISE a externí ISE odkazují na [koncový bod, který je vybraný při vytváření ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Další informace najdete v tématu [přístup ke koncovému bodu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Účel | Směr | Cílové porty | Značka zdrojové služby | Značka cílové služby | Poznámky |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Komunikace mezi podsítí v rámci virtuální sítě | Příchozí & odchozí | * | Adresní prostor pro virtuální síť s podsítěmi ISE | Adresní prostor pro virtuální síť s podsítěmi ISE | Vyžaduje se pro tok provozu *mezi* podsítěmi ve vaší virtuální síti. <p><p>**Důležité**: Pokud chcete provoz směrovat mezi *součásti* v každé podsíti, ujistěte se, že jste otevřeli všechny porty v každé podsíti. |
| Komunikace s aplikací logiky | Příchozí | 443 | Interní ISE: <br>VirtualNetwork <p><p>Externí ISE: <br>Internet <br>(viz sloupec **poznámky** ) | VirtualNetwork | Místo toho, abyste mohli používat značku **internetové** služby, můžete zadat zdrojovou IP adresu pro počítač nebo službu, která ve vaší aplikaci logiky volá jakékoli triggery žádostí nebo Webhooky. <p><p>**Důležité**: zavřením nebo blokováním tohoto portu zabráníte volání http do Logic Apps, které mají aktivační události požadavků. |
| Historie spuštění aplikace logiky | Příchozí | 443 | Interní ISE: <br>VirtualNetwork <p><p>Externí ISE: <br>Internet <br>(viz sloupec **poznámky** ) | VirtualNetwork | Místo toho, abyste mohli používat značku **internetové** služby, můžete zadat zdrojovou IP adresu pro počítač nebo službu, ze které chcete zobrazit historii spuštění vaší aplikace logiky. <p><p>**Důležité**: Přestože zavřením nebo blokováním tohoto portu nebráníte zobrazení historie spuštění, nemůžete zobrazit vstupy a výstupy pro každý krok v této historii spuštění. |
| Návrhář Logic Apps – dynamické vlastnosti | Příchozí | 454 | LogicAppsManagement | VirtualNetwork | Požadavky pocházejí z [příchozích](../logic-apps/logic-apps-limits-and-config.md#inbound) IP adres koncového bodu přístupu Logic Apps pro tuto oblast. |
| Nasazení konektoru | Příchozí | 454 | AzureConnectors | VirtualNetwork | Vyžaduje se pro nasazení a aktualizaci konektorů. Zavřením nebo blokováním tohoto portu dojde k selhání nasazení ISE a znemožňuje aktualizace a opravy konektoru. |
| Kontrolu stavu sítě | Příchozí | 454 | LogicApps | VirtualNetwork | Požadavky pocházejí z koncového bodu přístupu Logic Apps pro [příchozí](../logic-apps/logic-apps-limits-and-config.md#inbound) i [odchozí](../logic-apps/logic-apps-limits-and-config.md#outbound) IP adresy pro tuto oblast. |
| Závislost správy App Service | Příchozí | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Komunikace z Azure Traffic Manager | Příchozí | Interní ISE: 454 <p><p>Externí ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| Koncový bod správy API Management | Příchozí | 3443 | APIManagement | VirtualNetwork | |
| Nasazení zásad konektoru | Příchozí | 3443 | APIManagement | VirtualNetwork | Vyžaduje se pro nasazení a aktualizaci konektorů. Zavřením nebo blokováním tohoto portu dojde k selhání nasazení ISE a znemožňuje aktualizace a opravy konektoru. |
| Komunikace z aplikace logiky | Odchozí | 80, 443 | VirtualNetwork | Liší se v závislosti na cíli. | Koncové body pro externí službu, se kterou vaše aplikace logiky potřebuje komunikovat. |
| Azure Active Directory | Odchozí | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Správa připojení | Odchozí | 443 | VirtualNetwork  | AppService | |
| Publikování diagnostických protokolů & metriky | Odchozí | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Storage závislost | Odchozí | 80, 443, 445 | VirtualNetwork | Storage | |
| Závislost Azure SQL | Odchozí | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Odchozí | 1886 | VirtualNetwork | AzureMonitor | Požadováno pro publikování stavu do Resource Health |
| Závislost z protokolu k zásadám centra událostí a agentům monitorování | Odchozí | 5672 | VirtualNetwork | Centrum událostí | |
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi rolí | Příchozí <br>Odchozí | 6379 – 6383 | VirtualNetwork | VirtualNetwork | Aby ISE mohli pracovat s Azure cache pro Redis, musíte tyto [odchozí a příchozí porty, které jsou popsané v mezipaměti Azure, otevřít i pro Redis Nejčastější dotazy](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření ISE

1. V [Azure Portal](https://portal.azure.com)do hlavního pole Azure Search jako filtr zadejte `integration service environments` a vyberte **prostředí integrační služby**.

   ![Najde a vybere "prostředí integrační služby".](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. V podokně **prostředí integrační služby** vyberte **Přidat**.

   ![Najde a vybere "prostředí integrační služby".](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Zadejte tyto údaje pro vaše prostředí a pak vyberte **zkontrolovat + vytvořit**, například:

   ![Zadání podrobností prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požadováno | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Předplatné Azure, které se má použít pro vaše prostředí |
   | **Skupina prostředků** | Ano | <*Azure-Resource-Group-name*> | Nová nebo existující skupina prostředků Azure, ve které chcete vytvořit prostředí. |
   | **Název prostředí integrační služby** | Ano | <*prostředí – název*> | Název ISE, který může obsahovat jenom písmena, číslice, spojovníky (`-`), podtržítka (`_`) a tečky (`.`). |
   | **Umístění** | Ano | <*Azure-Datacenter – oblast*> | Oblast datacenter Azure, kde se má vaše prostředí nasadit |
   | **SKU** | Ano | **Premium** nebo **Developer (bez smlouvy SLA)** | SKU ISE, která se má vytvořit a použít. Rozdíly mezi těmito SKU najdete v tématu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Důležité**: Tato možnost je k dispozici pouze při vytváření ISE a nedá se změnit později. |
   | **Další kapacita** | Nárok <br>Ano <p><p>Maximalizac <br>Nelze použít | Nárok <br>0 až 10 <p><p>Maximalizac <br>Nelze použít | Počet dalších jednotek zpracování, které se mají použít pro tento prostředek ISE. Pokud chcete přidat kapacitu po vytvoření, přečtěte si téma [Přidání kapacity ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Koncový bod přístupu** | Ano | **Interní** nebo **externí** | Typ koncových bodů přístupu, které se mají použít pro ISE. Tyto koncové body určují, jestli triggery Request nebo Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě. <p><p>Váš výběr také ovlivňuje způsob zobrazení a přístupu ke vstupům a výstupům v historii spuštění aplikace logiky. Další informace najdete v tématu [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Důležité**: Tato možnost je k dispozici pouze při vytváření ISE a nedá se změnit později. |
   | **Virtuální síť** | Ano | <*Azure-Virtual-Network-name*> | Virtuální síť Azure, do které chcete vložit své prostředí, aby měly aplikace logiky v tomto prostředí přístup k vaší virtuální síti. Pokud nemáte síť, [vytvořte nejdřív virtuální síť Azure](../virtual-network/quick-create-portal.md). <p><p>**Důležité**: Toto vkládání můžete provést *jenom* při vytváření ISE. |
   | **Podsítě** | Ano | <> *seznam prostředků podsítě* | ISE vyžaduje čtyři *prázdné* podsítě pro vytváření a nasazování prostředků ve vašem prostředí. Chcete-li vytvořit každou podsíť, [postupujte podle kroků v této tabulce](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Vytvořit podsíť**

   Pro vytváření a nasazování prostředků ve vašem prostředí ISE potřebuje čtyři *prázdné* podsítě, které nejsou delegované na žádnou službu. Každá podsíť podporuje jinou komponentu Logic Apps, která se používá ve vašem ISE. Po vytvoření prostředí *nemůžete* tyto adresy podsítě změnit. Každá podsíť musí splňovat tyto požadavky:

   * Má název, který začíná abecedním znakem nebo podtržítkem (bez čísel), a nepoužívá tyto znaky: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.

   * Používá [Formát CIDR (Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) a adresní prostor třídy B.

   * Používá minimálně `/27` v adresním prostoru, protože každá podsíť vyžaduje *minimálně*32 adres. Příklad:

     * `10.0.0.0/28` má pouze 16 adres a je příliš malý, protože 2<sup>(32-28)</sup> je 2<sup>4</sup> nebo 16.

     * `10.0.0.0/27` má 32 adres, protože 2<sup>(32-27)</sup> je 2<sup>5</sup> nebo 32.

     * `10.0.0.0/24` má 256 adres, protože 2<sup>(32-24)</sup> je 2<sup>8</sup> nebo 256. Víc adres ale neposkytuje žádné další výhody.

     Další informace o výpočtu adres najdete v tématu [bloky CIDR protokolu IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

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
      * **Rozsah adres (blok CIDR)** : rozsah vaší podsítě ve virtuální síti a ve formátu CIDR

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
   > Pokud se nasazení nepovede nebo odstraníte ISE, může Azure trvat až hodinu, než se vaše podsítě uvolní. Tato prodleva znamená, že možná budete muset počkat, než tyto podsítě znovu použijete v jiné ISE.
   >
   > Pokud virtuální síť odstraníte, Azure obvykle trvá až dvě hodiny, než se uvolní vaše podsítě, ale tato operace může trvat delší dobu. 
   > Při odstraňování virtuálních sítí se ujistěte, že nejsou připojené žádné prostředky. 
   > Viz [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Pokud chcete zobrazit své prostředí, vyberte **Přejít k prostředku** , pokud Azure po dokončení nasazení automaticky nepřejde do vašeho prostředí.

1. Pokud chcete zjistit stav sítě pro svůj ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Pokud chcete začít vytvářet aplikace logiky a další artefakty v ISE, přečtěte si téma [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Spravované konektory ISE, které jsou k dispozici po vytvoření ISE, se neobjeví automaticky v dialogu pro výběr konektoru v návrháři aplikace logiky. Než budete moct používat tyto konektory ISE, musíte [tyto konektory do ISE přidat](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) ručně, aby se zobrazily v návrháři aplikace logiky.

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
