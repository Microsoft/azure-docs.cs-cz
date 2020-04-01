---
title: Připojení k virtuálním sítím Azure pomocí služby ISE
description: Vytvoření prostředí integrační ch služeb (ISE), které bude mít přístup k virtuálním sítím Azure (VNET) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 6683c1b78b0e7ecba162026708c83843e2c08180
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478883"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps pomocí prostředí integrační služby (ISE)

Pro scénáře, kde vaše aplikace logiky a účty integrace potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md), [ *vytvořte prostředí služby integrace* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ISE je izolované prostředí, které používá vyhrazené úložiště a další prostředky, které jsou udržovány odděleně od "globální" služby Logic Apps pro více klientů. Toto oddělení také snižuje jakýkoli dopad, který ostatní tenantové Azure může mít na výkon vašich aplikací. ISE také poskytuje vlastní statické IP adresy. Tyto IP adresy jsou oddělené od statických IP adres, které jsou sdíleny aplikacemi logiky ve veřejné službě s více klienty.

Když vytvoříte ISE, Azure vloží tuto *službu* ISE do vaší virtuální sítě Azure, která pak nasadí službu Logic Apps do vaší virtuální sítě. Při vytváření aplikace logiky nebo účtu integrace vyberte ise jako jejich umístění. Vaše aplikace logiky nebo účet integrace pak můžete přímo přistupovat k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby, ve vaší virtuální síti.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Aby aplikace logiky a účty integrace spolupracovaly v ise, obě musí používat *stejnou ise* jako jejich umístění.

Služba ISE zvýšila limity pro dobu trvání spuštění, uchovávání úložiště, propustnost, časové limity požadavků a odpovědí HTTP, velikosti zpráv a vlastní požadavky konektoru. Další informace najdete v [tématu Limity a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Další informace o ises najdete [v tématu přístup k prostředkům virtuální sítě Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Tento článek ukazuje, jak tyto úkoly dokončit pomocí portálu Azure:

* Povolte přístup pro svou ise.
* Vytvořte si ISE.
* Přidejte další kapacitu do vašeho ISE.

ISE můžete také vytvořit pomocí [ukázkové šablony rychlého startu Nástroje pro prostředky Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) nebo pomocí rozhraní API REST logic apps, včetně nastavení klíčů spravovaných zákazníkem:

* [Vytvoření prostředí integrační služby (ISE) pomocí rozhraní API REST logic apps](../logic-apps/create-integration-service-environment-rest-api.md)
* [Nastavení klíčů spravovaných zákazníkem pro šifrování dat v klidovém stavu pro společnosti ISE](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplikace logiky, integrované aktivační události, předdefinované akce a konektory, které běží ve vaší službě ISE, používají cenový plán odlišný od cenového plánu založeného na spotřebě. Informace o tom, jak fungují ceny a fakturace pro ises, najdete v tématu [Cenové homodelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps pricing](../logic-apps/logic-apps-pricing.md).

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Pokud nemáte virtuální síť, přečtěte si, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md).

  * Vaše virtuální síť musí mít čtyři *prázdné* podsítě pro vytváření a nasazování prostředků ve vaší ISE. Každá podsíť podporuje jinou komponentu Logic Apps, která se používá ve vaší službě ISE. Tyto podsítě můžete vytvořit předem nebo můžete počkat, až vytvoříte ise, kde můžete vytvářet podsítě současně. Další informace o [požadavcích na podsíť](#create-subnet).

  * Názvy podsítí musí začínat abecedním znakem nebo podtržítkem `?`a `/`nelze je používat: `<` `>`, , , `%` `&`, `\\`, . 
  
  * Pokud chcete nasadit ISE prostřednictvím šablony Azure Resource Manager, nejprve se ujistěte, že delegovat jednu prázdnou podsíť Microsoft.Logic/integrationServiceEnvironment. Toto delegování nemusíte provádět při nasazení prostřednictvím portálu Azure.

  * Ujistěte se, že vaše virtuální síť [umožňuje přístup pro vaše ISE](#enable-access) tak, aby vaše ISE může pracovat správně a zůstat přístupné.

  * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), který poskytuje privátní připojení ke cloudovým službám Microsoftu, které usnadňuje poskytovatel připojení, je nutné [vytvořit směrovací tabulku,](../virtual-network/manage-route-table.md) která má následující trasu a propojit tuto tabulku s každou podsítí používanou službou ISE:

    **Název**: <*název trasy*><br>
    **Předpona adresy**: 0.0.0.0/0<br>
    **Další hop**: Internet

* Pokud chcete pro virtuální síť Azure používat vlastní servery DNS, [nastavte tyto servery takto před nasazením](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) služby ISE do virtuální sítě. Další informace o správě nastavení serveru DNS naleznete v [tématu Vytvoření, změna nebo odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Pokud změníte nastavení serveru DNS nebo serveru DNS, je nutné restartovat službu ISE, aby služba ISE mohla tyto změny vyzvednout. Další informace naleznete [v tématu Restart ise](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Povolení přístupu pro prostředí integrační služby (ISE)

Při použití ise s virtuální sítí Azure, běžné řešení problému s jedním nebo více blokovaných portů. Konektory, které používáte pro vytváření připojení mezi ise a cílových systémů může mít také své vlastní požadavky na port. Pokud například komunikujete se systémem FTP pomocí konektoru FTP, musí být k dispozici port, který používáte v systému FTP, například port 21 pro odesílání příkazů.

Chcete-li se ujistit, že je vaše služba ISE přístupná a že aplikace logiky v této službě ISE mohou komunikovat v jednotlivých podsítích ve vaší virtuální síti, [otevřete porty popsané v této tabulce pro každou podsíť](#network-ports-for-ise). Pokud nejsou k dispozici požadované porty, nebude vaše ise fungovat správně.

* Pokud máte více instancí ISE, které potřebují přístup k jiným koncovým bodům, které mají omezení IP, nasaďte [bránu Azure Firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic) do virtuální sítě a směrujte odchozí provoz přes tuto bránu firewall nebo síťové virtuální zařízení. Potom můžete [nastavit jednu, odchozí, veřejnou, statickou a předvídatelnou IP adresu,](connect-virtual-network-vnet-set-up-single-ip-address.md) kterou mohou všechny instance ISE ve vaší virtuální síti použít ke komunikaci s cílovými systémy. Tímto způsobem není třeba nastavit další otvory brány firewall v těchto cílových systémech pro každou ISE.

   > [!NOTE]
   > Tento přístup můžete použít pro jednu ise, pokud váš scénář vyžaduje omezení počtu ADRES IP, které potřebují přístup. Zvažte, zda dodatečné náklady na bránu firewall nebo zařízení virtuální sítě smysl pro váš scénář. Přečtěte si další informace o [cenách azure firewallu](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Pokud jste vytvořili novou virtuální síť Azure a podsítě bez jakýchkoli omezení, není nutné nastavit [skupiny zabezpečení sítě (NSGs)](../virtual-network/security-overview.md#network-security-groups) ve virtuální síti pro řízení provozu mezi podsítěmi.

* V existující virtuální síti můžete *volitelně* nastavit skupiny zabezpečení sítě [filtrováním síťových přenosů mezi podsítěmi](../virtual-network/tutorial-filter-network-traffic.md). Pokud chcete přejít touto cestou nebo pokud již používáte sítě zabezpečení sítě, ujistěte se, že [otevřete porty v této tabulce](#network-ports-for-ise) ve virtuální síti, kde máte sítě zabezpečení sítě nebo chcete nastavit sítě zabezpečení sítě.

  > [!NOTE]
  > Používáte-li [pravidla zabezpečení skupiny zabezpečení skupiny zabezpečení ,](../virtual-network/security-overview.md#security-rules)je třeba použít protokoly TCP *i* UDP. Pravidla zabezpečení skupiny zabezpečení nsg popisují porty, které je nutné otevřít pro adresy IP, které potřebují přístup k těmto portům. Ujistěte se, že všechny brány firewall, směrovače nebo jiné položky, které existují mezi těmito koncovými body také zachovat tyto porty přístupné pro tyto adresy IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Síťové porty používané vaší ise

Tato tabulka popisuje porty ve vaší virtuální síti Azure, které vaše ISE používá a kde se tyto porty používají. Chcete-li snížit složitost při vytváření pravidel zabezpečení, [značky služeb](../virtual-network/service-tags-overview.md) v tabulce představují skupiny předpon IP adres pro konkrétní službu Azure.

> [!IMPORTANT]
> Zdrojové porty jsou dočasné, proto se ujistěte, že jste je nastavili pro `*` všechna pravidla. Pokud je uvedeno, interní ISE a externí ISE odkazují na [koncový bod, který je vybrán při vytváření ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Další informace naleznete [v tématu Endpoint access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Účel | Směr | Cílové porty | Zdrojová značka servisu | Značka cílové služby | Poznámky |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Intersubnet komunikace v rámci vaší virtuální sítě | Odchozí & | * | Adresní prostor pro virtuální síť, která má podsítě ise | Adresní prostor pro virtuální síť, která má podsítě ise | Vyžadováno pro tok provozu *mezi* podsítěmi ve virtuální síti. <p><p>**Důležité:** Chcete-li, aby provoz plynul mezi *součástmi* v každé podsíti, ujistěte se, že otevřete všechny porty v rámci každé podsítě. |
| Komunikace s aplikací logiky | Příchozí | 443 | Interní ise: <br>VirtualNetwork <p><p>Externí ISE: <br>Internet <br>(viz sloupec **Poznámky)** | VirtualNetwork | Místo použití značky **služby Internet** můžete zadat zdrojovou IP adresu počítače nebo služby, která volá aktivační události libovolného požadavku nebo webhooky ve vaší aplikaci logiky. <p><p>**Důležité:** Zavření nebo blokování tohoto portu zabrání volání HTTP do aplikací logiky, které mají aktivační události požadavku. |
| Historie spuštění aplikace logiky | Příchozí | 443 | Interní ise: <br>VirtualNetwork <p><p>Externí ISE: <br>Internet <br>(viz sloupec **Poznámky)** | VirtualNetwork | Místo použití značky **služby Internet** můžete zadat zdrojovou IP adresu počítače nebo služby, ze které chcete zobrazit historii spuštění aplikace logiky. <p><p>**Důležité:** Přestože zavření nebo zablokování tohoto portu nezabrání zobrazení historie spuštění, nemůžete zobrazit vstupy a výstupy pro každý krok v této historii spuštění. |
| Návrhář aplikací logiky – dynamické vlastnosti | Příchozí | 454 | LogicAppsManagement | VirtualNetwork | Požadavky pocházejí z koncových adres [koncových](../logic-apps/logic-apps-limits-and-config.md#inbound) adres koncových bodů logic apps pro tuto oblast. |
| Nasazení konektoru | Příchozí | 454 | Konektory Azure | VirtualNetwork | Vyžadováno pro nasazení a aktualizaci konektorů. Zavření nebo zablokování tohoto portu způsobí selhání nasazení ise a zabrání aktualizacím nebo opravám konektorů. |
| Kontrola stavu sítě | Příchozí | 454 | LogicApps | VirtualNetwork | Požadavky pocházejí z koncového bodu přístupu aplikace Logic Apps pro [příchozí](../logic-apps/logic-apps-limits-and-config.md#inbound) i [odchozí](../logic-apps/logic-apps-limits-and-config.md#outbound) IP adresy pro tuto oblast. |
| Závislost správy služby App Service | Příchozí | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Komunikace z Azure Traffic Manageru | Příchozí | Interní ISE: 454 <p><p>Externí ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| Api Management - koncový bod pro správu | Příchozí | 3443 | APIManagement | VirtualNetwork | |
| Nasazení zásad konektoru | Příchozí | 3443 | APIManagement | VirtualNetwork | Vyžadováno pro nasazení a aktualizaci konektorů. Zavření nebo zablokování tohoto portu způsobí selhání nasazení ise a zabrání aktualizacím nebo opravám konektorů. |
| Komunikace z aplikace logiky | Odchozí | 80, 443 | VirtualNetwork | Liší se v závislosti na destinaci | Koncové body pro externí službu, se kterou vaše aplikace logiky potřebuje komunikovat. |
| Azure Active Directory | Odchozí | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Správa připojení | Odchozí | 443 | VirtualNetwork  | AppService | |
| Publikovat diagnostické protokoly & metriky | Odchozí | 443 | VirtualNetwork  | AzureMonitor | |
| Závislost azure úložiště | Odchozí | 80, 443, 445 | VirtualNetwork | Storage | |
| Závislost Azure SQL | Odchozí | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Odchozí | 1886 | VirtualNetwork | AzureMonitor | Požadováno pro publikování stavu do stavu zdroje |
| Závislost z protokolu na agenta centra událostí a monitorování | Odchozí | 5672 | VirtualNetwork | Centrum událostí | |
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi rolí | Příchozí <br>Odchozí | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Aby služba ISE fungovala s Azure Cache for Redis, musíte také otevřít tyto [odchozí a příchozí porty popsané v nejčastějších dotazech k Azure Cache for Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření prostředí integrační služby (ISE)

1. Na [webu Azure Portal](https://portal.azure.com)zadejte `integration service environments` do hlavního vyhledávacího pole Azure jako filtr a vyberte prostředí **integračních služeb**.

   ![Najít a vybrat "Prostředí integračních služeb"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. V podokně **Prostředí integračních služeb** vyberte **přidat**.

   ![Najít a vybrat "Prostředí integračních služeb"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Zadejte tyto podrobnosti pro své prostředí a pak vyberte **Zkontrolovat + vytvořit**, například:

   ![Poskytněte podrobnosti o prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*Název předplatného Azure*> | Předplatné Azure, které se používá pro vaše prostředí |
   | **Skupina prostředků** | Ano | <*Název skupiny Azure-resource-group*> | Nová nebo existující skupina prostředků Azure, ve které chcete vytvořit prostředí |
   | **Název prostředí integrační služby** | Ano | <*název prostředí*> | Název ise, který může obsahovat pouze písmena, čísla, pomlčky (`-`), podtržítka (`_`) a tečky (`.`). |
   | **Umístění** | Ano | <*Azure-datacenter-oblast*> | Oblast datového centra Azure, kde se nasadí vaše prostředí |
   | **Sku** | Ano | **Premium** nebo **vývojář (bez SLA)** | Skladová položka ise k vytvoření a použití. Rozdíly mezi těmito sku, naleznete v tématu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Důležité:** Tato možnost je k dispozici pouze při vytváření služby ISE a nelze ji později změnit. |
   | **Dodatečná kapacita** | Premium: <br>Ano <p><p>Vývojář: <br>Neuvedeno | Premium: <br>0 až 10 <p><p>Vývojář: <br>Neuvedeno | Počet dalších jednotek zpracování, které mají být pro tento prostředek ISE určeny. Pokud chcete po vytvoření přidat kapacitu, přečtěte si informace o [přidání kapacity ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Koncový bod aplikace Access** | Ano | **Interní** nebo **externí** | Typ koncových bodů přístupu, které chcete použít pro ise. Tyto koncové body určují, jestli aktivační události požadavku nebo webhooku v aplikacích logiky ve vaší službě ISE můžou přijímat volání mimo vaši virtuální síť. <p><p>Váš výběr také ovlivňuje způsob, jakým můžete zobrazit a přistupovat k vstupům a výstupům v aplikaci logiky spustí historii. Další informace naleznete v tématu [ISE přístup ke koncovému bodu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Důležité:** Tato možnost je k dispozici pouze při vytváření služby ISE a nelze ji později změnit. |
   | **Virtuální síť** | Ano | <*Název virtuální sítě Azure*> | Virtuální síť Azure, do které chcete vložit vaše prostředí, aby aplikace logiky v tomto prostředí měly přístup k vaší virtuální síti. Pokud nemáte síť, [vytvořte nejprve virtuální síť Azure](../virtual-network/quick-create-portal.md). <p><p>**Důležité:** Tuto injekci můžete provést *pouze* při vytváření ISE. |
   | **Podsítí** | Ano | <*seznam zdrojů podsítě*> | ISE vyžaduje čtyři *prázdné* podsítě pro vytváření a nasazování prostředků ve vašem prostředí. Chcete-li vytvořit jednotlivé podsítě, [postupujte podle kroků v této tabulce](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Vytvoření podsítě**

   K vytvoření a nasazení prostředků ve vašem prostředí potřebuje ise čtyři *prázdné* podsítě, které nejsou delegovány na žádnou službu. Každá podsíť podporuje jinou komponentu Logic Apps, která se používá ve vaší službě ISE. Tyto adresy podsítí *nelze* po vytvoření prostředí změnit. Každá podsíť musí splňovat tyto požadavky:

   * Má název začínající abecedním znakem nebo podtržítkem (bez čísel) `&`a `\\` `?`nepoužívá `/`tyto znaky: `<`, , `>` `%`, , .

   * Používá [formát CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) a adresní prostor třídy B.

   * Používá alespoň `/27` a v adresním prostoru, protože každá podsíť vyžaduje *minimálně*32 adres . Například:

     * `10.0.0.0/28`má pouze 16 adres a je příliš malý, protože 2<sup>(32-28)</sup> je 2<sup>4</sup> nebo 16.

     * `10.0.0.0/27`má 32 adres, protože 2<sup>(32-27)</sup> je 2<sup>5</sup> nebo 32.

     * `10.0.0.0/24`má 256 adres, protože 2<sup>(32-24)</sup> je 2<sup>8</sup> nebo 256. Více adres však neposkytuje žádné další výhody.

     Další informace o výpočtu adres naleznete v [tématu Bloky CIDR i. IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), budete muset [vytvořit tabulku postupu,](../virtual-network/manage-route-table.md) která má následující trasu a propojit tuto tabulku s každou podsítí, která je používána vaší ISE:

     **Název**: <*název trasy*><br>
     **Předpona adresy**: 0.0.0.0/0<br>
     **Další hop**: Internet

   1. V seznamu **Podsítě** vyberte **Spravovat konfiguraci podsítě**.

      ![Správa konfigurace podsítě](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. V podokně **Podsítě** vyberte **Podsíť**.

      ![Přidání čtyř prázdných podsítí](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. V podokně **Přidat podsíť** zadejte tyto informace.

      * **Název**: Název podsítě
      * **Rozsah adres (blok CIDR):** Rozsah vaší podsítě ve virtuální síti a ve formátu CIDR

      ![Přidání podrobností podsítě](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Až to bude hotové, vyberte **OK**.

   1. Opakujte tyto kroky pro další tři podsítě.

      > [!NOTE]
      > Pokud podsítě, které se pokusíte vytvořit, nejsou platné, na webu Azure Portal se zobrazí zpráva, ale nezablokuje váš průběh.

   Další informace o vytváření podsítí naleznete [v tématu Přidání podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

1. Až Azure úspěšně ověří informace o službě ISE, vyberte **Vytvořit**, například:

   ![Po úspěšném ověření vyberte "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure začne nasazovat vaše prostředí, které obvykle trvá do dvou hodin. V některých případě může nasazení trvat až čtyři hodiny. Chcete-li zkontrolovat stav nasazení, vyberte na panelu nástrojů Azure ikonu oznámení, která otevře podokno oznámení.

   ![Zkontrolovat stav nasazení](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Pokud nasazení úspěšně skončí, Azure zobrazí toto oznámení:

   ![Nasazení proběhlo úspěšně.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   V opačném případě postupujte podle pokynů k webu Azure portal pro řešení potíží nasazení.

   > [!NOTE]
   > Pokud nasazení selže nebo odstraníte ise, Azure může trvat až hodinu před uvolněním podsítí. Toto zpoždění znamená, že budete muset počkat před opětovnou použitím těchto podsítí v jiné ise.
   >
   > Pokud odstraníte virtuální síť, Azure obvykle trvá až dvě hodiny před uvolněním podsítí, ale tato operace může trvat déle. 
   > Při mazání virtuálních sítí se ujistěte, že nejsou stále připojeny žádné prostředky. 
   > Viz [Odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Pokud chcete zobrazit své prostředí, vyberte **Přejít na prostředek,** pokud Azure po dokončení nasazení automaticky nepřejde do vašeho prostředí.

1. Chcete-li zkontrolovat stav sítě pro ise, naleznete [v tématu Správa prostředí integračních služeb](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Pokud chcete začít vytvářet aplikace logiky a další artefakty ve vaší službě ISE, přečtěte si informace o [přidání prostředků do prostředí integračních služeb](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Spravované konektory ISE, které budou k dispozici po vytvoření služby ISE, se automaticky nezobrazí ve výběru konektorů v Návrháři aplikací logiky. Před použitím těchto konektorů ISE je nutné [tyto konektory ručně přidat do ise, aby](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) se zobrazily v Návrháři aplikací logiky.

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integračních služeb](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Další informace o [virtuální síti Azure](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuálních sítí pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
