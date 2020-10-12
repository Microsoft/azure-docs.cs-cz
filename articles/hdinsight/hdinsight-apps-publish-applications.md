---
title: Publikování aplikací Azure HDInsight
description: Naučte se vytvořit aplikaci HDInsight a pak ji publikovat v Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 2cee60a71f6f19e09194dc689f95999bb11faad3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086462"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikování aplikace HDInsight v Azure Marketplace
Aplikaci Azure HDInsight můžete nainstalovat na cluster HDInsight se systémem Linux. V tomto článku se dozvíte, jak publikovat aplikaci HDInsight v Azure Marketplace. Obecné informace o publikování v Azure Marketplace najdete v tématu [publikování nabídky v Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

Aplikace HDInsight používají model *Přineste si vlastní licenci (BYOL)* . Ve scénáři BYOL je poskytovatel aplikace zodpovědný za licencování aplikace uživatelům aplikace. Uživatelé aplikací se účtují jenom za prostředky Azure, které vytvořily, jako je cluster HDInsight, a virtuální počítače a uzly clusteru. V současné době se k fakturaci aplikace nepoužívá v Azure.

Další informace najdete v těchto článcích souvisejících s aplikacemi HDInsight:

* [Nainstalujte aplikace HDInsight](hdinsight-apps-install-applications.md). Přečtěte si, jak nainstalovat aplikaci HDInsight do svých clusterů.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md). Naučte se instalovat a testovat vlastní aplikace HDInsight.

## <a name="prerequisites"></a>Požadavky
Pokud chcete svou vlastní aplikaci odeslat na webu Marketplace, nejdřív [vytvořte a otestujte svoji vlastní aplikaci](hdinsight-apps-install-custom-applications.md).

Musíte taky zaregistrovat svůj vývojářský účet. Další informace najdete v tématu [publikování nabídky v Azure Marketplace](../marketplace/marketplace-publishers-guide.md) a [Vytvoření účtu Microsoft Developer](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definování aplikace
V rámci publikování aplikací na webu Marketplace jsou zapojené dva kroky. Nejprve definujte *createUiDef.jsv* souboru. createUiDef.jsv souboru označuje clustery, se kterými je aplikace kompatibilní. Pak publikujte šablonu z Azure Portal. Tady je ukázka createUiDef.jssouboru:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Pole | Description | Možné hodnoty |
| --- | --- | --- |
| typy |Typy clusterů, se kterými je aplikace kompatibilní. |Hadoop, HBA, proplavení, Spark (nebo libovolná kombinace těchto) |
| verze |Typy clusterů HDInsight, se kterými je aplikace kompatibilní. |3.4 |

## <a name="application-installation-script"></a>Instalační skript aplikace
Když je aplikace nainstalovaná v clusteru (buď v existujícím clusteru, nebo na novém), vytvoří se hraniční uzel. Instalační skript aplikace se spouští na hraničním uzlu.

  > [!IMPORTANT]  
  > Název instalačního skriptu aplikace musí být pro určitý cluster jedinečný. Název skriptu musí mít následující formát:
  > 
  > "název": "[Concat (' odstín-instalace-v0 ', '-', uniquestring (' applicationName ')]"
  > 
  > Název skriptu obsahuje tři části:
  > 
  > * Předpona názvu skriptu, která musí obsahovat buď název aplikace, nebo název, který je pro aplikaci relevantní.
  > * Pomlčka pro čitelnost.
  > * Jedinečná funkce řetězce s názvem aplikace jako parametrem.
  > 
  > V seznamu trvalých akcí skriptů se předchozí příklad zobrazí jako **odstín-Install-v0-4wkahss55hlas**. Podívejte se na [ukázkovou datovou část JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Instalační skript musí mít následující vlastnosti:
* Skript je idempotentní. Více volání skriptu vyprodukuje stejný výsledek.
* Ve skriptu je správně zadaná verze. Při upgradu nebo testování změn použijte jiné umístění pro skript. Tím je zajištěno, že zákazníci, kteří instalují aplikaci, nebudou vašimi aktualizacemi ani testováním ovlivněni. 
* Skript má v každém okamžiku odpovídající protokolování. Obvykle jsou protokoly skriptů jediným způsobem, jak ladit problémy s instalací aplikace.
* Volání externích služeb nebo prostředků mají adekvátní pokusy, aby instalace neovlivnila přechodné problémy se sítí.
* Pokud skript spustí služby na uzlech, služby se monitorují a nakonfigurují tak, aby se spouštěly automaticky, pokud dojde k restartování uzlu.

## <a name="package-the-application"></a>Zabalení aplikace
Vytvořte soubor. zip, který obsahuje všechny soubory, které jsou nutné k instalaci aplikace HDInsight. K publikování aplikace použijete soubor. zip. Soubor. zip obsahuje následující soubory:

* createUiDefinition.jsna
* mainTemplate.jszapnuté (ukázku najdete v tématu [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).)
* Všechny požadované skripty

> [!NOTE]  
> Soubory aplikace (včetně všech souborů webové aplikace) můžete hostovat na jakémkoli veřejně přístupném koncovém bodu.

## <a name="publish-the-application"></a>Publikování aplikace
Publikování aplikace HDInsight:

1. Přihlaste se k [publikování Azure](https://publish.windowsazure.com/).
2. V nabídce vlevo vyberte **šablony řešení**.
3. Zadejte název a potom vyberte **vytvořit novou šablonu řešení**.
4. Pokud jste svoji organizaci ještě nezaregistrovali, vyberte **vytvořit účet Dev Center a připojte se k programu Azure**.  Další informace najdete v tématu [Vytvoření účtu Microsoft Developer](../marketplace/marketplace-publishers-guide.md).
5. Chcete-li začít, vyberte možnost **definovat některé topologie**. Šablona řešení je "nadřazená" všem jeho topologiím. V jedné nabídce nebo šabloně řešení můžete definovat více topologií. Když je nabídka vložena do přípravy, je vložena se všemi topologiemi. 
6. Zadejte název topologie a pak vyberte **+** .
7. Zadejte novou verzi a pak vyberte **+** .
8. Nahrajte soubor. zip, který jste vytvořili při zabalení aplikace.  
9. Vyberte **žádost o certifikaci**. Certifikační tým Microsoftu zkontroluje soubory a potvrdí topologii.

## <a name="next-steps"></a>Další kroky
* Naučte se [instalovat aplikace HDInsight](hdinsight-apps-install-applications.md) do svých clusterů.
* Naučte se [instalovat vlastní aplikace HDInsight](hdinsight-apps-install-custom-applications.md) a nasazovat nepublikovanou aplikaci HDInsight do HDInsight.
* Naučte se [používat akci skriptu k přizpůsobení clusterů HDInsight se systémem Linux](hdinsight-hadoop-customize-cluster-linux.md) a přidání dalších aplikací. 
* Naučte se [vytvářet clustery Apache Hadoop se systémem Linux v HDInsight pomocí šablon Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Naučte se [používat prázdný hraniční uzel v HDInsight](hdinsight-apps-use-edge-node.md) pro přístup ke clusterům HDInsight, testování aplikací HDInsight a hostování aplikací HDInsight.

