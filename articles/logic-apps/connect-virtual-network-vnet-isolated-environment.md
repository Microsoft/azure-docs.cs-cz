---
title: Připojení k virtuálním sítím Azure z Azure Logic Apps prostřednictvím integrace služby prostředí (ISE)
description: Vytvořit prostředí integrační služby (ISE) aby logic apps a účty pro integraci měli přístup k virtuálním sítím Azure (Vnet), při zachování soukromých a izolované od veřejné nebo "globální" Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: b452485ccf235d1f245989e40840f2f0b3b2ae45
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544538"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps s využitím integrace služby prostředí (ISE)

Pro scénáře, ve kterém logic apps a účty pro integraci potřebují přístup k [virtuální síť Azure](../virtual-network/virtual-networks-overview.md), vytvořte [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE je privátní a izolované prostředí, která používá vyhrazeného úložiště a další prostředky, které jsou udržovány odděleně od veřejné nebo "globální" služba Logic Apps. Toto oddělení také snižuje předejde jiných tenantů Azure může mít na výkon vaší aplikace. Je vaše ISE *vložený* do ke službě Azure virtual network, která pak nasadí služba Logic Apps do vaší virtuální sítě. Při vytváření logiku aplikace nebo integračního účtu, vyberte tento ISE jako jejich umístění. Váš účet integrace nebo aplikace logiky můžete pak přímý přístup k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby ve vaší virtuální síti.

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Tento článek popisuje, jak k dokončení těchto úloh:

* Nastavení porty ve vaší virtuální sítí Azure tak prostřednictvím prostředí integrační služby (ISE) mohou projít provoz mezi podsítěmi ve virtuální síti.

* Vytvořte prostředí integrační služby (ISE).

* Vytvoření aplikace logiky, který může spouštět ve vaší ISE.

* Vytvoření účtu pro integraci pro vaše aplikace logiky v vaše ISE.

Další informace o prostředí integrační služby naleznete v tématu [přístup k prostředkům Azure Virtual Network v Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

  > [!IMPORTANT]
  > Logic apps, integrované aktivačních událostí, integrované akce a konektory, které běží ve vaší ISE cenový plán liší od založenou na skutečné spotřebě cenového plánu. Další informace najdete v tématu [ceny Logic Apps](../logic-apps/logic-apps-pricing.md).

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Pokud nemáte virtuální síť, zjistěte, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md). 

  * Vaše virtuální síť musí mít čtyři *prázdný* podsítě pro nasazení a vytváření prostředků ve vaší ISE. Tyto podsítě můžete vytvořit předem nebo můžete počkat, dokud nevytvoříte vašeho ISE, kde můžete vytvořit podsítě ve stejnou dobu. Další informace o [podsítě požadavky](#create-subnet). 
  
    > [!NOTE]
    > Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), která poskytuje privátní připojení ke cloudovým službám Microsoftu, je nutné [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) , který má následující směrování a propojit danou tabulku s každou podsíť, která používá vaše ISE:
    > 
    > **Název**: <*název trasy*><br>
    > **Předpona adresy**: 0.0.0.0/0<br>
    > **Směrování**: Internet

  * Ujistěte se, že vaše virtuální síť [zpřístupní tyto porty](#ports) tak, aby vaše ISE správně funguje a zůstane dostupný.

* Pokud chcete použít vlastní servery DNS pro vaši virtuální síť Azure, [nastavit tyto servery pomocí následujících kroků](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) před nasazením vaší ISE k virtuální síti. V opačném případě při každé změně vašeho serveru DNS, máte také restartovat ISE, což je funkce, která je k dispozici ve verzi public preview ISE.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Nastavte síťové porty

Prostředí integrační služby (ISE) fungovat správně a zůstat přístupný, musí mít určité porty, které jsou k dispozici ve vaší virtuální síti. Jinak pokud kterýkoliv z těchto portů je k dispozici, může ztratit přístup k vaší ISE, což může přestat pracovat. Při použití ISE ve virtuální síti běžný problém instalační program má jeden nebo více portů blokované. Pro připojení mezi vaší ISE a cílový systém konektor, který můžete použít také může mít vlastní požadavky na porty. Například pokud komunikovat s FTP systémem pomocí konektoru služby FTP, ujistěte se, že port, který používáte v systému FTP, jako je například 21 portu pro odesílání příkazů, je k dispozici.

K řízení provozu mezi podsítěmi virtuální sítě nasadíte kdekoli vašeho ISE, můžete nastavit [skupiny zabezpečení sítě](../virtual-network/security-overview.md) pro tyto podsítě podle [filtrování síťového provozu mezi podsítěmi](../virtual-network/tutorial-filter-network-traffic.md). Tyto tabulky popisují porty ve vaší virtuální síti, která používá vaše ISE a kde získat používají tyto porty. [Značky služeb Resource Manageru](../virtual-network/security-overview.md#service-tags) představuje skupinu předpon IP adres, které pomáhá minimalizovat složitost při vytváření pravidel zabezpečení.

> [!IMPORTANT]
> Pro interní komunikace uvnitř podsítě ISE vyžaduje otevření všechny porty v rámci těchto podsítí.

| Účel | Direction | Porty | Značka zdrojové služby | Značka cílové služby | Poznámky |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Sdělení Azure Logic Apps | Odchozí | 80 & 443 | VirtualNetwork | Internet | Port, který závisí na externí služby, se kterým komunikuje služba Logic Apps |
| Azure Active Directory | Odchozí | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Závislosti Azure Storage | Odchozí | 80 & 443 | VirtualNetwork | Úložiště | |
| Intersubnet komunikace | Příchozí a odchozí | 80 & 443 | VirtualNetwork | VirtualNetwork | Pro komunikaci mezi podsítěmi |
| Komunikace s Azure Logic Apps | Příchozí | 443 | Internet  | VirtualNetwork | IP adresa pro počítač nebo služba, která volá všechny triggeru požadavku nebo webhooku, která existuje ve vaší aplikaci logiky. Zavření nebo zablokování tohoto portu brání volání aplikace logiky s triggery požadavku HTTP.  |
| Historie spuštění aplikace logiky | Příchozí | 443 | Internet  | VirtualNetwork | IP adresa pro počítač, ze které můžete zobrazit aplikace logiky na historie spuštění. I když zavření nebo blokování tento port není zabránit zobrazení historie spuštění, nelze zobrazit vstupy a výstupy jednotlivých kroků v tomto historie spuštění. |
| Správa připojení | Odchozí | 443 | VirtualNetwork  | Internet | |
| Publikování diagnostické protokoly a metriky | Odchozí | 443 | VirtualNetwork  | AzureMonitor | |
| Komunikace z Azure Traffic Manageru | Příchozí | 443 | AzureTrafficManager | VirtualNetwork | |
| Návrhář pro Logic Apps – dynamické vlastnosti | Příchozí | 454 | Internet  | VirtualNetwork | Požadavky pocházejí z aplikace logiky [přístup ke koncovému bodu příchozí IP adresy v dané oblasti](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Závislost aplikace Service Management | Příchozí | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Nasazení konektoru | Příchozí | 454 & 3443 | Internet  | VirtualNetwork | Nezbytné pro nasazení a aktualizaci konektory. Zavření nebo zablokování tohoto portu způsobí, že nasazení ISE selhání a brání konektor aktualizace a opravy. |
| Azure závislost SQL | Odchozí | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Odchozí | 1886 | VirtualNetwork | Internet | Pro publikování stav Resource Health |
| API Management – koncový bod správy | Příchozí | 3443 | APIManagement  | VirtualNetwork | |
| Závislost z protokolu do zásady centra událostí a agenta monitorování | Odchozí | 5672 | VirtualNetwork  | EventHub | |
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi Role | Příchozí <br>Odchozí | 6379-6383 | VirtualNetwork  | VirtualNetwork | Navíc ISE pracovat s mezipamětí Azure Redis, je nutné otevřít tyto [odchozí a příchozí porty popsaných v ukládání do mezipaměti Azure redis Cache – nejčastější dotazy](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Nástroj pro vyrovnávání zatížení Azure | Příchozí | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření vašeho ISE

Pokud chcete vytvořit prostředí integrační služby (ISE), postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **vytvořit prostředek**.
Do vyhledávacího pole zadejte jako filtr "prostředí integrační služby".

   ![Vytvořit nový prostředek](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. V podokně vytvoření prostředí integrační služby, zvolte **vytvořit**.

   ![Zvolte možnost "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Zadejte tyto údaje pro vaše prostředí a pak zvolte **revize + vytvořit**, například:

   ![Zadejte podrobnosti prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požaduje se | Value | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Předplatné Azure, které můžete použít pro vaše prostředí |
   | **Skupina prostředků** | Ano | <*Azure-resource-group-name*> | Skupina prostředků Azure, ve kterém chcete vytvořit prostředí |
   | **Název prostředí integrační služby** | Ano | <*environment-name*> | Název prostředí |
   | **Umístění** | Ano | <*Azure-datacenter-region*> | Oblast datového centra Azure, jak nasadíte prostředí |
   | **Zvýšení kapacity** | Ano | 0 až 10 | Počet jednotek další zpracování pro tento prostředek ISE. Po vytvoření navyšovat kapacitu, najdete v článku [ISE přidat kapacitu](#add-capacity). |
   | **Virtuální síť** | Ano | <*Azure-virtual-network-name*> | Virtuální síť Azure ve které chcete vložit prostředí, takže aplikace logiky v daném prostředí mají přístup k vaší virtuální sítě. Pokud nemáte sítě, [nejprve vytvořte virtuální síť Azure](../virtual-network/quick-create-portal.md). <p>**Důležité**: Je možné *pouze* provádět tento vkládání při vytváření vašeho ISE. |
   | **Podsítě** | Ano | <*subnet-resource-list*> | ISE vyžaduje čtyři *prázdný* podsítě pro vytváření prostředků ve vašem prostředí. K vytvoření každé podsíti [, použijte postup v této tabulce](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Vytvoření podsítě**

   K vytváření prostředků ve vašem prostředí, musí vaše ISE čtyři *prázdný* podsítě, které nejsou delegovat na libovolnou službu. 
   Můžete *nelze* po vytvoření prostředí změnit tyto adresy podsítě. Každá podsíť musí splňovat tato kritéria:

   * Má název, který začíná znakem abecedy nebo podtržítkem a nemá tyto znaky: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Používá [notace CIDR (Classless Inter-Domain Routing) formát](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) a prostor adres třídy B.

   * Používá nejméně jednoho `/27` adresu místo, protože každá podsíť musí mít 32 adres jako *minimální*. Příklad:

     * `10.0.0.0/27` protože má 32 adres 2<sup>(32-27)</sup> je 2<sup>5</sup> nebo 32.

     * `10.0.0.0/24` protože má 256 adresy 2<sup>(32-24)</sup> je 2<sup>8</sup> nebo 256.

     * `10.0.0.0/28` má jenom 16 adresy a je příliš malá, protože 2<sup>(32-28)</sup> je 2<sup>4</sup> nebo 16.

     Další informace o výpočet adresy najdete v tématu [bloky IPv4 CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md), nezapomeňte [vytvořit směrovací tabulku](../virtual-network/manage-route-table.md) , který má následující směrování a propojit danou tabulku s každou podsíť, která používá vaše ISE:

     **Název**: <*název trasy*><br>
     **Předpona adresy**: 0.0.0.0/0<br>
     **Směrování**: Internet

   1. V části **podsítě** klikněte na položku **spravovat konfiguraci podsítě**.

      ![Spravovat konfiguraci podsítě](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Na **podsítě** podokně zvolte **podsítě**.

      ![Přidat podsíť](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Na **přidat podsíť** podokně zadejte tyto informace.

      * **Název**: Název pro vaši podsíť
      * **Rozsah adres (blok CIDR)**: Rozsah vaší podsítě ve virtuální síti a ve formátu CIDR

      ![Přidat podrobnosti o podsíti](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Jakmile budete hotovi, zvolte **OK**.

   1. Tento postup opakujte pro tři další podsítě.

      > [!NOTE]
      > Pokud podsítě, můžete zkusit vytvořit nejsou platné, na webu Azure portal zobrazí zprávu, ale nebude bránit pokroku ve studiu.

1. Když Azure úspěšně ověří vaše ISE informace, zvolte **vytvořit**, například:

   ![Po úspěšném ověření zvolte možnost "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure spouští vaše prostředí, ale tento proces nasazení *může* trvat až dvě hodiny před dokončením. 
   Chcete-li zkontrolovat stav nasazení, na panelu nástrojů Azure, vyberte ikonu oznámení, které se otevře podokno oznámení.

   ![Zkontrolujte stav nasazení](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Pokud se nasazení dokončí úspěšně, zobrazí toto upozornění Azure:

   ![Nasazení bylo úspěšné.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   V opačném případě postupujte podle pokynů Azure portálu pro řešení potíží s nasazením.

   > [!NOTE]
   > Pokud nasazení selže nebo je odstranit ISE, Azure může trvat až jednu hodinu před uvolněním podsítě. Toto zpoždění znamená, že znamená, že budete muset počkat před opětovným použitím těchto podsítí v jiném prostředí ISE. 
   >
   > Pokud odstraníte virtuální síť, Azure obvykle trvá až dvě hodiny před uvolněním do podsítě, ale tato operace může trvat delší dobu. 
   > Při odstraňování virtuální sítě, ujistěte se, že jsou stále připojeni žádné prostředky. Zobrazit [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Chcete-li zobrazit své prostředí, zvolte **přejít k prostředku** Pokud Azure nemá automaticky přejít do svého prostředí po dokončení nasazení.  

Další informace o vytváření podsítí najdete v tématu [přidat podsíť virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Vytvoření aplikace logiky – ISE

K vytváření aplikací logiky, které běží v prostředí integrační služby (ISE) [obvyklým způsobem vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) s výjimkou při nastavení **umístění** vlastnosti, vyberte vaše ISE z  **Prostředí integrační služby** části, například:

  ![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

Rozdíly v jak aktivační události a akce pracovních a jak jsou označené při použití ISE ve srovnání s globální služba Logic Apps, najdete v tématu [izolované a globální v přehledu ISE](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Vytvořit účet pro integraci – ISE

Pokud chcete používat účet integrace s logic apps v prostředí integrační služby (ISE), musíte použít tento účet pro integraci *stejné prostředí* jako logic apps. Aplikace logiky do ISE může odkazovat pouze účty pro integraci v prostředí ISE stejné.

Chcete-li vytvořit integrační účet, který používá ISE, [obvyklým způsobem vytvoření účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s výjimkou při nastavení **umístění** vlastnost, vyberte vaše ISE z **integrace Služba prostředí** části, například:

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Přidat kapacitu ISE

Základní jednotka ISE chyba opravena kapacity, takže pokud potřebujete větší propustnost, můžete přidat více jednotek škálování. Je to možné automatického škálování na základě metrik výkonu nebo na základě počtu jednotek další zpracování. Pokud vyberete možnost automatického škálování na základě metrik, můžete vybrat z různých kritérií a zadání podmínek prahovou hodnotu pro splnění tohoto kritéria.

1. Na webu Azure Portal najdete vaše ISE.

1. Zkontrolujte metriky využití a výkonu pro vaše ISE v hlavní nabídce vašeho ISE, vyberte **přehled**.

   ![Zobrazit využití ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. V části Nastavení automatického škálování, **nastavení**vyberte **horizontální navýšení kapacity**. Na **konfigurovat** kartě **povolit automatické škálování**.

   ![Zapnout automatické škálování](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Pro **název nastavení automatického škálování**, zadejte název pro nastavení.

1. V **výchozí** zvolte buď **škálování podle metriky** nebo **škálovat na konkrétní počet instancí**.

   * Pokud se rozhodnete založený na instancích, zadejte počet jednotek zpracování (včetně) mezi 0 a 10.

   * Pokud se rozhodnete, na základě metrik, postupujte podle těchto kroků:

     1. V **pravidla** zvolte **přidat pravidlo**.

     1. Na **pravítko měřítka** podokně provést, když se pravidlo aktivuje nastavení kritéria a akce.

     1. Jakmile budete hotovi, zvolte **přidat**.

1. Jakmile budete hotovi s nastavením automatického škálování, uložte provedené změny.

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
