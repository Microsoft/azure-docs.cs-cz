---
title: Automatické škálování clusterů Azure HDInsight
description: Pomocí funkce automatického škálování HDInsight pro automatické škálování clusterů
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 9a2fd8441de099a5423e9681a9dfe3ee12f08cec
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479465"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatické škálování clusterů Azure HDInsight

Cluster Azure HDInsight automatického škálování, které funkce se automaticky škáluje podle počtu pracovních uzlů v clusteru směrem nahoru a dolů na základě zatížení v rámci předdefinovaného rozsahu. Při vytváření nového clusteru HDInsight můžete nastavit minimální a maximální počet pracovních uzlů. Automatické škálování a monitorování požadavků na prostředky analýzy načíst a upraví počet pracovních uzlů nahoru nebo dolů, odpovídajícím způsobem. Neexistuje žádné další poplatky za tuto funkci.

## <a name="getting-started"></a>Začínáme

### <a name="create-cluster-with-azure-portal"></a>Vytvoření clusteru pomocí webu Azure portal

> [!Note]
> Automatické škálování je momentálně podporována pouze pro verze clusterů Azure HDInsight Hive, MapReduce a Spark 3.6.

Postupujte podle kroků v [ ](hdinsight-hadoop-create-linux-clusters-portal.md) a v kroku 5, **velikost clusteru**vyberte **pracovní uzel automatického škálování (preview)** jak je znázorněno níže. 

    ![Enable worker node autoscale option](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

Zaškrtnutím této možnosti můžete zadat:

* Počáteční počet uzlů pracovního procesu
* Minimální počet uzlů pracovního procesu
* Maximální počet uzlů pracovního procesu

Počáteční počet uzlů pracovního procesu se musí nacházet mezi minimální a maximální (včetně). Tato hodnota určuje počáteční velikost clusteru při jeho vytvoření. Minimální počet uzlů pracovního procesu musí být větší než nula.

Až zvolíte typ virtuálního počítače pro každý typ uzlu, bude moci zobrazit odhadované náklady na rozsah pro celý cluster. Potom můžete upravit tato nastavení vyhovující vašemu rozpočtu.

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader z vašich hlavních uzlů v kombinaci s maximální počet pracovních uzlů nesmí překročit kapacitu kvóty. Nicméně tato kvóta je doporučené omezení; vždy můžete vytvořit lístek podpory a její navýšení snadno.

> [!Note]
> Pokud překročíte limit kvóty celkový počet jader, zobrazí se chybová zpráva s oznámením "překročil maximální počet uzel dostupných jader v této oblasti, zvolte prosím jiné oblasti nebo požádejte podporu o navýšení kvóty."

### <a name="create-cluster-with-an-resource-manager-template"></a>Vytvoření clusteru pomocí šablony Resource Manageru

Při vytváření clusteru služby HDInsight pomocí šablony Resource Manageru, je potřeba přidat následující nastavení v části "pracovní uzel" "computeProfile":

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

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>Povolení a zakázání automatického škálování pro spuštěný cluster

Povolení automatického škálování pro spuštěný cluster nepodporuje během ve verzi private preview. Musí být povoleno při vytváření clusteru.

Zakázání automatického škálování nebo změna nastavení automatického škálování pro spuštěný cluster se nepodporuje ve verzi private preview. Musíte odstranit cluster a vytvořte nový cluster odstranit nebo upravit nastavení.

## <a name="monitoring"></a>Monitorování

Škálování clusteru nahoru a dolů historie jako součást clusteru metrik můžete zobrazit. Můžete zobrazit seznam všech akcí škálování za poslední den, týden nebo delší časové období.

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
