---
title: Nasazení Azure Databricks ve vaší virtuální síti
description: Tento článek popisuje, jak nasadit Azure Databricks do vaší virtuální sítě, označované taky jako vkládání virtuální sítě.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 0bb3221c201e6dd4dd17cca8ef7e3ed3331de228
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432659"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Nasazení Azure Databricks ve vaší virtuální síti

Výchozím nasazením Azure Databricks je plně spravovaná služba v Azure: všechny prostředky roviny dat, včetně virtuální sítě (VNet), se nasazují do uzamčené skupiny prostředků. Pokud ale požadujete vlastní nastavení sítě, můžete nasadit Azure Databricks prostředky ve své vlastní virtuální síti (označované také jako injektáže virtuální sítě), kde vám umožní:

* Připojte Azure Databricks k jiným službám Azure (například Azure Storage) zabezpečeným způsobem pomocí koncových bodů služby.
* Připojení k místním zdrojům dat pro použití s nástrojem Azure Databricks s využitím uživatelsky definovaných tras.
* Připojte Azure Databricks k síťovému virtuálnímu zařízení pro kontrolu všech odchozích přenosů a proveďte akce podle pravidel povolení a zamítnutí.
* Nakonfigurujte Azure Databricks pro použití vlastního DNS.
* Nakonfigurujte pravidla skupiny zabezpečení sítě (NSG), abyste určili omezení odchozích přenosů.
* Nasaďte Azure Databricks clustery do stávající virtuální sítě.

Nasazení Azure Databricks prostředků do vlastní virtuální sítě také umožňuje využít výhod flexibilních rozsahů CIDR (kdekoli mezi/16-/24 pro virtuální síť a mezi/18-/26 pro podsítě).

  > [!NOTE]
  > Virtuální síť nemůžete nahradit existujícím pracovním prostorem. Pokud váš aktuální pracovní prostor nemůže přizpůsobit požadovaný počet aktivních uzlů clusteru, vytvořte ve větší virtuální síti jiný pracovní prostor. Pomocí [těchto podrobných kroků migrace](howto-regional-disaster-recovery.md#detailed-migration-steps) zkopírujte prostředky (poznámkové bloky, konfigurace clusteru, úlohy) ze starého do nového pracovního prostoru.

## <a name="virtual-network-requirements"></a>Požadavky na virtuální síť

V Azure Portal můžete použít rozhraní Azure Databricksho nasazení pracovního prostoru k automatické konfiguraci existující virtuální sítě s požadovanými podsítěmi, skupinou zabezpečení sítě a povoleným nastavením, nebo můžete použít Azure Resource Manager šablony pro konfiguraci virtuální sítě a nasazení pracovního prostoru.

Virtuální síť, do které nasadíte pracovní prostor Azure Databricks, musí splňovat následující požadavky:

### <a name="location"></a>Umístění

Virtuální síť se musí nacházet ve stejném umístění jako pracovní prostor Azure Databricks.

### <a name="subnets"></a>Podsítě

Virtuální síť musí zahrnovat dvě podsítě vyhrazené pro Azure Databricks:

   1. Privátní podsíť s nakonfigurovanou skupinou zabezpečení sítě, která umožňuje interní komunikaci mezi clustery

   2. Veřejná podsíť s nakonfigurovanou skupinou zabezpečení sítě, která umožňuje komunikaci s rovinou ovládacího prvku Azure Databricks.

### <a name="address-space"></a>Adresní prostor

Blok CIDR mezi/16-/24 pro virtuální síť a blok CIDR mezi/18-/26 pro privátní a veřejné podsítě.

### <a name="whitelisting"></a>Uvedení na seznamu povolených

Všechny odchozí a příchozí přenosy mezi podsítěmi a rovinou ovládacího prvku Azure Databricks musí být na seznamu povolených.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

Tato část popisuje, jak vytvořit pracovní prostor Azure Databricks v Azure Portal a nasadit ho do vlastní existující virtuální sítě. Služba Azure Databricks aktualizuje virtuální síť se dvěma novými podsítěmi a skupinami zabezpečení sítě pomocí rozsahů CIDR, které poskytujete, prostřednictvím seznamu povolených přenosů příchozích a odchozích podsítí a nasadí pracovní prostor do aktualizované virtuální sítě.

## <a name="prerequisites"></a>Předpoklady

Musíte mít virtuální síť, do které budete nasazovat Azure Databricks pracovní prostor. Můžete použít existující virtuální síť nebo vytvořit novou, ale virtuální síť musí být ve stejné oblasti jako Azure Databricks pracovní prostor, který chcete vytvořit. Pro virtuální síť je vyžadován rozsah CIDR mezi/16-/24.

  > [!Warning]
  > Pracovní prostor s menší virtuální sítí – což je nižší rozsah CIDR – může vycházet z IP adres (síťové místo) rychleji než pracovní prostor s větší virtuální sítí. Například pracovní prostor s virtuální sítí/24 a/26 podsítěmi může mít aktivní maximálně 64 uzlů v čase, zatímco pracovní prostor s virtuální sítí/20 a/22 může obsahovat maximálně 1024 uzlů.

  Vaše podsítě se vytvoří automaticky při konfiguraci pracovního prostoru a vy budete mít možnost během konfigurace poskytnout rozsah CIDR pro tyto podsítě.

## <a name="configure-the-virtual-network"></a>Konfigurace virtuální sítě

1. V Azure Portal kliknutím na **+ vytvořit prostředek > Analytics > Azure Databricks** otevřete dialog Azure Databricks Service.

2. Postupujte podle kroků konfigurace popsaných v části Krok 2: vytvoření pracovního prostoru Azure Databricks v příručce Začínáme a v možnosti Virtual Network vyberte pracovní prostor nasadit Azure Databricks.

   ![Vytvoření služby Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Vyberte virtuální síť, kterou chcete použít.

   ![Možnosti virtuální sítě](./media/vnet-injection/select-vnet.png)

4. Poskytněte rozsahy CIDR v bloku mezi/18-/26 pro dvě podsítě vyhrazené pro Azure Databricks:

   * Vytvoří se veřejná podsíť s přidruženou skupinou zabezpečení sítě, která umožňuje komunikaci s rovinou ovládacího prvku Azure Databricks.
   * Privátní podsíť se vytvoří s přidruženou skupinou zabezpečení sítě, která umožňuje interní komunikaci mezi clustery.

5. Kliknutím na **vytvořit** nasadíte pracovní prostor Azure Databricks do virtuální sítě.

## <a name="advanced-resource-manager-configurations"></a>Pokročilé konfigurace Resource Manageru

Pokud chcete mít větší kontrolu nad konfigurací virtuální sítě – například chcete použít existující podsítě, použít stávající skupiny zabezpečení sítě nebo vytvořit vlastní pravidla zabezpečení – můžete použít následující šablony Azure Resource Manager místo Konfigurace virtuálních sítí portálu a nasazení pracovního prostoru.

### <a name="all-in-one"></a>Vše v jednom

Pokud chcete vytvořit virtuální síť, skupiny zabezpečení sítě a Azure Databricks pracovní prostor v jednom, použijte [šablonu All-in-One pro vložené pracovní prostory datacihly ve virtuální](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)síti.

Když použijete tuto šablonu, nemusíte provádět žádné Ruční přidávání do seznamu povolených přenosů v podsíti.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Chcete-li vytvořit skupiny zabezpečení sítě s požadovanými pravidly pro existující virtuální síť, použijte [šablonu skupiny zabezpečení sítě pro vkládání virtuální sítě pomocí datacihly](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Když použijete tuto šablonu, nemusíte provádět žádné Ruční přidávání do seznamu povolených přenosů v podsíti.

### <a name="virtual-network"></a>Virtuální síť

Pokud chcete vytvořit virtuální síť se správnými veřejnými a soukromými podsítěmi, použijte [šablonu Virtual Network pro vkládání virtuální sítě pomocí datacihly](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Pokud tuto šablonu použijete bez použití šablony skupiny zabezpečení sítě, musíte ručně přidat pravidla přidávání do skupin zabezpečení sítě, která používáte s virtuální sítí.

### <a name="azure-databricks-workspace"></a>Pracovní prostor Azure Databricks

Pokud chcete nasadit Azure Databricks pracovní prostor do existující virtuální sítě, která má veřejné a privátní podsítě a správně nakonfigurované skupiny zabezpečení sítě, použijte [šablonu pracovního prostoru pro vkládání virtuální sítě datacihly](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Pokud tuto šablonu použijete bez použití šablony skupiny zabezpečení sítě, musíte ručně přidat pravidla přidávání do skupin zabezpečení sítě, která používáte s virtuální sítí.

## <a name="whitelisting-subnet-traffic"></a>Seznam povolených přenosů podsítí

Pokud při vytváření skupin zabezpečení sítě nepoužíváte šablony [Azure Portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) nebo [Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) , musíte do svých podsítí ručně zařadit do seznamu povolených následujících přenosů.

|Směr|Protocol (Protokol)|Zdroj|Zdrojový port|Cíl|Cílový port|
|---------|--------|------|-----------|-----------|----------------|
|Příchozí|\*|VirtualNetwork|\*|\*|\*|
|Příchozí|\*|Řídicí rovina IP adresy NAT|\*|\*|22|
|Příchozí|\*|Řídicí rovina IP adresy NAT|\*|\*|5557|
|Odchozí|\*|\*|\*|WebApp IP adresa|\*|
|Odchozí|\*|\*|\*|SQL (značka služby)|\*|
|Odchozí|\*|\*|\*|Storage (značka služby)|\*|
|Odchozí|\*|\*|\*|VirtualNetwork|\*|

Povolený provoz v podsíti pomocí následujících IP adres. Pro SQL (metastore) a úložiště (artefakty a úložiště protokolů) byste měli použít značky SQL a [služby](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)úložiště.

|Oblast Azure Databricks|Služba|Veřejná IP adresa|
|-----------------------|-------|---------|
|USA – východ|Řízení překladu roviny ovládacího prvku </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Východ USA 2|Řízení překladu roviny ovládacího prvku </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Středoseverní USA|Řízení překladu roviny ovládacího prvku </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Střední USA|Řízení překladu roviny ovládacího prvku </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Středojižní USA|Řízení překladu roviny ovládacího prvku </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Západní USA|Řízení překladu roviny ovládacího prvku </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Západní USA 2|Řízení překladu roviny ovládacího prvku </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Kanada – střed|Řízení překladu roviny ovládacího prvku </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kanada – východ|Řízení překladu roviny ovládacího prvku </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Velká Británie – západ|Řízení překladu roviny ovládacího prvku </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Velká Británie – jih|Řízení překladu roviny ovládacího prvku </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Západní Evropa|Řízení překladu roviny ovládacího prvku </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Severní Evropa|Řízení překladu roviny ovládacího prvku </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Střední Indie|Řízení překladu roviny ovládacího prvku </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Jižní Indie|Řízení překladu roviny ovládacího prvku </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Západní Indie|Řízení překladu roviny ovládacího prvku </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Jihovýchodní Asie|Řízení překladu roviny ovládacího prvku </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Východní Asie|Řízení překladu roviny ovládacího prvku </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Austrálie – východ|Řízení překladu roviny ovládacího prvku </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrálie – jihovýchod|Řízení překladu roviny ovládacího prvku </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrálie – střed|Řízení překladu roviny ovládacího prvku </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrálie – střed 2|Řízení překladu roviny ovládacího prvku </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japonsko – východ|Řízení překladu roviny ovládacího prvku </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japonsko – západ|Řízení překladu roviny ovládacího prvku </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Řešení potíží

### <a name="workspace-launch-errors"></a>Chyby při spuštění pracovního prostoru

Spuštění pracovního prostoru ve vlastní virtuální síti se nepovede na obrazovce Azure Databricks přihlašování s následující chybou: **došlo k chybě při vytváření pracovního prostoru. Ujistěte se, že je vlastní konfigurace sítě správná, a zkuste to znovu. "**

Tato chyba je způsobena konfigurací sítě, která nesplňuje požadavky. Při vytváření pracovního prostoru ověřte, že jste postupovali podle pokynů v tomto tématu.

### <a name="cluster-creation-errors"></a>Chyby při vytváření clusteru

**Instance nejsou dostupné: prostředky nejsou dosažitelné přes SSH.**

Možná příčina: provoz z roviny ovládacího prvku na pracovníky je blokován. Opravte tím, že zajistíte, aby příchozí pravidla zabezpečení splňovala požadavky. Pokud nasazujete do existující virtuální sítě připojené k místní síti, Projděte si instalační program s použitím informací uvedených v části připojení pracovního prostoru Azure Databricks k místní síti.

**Neočekávaná chyba při spuštění: při nastavování clusteru došlo k neočekávané chybě. Pokud potíže potrvají, zkuste to znovu a kontaktujte Azure Databricks. Vnitřní chybová zpráva: při umísťování uzlu vypršel časový limit.**

Možná příčina: provoz z pracovních procesů do koncových bodů Azure Storage je blokovaný. Opravte tím, že zajistíte, aby odchozí pravidla zabezpečení splňovala požadavky. Pokud používáte vlastní servery DNS, podívejte se také na stav serverů DNS ve vaší virtuální síti.

**Selhání spuštění poskytovatele cloudu: při nastavování clusteru došlo k chybě poskytovatele cloudu. Další informace najdete v průvodci Azure Databricks. Kód chyby Azure: AuthorizationFailed/InvalidResourceReference.**

Možná příčina: virtuální síť nebo podsítě už neexistují. Ujistěte se, že virtuální síť a podsítě existují.

**Cluster byl ukončen. Důvod: Chyba při spuštění Sparku: Spark nebylo možné spustit včas. K tomuto problému může dojít v důsledku nefunkčnosti metastore Hive, neplatných konfigurací Sparku nebo při nefunkčnosti skriptů init. Chcete-li vyřešit tento problém, přečtěte si protokoly ovladačů Spark a kontaktujte datacihly, pokud potíže potrvají. Zpráva o interní chybě: Spark se nepovedlo spustit: ovladač se nepovedlo spustit v čase.**

Možná příčina: kontejner nemůže komunikovat s instancí hostování nebo účtem úložiště DBFS. Opravte přidáním vlastní trasy do podsítí pro účet úložiště DBFS s dalším segmentem směrování Internet.

### <a name="notebook-command-errors"></a>Chyby příkazu poznámkového bloku

**Příkaz nereaguje.**

Možná příčina: komunikace mezi pracovními procesy je blokovaná. Opravte zajištěním, aby příchozí pravidla zabezpečení splňovala požadavky.

**Pracovní postup poznámkového bloku se nezdařil s výjimkou: com. datacihly. WorkflowException: org. Apache. http. ConnectTimeoutException**

Možná příčina: provoz od pracovníků po Azure Databricks WebApp je blokovaný. Opravte tím, že odchozí pravidla zabezpečení splňují požadavky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce, transformace a načítání dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
