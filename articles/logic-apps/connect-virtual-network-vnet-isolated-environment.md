---
title: Připojení k virtuálním sítím Azure pomocí ISE
description: Vytvoření prostředí ISE (Integration Service Environment), které má přístup k virtuálním sítím Azure (virtuální sítě) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 6e301cf8e24ba740b56c63d7f95b50617ba25383
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792704"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps pomocí prostředí integrační služby (ISE)

Pro scénáře, ve kterých aplikace logiky a účty pro integraci potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md), vytvořte [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE je soukromé a izolované prostředí, které využívá vyhrazené úložiště a další prostředky, které jsou oddělené od veřejné nebo "globální" Logic Apps služby. Toto oddělení také snižuje vliv na výkon, který můžou mít jiní klienti Azure na výkon vašich aplikací.

Při vytváření ISE Azure *vloží* do vaší virtuální sítě Azure, které ISE, nasadí službu Logic Apps do vaší virtuální sítě. Když vytvoříte aplikaci logiky nebo účet pro integraci, vyberte ISE jako své umístění. Vaše aplikace logiky nebo účet pro integraci pak můžou přímo přistupovat k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby, ve vaší virtuální síti.

![Vybrat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Aby Logic Apps a integrační účty fungovaly společně v ISE, musí jako své umístění používat *stejný ISE* .

ISE má vyšší omezení doby trvání spuštění, uchovávání úložiště, propustnosti, požadavků HTTP a časových limitů odpovědí, velikostí zpráv a požadavků vlastních konektorů. Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md). Další informace o ISEs najdete v tématu [přístup k prostředkům Azure Virtual Network z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

V tomto článku se dozvíte, jak tyto úlohy provést:

* Povolte přístup k vašemu ISE.
* Vytvořte své ISE.
* Přidejte do svého ISEu další kapacitu.

> [!IMPORTANT]
> Logic Apps, integrované triggery, integrované akce a konektory spouštěné ve vašem ISE používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Pokud nemáte virtuální síť, přečtěte si, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md).

  * Vaše virtuální síť musí mít čtyři *prázdné* podsítě pro vytváření a nasazování prostředků v ISE. Tyto podsítě můžete vytvořit předem nebo můžete počkat, dokud nevytvoříte ISE, kde můžete vytvářet podsítě ve stejnou dobu. Přečtěte si další informace o [požadavcích na podsíť](#create-subnet).

  * Názvy podsítí musí začínat znakem abecedy nebo podtržítkem a nesmí tyto znaky používat: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Pokud chcete nasadit ISE pomocí šablony Azure Resource Manager, nejprve se ujistěte, že delegujete jednu prázdnou podsíť do Microsoft. Logic/integrationServiceEnvironment. Toto delegování nemusíte dělat při nasazení prostřednictvím Azure Portal.

  * Ujistěte se, že vaše virtuální síť [umožňuje přístup k vašemu ISE](#enable-access) , takže vaše ISE může správně fungovat a zůstat přístupná.

  * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), který poskytuje privátní připojení ke cloudovým službám Microsoftu, musíte [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) , která má následující trasu a propojit ji s každou podsítí, kterou používá vaše ISE:

    **Název**: <*trasa-Name*><br>
    **Předpona adresy**: 0.0.0.0/0<br>
    **Další segment směrování**: Internet

* Pokud chcete pro službu Azure Virtual Network používat vlastní servery DNS, [nastavte tyto servery pomocí následujících kroků](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ještě před nasazením ISE do virtuální sítě. Jinak budete muset pokaždé, když změníte server DNS, restartovat taky ISE.

  > [!IMPORTANT]
  > Pokud změníte nastavení serveru DNS po vytvoření ISE, ujistěte se, že jste restartovali ISE. Další informace o správě nastavení serveru DNS najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Povolit přístup pro ISE

Pokud používáte ISE s virtuální sítí Azure, běžný problém instalace je jeden nebo více blokovaných portů. Konektory používané pro vytváření připojení mezi ISE a cílovými systémy mohou mít také vlastní požadavky na porty. Pokud například komunikujete se systémem FTP pomocí konektoru FTP, musí být port, který používáte v systému FTP, k dispozici, například port 21 pro odesílání příkazů.

Abyste se ujistili, že je váš ISE přístupný a že aplikace logiky v této ISE můžou komunikovat napříč podsítěmi ve vaší virtuální síti, [otevřete porty v této tabulce](#network-ports-for-ise). Pokud některé požadované porty nejsou k dispozici, vaše ISE nebude fungovat správně.

* Pokud máte víc ISEs a vaše virtuální síť používá [Azure firewall](../firewall/overview.md) nebo [síťové virtuální zařízení](../virtual-network/virtual-networks-overview.md#filter-network-traffic), můžete pro komunikaci s cílovými systémy [nastavit jednu, odchozí, veřejnou a předvídatelná IP adresu](connect-virtual-network-vnet-set-up-single-ip-address.md) . Tímto způsobem nemusíte pro každý ISE v cílovém umístění nastavovat další otevřená brány firewall.

* Pokud jste vytvořili novou virtuální síť Azure a podsítě bez jakýchkoli omezení, nemusíte ve virtuální síti nastavovat [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/security-overview.md#network-security-groups) , abyste mohli řídit provoz napříč podsítěmi.

* V existující virtuální síti můžete *volitelně* nastavit skupin zabezpečení sítě [filtrováním síťového provozu mezi podsítěmi](../virtual-network/tutorial-filter-network-traffic.md). Pokud zvolíte tuto trasu, ujistěte se, že ve virtuální síti, ve které chcete nastavit skupin zabezpečení sítě, [otevřete porty v této tabulce](#network-ports-for-ise). Pokud používáte [pravidla zabezpečení NSG](../virtual-network/security-overview.md#security-rules), budete potřebovat protokoly TCP i UDP.

* Pokud jste již dříve existující skupin zabezpečení sítě, ujistěte se, že jste [v této tabulce otevřeli porty](#network-ports-for-ise). Pokud používáte [pravidla zabezpečení NSG](../virtual-network/security-overview.md#security-rules), budete potřebovat protokoly TCP i UDP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Síťové porty používané vaším ISE

Tato tabulka popisuje porty ve vaší virtuální síti Azure, kterou používá ISE, a kde se tyto porty používají. [Značky služby Správce prostředků](../virtual-network/security-overview.md#service-tags) představují skupinu předpon IP adres, které při vytváření pravidel zabezpečení pomůžou minimalizovat složitost.

> [!IMPORTANT]
> Zdrojové porty jsou dočasné, takže se ujistěte, že jste je nastavili tak, aby `*` pro všechna pravidla.
> Pro interní komunikaci v rámci podsítí vyžaduje vaše ISE otevírání všech portů v těchto podsítích.

| Účel | Směr | Cílové porty | Značka zdrojové služby | Značka cílové služby | Poznámky |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Komunikace z Azure Logic Apps | Odchozí | 80, 443 | VirtualNetwork | Internet | Port závisí na externí službě, se kterou služba Logic Apps komunikuje. |
| Azure Active Directory | Odchozí | 80, 443 | VirtualNetwork | Azureactivedirectory selhala | |
| Azure Storage závislost | Odchozí | 80, 443 | VirtualNetwork | Úložiště | |
| Komunikace mezi podsítěmi | Příchozí & odchozí | 80, 443 | VirtualNetwork | VirtualNetwork | Pro komunikaci mezi podsítěmi |
| Komunikace s Azure Logic Apps | Příchozí | 443 | Koncové body interního přístupu: <br>VirtualNetwork <p><p>Koncové body externího přístupu: <br>Internet <p><p>**Poznámka**: tyto koncové body odkazují na nastavení koncového bodu, které bylo [vybráno při vytváření ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Další informace najdete v tématu [přístup ke koncovému bodu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | IP adresa počítače nebo služby, která volá jakoukoli Trigger žádosti nebo Webhook, který existuje ve vaší aplikaci logiky. Zavření nebo blokování tohoto portu zabrání volání HTTP do Logic Apps s triggery žádostí. |
| Historie spuštění aplikace logiky | Příchozí | 443 | Koncové body interního přístupu: <br>VirtualNetwork <p><p>Koncové body externího přístupu: <br>Internet <p><p>**Poznámka**: tyto koncové body odkazují na nastavení koncového bodu, které bylo [vybráno při vytváření ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-environment). Další informace najdete v tématu [přístup ke koncovému bodu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | IP adresa počítače, ze kterého se zobrazuje historie spuštění aplikace logiky I když uzavření nebo blokování tohoto portu nebrání v zobrazení historie spuštění, nemůžete zobrazit vstupy a výstupy pro každý krok v této historii spuštění. |
| Správa připojení | Odchozí | 443 | VirtualNetwork  | AppService | |
| Publikování diagnostických protokolů & metriky | Odchozí | 443 | VirtualNetwork  | AzureMonitor | |
| Komunikace z Azure Traffic Manager | Příchozí | 443 | AzureTrafficManager | VirtualNetwork | |
| Návrhář Logic Apps – dynamické vlastnosti | Příchozí | 454 | IP adresy, které mají být povoleny, najdete ve sloupci poznámky. | VirtualNetwork | Požadavky pocházejí z [příchozích](../logic-apps/logic-apps-limits-and-config.md#inbound) IP adres koncového bodu přístupu Logic Apps pro tuto oblast. |
| Kontrolu stavu sítě | Příchozí | 454 | IP adresy, které mají být povoleny, najdete ve sloupci poznámky. | VirtualNetwork | Požadavky pocházejí z koncového bodu přístupu Logic Apps pro [příchozí](../logic-apps/logic-apps-limits-and-config.md#inbound) i [odchozí](../logic-apps/logic-apps-limits-and-config.md#outbound) IP adresy pro tuto oblast. |
| Závislost správy App Service | Příchozí | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Nasazení konektoru | Příchozí | 454 | AzureConnectors | VirtualNetwork | Nutné pro nasazení a aktualizaci konektorů. Zavřením nebo blokováním tohoto portu dojde k selhání nasazení ISE a znemožňuje aktualizace a opravy konektoru. |
| Nasazení zásad konektoru | Příchozí | 3443 | Internet | VirtualNetwork | Nutné pro nasazení a aktualizaci konektorů. Zavřením nebo blokováním tohoto portu dojde k selhání nasazení ISE a znemožňuje aktualizace a opravy konektoru. |
| Závislost Azure SQL | Odchozí | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Odchozí | 1886 | VirtualNetwork | AzureMonitor | Pro publikování stavu do Resource Health |
| Koncový bod správy API Management | Příchozí | 3443 | APIManagement | VirtualNetwork | |
| Závislost z protokolu k zásadám centra událostí a agentům monitorování | Odchozí | 5672 | VirtualNetwork | Centrum událostí | |
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi rolí | Příchozí <br>Odchozí | 6379-6383 | VirtualNetwork | VirtualNetwork | Aby ISE mohli pracovat s Azure cache pro Redis, musíte tyto [odchozí a příchozí porty, které jsou popsané v mezipaměti Azure, otevřít i pro Redis Nejčastější dotazy](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Nástroj pro vyrovnávání zatížení Azure | Příchozí | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření ISE

K vytvoření prostředí ISE (Integration Service Environment) použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)v hlavní nabídce Azure vyberte **vytvořit prostředek**.
Do vyhledávacího pole zadejte jako filtr "prostředí integrační služby".

   ![Vytvořit nový prostředek](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. V podokně vytváření prostředí integrační služby klikněte na možnost **vytvořit**.

   ![Vyberte vytvořit.](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Zadejte tyto údaje pro vaše prostředí a pak zvolte možnost **zkontrolovat + vytvořit**, například:

   ![Zadání podrobností prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Předplatné Azure, které se má použít pro vaše prostředí |
   | **Skupina prostředků** | Ano | <*Azure-Resource-Group-name*> | Skupina prostředků Azure, ve které chcete vytvořit prostředí |
   | **Název prostředí integrační služby** | Ano | <*prostředí – název*> | Název ISE, který může obsahovat jenom písmena, číslice, spojovníky (`-`), podtržítka (`_`) a tečky (`.`). |
   | **Umístění** | Ano | <*Azure-Datacenter – oblast*> | Oblast datacenter Azure, kde se má vaše prostředí nasadit |
   | **SKU** | Ano | **Premium** nebo **Developer (bez smlouvy SLA)** | SKU ISE, která se má vytvořit a použít. Rozdíly mezi těmito SKU najdete v tématu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Důležité**: Tato možnost je k dispozici pouze při vytváření ISE a nedá se změnit později. |
   | **Další kapacita** | Premium: <br>Ano <p><p>Developer: <br>Nevztahuje se | Premium: <br>0 až 10 <p><p>Developer: <br>Nevztahuje se | Počet dalších jednotek zpracování, které se mají použít pro tento prostředek ISE. Pokud chcete přidat kapacitu po vytvoření, přečtěte si téma [Přidání kapacity ISE](#add-capacity). |
   | **Koncový bod přístupu** | Ano | **Interní** nebo **externí** | Typ koncových bodů přístupu, který se má použít pro váš ISE, který určuje, jestli triggery Request nebo Webhooku v Logic Apps v ISE můžou přijímat volání z vnějšku vaší virtuální sítě. Typ koncového bodu má také vliv na přístup k vstupům a výstupům v historii spuštění aplikace logiky. Další informace najdete v tématu [přístup ke koncovému bodu](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Důležité**: Tato možnost je k dispozici pouze při vytváření ISE a nedá se změnit později. |
   | **Virtuální síť** | Ano | <*Azure-Virtual-Network-name*> | Virtuální síť Azure, do které chcete vložit své prostředí, aby měly aplikace logiky v tomto prostředí přístup k vaší virtuální síti. Pokud nemáte síť, [vytvořte nejdřív virtuální síť Azure](../virtual-network/quick-create-portal.md). <p>**Důležité**: Toto vkládání můžete provést *jenom* při vytváření ISE. |
   | **Podsítě** | Ano | <> *seznam prostředků podsítě* | ISE vyžaduje čtyři *prázdné* podsítě pro vytváření a nasazování prostředků ve vašem prostředí. Chcete-li vytvořit každou podsíť, [postupujte podle kroků v této tabulce](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Vytvořit podsíť**

   Pro vytváření a nasazování prostředků ve vašem prostředí ISE potřebuje čtyři *prázdné* podsítě, které nejsou delegované na žádnou službu. Po vytvoření prostředí *nemůžete* tyto adresy podsítě změnit.

   > [!IMPORTANT]
   > 
   > Názvy podsítí musí začínat znakem abecedy nebo podtržítkem (bez čísel) a tyto znaky nepoužívají: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.

   Každá podsíť musí taky splňovat tyto požadavky:

   * Používá [Formát CIDR (Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) a adresní prostor třídy B.

   * Používá *minimálně `/27`* v adresním prostoru, protože každá podsíť musí mít minimálně *32 adres* . Například:

     * `10.0.0.0/27` má 32 adres, protože 2<sup>(32-27)</sup> je 2<sup>5</sup> nebo 32.

     * `10.0.0.0/24` má 256 adres, protože 2<sup>(32-24)</sup> je 2<sup>8</sup> nebo 256.

     * `10.0.0.0/28` má pouze 16 adres a je příliš malý, protože 2<sup>(32-28)</sup> je 2<sup>4</sup> nebo 16.

     Další informace o výpočtu adres najdete v tématu [bloky CIDR protokolu IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), musíte [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) , která má následující trasu a propojit ji s každou podsítí, kterou používá vaše ISE:

     **Název**: <*trasa-Name*><br>
     **Předpona adresy**: 0.0.0.0/0<br>
     **Další segment směrování**: Internet

   1. V seznamu **podsítě** vyberte možnost **spravovat konfiguraci podsítě**.

      ![Správa konfigurace podsítě](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. V podokně **podsítě** vyberte možnost **podsíť**.

      ![Přidání podsítě](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. V podokně **Přidat podsíť** zadejte tyto informace.

      * **Název**: název vaší podsítě.
      * **Rozsah adres (blok CIDR)** : rozsah vaší podsítě ve virtuální síti a ve formátu CIDR

      ![Přidat podrobnosti podsítě](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Až budete hotovi, klikněte na **OK**.

   1. Opakujte tyto kroky pro tři další podsítě.

      > [!NOTE]
      > Pokud podsítě, které se pokoušíte vytvořit, nejsou platné, Azure Portal zobrazí zprávu, ale neblokuje průběh.

   Další informace o vytváření podsítí najdete v tématu [Přidání podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

1. Až Azure úspěšně ověří vaše ISE informace, vyberte **vytvořit**, třeba:

   ![Po úspěšném ověření vyberte vytvořit.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure spustí nasazení vašeho prostředí, ale tento proces *může* trvat až dvě hodiny, než se dokončí. Pokud chcete zjistit stav nasazení, na panelu nástrojů Azure vyberte ikonu oznámení, která otevře podokno oznámení.

   ![Zkontroluje stav nasazení.](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Pokud se nasazení úspěšně dokončí, Azure zobrazí toto oznámení:

   ![Nasazení bylo úspěšné.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Jinak postupujte podle pokynů Azure Portal pro řešení potíží s nasazením.

   > [!NOTE]
   > Pokud se nasazení nepovede nebo odstraníte ISE, může Azure trvat až hodinu, než se vaše podsítě uvolní. Tato prodleva znamená, že možná budete muset počkat, než tyto podsítě znovu použijete v jiné ISE.
   >
   > Pokud virtuální síť odstraníte, Azure obvykle trvá až dvě hodiny, než se uvolní vaše podsítě, ale tato operace může trvat delší dobu. 
   > Při odstraňování virtuálních sítí se ujistěte, že nejsou připojené žádné prostředky. 
   > Viz [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Pokud chcete zobrazit své prostředí, vyberte **Přejít k prostředku** , pokud Azure po dokončení nasazení automaticky nepřejde do vašeho prostředí.

1. Pokud chcete zjistit stav sítě pro svůj ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Pokud chcete začít vytvářet aplikace logiky a další artefakty v ISE, přečtěte si téma [Přidání artefaktů do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Přidat kapacitu ISE

Základní jednotka ISE úrovně Premium má pevnou kapacitu, takže pokud potřebujete větší propustnost, můžete přidat další jednotky škálování, a to buď během vytváření, nebo později. Automatické škálování můžete na základě metrik výkonu nebo na základě řady dalších jednotek zpracování. Pokud zvolíte automatické škálování na základě metrik, můžete si vybrat z různých kritérií a zadat mezní podmínky pro splnění těchto kritérií. SKU vývojáře neobsahuje možnost přidávat jednotky škálování.

1. V Azure Portal Najděte své ISE.

1. Pokud chcete zkontrolovat metriky využití a výkonu pro ISE, v hlavní nabídce vaší ISE vyberte **Přehled**.

   ![Zobrazit využití pro ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Automatické škálování nastavíte tak, že v části **Nastavení**vyberete horizontální navýšení **kapacity**. Na kartě **Konfigurovat** vyberte **Povolit automatické škálování**.

   ![Zapnout automatické škálování](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Pro **název nastavení automatického škálování**zadejte název vašeho nastavení.

1. Ve **výchozím** oddílu vyberte možnost škálování na **základě metriky** nebo **škály na konkrétní počet instancí**.

   * Pokud zvolíte možnost založený na instanci, zadejte počet jednotek zpracování mezi 0 a 10 včetně.

   * Zvolíte-li možnost založenou na metrikách, postupujte následovně:

     1. V části **pravidla** vyberte **Přidat pravidlo**.

     1. V podokně **pravidlo škálování** nastavte kritéria a akci, která se má provést při triggeru pravidla.

     1. Až skončíte, klikněte na **Přidat**.

1. Až skončíte s nastavením automatického škálování, uložte změny.

## <a name="next-steps"></a>Další kroky

* [Přidání artefaktů do prostředí integračních služeb](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Kontrolovat stav sítě pro prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informace o [integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
