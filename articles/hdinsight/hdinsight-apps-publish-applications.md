---
title: Publikování aplikací pro Azure HDInsight
description: Zjistěte, jak vytvořit aplikaci HDInsight a potom ji publikovat na webu Azure Marketplace.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: c8b54631f0d37e0e90135dd9e23ff85591cead1b
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495368"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikování aplikace HDInsight na Azure Marketplace
Aplikace Azure HDInsight můžete nainstalovat na clusteru HDInsight se systémem Linux. V tomto článku se dozvíte, jak k publikování aplikace HDInsight na Azure Marketplace. Obecné informace o publikování na webu Azure Marketplace najdete v tématu [publikování nabídky na webu Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

HDInsight aplikace používají *používání vlastní licence (BYOL)* modelu. Ve scénáři BYOL poskytovatele aplikace zodpovídá za licencování aplikace pro uživatele aplikace. Uživatelé aplikace se účtují jenom za prostředky Azure, který vytvoří, jako je například HDInsight cluster a virtuální počítače cluster a uzly. Fakturace za samotné aplikace se v současné době nedojde v Azure.

Další informace najdete v těchto článcích týkající se aplikací HDInsight:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md). Zjistěte, jak nainstalovat aplikaci HDInsight v clusterech služby.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md). Naučte se instalovat a testovat vlastní aplikace HDInsight.

## <a name="prerequisites"></a>Požadavky
Odeslat vlastní aplikace na webu Marketplace, nejprve [vytvářet a testovat vlastní aplikace](hdinsight-apps-install-custom-applications.md).

Také musíte zaregistrovat vývojářský účet. Další informace najdete v tématu [publikování nabídky na webu Azure Marketplace](../marketplace/marketplace-publishers-guide.md) a [vytvoření účtu Microsoft Developer](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definování aplikace
Dva kroky jsou součástí publikování aplikací na webu Marketplace. Nejprve definujte *createUiDef.json* souboru. Soubor createUiDef.json označuje, které clustery je kompatibilní s vaší aplikace. Pak publikujte šablonu z webu Azure portal. Tady je ukázkový soubor createUiDef.json:

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
| typy |Typy clusterů, se kterými je aplikace kompatibilní. |Hadoop, HBase, Storm, Spark (nebo libovolnou kombinaci těchto) |
| verze |Typy clusterů HDInsight, se kterými je aplikace kompatibilní. |3.4 |

## <a name="application-installation-script"></a>Skript instalace aplikace
Když v clusteru (buď v existujícím clusteru nebo na nový) je nainstalovaná určitá aplikace, vytvoří se hraniční uzel. Skript instalace aplikace běží na hraničním uzlu.

  > [!IMPORTANT]
  > Název skriptu instalace aplikace musí být jedinečné pro konkrétní cluster. Název skriptu musí mít následující formát:
  > 
  > "name": "[concat (" hue-install-v0 ','-', uniquestring('applicationName')] "
  > 
  > Název skriptu má tři části:
  > 
  > * Předpona názvu skriptu, který musí obsahovat název aplikace nebo název relevantní pro aplikaci.
  > * Pomlčkou, pro lepší čitelnost.
  > * Jedinečnou funkci řetězce, s názvem aplikace jako parametr.
  > 
  > V seznamu akcí trvalého skriptu, zobrazí se v předchozím příkladu jako **hue-install-v0-4wkahss55hlas**. Najdete v článku [ukázkovou datovou část JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Instalační skript, musí mít následující vlastnosti:
* Skript je idempotentní. Několik volání skriptu přinesou stejný výsledek.
* Skript je správně označené verzí. Používejte jiné umístění pro skript, když se upgrade nebo testování změn. Tím se zajistí, že zákazníci, kteří instalují aplikace nejsou ovlivněná vaše aktualizace nebo testování. 
* Tento skript má odpovídající protokolování v každém bodu. Protokoly skriptu jsou obvykle jediný způsob, jak ladit problémy s instalací aplikace.
* Volání k externím službám nebo zdrojům mít odpovídající opakování, aby instalace není ovlivněn přechodných síťových problémů.
* Pokud se skript spustí služby na uzlech, služby jsou sledovány a nakonfigurovat tak, aby se spouštěla automaticky, pokud dojde k restartování uzlu.

## <a name="package-the-application"></a>Balíček aplikace
Vytvořte soubor .zip, který obsahuje všechny soubory, které jsou nutné k instalaci aplikace HDInsight. Použít soubor .zip [publikovat aplikaci](#publish-application). Soubor ZIP obsahuje následující soubory:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (ukázku najdete v tématu [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).)
* Všechny požadované skripty

> [!NOTE]
> Soubory aplikace (včetně všech souborů webové aplikace), můžete hostovat na jakémkoli veřejně přístupném koncovém bodu.
> 

## <a name="publish-the-application"></a>Publikování aplikace
Chcete-li publikovat aplikace HDInsight:

1. Přihlaste se k [publikování Azure](https://publish.windowsazure.com/).
2. V nabídce vlevo vyberte **šablony řešení**.
3. Zadejte název a potom vyberte **vytvořit novou šablonu řešení**.
4. Pokud jste ještě nezaregistrovali vaší organizaci, vyberte **účtu centra vývojářů pro vytvoření a zapojte se do programu Azure**.  Další informace najdete v tématu [vytvoření účtu Microsoft Developer](../marketplace/marketplace-publishers-guide.md).
5. Vyberte **definovat některé topologie, abyste mohli začít**. Šablona řešení je "nadřazený objekt" všem svým topologiím. V jedné šabloně nabídky nebo řešení můžete definovat více topologií. Když se nabídka převede do přípravy, převede se se svým topologiím. 
6. Zadejte název topologie a potom vyberte **+**.
7. Zadejte novou verzi a pak vyberte **+**.
8. Nahrání souboru ZIP, který jste vytvořili, když jste [zabalené aplikace](#package-application).  
9. Vyberte **žádost o certifikaci**. Tým certifikace společnosti Microsoft soubory zkontroluje a topologii certifikuje.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [instalovat aplikace HDInsight](hdinsight-apps-install-applications.md) ve vašich clusterů.
* Zjistěte, jak [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md) a nasazovat nepublikované aplikace HDInsight do HDInsight.
* Zjistěte, jak [přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md) a přidat další aplikace. 
* Zjistěte, jak [vytvářet clustery založené na Linuxu Apache Hadoop v HDInsight pomocí šablon Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Zjistěte, jak [použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md) pro přístup k clusterů HDInsight, testovat aplikace HDInsight a hostovat aplikace HDInsight.

