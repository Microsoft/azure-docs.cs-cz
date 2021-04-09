---
title: Rychlý Start – nasazení spravovaného clusteru Apache Spark s využitím Azure Databricks
description: V tomto rychlém startu se dozvíte, jak nasadit spravovaný cluster Apache Spark s Azure Databricks pomocí Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748493"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Rychlý Start: nasazení spravovaného clusteru Apache Spark (Preview) s Azure Databricks

Spravovaná instance Azure pro Apache Cassandra poskytuje automatizované operace nasazení a škálování pro spravovaná Open Source datová centra Apache Cassandra, zrychlení hybridních scénářů a snížení průběžné údržby.

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto rychlém startu se dozvíte, jak použít Azure Portal k vytvoření plně spravovaného clusteru Apache Spark v rámci Azure Virtual Network spravované instance Azure spravované instance pro cluster Apache Cassandra. Cluster Spark se vytvoří v Azure Databricks. Později můžete vytvořit nebo připojit poznámkové bloky ke clusteru, číst data z různých zdrojů dat a analyzovat přehledy.

Můžete si také přečíst podrobné pokyny k [nasazení Azure Databricks v Azure Virtual Network (vkládání Virtual Network)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Vytvoření clusteru Azure Databricks

Pomocí těchto kroků vytvoříte cluster Azure Databricks v Virtual Network, která má spravovanou instanci Azure pro Apache Cassandra:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyhledejte **skupiny prostředků** a přejděte do skupiny prostředků, která obsahuje Virtual Network, kde je vaše spravovaná instance nasazená.

1. Otevřete prostředek **Virtual Network** a poznamenejte si **adresní prostor**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Získejte adresní prostor vašeho Virtual Network." border="true":::

1. Ve skupině prostředků vyberte **Přidat** a vyhledejte **Azure Databricks** v poli Hledat:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Vyhledejte Azure Databricks." border="true":::

1. Vyberte **vytvořit** a vytvořte účet Azure Databricks:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Vytvořte účet Azure Databricks." border="true":::

1. Vyplňte následující hodnoty:

   * **Název pracovního prostoru** – zadejte název pracovního prostoru datacihly.
   * **Oblast** – Ujistěte se, že jste vybrali stejnou oblast jako vaše Virtual Network.
   * **Cenová úroveň** – vyberte si z úrovně Standard, Premium nebo zkušební verzi. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Pro účet datacihly zadejte název pracovního prostoru, oblast a cenovou úroveň." border="true":::

1. Dále vyberte kartu **síť** a vyplňte následující podrobnosti:

   * **Nasaďte Azure Databricks pracovní prostor v Virtual Network (VNET)** – vyberte **Ano**.
   * **Virtual Network** – v rozevíracím seznamu vyberte Virtual Network, kde existuje vaše spravovaná instance.
   * **Název veřejné podsítě** – zadejte název veřejné podsítě.
   * **Rozsah směrování veřejných podsítí** – zadejte rozsah IP adres pro veřejnou podsíť.
   * **Název privátní podsítě** – zadejte název privátní podsítě.
   * **Rozsah CIDR privátní podsítě** – zadejte rozsah IP adres pro privátní podsíť.

   Aby se předešlo kolizím rozsahu, ujistěte se, že jste vybrali vyšší rozsahy. V případě potřeby vydělte rozsahy pomocí [kalkulačky vizuálních podsítí](https://www.fryguy.net/wp-content/tools/subnets.html) :

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Použijte kalkulačku Virtual Network podsítí." border="true":::

   Následující snímek obrazovky ukazuje příklady podrobností v podokně síť:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Zadejte názvy veřejných a privátních podsítí." border="true":::

1. Vyberte **zkontrolovat a vytvořit** a pak **vytvořte** a nasaďte pracovní prostor.

1. Po vytvoření **pracovního prostoru spustit** .

1. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **nový cluster**.

1. V podokně **nový cluster** přijměte výchozí hodnoty pro všechna pole kromě následujících polí:

   * **Název clusteru** – zadejte název clusteru.
   * **Databricks runtime verze** – vyberte Scala 2,11 nebo nižší verzi, kterou podporuje konektor Cassandra.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Vyberte verzi modulu runtime datacihly a clusteru Spark." border="true":::

1. Rozbalte **Rozšířené možnosti** a přidejte následující konfiguraci. Ujistěte se, že jste nahradili IP adresy a přihlašovací údaje uzlu:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. Na kartě **knihovny** nainstalujte nejnovější verzi konektoru Sparku pro Cassandra (*Spark-Cassandra-Connector*) a restartujte cluster:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Nainstalujte konektor Cassandra." border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tento cluster spravované instance, odstraňte jej pomocí následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili pro tento rychlý Start.
1. V podokně s **přehledem** skupiny prostředků vyberte **Odstranit skupinu prostředků**.
3. V dalším okně zadejte název skupiny prostředků, kterou chcete odstranit, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit plně spravovaný cluster Apache Spark v Virtual Network spravované instance Azure pro cluster Apache Cassandra. V dalším kroku se dozvíte, jak spravovat prostředky clusteru a datacentra: 

> [!div class="nextstepaction"]
> [Správa spravované instance Azure pro prostředky Apache Cassandra pomocí Azure CLI](manage-resources-cli.md)

