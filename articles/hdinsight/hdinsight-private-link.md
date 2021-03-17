---
title: Zabezpečte a izolujte clustery Azure HDInsight pomocí privátního propojení (Preview).
description: Naučte se izolovat clustery Azure HDInsight ve virtuální síti pomocí privátního propojení Azure.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bc7834a0f8272da3f8954c7dd9f3e18163795cba
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939358"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Zabezpečte a izolujte clustery Azure HDInsight pomocí privátního propojení (Preview).

Ve [výchozí architektuře virtuální sítě](./hdinsight-virtual-network-architecture.md)Azure HDInsight poskytovatel prostředků služby HDInsight (RP) komunikuje s clusterem pomocí veřejných IP adres. Některé scénáře vyžadují kompletní izolaci sítě bez použití veřejných IP adres. V tomto článku se seznámíte s pokročilými ovládacími prvky, které můžete použít k vytvoření privátního clusteru HDInsight. Informace o tom, jak omezit provoz do clusteru a z něj bez úplné izolace sítě, najdete v tématu [řízení síťového provozu ve službě Azure HDInsight](./control-network-traffic.md).

Můžete vytvořit privátní clustery HDInsight konfigurací specifických vlastností sítě v šabloně Azure Resource Manager (ARM). Existují dvě vlastnosti, které slouží k vytvoření privátních clusterů HDInsight:

* Odeberte veřejné IP adresy nastavením `resourceProviderConnection` na odchozí.
* Povolit privátní propojení Azure a používat [privátní koncové body](../private-link/private-endpoint-overview.md) nastavením `privateLink` na povoleno.

## <a name="remove-public-ip-addresses"></a>Odebrat veřejné IP adresy

Ve výchozím nastavení používá HDInsight RP *příchozí* připojení ke clusteru pomocí veřejných IP adres. Když `resourceProviderConnection` je vlastnost Network nastavená na *odchozí*, obrátí se připojení k HDInsight RP, aby se připojení vždy iniciovala v rámci clusteru až po RP. Bez příchozího připojení nemusí být k dispozici žádné vstupní značky služeb ani veřejné IP adresy.

Základní nástroje pro vyrovnávání zatížení používané ve výchozí architektuře virtuální sítě automaticky poskytují veřejné NAT (překlad síťových adres) pro přístup k požadovaným odchozím závislostem, jako je například HDInsight RP. Pokud chcete omezit odchozí připojení k veřejnému Internetu, můžete [nakonfigurovat bránu firewall](./hdinsight-restrict-outbound-traffic.md), ale nejedná se o požadavek.

Konfigurace `resourceProviderConnection` pro odchozí připojení taky umožňuje přístup k prostředkům specifickým pro cluster, jako je Azure Data Lake Storage Gen2 nebo externí metaúložiště, pomocí privátních koncových bodů. Používání privátních koncových bodů pro tyto prostředky není povinné, ale pokud máte v úmyslu mít pro tyto prostředky privátní koncové body, musíte nakonfigurovat privátní koncové body a položky DNS, `before` které vytvoříte cluster HDInsight. V době vytváření clusteru doporučujeme vytvořit a poskytnout všechny externí databáze SQL, které potřebujete, jako je Apache Ranger, Ambari, Oozie a podregistr metaúložiště. Požadavek znamená, že všechny tyto prostředky musí být přístupné zevnitř podsítě clusteru, a to buď prostřednictvím vlastního privátního koncového bodu, nebo jinak.

Používání privátních koncových bodů pro Azure Key Vault se nepodporuje. Pokud používáte Azure Key Vault pro šifrování CMK v klidovém prostředí, musí být koncový bod Azure Key Vault dostupný z podsítě HDInsight bez privátního koncového bodu.

Následující diagram znázorňuje, jak může vypadat možná architektura virtuální sítě HDInsight, například když `resourceProviderConnection` je nastavená na odchozí:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagram architektury HDInsight s použitím připojení odchozího poskytovatele prostředků":::

Po vytvoření clusteru byste měli nastavit správné rozlišení DNS. V veřejné zóně DNS spravované v Azure se vytvoří následující záznam DNS s kanonickým názvem (CNAME): `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

Pro přístup ke clusteru pomocí plně kvalifikovaných názvů domény clusteru můžete buď použít soukromé IP adresy interního nástroje pro vyrovnávání zatížení, nebo použít vlastní zónu Privátní DNS k přepsání koncových bodů clusteru podle potřeby. Můžete mít například zónu Privátní DNS, `azurehdinsight.net` . a podle potřeby přidejte své soukromé IP adresy:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Povolit privátní propojení

Privátní odkaz, který je ve výchozím nastavení zakázán, vyžaduje rozsáhlou znalost sítě pro nastavení tras definovaných uživatelem (UDR) a pravidla brány firewall, aby bylo možné vytvořit cluster správně. Použití tohoto nastavení je volitelné, ale je k dispozici pouze v případě, že `resourceProviderConnection` je vlastnost síť nastavena na *odchozí* , jak je popsáno v předchozí části.

Když `privateLink` je nastavená možnost *Povolit*, vytvoří se interní standardní nástroj pro [Vyrovnávání zatížení](../load-balancer/load-balancer-overview.md) (SLB) a pro každý SLB se zřídí služba Azure Private Link. Služba privátního propojení umožňuje přístup ke clusteru HDInsight z privátních koncových bodů.

Standardní nástroje pro vyrovnávání zatížení neposkytují automaticky [veřejné odchozí služby NAT](../load-balancer/load-balancer-outbound-connections.md) , jako jsou základní nástroje pro vyrovnávání zatížení. Pro odchozí závislosti musíte zadat vlastní řešení NAT, například [Virtual Network NAT](../virtual-network/nat-overview.md) nebo [bránu firewall](./hdinsight-restrict-outbound-traffic.md). Váš cluster HDInsight pořád potřebuje přístup k odchozím závislostem. Pokud tyto odchozí závislosti nejsou povolené, vytvoření clusteru může selhat.

### <a name="prepare-your-environment"></a>Příprava prostředí

Aby bylo možné úspěšně vytvořit služby privátních odkazů, je nutné explicitně [zakázat zásady sítě pro službu privátního propojení](../private-link/disable-private-link-service-network-policy.md).

Následující diagram ukazuje příklad konfigurace sítě, která je potřeba před vytvořením clusteru. V tomto příkladu musí být veškerý odchozí provoz [nuceně](../firewall/forced-tunneling.md) Azure firewall pomocí udr a před vytvořením clusteru by měly být v bráně firewall povolené požadované odchozí závislosti. U Balíček zabezpečení podniku clusterů může být připojení k síti Azure Active Directory Domain Services dostupné pomocí partnerského vztahu virtuální sítě.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagram prostředí privátního propojení před vytvořením clusteru":::

Po nastavení sítě můžete vytvořit cluster s připojením odchozího poskytovatele prostředků a s povoleným privátním odkazem, jak je znázorněno na následujícím obrázku. V této konfiguraci nejsou žádné veřejné IP adresy a služba privátního propojení se zřizuje pro každý nástroj pro vyrovnávání zatížení úrovně Standard.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagram prostředí privátního propojení po vytvoření clusteru":::

### <a name="access-a-private-cluster"></a>Přístup k privátnímu clusteru

Chcete-li získat přístup k privátním clusterům, můžete použít soukromé IP adresy interního nástroje pro vyrovnávání zatížení přímo nebo můžete použít rozšíření DNS privátního propojení a soukromé koncové body. Pokud `privateLink` je nastavení nastaveno na povoleno, můžete vytvořit vlastní privátní koncové body a nakonfigurovat překlad DNS prostřednictvím privátních zón DNS.

Soukromé položky odkazů vytvořené ve veřejné zóně DNS spravované v Azure `azurehdinsight.net` jsou následující:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

Následující obrázek ukazuje příklad privátních záznamů DNS potřebných pro přístup ke clusteru z virtuální sítě, která není v partnerském vztahu, nebo nemá přímý přístup ke službě Vyrovnávání zatížení clusteru. Pomocí privátní zóny Azure můžete přepsat `*.privatelink.azurehdinsight.net` plně kvalifikované názvy domény a vyřešit je na vlastní IP adresy privátních koncových bodů.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagram architektury privátního propojení":::

## <a name="how-to-create-clusters"></a>Jak vytvářet clustery?
### <a name="use-arm-template-properties"></a>Použít vlastnosti šablony ARM

Následující fragment kódu JSON obsahuje dvě vlastnosti sítě, které v šabloně ARM potřebujete ke konfiguraci, aby bylo možné vytvořit privátní cluster HDInsight.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Úplnou šablonu s mnoha funkcemi zabezpečení HDInsight Enterprise, včetně privátního propojení, najdete v tématu [Šablona zabezpečení HDInsight Enterprise](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Použití Azure Powershell

Pokud chcete použít PowerShell, přečtěte [si tento příklad.](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)

### <a name="use-azure-cli"></a>Použití Azure CLI
Pokud chcete použít rozhraní příkazového řádku Azure, přečtěte [si tento příklad.](/cli/azure/hdinsight#az_hdinsight_create-examples)

## <a name="next-steps"></a>Další kroky

* [Balíček zabezpečení podniku pro Azure HDInsight](enterprise-security-package.md)
* [Obecné informace o podnikovém zabezpečení a pokyny v Azure HDInsight](./domain-joined/general-guidelines.md)
