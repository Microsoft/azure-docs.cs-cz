---
title: Šifrování Azure HDInsight při přenosu
description: Přečtěte si o funkcích zabezpečení, které zajišťují šifrování při přenosu pro cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946850"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Šifrování IPSec při přenosu pro Azure HDInsight

Tento článek popisuje implementaci šifrování při přenosu pro komunikaci mezi uzly clusteru Azure HDInsight.

## <a name="background"></a>Pozadí

Azure HDInsight nabízí celou řadu funkcí zabezpečení pro zabezpečení podnikových dat. Tato řešení se seskupují pod pilíři zabezpečení hraničního zabezpečení, ověřování, autorizace, auditování, šifrování a dodržování předpisů. Šifrování se dá použít pro data v klidovém režimu i při přenosu.

Šifrování v klidovém prostředí se zabývá šifrováním na straně serveru na účtech Azure Storage a šifrováním disku na virtuálních počítačích Azure, které jsou součástí clusteru HDInsight.

Šifrování dat při přenosu v HDInsight se dosahuje pomocí [protokolu TLS (Transport Layer Security)](../transport-layer-security.md) pro přístup ke branám clusteru a [Internet Protocol zabezpečení (IPSec)](https://wikipedia.org/wiki/IPsec) mezi uzly clusteru. Protokol IPSec může být volitelně povolen mezi všemi hlavními uzly, pracovními uzly, hraničními uzly, uzly Zookeeper a také uzly brány a [ID zprostředkovatele](./identity-broker.md) .

## <a name="enable-encryption-in-transit"></a>Povolit šifrování při přenosu

### <a name="azure-portal"></a>portál Azure

Chcete-li vytvořit nový cluster se šifrováním při přenosu povoleno pomocí Azure Portal, proveďte následující kroky:

1. Zahajte normální proces vytváření clusteru. Projděte si téma [Vytvoření clusterů se systémem Linux ve službě HDInsight pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) pro počáteční kroky vytváření clusteru.
1. Dokončete karty **základy** a **úložiště** . Přejděte na kartu **zabezpečení + sítě** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Vytvoření clusteru – karta zabezpečení a sítě.":::

1. Na kartě **zabezpečení a sítě** zaškrtněte políčko **Povolit šifrování v cestě** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Vytvoření clusteru – povolí šifrování při přenosu.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Vytvoření clusteru se šifrováním v přenosu povoleném prostřednictvím rozhraní příkazového řádku Azure

Šifrování při přenosu je povoleno pomocí `isEncryptionInTransitEnabled` Vlastnosti.

Můžete [Stáhnout vzorovou šablonu a soubor parametrů](https://github.com/Azure-Samples/hdinsight-enterprise-security). Než použijete šablonu a fragment kódu Azure CLI níže, nahraďte následující zástupné symboly jejich správnými hodnotami:

| Zástupný symbol | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | ID vašeho předplatného Azure |
| `<RESOURCE_GROUP>` | Skupina prostředků, ve které chcete vytvořit nový cluster a účet úložiště. |
| `<STORAGEACCOUNTNAME>` | Existující účet úložiště, který se má používat s clusterem. Název by měl mít tvar. `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | Název clusteru HDInsight. |
| `<PASSWORD>` | Vaše zvolené heslo pro přihlášení ke clusteru pomocí SSH a řídicího panelu Ambari |
| `<VNET_NAME>` | Virtuální síť, do které bude cluster nasazen. |

Níže uvedený fragment kódu provede následující úvodní kroky:

1. Přihlásí se k účtu Azure.
1. Nastaví aktivní předplatné, ve kterém se budou provádět operace vytvoření.
1. Vytvoří novou skupinu prostředků pro nové aktivity nasazení.
1. Nasaďte šablonu a vytvořte nový cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Další kroky

* [Přehled podnikového zabezpečení ve službě Azure HDInsight](hdinsight-security-overview.md)
* [Synchronizace Azure Active Directory uživatelů s clusterem HDInsight](../disk-encryption.md).
