---
title: Publikování aplikací Azure HDInsight | Microsoft Docs
description: Zjistěte, jak vytvořit aplikaci HDInsight a potom ho publikovat na webu Azure Marketplace.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: aef54782c200ad0be261959705b9b10cd0ebb064
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikování aplikace HDInsight v Azure Marketplace
Aplikace Azure HDInsight můžete nainstalovat na clusteru HDInsight se systémem Linux. V tomto článku se dozvíte, jak publikovat aplikace HDInsight do Azure Marketplace. Obecné informace o publikování v Azure Marketplace najdete v tématu [publikování nabídky v Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Aplikace HDInsight využívají *přineste si vlastní licenci (BYOL)* modelu. Ve scénáři BYOL poskytovatele aplikace zodpovídá za licencování aplikace pro uživatele aplikace. Uživatelé aplikace budou účtovat pouze prostředky Azure, které vytvoří, jako je například HDInsight cluster a virtuální počítače a uzly clusteru. V současné době fakturace pro vlastní aplikace neprobíhá v Azure.

Další informace naleznete v článcích týkající se aplikací HDInsight:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md). Postup instalace aplikace HDInsight v clusterech služby.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md). Naučte se instalovat a testovat vlastní aplikace HDInsight.

## <a name="prerequisites"></a>Požadavky
Chcete-li odeslat vlastní aplikace na webu Marketplace, nejprve [vytvořit a otestovat vlastní aplikaci](hdinsight-apps-install-custom-applications.md).

Je také nutné zaregistrovat vývojářský účet. Další informace najdete v tématu [publikování nabídky v Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) a [vytvoření účtu Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-the-application"></a>Definování aplikace
Dva kroky jsou součástí publikování aplikací na webu Marketplace. Nejprve definovat *createUiDef.json* souboru. Soubor createUiDef.json Určuje, které clustery je kompatibilní s vaší aplikace. Pak publikujte šablonu z portálu Azure. Tady je ukázkový soubor createUiDef.json:

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

## <a name="application-installation-script"></a>Skript instalace aplikace
Při instalaci aplikace na clusteru s podporou (buď na stávajícího clusteru nebo na novou), se vytvoří hraniční uzel. Skript instalace aplikace běží na uzlu edge.

  > [!IMPORTANT]
  > Název skriptu instalace aplikace musí být jedinečný pro konkrétní cluster. Název skriptu musí mít následující formát:
  > 
  > "název": "[concat ('hue-install-v0 ','-', uniquestring('applicationName')]"
  > 
  > Název skriptu má tři části:
  > 
  > * Předpona názvu skriptu, který musí obsahovat název aplikace nebo název relevantní pro aplikaci.
  > * Pomlčka, čitelnější.
  > * Jedinečnou funkci řetězce, s názvem aplikace jako parametr.
  > 
  > V seznamu akcí trvalého skriptu v předchozím příkladu se zobrazí jako **hue-install-v0-4wkahss55hlas**. V tématu [ukázku datové části JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Skript instalace musíte mít následující vlastnosti:
* Skript je idempotent. Několik volání do skriptu vytvořila stejný výsledek.
* Skript je správně verzí. Při upgradu nebo při testování změny používejte jiné umístění pro skript. Tím se zajistí zákazníkům, kteří instalují aplikaci není ovlivněných vaše aktualizace nebo testování. 
* Tento skript má odpovídající protokolování u každého bodu. Skript protokoly jsou obvykle jediný způsob, jak ladění problémů s instalací aplikace.
* Volání externí služby nebo prostředky mít odpovídající opakování tak, aby instalace není ovlivněn přechodný síťový problémy.
* Pokud se skript spustí služby na uzlech, služby jsou monitorovány a nakonfigurována na automatické spouštění. Pokud dojde k restartování uzlu.

## <a name="package-the-application"></a>Balíček aplikace
Vytvořte soubor .zip, který obsahuje všechny soubory, které jsou nutné k instalaci aplikace HDInsight. Můžete použít soubor .zip na [publikování aplikace](#publish-application). Soubor ZIP obsahuje následující soubory:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (ukázku, najdete v části [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).)
* Všechny požadované skripty

> [!NOTE]
> Soubory aplikace (včetně souborů žádné webové aplikace) můžete hostovat na jakékoli veřejně přístupném koncovém bodu.
> 

## <a name="publish-the-application"></a>Publikování aplikace
Publikování aplikace HDInsight:

1. Přihlaste se k [Azure publikování](https://publish.windowsazure.com/).
2. V nabídce vlevo vyberte **šablony řešení**.
3. Zadejte název a potom vyberte **vytvořte novou šablonu řešení**.
4. Pokud jste ještě nezaregistrovali vaší organizace, vyberte **účet Centra vývojářů pro vytvoření a zapojte se do programu Azure**.  Další informace najdete v tématu [vytvoření účtu Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Vyberte **definovat některé topologie začít**. Šablona řešení je "nadřazená" pro všechny její topologie. V jedné šabloně nabídky nebo řešení můžete definovat více topologií. Pokud je nabídka vložena do přípravy, je vložena se svým topologiím. 
6. Zadejte název topologie a potom vyberte **+**.
7. Zadejte novou verzi a potom vyberte **+**.
8. Nahrát soubor .zip, který jste vytvořili, když jste [zabalené aplikace](#package-application).  
9. Vyberte **požádat o certifikaci**. Tým certifikace společnosti Microsoft soubory zkontroluje a topologii certifikuje.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [instalace aplikací HDInsight](hdinsight-apps-install-applications.md) clusterů.
* Zjistěte, jak [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md) a nasazovat nepublikované aplikace HDInsight do HDInsight.
* Zjistěte, jak [použití akce skriptu k přizpůsobení clusterů HDInsight se systémem Linux](hdinsight-hadoop-customize-cluster-linux.md) a přidat další aplikace. 
* Zjistěte, jak [vytvářet clustery systémem Linux Hadoop v HDInsight pomocí šablony Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Zjistěte, jak [v HDInsight pomocí prázdné hraniční uzel, na](hdinsight-apps-use-edge-node.md) pro přístup k clusterů HDInsight, testování aplikací HDInsight a hostování aplikace HDInsight.

