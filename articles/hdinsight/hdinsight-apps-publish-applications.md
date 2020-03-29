---
title: Publikování aplikací Azure HDInsight
description: Přečtěte si, jak vytvořit aplikaci HDInsight a pak ji publikovat na Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64685322"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikování aplikace HDInsight na Azure Marketplace
Aplikaci Azure HDInsight můžete nainstalovat do clusteru HDInsight založeného na Linuxu. V tomto článku se dozvíte, jak publikovat aplikaci HDInsight na Azure Marketplace. Obecné informace o publikování na Azure Marketplace najdete v [tématu Publikování nabídky na Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

Aplikace HDInsight používají model *Bring Your Own License (BYOL).* Ve scénáři BYOL je poskytovatel aplikace zodpovědný za licencování aplikace uživatelům aplikace. Uživatelé aplikací se účtují jenom za prostředky Azure, které vytvářejí, jako je cluster HDInsight a virtuální počítače a uzly clusteru. V současné době fakturace pro samotnou aplikaci nedochází v Azure.

Další informace naleznete v těchto článcích týkajících se aplikací HDInsight:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md). Přečtěte si, jak nainstalovat do clusterů aplikaci HDInsight.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md). Přečtěte si, jak nainstalovat a otestovat vlastní aplikace HDInsight.

## <a name="prerequisites"></a>Požadavky
Chcete-li odeslat vlastní aplikaci na marketplace, nejprve [vytvořte a otestujte vlastní aplikaci](hdinsight-apps-install-custom-applications.md).

Musíte také zaregistrovat svůj vývojářský účet. Další informace najdete [v tématu Publikování nabídky na Azure Marketplace](../marketplace/marketplace-publishers-guide.md) a Vytvoření účtu Microsoft [Developer](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definování aplikace
Dva kroky se týkají publikování aplikací na webu Marketplace. Nejprve definujte soubor *createUiDef.json.* Soubor createUiDef.json označuje, se kterými clustery je aplikace kompatibilní. Potom publikujte šablonu z webu Azure Portal. Zde je ukázka souboru createUiDef.json:

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

| Pole | Popis | Možné hodnoty |
| --- | --- | --- |
| typy |Typy clusterů, se kterými je aplikace kompatibilní. |Hadoop, HBase, Storm, Spark (nebo jejich kombinace) |
| verze |Typy clusterů HDInsight, se kterými je aplikace kompatibilní. |3.4 |

## <a name="application-installation-script"></a>Instalační skript aplikace
Pokud je aplikace nainstalována v clusteru (buď v existujícím clusteru, nebo v novém), vytvoří se hraniční uzel. Instalační skript aplikace je spuštěn na hraničním uzlu.

  > [!IMPORTANT]  
  > Název instalačního skriptu aplikace musí být jedinečný pro konkrétní cluster. Název skriptu musí mít následující formát:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring('applicationName')]"
  > 
  > Název skriptu má tři části:
  > 
  > * Předpona názvu skriptu, která musí obsahovat název aplikace nebo název relevantní pro aplikaci.
  > * Pomlčka, pro čitelnost.
  > * Jedinečná funkce řetězce s názvem aplikace jako parametrem.
  > 
  > V seznamu trvalých akcí skriptu je předchozí příklad zobrazen jako **hue-install-v0-4wkahss55hlas**. Podívejte se na [ukázku datové části JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Instalační skript musí mít následující vlastnosti:
* Scénář je idempotentní. Více volání skriptu vytvořit stejný výsledek.
* Skript je správně verzí. Při upgradu nebo testování změn použijte pro skript jiné umístění. Tím je zajištěno, že zákazníci, kteří instalují aplikaci, nebudou vašimi aktualizacemi nebo testováním ovlivněny. 
* Skript má odpovídající protokolování v každém bodě. Protokoly skriptů jsou obvykle jediným způsobem, jak ladit problémy s instalací aplikace.
* Volání externích služeb nebo prostředků mají odpovídající opakování tak, aby instalace není ovlivněna přechodnými problémy se sítí.
* Pokud skript spustí služby na uzlech, služby jsou sledovány a konfigurovány tak, aby se spouštěly automaticky, pokud dojde k restartování uzlu.

## <a name="package-the-application"></a>Zabalte aplikaci
Vytvořte soubor ZIP, který obsahuje všechny soubory potřebné k instalaci aplikace HDInsight. K publikování aplikace se používá soubor ZIP. Soubor ZIP obsahuje následující soubory:

* createUiDefinition.json
* mainTemplate.json (Ukázka najdete v tématu [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).)
* Všechny požadované skripty

> [!NOTE]  
> Soubory aplikace (včetně všech souborů webové aplikace) můžete hostovat na libovolném veřejně přístupném koncovém bodu.

## <a name="publish-the-application"></a>Publikování aplikace
Publikování aplikace HDInsight:

1. Přihlaste se k [Azure Publishing](https://publish.windowsazure.com/).
2. V levé nabídce vyberte **Šablony řešení**.
3. Zadejte název a pak vyberte **Vytvořit novou šablonu řešení**.
4. Pokud jste ještě nezaregistrovali svou organizaci, vyberte **Vytvořit účet Dev Center a připojte se k programu Azure**.  Další informace naleznete [v tématu Vytvoření účtu Microsoft Developer](../marketplace/marketplace-publishers-guide.md).
5. Chcete-li **začít, vyberte možnost Definovat některé topologie**. Šablona řešení je "nadřazený" pro všechny jeho topologie. V jedné nabídce nebo šabloně řešení můžete definovat více topologie. Když je nabídka posunuta do pracovní fáze, je posunuta se všemi jejími topologiemi. 
6. Zadejte název topologie a **+** vyberte .
7. Zadejte novou verzi a **+** vyberte položku .
8. Nahrajte soubor ZIP, který jste vytvořili při balení aplikace.  
9. Vyberte **možnost Požádat o certifikaci**. Certifikační tým společnosti Microsoft zkontroluje soubory a certifikuje topologii.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [nainstalovat aplikace HDInsight](hdinsight-apps-install-applications.md) do clusterů.
* Přečtěte si, jak [nainstalovat vlastní aplikace HDInsight](hdinsight-apps-install-custom-applications.md) a nasadit nepublikovanou aplikaci HDInsight do HDInsightu.
* Přečtěte si, jak [pomocí akce Skript přizpůsobit clustery HDInsight založených na Linuxu](hdinsight-hadoop-customize-cluster-linux.md) a přidat další aplikace. 
* Naučte se [vytvářet linuxové clustery Apache Hadoop ve službě HDInsight pomocí šablon Azure Resource Manager .](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* Přečtěte si, jak [používat prázdný uzel okraje v HDInsightu](hdinsight-apps-use-edge-node.md) pro přístup ke clusterům HDInsight, testování aplikací HDInsight a hostování aplikací HDInsight.

