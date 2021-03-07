---
title: Rychlý Start – vytvoření spravované instance Azure pro cluster Apache Cassandra z Azure Portal
description: V tomto rychlém startu se dozvíte, jak vytvořit spravovanou instanci Azure pro cluster Apache Cassandra pomocí Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: d94bedad1ba7a2c6d814021b733404ccc58148ed
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424678"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Rychlý Start: vytvoření spravované instance Azure pro cluster Apache Cassandra z Azure Portal (Preview)
 
Spravovaná instance Azure pro Apache Cassandra poskytuje automatizované operace nasazení a škálování pro spravovaná Open Source datová centra Apache Cassandra, zrychlení hybridních scénářů a snížení průběžné údržby.

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento rychlý Start ukazuje, jak použít Azure Portal k vytvoření spravované instance Azure pro cluster Apache Cassandra.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Vytvoření clusteru spravované instance

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Na panelu hledání vyhledejte **spravovanou instanci pro Apache Cassandra** a vyberte výsledek.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Vyhledejte spravovanou instanci pro Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Vyberte tlačítko **vytvořit spravovanou instanci pro cluster Apache Cassandra** .

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Vytvořte cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. V podokně **vytvořit spravovanou instanci pro Apache Cassandra** zadejte následující podrobnosti:

   * **Předplatné** – v rozevíracím seznamu vyberte své předplatné Azure.
   * **Skupina prostředků**– určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace najdete v článku Přehled [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) .
   * **Název clusteru** – zadejte název clusteru.
   * **Umístění** – umístění, do kterého se má cluster nasadit
   * **SKU** – typ SKU pro váš cluster.
   * **Ne. uzlů**– počet uzlů v clusteru. Tyto uzly slouží jako repliky pro vaše data.
   * **Počáteční heslo správce Cassandra** – heslo, které se používá k vytvoření clusteru.
   * **Potvrďte heslo správce Cassandra** – zadejte heslo znovu.

    > [!NOTE]
    > Během veřejné verze Preview můžete cluster Managed instance vytvořit v oblastech *východní USA, západní USA, východní USA 2, západní USA 2, střed USA, střed USA – jih, Severní Evropa, západní Evropa, jižní východní Asie a Austrálie – východ* .

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Vyplňte formulář vytvořit cluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Pak vyberte kartu **síť** .

1. V podokně **síť** vyberte **Virtual Network** název a **podsíť**. Můžete vybrat existující Virtual Network nebo vytvořit novou.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Nakonfigurujte podrobnosti o síti." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Pokud jste v posledním kroku vytvořili novou virtuální síť, přejděte ke kroku 8. Pokud jste před vytvořením clusteru vybrali existující virtuální síť, musíte použít některá zvláštní oprávnění pro Virtual Network a podsíť. K tomu použijte `az role assignment create` příkaz, nahraďte,, `<subscription ID>` `<resource group name>` `<VNet name>` a `<subnet name>` s příslušnými hodnotami:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Hodnoty a `role` v předchozím příkazu jsou pevné zásady služby a identifikátory rolí v uvedeném pořadí.

1. Teď, když jste dokončili práci se sítí, klikněte na **zkontrolovat + vytvořit**  >  **vytvořit** .

    > [!NOTE]
    > Vytvoření clusteru může trvat až 15 minut.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Zkontrolujte souhrn a vytvořte cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Po dokončení nasazení zkontrolujte skupinu prostředků, abyste viděli nově vytvořený cluster Managed instance:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Stránka s přehledem po vytvoření clusteru." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Pokud chcete procházet uzly clusteru, přejděte do podokna Virtual Network, které jste použili k vytvoření clusteru, a otevřete podokno **Přehled** , abyste je mohli zobrazit:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Zobrazení prostředků clusteru." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>Připojování ke clusteru

Spravovaná instance Azure pro Apache Cassandra nevytváří uzly s veřejnými IP adresami, takže pokud se chcete připojit ke svému nově vytvořenému clusteru Cassandra, budete muset v rámci virtuální sítě vytvořit jiný prostředek. Může to být aplikace nebo virtuální počítač s nainstalovaným Open Source dotazovacím nástrojem pro Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) . K nasazení virtuálního počítače s Ubuntu můžete použít [šablonu](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) . Při nasazení se připojte k počítači pomocí SSH a nainstalujte CQLSH pomocí níže uvedených příkazů:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tento cluster spravované instance, odstraňte jej pomocí následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili pro tento rychlý Start.
1. V podokně s **přehledem** skupiny prostředků vyberte **Odstranit skupinu prostředků**.
1. V dalším okně zadejte název skupiny prostředků, kterou chcete odstranit, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit spravovanou instanci Azure pro cluster Apache Cassandra pomocí Azure Portal. Teď můžete začít pracovat s clusterem:

> [!div class="nextstepaction"]
> [Nasazení clusteru spravovaného Apache Spark s využitím Azure Databricks](deploy-cluster-databricks.md)
