---
title: Automatické škálování clusterů Azure HDInsight (preview)
description: Pomocí funkce automatického škálování HDInsight pro automatické škálování clusterů
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000101"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatické škálování clusterů Azure HDInsight (preview)

> [!Important]
> Funkce automatického škálování funguje jenom pro Spark, Hive a MapReduce clustery, které jsou vytvořené po 8. května 2019. 

Funkce automatického škálování clusteru Azure HDInsight se automaticky škáluje, počet pracovních uzlů v clusteru nahoru a dolů. Momentálně nelze škálovat jiné typy uzlů v clusteru.  Při vytváření nového clusteru HDInsight můžete nastavit minimální a maximální počet pracovních uzlů. Automatické škálování pak monitoruje analytics zatížení požadavků na prostředky a počet uzlů pracovního procesu se škáluje směrem nahoru nebo dolů. Neexistuje žádné další poplatky za tuto funkci.

## <a name="cluster-compatibility"></a>Kompatibilita clusteru

Následující tabulka popisuje typy clusterů a verze, které jsou kompatibilní s funkcí automatického škálování.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 bez ESP | Ano | Ano | Ne | Ne | Ne | Ne | Ne |
| HDInsight 4.0 bez ESP | Ano | Ano | Ne | Ne | Ne | Ne | Ne |
| HDInsight 3.6 s ESP | Ano | Ano | Ne | Ne | Ne | Ne | Ne |
| HDInsight 3.6 s ESP | Ano | Ano | Ne | Ne | Ne | Ne | Ne |

## <a name="how-it-works"></a>Jak to funguje

Můžete vybrat na základě zatížení škálování nebo založené na plánu škálování pro váš cluster HDInsight. Škálování na základě zatížení změní z počtu uzlů v clusteru, v rámci rozsahu, který jste nastavili, k zajištění optimální využití procesoru a minimalizovat provozní náklady.

Založené na plánu škálování změny z počtu uzlů v clusteru na základě podmínek, které se projeví v určitých časech. Tyto podmínky škálování clusteru do požadovaného počtu uzlů.

### <a name="metrics-monitoring"></a>Metriky monitorování

Automatické škálování nepřetržitě monitoruje cluster a shromažďuje následující metriky:

* **Celkový počet čekajících procesoru**: Celkový počet jader potřebný k spuštění všech kontejnerů čekající na vyřízení.
* **Celkový počet čekajících paměti**: Celkové paměti (v MB) potřebných k spuštění všech čekajících kontejnery.
* **Celkové volné procesoru**: Součet všech nepoužívaných jader na aktivní pracovní uzly.
* **Celkové volné paměti**: Součet nevyužité paměti (v MB) na aktivní pracovní uzly.
* **Použitá paměť na jeden uzel**: Zatížení pracovního uzlu. Pracovní uzel, na kterém se používá 10 GB paměti, považován za větší zatížení než pracovního procesu s 2 GB paměti.
* **Počet hlavních serverů aplikace podle počtu uzlů**: Počet kontejnerů aplikací Master (dop.) běžící na pracovním uzlu. Pracovního uzlu, který je hostitelem dvěma dop kontejnery, se považuje za důležitější než pracovního uzlu, který je hostitelem nulové AM kontejnery.

Výše uvedené metriky jsou kontrolovány každých 60 sekund. Automatické škálování díky horizontálního navýšení nebo snížení rozhodování na základě těchto metrik.

### <a name="load-based-cluster-scale-up"></a>Vertikálně navýšit kapacitu na základě zatížení clusteru

Při zjištění těchto podmínek, automatické škálování vydá požadavek vertikálně navýšit kapacitu:

* Celkový počet čekajících procesoru je větší než celkový čas procesoru zdarma po dobu více než 3 minut.
* Celkový počet čekajících paměti je větší než celkové volné paměti pro více než tři minuty.

Služba HDInsight vypočítá, kolik nových pracovních uzlech jsou potřeba ke splnění požadavků aktuálního procesoru a požadavky na paměť a pak vydá požadavek vertikálně navýšit kapacitu na přidání požadovaný počet uzlů.

### <a name="load-based-cluster-scale-down"></a>Na základě zatížení clusteru vertikální snížení kapacity

Při zjištění těchto podmínek, automatického škálování se vydejte žádost o vertikální snížení kapacity:

* Celkový počet čekajících procesoru je menší než celkový čas procesoru zdarma po dobu více než 10 minut.
* Celkový počet čekajících paměti je menší než celkové volné paměti pro více než 10 minut.

Automatické škálování podle počtu kontejnerů AM za uzel a aktuální využití procesoru a paměti požadavky, problémy žádost o odebrání počet uzlů. Služba také detekuje, které jsou kandidáty pro odstranění podle aktuální provádění úlohy uzly. Vertikálně operace nejprve decommissions uzly a odebere je z clusteru.

## <a name="get-started"></a>Začínáme

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Vytvoření clusteru pomocí automatického škálování založeného na zatížení

Pokud chcete povolit funkce automatického škálování se škálování na základě zatížení, proveďte následující kroky jako součást procesu vytváření běžných clusteru:

1. Vyberte **vlastní (velikost, nastavení, aplikace)** spíše než **rychlé vytvoření**.
1. Na **vlastní** kroku 5 (**velikost clusteru**), zkontrolujte **automatického škálování uzlů pracovního procesu** zaškrtávací políčko.
1. Vyberte možnost **na základě zatížení** pod **typ automatického škálování**.
1. Zadejte požadované hodnoty pro následující vlastnosti:  

    * Počáteční **počet pracovních uzlů**.  
    * **Minimální** počet uzlů pracovního procesu.  
    * **Maximální** počet uzlů pracovního procesu.  

    ![Povolit možnost automatického škálování na základě zatížení uzlu pracovního procesu](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Počáteční počet uzlů pracovního procesu se musí nacházet mezi minimální a maximální (včetně). Tato hodnota určuje počáteční velikost clusteru při jeho vytvoření. Minimální počet uzlů pracovního procesu musí být větší než nula.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Vytvoření clusteru s automatickým Škálováním založené na plánu

Pokud chcete povolit funkce automatického škálování s založené na plánu škálování, proveďte následující kroky jako součást procesu vytváření běžných clusteru:

1. Vyberte **vlastní (velikost, nastavení, aplikace)** spíše než **rychlé vytvoření**.
1. Na **vlastní** kroku 5 (**velikost clusteru**), zkontrolujte **automatického škálování uzlů pracovního procesu** zaškrtávací políčko.
1. Zadejte **počet pracovních uzlů**, který určuje limit pro vertikální navyšování kapacity clusteru.
1. Vyberte možnost **založené na plánu** pod **typ automatického škálování**.
1. Klikněte na tlačítko **konfigurovat** otevřít **konfiguraci automatického škálování** okna.
1. Vyberte vaše časové pásmo a potom klikněte na tlačítko **+ přidat podmínku**
1. Vyberte dny v týdnu, který se má použít novou podmínku.
1. Upravte čas, podmínka by měla trvat účinek a počet uzlů, které cluster má být nastaveno měřítko pro.
1. V případě potřeby přidejte další podmínky.

    ![Povolit možnost automatického škálování na základě plánu uzlu pracovního procesu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Počet uzlů musí být mezi 1 a počet pracovních uzlů, které jste zadali před přidávání podmínek.

### <a name="final-creation-steps"></a>Postup vytvoření konečný

Pro změnu velikosti na základě zatížení i založené na plánu, vyberte typ virtuálního počítače pro uzly pracovního procesu kliknutím **velikost uzlu pracovního procesu** a **Head velikost uzlu**. Až zvolíte typ virtuálního počítače pro každý typ uzlu, se zobrazí odhadované náklady na rozsah pro celý cluster. Upravte typy virtuálních počítačů vyhovující vašemu rozpočtu.

![Povolit možnost automatického škálování na základě plánu uzlu pracovního procesu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader z vašich hlavních uzlů v kombinaci s maximální počet pracovních uzlů nesmí překročit kapacitu kvóty. Nicméně tato kvóta je doporučené omezení; vždy můžete vytvořit lístek podpory a její navýšení snadno.

> [!Note]  
> Pokud překročíte limit kvóty celkový počet jader, zobrazí se chybová zpráva s oznámením "překročil maximální počet uzel dostupných jader v této oblasti, zvolte prosím jiné oblasti nebo požádejte podporu o navýšení kvóty."

Další informace o vytváření clusteru HDInsight pomocí webu Azure portal najdete v tématu [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Vytvoření clusteru pomocí šablony Resource Manageru

#### <a name="load-based-autoscaling"></a>Automatické škálování na základě zatížení

Vytvoříte clusteru služby HDInsight pomocí automatického škálování založeného na zatížení šablonu Azure Resource Manageru tak, že přidáte `autoscale` uzlu `computeProfile`  >  `workernode` část s vlastnostmi `minInstanceCount` a `maxInstanceCount` jako je znázorněno v následujícím fragmentu kódu json.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
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

#### <a name="schedule-based-autoscaling"></a>Automatické škálování podle plánu

Vytvoříte clusteru služby HDInsight s automatickým Škálováním založené na plánu šablonu Azure Resource Manageru tak, že přidáte `autoscale` uzlu `computeProfile`  >  `workernode` oddílu. `autoscale` Obsahuje uzel `recurrence` , který má `timezone` a `schedule` , který popisuje, kdy bude probíhat změny.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Povolení a zákaz automatického škálování pro spuštěný cluster

Chcete-li povolit automatické škálování na spuštěný cluster, vyberte **velikost clusteru** pod **nastavení**. Pak klikněte na tlačítko **povolit automatické škálování**. Vyberte typ automatického škálování má a zadejte možnosti pro změnu velikosti na základě zatížení nebo na plán. Nakonec klikněte na tlačítko **Uložit**.

![Povolit možnost automatického škálování na základě plánu uzlu pracovního procesu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>Osvědčené postupy

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Výběr škálování na základě zatížení nebo na plán

Před provedením rozhodnutí na těchto režimech zvolit vezměte v úvahu následující faktory:

* Načíst odchylka: nemá zatížení clusteru postupujte podle vzoru konzistentní vzhledem k aplikacím v určitých časech v konkrétní dny. V opačném případě zatížení na základě plánování je lepší volbou.
* Smlouva SLA požadavky: Škálování automatického škálování je reaktivní místo prediktivní. Při spuštění zatížení pro zvýšení a kdy clusteru musí být v jeho Cílová velikost bude mít dostatek zpoždění mezi? Pokud existují přísných požadavků SLA a zatížení je dlouhodobý známé vzor, podle plánu je lepší volbou.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Vezměte v úvahu latence škálování nahoru nebo dolů operace

Může trvat 10 až 20 minut, než se škálování na dokončení operace. Při nastavování vlastní plán, naplánujte pro toto zpoždění. Například pokud potřebujete velikost clusteru bude 20 v 9:00:00, nastavte aktivační událost plánovače na dřívější čas, jako je například 8:30:00 tak, aby operace škálování dokončena v 9:00:00.

### <a name="preparation-for-scaling-down"></a>Příprava pro škálování

Během procesu škálování clusteru bude automatické škálování vyřadit z provozu uzly podle cílovou velikost. Pokud se na tyto uzly jsou spuštěné úlohy, automatické škálování počká, až jsou úlohy dokončeny. Protože každý pracovní uzel také slouží role v HDFS, dočasná data budou posunuty na zbývající uzly. Proto vám by měl Ujistěte se, že není dostatek místa na zbývající uzly k hostování dočasná data. 

Spuštěné úlohy budou ke spuštění a dokončení. K naplánování jako za normálních okolností s menším počtem dostupných pracovních uzlů počká, úlohy čekající na vyřízení.

## <a name="monitoring"></a>Monitorování

### <a name="cluster-status"></a>Stav clusteru

Stav clusteru, které jsou uvedeny na webu Azure Portal můžete sledovat aktivity automatického škálování.

![Povolit možnost automatického škálování na základě zatížení uzlu pracovního procesu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Všechny stavové zprávy na clusteru, které se můžou objevit jsou popsány v níže uvedeném seznamu.

| Stav clusteru | Vysvětlení |
|---|---|
| Běží | Cluster se normálně. Všechny předchozí aktivity automatického škálování, byly úspěšně dokončeny. |
| Aktualizuje se  | Konfigurace automatického škálování clusteru se aktualizuje.  |
| Konfigurace HdInsight  | Vertikální navýšení kapacity clusteru nebo vertikální snížení kapacity operace právě probíhá.  |
| Chyba aktualizace  | HDInsight nastaly problémy při aktualizaci konfigurace automatického škálování. Zákazníci můžou zvolit zopakujte aktualizaci nebo zakázat automatické škálování.  |
| Chyba  | Něco se nepovedlo s clusterem. proto se nedá použít. Tento cluster odstranit a vytvořit nové.  |

Chcete-li zobrazit aktuální počet uzlů v clusteru, přejděte na **velikost clusteru** graf na **přehled** stránky pro váš cluster, nebo klikněte na tlačítko **velikost clusteru** pod  **Nastavení**.

### <a name="operation-history"></a>Operace historie

Můžete zobrazit historii clusteru horizontálního navýšení nebo snížení jako součást clusteru metriky. Můžete také seznam všechny akce škálování za poslední den, týden nebo jiné časové období.

Vyberte **metriky** pod **monitorování**. Pak klikněte na tlačítko **přidat metriku** a **počet aktivních zaměstnanců** z **metrika** rozevíracím seznamu. Klikněte na tlačítko v pravém horním rohu, chcete-li změnit časový rozsah.

![Povolit možnost automatického škálování na základě plánu uzlu pracovního procesu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Další postup

* Přečtěte si informace o osvědčené postupy pro škálování clusterů ručně v [osvědčené postupy pro škálování](hdinsight-scaling-best-practices.md)
