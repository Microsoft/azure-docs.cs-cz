---
title: Automatické škálování clusterů Azure HDInsight (preview)
description: Pomocí funkce automatického škálování HDInsight pro automatické škálování clusterů
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 28f04f5ab3cf8310a6ee3828405910d34b31591b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227570"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatické škálování clusterů Azure HDInsight (preview)

>[!Important]
>Funkce automatického škálování pro HDInsight je momentálně ve verzi preview. Pošlete e-mail na adresu hdiautoscalepm@microsoft.com mít automatického škálování pro vaše předplatné povolená.

Cluster Azure HDInsight automatického škálování, které funkce se automaticky škáluje podle počtu pracovních uzlů v clusteru směrem nahoru a dolů na základě zatížení v rámci předdefinovaného rozsahu. Při vytváření nového clusteru HDInsight můžete nastavit minimální a maximální počet pracovních uzlů. Automatické škálování a monitorování požadavků na prostředky analýzy načíst a upraví počet pracovních uzlů nahoru nebo dolů, odpovídajícím způsobem. Neexistuje žádné další poplatky za tuto funkci.

## <a name="getting-started"></a>Začínáme

### <a name="create-a-cluster-with-the-azure-portal"></a>Vytvoření clusteru pomocí webu Azure portal

> [!Note]
> Automatické škálování je momentálně podporována pouze pro verze clusterů Azure HDInsight Hive, MapReduce a Spark 3.6.

Pokud chcete povolit funkce automatického škálování, proveďte postup jako součást procesu vytváření běžných clusteru:

1. Vyberte **vlastní (velikost, nastavení, aplikace)** spíše než **rychlé vytvoření**.
2. Na **vlastní** kroku 5 (**velikost clusteru**) zkontrolujte **automatického škálování uzlů pracovního procesu** zaškrtávací políčko.
3. Zadejte požadované hodnoty pro následující vlastnosti:  

    * Počáteční **počet pracovních uzlů**.  
    * **Minimální** počet uzlů pracovního procesu.  
    * **Maximální** počet uzlů pracovního procesu.  

![Možnost automatického škálování uzlů pracovního procesu](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Počáteční počet uzlů pracovního procesu se musí nacházet mezi minimální a maximální (včetně). Tato hodnota určuje počáteční velikost clusteru při jeho vytvoření. Minimální počet uzlů pracovního procesu musí být větší než nula.

Až zvolíte typ virtuálního počítače pro každý typ uzlu, bude moci zobrazit odhadované náklady na rozsah pro celý cluster. Potom můžete upravit tato nastavení vyhovující vašemu rozpočtu.

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader z vašich hlavních uzlů v kombinaci s maximální počet pracovních uzlů nesmí překročit kapacitu kvóty. Nicméně tato kvóta je doporučené omezení; vždy můžete vytvořit lístek podpory a její navýšení snadno.

> [!Note]  
> Pokud překročíte limit kvóty celkový počet jader, zobrazí se chybová zpráva s oznámením "překročil maximální počet uzel dostupných jader v této oblasti, zvolte prosím jiné oblasti nebo požádejte podporu o navýšení kvóty."

Další informace o vytváření clusteru HDInsight pomocí webu Azure portal najdete v tématu [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Vytvoření clusteru pomocí šablony Resource Manageru

Vytvoření clusteru HDInsight pomocí šablony Azure Resource Manageru, přidejte `autoscale` uzlu `computeProfile`  >  `workernode` část s vlastnostmi `minInstanceCount` a `maxInstanceCount` jak je znázorněno v následujícím fragmentu kódu json.

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

Další informace o vytváření clusterů se šablonami Resource Manageru, najdete v části [vytvořit Apache Hadoop clusterů v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Povolení a zákaz automatického škálování pro spuštěný cluster

Můžete povolit nebo zakázat automatické škálování pro clustery HDInsight vytvořená po 1. ledna 2019 prostřednictvím webu Azure portal.

## <a name="monitoring"></a>Monitorování

Můžete zobrazit historii clusteru horizontálního navýšení nebo snížení jako součást clusteru metriky. Můžete také zařadit všechny akce škálování za poslední den, týden nebo delší časové období.

## <a name="how-it-works"></a>Jak to funguje

### <a name="metrics-monitoring"></a>Metriky monitorování

Automatické škálování nepřetržitě monitoruje cluster a shromažďuje následující metriky:

1. **Celkový počet čekajících procesoru**: Celkový počet jader potřebný k spuštění všech kontejnerů čekající na vyřízení.
2. **Celkový počet čekajících paměti**: Celkové paměti (v MB) potřebných k spuštění všech čekajících kontejnery.
3. **Celkové volné procesoru**: Součet všech nepoužívaných jader na aktivní pracovní uzly.
4. **Celkové volné paměti**: Součet nevyužité paměti (v MB) na aktivní pracovní uzly.
5. **Použitá paměť na jeden uzel**: Zatížení pracovního uzlu. Pracovní uzel, na kterém se používá 10 GB paměti, považován za větší zatížení než pracovního procesu s 2 GB paměti.
6. **Počet hlavních serverů aplikace podle počtu uzlů**: Počet kontejnerů aplikací Master (dop.) běžící na pracovním uzlu. Pracovního uzlu, který je hostitelem dvěma dop kontejnery, se považuje za důležitější než pracovního uzlu, který je hostitelem nulové AM kontejnery.

Výše uvedené metriky jsou kontrolovány každých 60 sekund. Automatické škálování bude rozhodovat horizontálního navýšení nebo snížení na základě těchto metrikách.

### <a name="cluster-scale-up"></a>Vertikální škálování clusteru

Při zjištění těchto podmínek, automatické škálování vydá požadavek vertikálně navýšit kapacitu:

* Celkový počet čekajících procesoru je větší než celkový volný čas procesoru pro více než 1 minuta.
* Celkový počet čekajících paměti je větší než celkové volné paměti pro více než 1 minuta.

Můžeme počítat počet nových pracovních uzlech je třeba splnit požadavky na aktuální využití procesoru a paměti a potom vydat žádost vertikálně navýšit kapacitu, která se přidá tento počet nových pracovních uzlech.

### <a name="cluster-scale-down"></a>Vertikální snížení kapacity clusteru

Při zjištění těchto podmínek, automatického škálování se vydejte žádost o vertikální snížení kapacity:

* Celkový počet čekajících procesoru je menší než celkový čas procesoru zdarma po dobu více než 10 minut.
* Celkový počet čekajících paměti je menší než celkové volné paměti pro více než 10 minut.

Podle počtu kontejnerů AM za uzel a aktuální využití procesoru a paměti požadavky, automatické škálování vydá požadavek na odebrání počtu uzlů, určení uzlů, které jsou možné kandidáty pro odstranění. Ve výchozím nastavení budou odebrány dva uzly v jednom cyklu.

## <a name="next-steps"></a>Další postup

* Přečtěte si informace o osvědčené postupy pro škálování clusterů ručně v [osvědčené postupy pro škálování](hdinsight-scaling-best-practices.md)
