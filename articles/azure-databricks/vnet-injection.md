---
title: Nasazení Azure Databricks ve vaší virtuální síti (Preview)
description: Tento článek popisuje, jak nasadit Azure Databricks k virtuální síti, označované také jako virtuální síť vkládání.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003441"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Nasazení Azure Databricks ve vaší virtuální síti (Preview)

Výchozím nasazení produktu Azure Databricks je plně spravovaná služba v Azure: všechny prostředky roviny dat, včetně virtual network (VNet), jsou nasazené do skupiny prostředků uzamčené. Pokud budete potřebovat vlastní nastavení sítě, ale můžete nasadit prostředky Azure Databricks ve své vlastní virtuální síti (také nazývané VNet vkládání), když vám umožní:

* Azure Databricks se připojte k jiným službám Azure (například Azure Storage) v lépe zabezpečeným způsobem pomocí koncových bodů služby.
* Připojení k místním datům zdroje pro použití s Azure Databricks s využitím uživatelsky definovaných tras.
* Připojení Azure Databricks k síťové virtuální zařízení kontrolovat veškerý odchozí provoz a provádět akce podle povolit a zakázat pravidla.
* Konfigurace Azure Databricks používat vlastní službu DNS.
* Konfigurace sítě pravidla skupiny zabezpečení (NSG) k určení omezení odchozí provoz.
* Nasazení clusterů Azure Databricks v existující virtuální síti.

Nasazení prostředků Azure Databricks k virtuální síti také vám umožní využít flexibilní rozsahů CIDR (kdekoli mezi /16-/ 24 virtuální sítě a mezi /18-/ 26 pro podsítě).

  > [!NOTE]
  > Nelze nahradit virtuální sítě pro existující pracovní prostor. Pokud aktuální pracovní prostor nemohou pojmout požadovaný počet aktivní uzly clusteru, vytvořte jiný pracovní prostor ve větší virtuální síti. Postupujte podle [tyto podrobné kroky migrace](howto-regional-disaster-recovery.md#detailed-migration-steps) se zkopírovat prostředky (poznámkových bloků, konfigurace clusteru, úlohy) ze staré na nový pracovní prostor.

## <a name="virtual-network-requirements"></a>Požadavky na virtuální síť

Rozhraní nasazení pracovního prostoru Azure Databricks na portálu Azure portal můžete použít existující virtuální sítě automaticky nakonfigurovat požadované podsítě, skupiny zabezpečení sítě a nastavení na seznam povolených, nebo můžete použít Azure Resource Manageru šablony konfigurace virtuální sítě a nasazení pracovního prostoru.

Virtuální síť, která nasazení pracovního prostoru Azure Databricks k musí splňovat následující požadavky:

### <a name="location"></a>Location

Virtuální sítě se musí nacházet ve stejném umístění jako pracovní prostor Azure Databricks.

### <a name="subnets"></a>Podsítě

Virtuální síť musí obsahovat dvě podsítě, které jsou vyhrazené pro Azure Databricks:

   1. Privátní podsítě se skupinou zabezpečení nakonfigurovanou síť, která umožňuje clusteru interní komunikace

   2. Veřejné podsítě se skupinou zabezpečení sítě nakonfigurované, který umožňuje komunikaci s Azure Databricks rovina řízení.

### <a name="address-space"></a>Adresní prostor

Blok CIDR mezi /16 /24 pro virtuální síť a blok CIDR mezi /18 /26 pro privátní a veřejné podsítě.

### <a name="whitelisting"></a>Uvedení na seznamu povolených

Všechny odchozí a příchozí provoz mezi podsítěmi a Azure Databricks rovina řízení musí být uvedeny v seznamu povolených.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

Tato část popisuje, jak vytvořit pracovní prostor Azure Databricks na portálu Azure a jejím nasazením v existující virtuální síti. Azure Databricks aktualizuje virtuální síť se dvěma nové podsítě a skupiny zabezpečení sítě pomocí vámi zadané rozsahy CIDR, příchozí seznamů povolených a podsíť odchozích přenosů a nasadí pracovního prostoru virtuální síť se aktualizovala.

## <a name="prerequisites"></a>Požadavky

Musí mít virtuální síť, do kterého nasazujete pracovního prostoru Azure Databricks. Můžete použít existující virtuální síť nebo vytvořte novou, ale virtuální síť musí být ve stejné oblasti jako pracovní prostor Azure Databricks, který chcete vytvořit. Rozsah CIDR mezi /16 /24 se vyžaduje pro virtuální síť.

  > [!Warning]
  > Pracovní prostor s menší virtual network – to znamená menší rozsah CIDR – může vyčerpala volné IP adresy (prostoru sítě) rychleji než pracovní prostor s větší virtuální sítě. Například pracovní prostor se/24 je virtuální síť a /26 podsítě může mít maximálně 64 uzlů v každém okamžiku aktivní, zatímco pracovní prostor se /20 virtuální sítě a /22 podsítě může zastřešovat i maximálně 1 024 uzly.

  Podsítě se automaticky vytvoří při konfiguraci pracovního prostoru a budete mít možnost zadat rozsah CIDR. pro podsítě během konfigurace.

## <a name="configure-the-virtual-network"></a>Konfigurace virtuální sítě

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek > Analytics > Azure Databricks** otevřete dialogové okno služby Azure Databricks.

2. Postupujte podle kroků konfigurace je popsáno v kroku 2: Vytvoření pracovního prostoru Azure Databricks ve v úvodní příručce k a vyberte pracovní prostor Azure Databricks nasadit ve variantě pro vaše virtuální sítě.

   ![Vytvoření služby Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Vyberte virtuální síť, kterou chcete použít.

   ![Možnosti virtuální sítě](./media/vnet-injection/select-vnet.png)

4. Zadejte rozsahy CIDR v bloku mezi /18 /26 pro dvě podsítě: vyhrazená pro Azure Databricks:

   * Veřejné podsítě se vytvoří s přidruženou skupinu zabezpečení sítě, která umožňuje komunikaci s Azure Databricks rovina řízení.
   * Privátní podsíť se vytvoří s přidruženou skupinu zabezpečení sítě, která umožňuje clusteru interní komunikace.

5. Klikněte na tlačítko **vytvořit** nasazení pracovního prostoru Azure Databricks k virtuální síti.

## <a name="advanced-resource-manager-configurations"></a>Konfigurace pokročilých resource manager

Pokud chcete mít větší kontrolu nad konfigurací virtuální sítě – například chcete použít existující podsítě, pomocí stávajících skupin zabezpečení sítě nebo vytvořit vlastní pravidla zabezpečení – můžete použít následující šablony Azure Resource Manager místo Konfigurace a pracovní prostor nasazení portálu virtuální sítě.

### <a name="all-in-one"></a>Vše v jednom

Chcete-li vytvořit virtuální síť, skupiny zabezpečení sítě a pracovní prostor Azure Databricks vše v jednom, použijte [All-in-one šablony pro pracovní prostory Databricks VNet vložený](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Při použití této šablony, není potřeba dělat žádné ruční přidávání na seznam povolených přenosů podsítě.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Chcete-li vytvořit skupiny zabezpečení sítě pomocí požadovaných pravidel pro existující virtuální sítě, použijte [šablony skupiny zabezpečení sítě pro virtuální síť vkládání Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Při použití této šablony, není potřeba dělat žádné ruční přidávání na seznam povolených přenosů podsítě.

### <a name="virtual-network"></a>Virtuální síť

Chcete-li vytvořit virtuální síť s správné veřejné a privátní podsítě, použijte [virtuální sítě šablony pro vkládání VNet Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Pokud použijete tuto šablonu bez také použití šablony skupiny zabezpečení sítě, je třeba ručně přidat pravidla přidávání na seznam povolených na skupiny zabezpečení sítě, které používáte s virtuální sítí.

### <a name="azure-databricks-workspace"></a>Pracovní prostor Azure Databricks

Chcete-li nasadit pracovnímu prostoru Azure Databricks do existující virtuální síť, která má veřejný a privátní podsítě a skupiny zabezpečení správně nakonfigurované síťové nastavení, použijte [šablony pracovního prostoru pro vkládání VNet Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Pokud použijete tuto šablonu bez také použití šablony skupiny zabezpečení sítě, je třeba ručně přidat pravidla přidávání na seznam povolených na skupiny zabezpečení sítě, které používáte s virtuální sítí.

## <a name="whitelisting-subnet-traffic"></a>Provoz podsítě na seznam povolených

Pokud použijete [webu Azure portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) nebo [šablon Azure Resource Manageru](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) k vytvoření skupiny zabezpečení sítě, je nutné ručně seznam povolených následující provoz na podsítě.

|Direction|Protocol (Protokol)|Zdroj|Zdrojový port|Cíl|Cílový port|
|---------|--------|------|-----------|-----------|----------------|
|Příchozí|\*|VirtualNetwork|\*|\*|\*|
|Příchozí|\*|Rovina řízení adres IP pro NAT|\*|\*|22|
|Příchozí|\*|Rovina řízení adres IP pro NAT|\*|\*|5557|
|Odchozí|\*|\*|\*|Webapp IP|\*|
|Odchozí|\*|\*|\*|SQL (značka služby)|\*|
|Odchozí|\*|\*|\*|Úložiště (značka služby)|\*|
|Odchozí|\*|\*|\*|VirtualNetwork|\*|

Seznam povolených adres podsítě provozu s využitím následující IP adresy. Pro SQL (metastore) a službu Storage (úložiště artefaktů a protokol), měli byste použít Sql a úložiště [značky služeb](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Oblasti Azure Databricks|Služba|Veřejná IP adresa|
|-----------------------|-------|---------|
|USA – východ|Rovina řízení NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Východní USA 2|Rovina řízení NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Středoseverní USA|Rovina řízení NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA – střed|Rovina řízení NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Středojižní USA|Rovina řízení NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Západní USA|Rovina řízení NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Západní USA 2|Rovina řízení NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Kanada – střed|Rovina řízení NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kanada – východ|Rovina řízení NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Spojené království – západ|Rovina řízení NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Velká Británie – jih|Rovina řízení NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Západní Evropa|Rovina řízení NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Severní Evropa|Rovina řízení NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Střed Indie|Rovina řízení NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indie – jih|Rovina řízení NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indie – západ|Rovina řízení NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Jihovýchodní Asie|Rovina řízení NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Východní Asie|Rovina řízení NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Austrálie – východ|Rovina řízení NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrálie – jihovýchod|Rovina řízení NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrálie – střed|Rovina řízení NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrálie – střed 2|Rovina řízení NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japonsko – východ|Rovina řízení NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japonsko – západ|Rovina řízení NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Řešení potíží

### <a name="workspace-launch-errors"></a>Spuštění chybám pracovního prostoru

Spouští se pracovní prostor ve vlastní virtuální síti se nezdaří v Azure Databricks přihlašovací obrazovka s následující chybou: **"Jsme došlo k chybě při vytváření pracovního prostoru. Ujistěte se, že vlastní síťová konfigurace je správná a zkuste to znovu."**

Tato chyba je způsobena nesplňuje požadavky na konfiguraci sítě. Potvrďte, že jste postupovali podle pokynů v tomto tématu při vytváření pracovního prostoru.

### <a name="cluster-creation-errors"></a>Chyby při vytváření clusteru

**Instancí nedostupný: Prostředky nebyly dostupné prostřednictvím SSH.**

Možná příčina: provoz z rovina řízení pracovníkům blokovaný. Opravte tím, že zajišťuje, že pravidla zabezpečení příchozích požadavků. Pokud provádíte nasazení do existující virtuální sítě připojené k vaší místní síti, zkontrolujte nastavení pomocí informací v pracovního prostoru Azure Databricks propojíte s vaší místní síti.

**Spuštění neočekávané selhání: Při nastavování clusteru došlo k neočekávané chybě. Zkuste to znovu a Pokud potíže potrvají, obraťte se Azure Databricks. Zpráva o interní chybě: Vypršel časový limit při umísťování uzlu.**

Možná příčina: je blokován provoz z pracovních procesů ke koncovým bodům služby Azure Storage. Opravte tím, že zajišťuje, že odchozí pravidla zabezpečení požadavkům. Pokud používáte vlastní servery DNS, kontrolovat stav serverů DNS ve vaší virtuální síti.

**Selhání spuštění poskytovatele cloudu: Při nastavování clusteru došlo k chybě zprostředkovatele cloudu. Další informace v příručce Azure Databricks. Kód chyby Azure: AuthorizationFailed/InvalidResourceReference.**

Možná příčina: virtuální sítě nebo podsítě není už neexistuje. Ujistěte se, že existují virtuální síť a podsítě.

**Byla ukončena clusteru. Důvod: Selhání spuštění Sparku: Spark se nepodařilo spustit včas. Tento problém může způsobovat nefunkční metastore Hive, neplatná konfigurace Spark nebo nefunkční skripty init. Najdete v protokolech ovladač Spark pro odstranění problému a Pokud potíže potrvají, obraťte se Databricks. Zpráva o interní chybě: Spark se nepodařilo spustit: Ovladače se nepodařilo spustit včas.**

Možné příčiny: Kontejner nemůže komunikovat s hostující instanci nebo DBFS účtu úložiště. Opravte přidáním vlastní trasu k podsíti pro účet úložiště DBFS s další segment směrování je Internet.

### <a name="notebook-command-errors"></a>Poznámkový blok příkazu chyby

**Příkaz nereaguje.**

Možná příčina: pracovního procesu worker komunikace je blokovaná. Vyřešit, ujistěte se, že pravidla zabezpečení příchozích požadavků.

**Poznámkový blok postupu dojde k výjimce: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Možná příčina: provoz z pracovních procesů do webové aplikace Azure Databricks je blokován. Vyřešit, ujistěte se, že splňují požadavky odchozí pravidla zabezpečení.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Extrakce, transformace a načítání dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
