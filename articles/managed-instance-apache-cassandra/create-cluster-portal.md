---
title: Rychlý Start – vytvoření spravované instance Azure pro cluster Apache Cassandra z Azure Portal
description: V tomto rychlém startu se dozvíte, jak vytvořit spravovanou instanci Azure pro cluster Apache Cassandra pomocí Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 315b505e9de853fbe4663eacdfe929149dbaa458
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873140"
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

    > [!NOTE]
    > Nasazení spravované instance Azure pro Apache Cassandra vyžaduje přístup k Internetu. Nasazení se nezdařilo v prostředích, kde je omezen přístup k Internetu. Ujistěte se, že neblokujete přístup v rámci vaší virtuální sítě do následujících důležitých služeb Azure, které jsou nezbytné pro správné fungování spravovaných Cassandra:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Monitorování Azure
    > - Azure Active Directory
    > - Zabezpečení Azure

1. Pokud jste v posledním kroku vytvořili novou virtuální síť, přejděte ke kroku 8. Pokud jste před vytvořením clusteru vybrali existující virtuální síť, musíte použít některá zvláštní oprávnění pro Virtual Network a podsíť. K tomu použijte `az role assignment create` příkaz, nahraďte `<subscription ID>` , `<resource group name>` a `<VNet name>` s příslušnými hodnotami:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > `assignee`Hodnoty a `role` v předchozím příkazu jsou pevné hodnoty. Tyto hodnoty zadejte přesně tak, jak je uvedeno v příkazu. V takovém případě se to nepovede, takže při vytváření clusteru dojde k chybám. Pokud narazíte na chyby při spuštění tohoto příkazu, možná nemáte oprávnění k jeho spuštění, kontaktujte svého správce a požádejte ho o oprávnění.

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

## <a name="troubleshooting"></a>Řešení potíží

Pokud při použití oprávnění k vašemu Virtual Network dojde k chybě, například *nelze najít uživatele nebo instanční objekt v databázi grafu pro ' e5007d2c-4b13-4a74-9b6a-605d99f03501 '*, můžete stejné oprávnění použít ručně z Azure Portal. Chcete-li použít oprávnění z portálu, přejděte do podokna **řízení přístupu (IAM)** ve stávající virtuální síti a přidejte přiřazení role pro "Azure Cosmos DB" do role "správce sítě". Pokud se při hledání "Azure Cosmos DB" zobrazí dvě položky, přidejte obě položky, jak je znázorněno na následujícím obrázku: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Použít oprávnění" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Přiřazení role Azure Cosmos DB se používá jenom pro účely nasazení. Spravovaná instance Azure spravovaná pro Apache Cassandra nemá žádné back-endové závislosti na Azure Cosmos DB.   

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
