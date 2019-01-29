---
title: Automatické škálování clusterů Azure HDInsight (Preview)
description: Pomocí funkce automatického škálování HDInsight pro automatické škálování clusterů
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: fd2d9bd325d79a1fd8aa0da74da64f6ba98decda
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101052"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatické škálování clusterů Azure HDInsight (Preview)

Cluster Azure HDInsight automatického škálování, které funkce se automaticky škáluje podle počtu pracovních uzlů v clusteru směrem nahoru a dolů na základě zatížení v rámci předdefinovaného rozsahu. Při vytváření nového clusteru HDInsight můžete nastavit minimální a maximální počet pracovních uzlů. Automatické škálování a monitorování požadavků na prostředky analýzy načíst a upraví počet pracovních uzlů nahoru nebo dolů, odpovídajícím způsobem. Neexistuje žádné další poplatky za tuto funkci.

## <a name="getting-started"></a>Začínáme

### <a name="create-a-cluster-with-the-azure-portal"></a>Vytvoření clusteru pomocí webu Azure portal

> [!Note]
> Automatické škálování je momentálně podporována pouze pro verze clusterů Azure HDInsight Hive, MapReduce a Spark 3.6.

Kompletní HDInsight clusteru vytváření postup pomocí webu Azure Portal najdete v [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  Povolení automatického škálování během procesu vytváření vyžaduje několik odchylky od obvykle instalační kroky.  

1. Vyberte **vlastní (velikost, nastavení, aplikace)** spíše než **rychlé vytvoření**.
2. Na vlastní krok 5 **velikost clusteru**, zkontrolujte **automatického škálování uzlů pracovního procesu** zaškrtávací políčko.
3. Zadejte požadované hodnoty pro:  
  &#8226;Počáteční **počet pracovních uzlů**.  
  &#8226;**Minimální** počet uzlů pracovního procesu.  
  &#8226;**Maximální** počet uzlů pracovního procesu.  

![Možnost automatického škálování uzlů pracovního procesu](./media/hdinsight-autoscale-clusters/usingAutoscale.png)


Počáteční počet uzlů pracovního procesu se musí nacházet mezi minimální a maximální (včetně). Tato hodnota určuje počáteční velikost clusteru při jeho vytvoření. Minimální počet uzlů pracovního procesu musí být větší než nula.

Až zvolíte typ virtuálního počítače pro každý typ uzlu, bude moci zobrazit odhadované náklady na rozsah pro celý cluster. Potom můžete upravit tato nastavení vyhovující vašemu rozpočtu.

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader z vašich hlavních uzlů v kombinaci s maximální počet pracovních uzlů nesmí překročit kapacitu kvóty. Nicméně tato kvóta je doporučené omezení; vždy můžete vytvořit lístek podpory a její navýšení snadno.

> [!Note]  
> Pokud překročíte limit kvóty celkový počet jader, zobrazí se chybová zpráva s oznámením "překročil maximální počet uzel dostupných jader v této oblasti, zvolte prosím jiné oblasti nebo požádejte podporu o navýšení kvóty."

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Vytvoření clusteru pomocí šablony Resource Manageru

Kompletní HDInsight clusteru vytváření postup pomocí šablon Resource Manageru najdete v [vytvořit Apache Hadoop clusterů v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  Při vytváření clusteru služby HDInsight pomocí šablony Azure Resource Manageru, budete muset přidat následující nastavení v části "workernode" "computeProfile" a odpovídajícím způsobem upravit:

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Povolení a zákaz automatického škálování pro spuštěný cluster

Povolení automatického škálování pro spuštěný cluster nepodporuje během ve verzi private preview. Musí být povoleno při vytváření clusteru.

Zakázání automatického škálování nebo změna nastavení automatického škálování pro spuštěný cluster se nepodporuje ve verzi private preview. Musíte odstranit cluster a vytvořte nový cluster odstranit nebo upravit nastavení.

## <a name="monitoring"></a>Monitorování

Škálování clusteru nahoru a vertikální snížení kapacity historie jako součást clusteru metrik můžete zobrazit. Můžete také zařadit všechny akce škálování za poslední den, týden nebo delší časové období.

## <a name="how-it-works"></a>Jak to funguje

### <a name="metrics-monitoring"></a>Metriky monitorování

Automatické škálování nepřetržitě monitoruje cluster a shromažďuje následující metriky:

1. **Celkový počet čekajících procesoru**: Celkový počet jader potřebný k spuštění všech kontejnerů čekající na vyřízení.
2. **Celkový počet čekajících paměti**: Celkové paměti (v MB) potřebných k spuštění všech čekajících kontejnery.
3. **Celkové volné procesoru**: Součet všech nepoužívaných jader na aktivní pracovní uzly.
4. **Celkové volné paměti**: Součet nevyužité paměti (v MB) na aktivní pracovní uzly.
5. **Použitá paměť na jeden uzel**: Zatížení pracovního uzlu. Pracovní uzel, na kterém se používá 10 GB paměti, považován za větší zatížení než pracovního procesu s 2 GB paměti.
6. **Počet hlavních serverů aplikace podle počtu uzlů**: Počet kontejnerů aplikací Master (dop.) běžící na pracovním uzlu. Hostování kontejnerů 2: 00 pracovního uzlu se považuje za důležitější než hostování kontejnerů 0 AM pracovního uzlu.

Výše uvedené metriky jsou kontrolovány každých 60 sekund. Automatické škálování se tvoří škálování a vertikálně snížit kapacitu rozhodování na základě těchto metrik.

### <a name="cluster-scale-up"></a>Škálování clusteru nahoru

Při zjištění těchto podmínek, automatické škálování vydá vertikálního navýšení žádosti:

* Celkový počet čekajících procesoru je větší než celkový volný čas procesoru pro více než 1 minuta.
* Celkový počet čekajících paměti je větší než celkové volné paměti pro více než 1 minuta.

My pak vypočítá N nových pracovních uzlech jsou potřeba splňovala aktuální požadavky na procesor a paměť a potom vydat vertikálního navýšení žádosti můžete si vyžádat N nových pracovních uzlech.

### <a name="cluster-scale-down"></a>Škálování clusteru dolů

Při zjištění těchto podmínek, automatické škálování vydá vertikálního snižování kapacity žádosti:

* Celkový počet čekajících procesoru je menší než celkový čas procesoru zdarma po dobu více než 10 minut.
* Celkový počet čekajících paměti je menší než celkové volné paměti pro více než 10 minut.

Podle počtu kontejnerů AM na uzlu, jakož i aktuální požadavky na procesor a paměť, automatické škálování vydá požadavek na odebrání uzlů N zadání uzlů, které jsou možné kandidáty pro odstranění. Ve výchozím nastavení budou odebrány dva uzly v jednom cyklu.

## <a name="next-steps"></a>Další postup

* Přečtěte si informace o osvědčené postupy pro škálování clusterů ručně v [osvědčené postupy pro škálování](hdinsight-scaling-best-practices.md)
