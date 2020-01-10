---
title: Automatické škálování clusterů Azure HDInsight
description: Použití funkce automatického škálování Azure HDInsight k automatickému Apache Hadoop škálování clusterů
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 45804bd3e81e7363010979b7a6e028356b3a5080
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780058"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatické škálování clusterů Azure HDInsight

> [!Important]
> Funkce automatického škálování funguje jenom pro clustery Spark, podregistr, LLAP a HBA, které se vytvořily až do 8. května 2019. 

Funkce automatického škálování clusteru Azure HDInsight automaticky škáluje počet pracovních uzlů v clusteru směrem nahoru a dolů. V tuto chvíli nejde škálovat jiné typy uzlů v clusteru.  Během vytváření nového clusteru HDInsight je možné nastavit minimální a maximální počet pracovních uzlů. Automatické škálování pak monitoruje požadavky na prostředky analytického zatížení a škáluje počet uzlů pracovních procesů nahoru nebo dolů. Pro tuto funkci se neúčtují žádné další poplatky.

## <a name="cluster-compatibility"></a>Kompatibilita clusteru

Následující tabulka popisuje typy clusterů a verze, které jsou kompatibilní s funkcí automatického škálování.

| Verze | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 bez protokolu ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |
| HDInsight 4,0 bez protokolu ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |
| HDInsight 3,6 s ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |
| HDInsight 4,0 s ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |

clustery \* HBA lze konfigurovat pouze pro škálování na základě plánu, nikoli na základě zatížení.

## <a name="how-it-works"></a>Jak to funguje

Pro cluster HDInsight můžete zvolit škálování na základě zatížení nebo škálování na základě plánu. Škálování na základě zatížení mění počet uzlů v clusteru v rozsahu, který jste nastavili, k zajištění optimálního využití procesoru a minimalizaci průběžných nákladů.

Škálování na základě plánu mění počet uzlů v clusteru na základě podmínek, které se v určitých časech projeví. Tyto podmínky škálují cluster na požadovaný počet uzlů.

### <a name="metrics-monitoring"></a>Monitorování metrik

Automatické škálování průběžně monitoruje cluster a shromažďuje následující metriky:

* **Celkový počet vyřízených procesorů**: celkový počet jader potřebných ke spuštění provádění všech nevyřízených kontejnerů.
* **Celkový počet nevyřízených paměti**: Celková paměť (v MB) požadovaná k zahájení provádění všech nevyřízených kontejnerů.
* **Celkový bezplatný procesor**: součet všech nevyužitých jader v aktivních pracovních uzlech.
* **Celková volná paměť**: součet nevyužité paměti (v MB) na aktivních pracovních uzlech.
* **Využitá paměť na uzel**: zatížení v pracovním uzlu. Pracovní uzel, na kterém se používá 10 GB paměti, se považuje za větší zatížení než pracovní proces se 2 GB využité paměti.
* **Počet hlavních serverů aplikací na uzel**: počet kontejnerů hlavních aplikací (am) spuštěných v pracovním uzlu. Pracovní uzel, který je hostitelem dvou kontejnerů AM, je považován za důležitější než pracovní uzel, který je hostitelem kontejnerů s nulovou hodnotou.

Výše uvedené metriky se kontrolují každých 60 sekund. Automatické škálování provádí rozhodování na úrovni horizontálního škálování a horizontálního škálování na základě těchto metrik.

### <a name="load-based-cluster-scale-up"></a>Škálování clusteru založené na zatížení

Při zjištění následujících podmínek bude automatické škálování vydávat požadavek na škálování na více míst:

* Celkový počet vyřízených PROCESORů je větší než 3 minuty, ale celkový počet volných PROCESORů.
* Celkový počet nevyřízených paměti je větší než celková volná paměť po dobu více než 3 minut.

Služba HDInsight počítá, kolik nových pracovních uzlů je potřebných pro splnění aktuálních požadavků na procesor a paměť, a pak vydá požadavek na horizontální navýšení kapacity k přidání požadovaného počtu uzlů.

### <a name="load-based-cluster-scale-down"></a>Škálování clusteru založené na zatížení – dolů

Při zjištění následujících podmínek bude automatické škálování vydávat požadavek na horizontální navýšení kapacity:

* Celkový počet nevyřízených PROCESORů je menší než celkový bezplatný procesor po dobu více než 10 minut.
* Celková paměť, která čeká na vyřízení, je menší než celková volná paměť po dobu více než 10 minut.

V závislosti na počtu kontejnerů AM na uzel a aktuálních požadavcích na procesor a paměť vystaví automatické škálování požadavek na odebrání určitého počtu uzlů. Služba také detekuje, které uzly jsou kandidáty na odebrání na základě aktuálního spuštění úlohy. Operace horizontálního snížení kapacity nejprve vyřadí uzly z provozu a pak je z clusteru odebere.

## <a name="get-started"></a>Začít

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Vytvoření clusteru s automatickým škálováním na základě zatížení

Pokud chcete použít automatické škálování v clusteru, musí být při vytvoření clusteru povolená možnost **Povolit automatické škálování** . 

Pokud chcete funkci automatického škálování povolit s škálováním na základě zatížení, proveďte v rámci normálního procesu vytváření clusteru následující kroky:

1. Na kartě **Konfigurace + ceny** zaškrtněte políčko **Povolit automatické škálování** .
1. V části **typ automatického škálování**vyberte **Load-based** .
1. Zadejte požadované hodnoty pro následující vlastnosti:  

    * Počáteční **počet uzlů** pro **pracovní uzel**
    * **Minimální** počet pracovních uzlů.
    * **Maximální** počet pracovních uzlů.

    ![Povolit automatické škálování na základě zatížení pracovních uzlů](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Počáteční počet uzlů pracovního procesu musí být mezi minimální a maximální (včetně). Tato hodnota určuje počáteční velikost clusteru při jeho vytvoření. Minimální počet uzlů pracovního procesu by měl být nastavený na tři nebo víc. . Škálování clusteru na méně než tři uzly může vést k zablokování v bezpečném režimu z důvodu nedostatečné replikace souborů. Další informace najdete [v tématu Získání zablokování v nouzovém režimu]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Vytvoření clusteru s automatickým škálováním na základě plánu

Pokud chcete funkci automatického škálování povolit s škálováním na základě plánu, proveďte v rámci normálního procesu vytváření clusteru následující kroky:

1. Na kartě **Konfigurace + ceny** zaškrtněte políčko **Povolit automatické škálování** .
1. Zadejte **počet uzlů** pro **pracovní uzel**, který určuje limit pro škálování clusteru.
1. V části **typ automatického škálování**vyberte možnost **plán – based** .
1. Kliknutím na **Konfigurovat** otevřete okno **Konfigurace automatického škálování** .
1. Vyberte své časové pásmo a pak klikněte na **+ Přidat podmínku** .
1. Vyberte dny v týdnu, na které se má nová podmínka vztahovat.
1. Upravte čas uplatnění podmínky a počet uzlů, na které se má cluster škálovat.
1. V případě potřeby přidejte další podmínky.

    ![Povolit vytváření na základě plánu pracovních uzlů](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Počet uzlů musí být mezi 3 a maximálním počtem pracovních uzlů, které jste zadali před přidáním podmínek.

### <a name="final-creation-steps"></a>Kroky konečného vytvoření

Pro škálování na základě zatížení a na základě plánu vyberte typ virtuálního počítače pro pracovní uzly tak, že v rozevíracím seznamu v části **Velikost uzlu**vyberete virtuální počítač. Po výběru typu virtuálního počítače pro každý typ uzlu můžete zobrazit odhadované rozsahy nákladů pro celý cluster. Upravte typy virtuálních počítačů tak, aby odpovídaly vašemu rozpočtu.

![Povolit velikost uzlu automatického škálování na základě plánu pracovního uzlu](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader hlavních uzlů v kombinaci s maximálním počtem pracovních uzlů nemůže překročit kvótu kapacity. Tato kvóta je ale měkké omezení; kdykoli můžete vytvořit lístek podpory, abyste ho mohli snadno zvýšit.

> [!Note]  
> Pokud překročíte celkový počet základních kvót, zobrazí se chybová zpráva s oznámením, že maximální uzel překročil dostupné jádra v této oblasti, zvolte prosím jinou oblast, nebo požádejte podporu o zvýšení kvóty.

Další informace o vytváření clusteru HDInsight pomocí Azure Portal najdete v tématu [Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Vytvoření clusteru s Správce prostředků šablonou

#### <a name="load-based-autoscaling"></a>Automatické škálování na základě zatížení

Cluster HDInsight s automatickým škálováním na základě zatížení můžete vytvořit pomocí šablony Azure Resource Manager přidáním uzlu `autoscale` do `computeProfile` > `workernode` oddílu s vlastnostmi `minInstanceCount` a `maxInstanceCount`, jak je znázorněno v následujícím fragmentu kódu JSON.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
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

Další informace o vytváření clusterů pomocí šablon Správce prostředků najdete v tématu [vytvoření Apache Hadoop clusterů ve službě HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Automatické škálování na základě plánu

Cluster HDInsight s automatickým škálováním na základě plánu můžete vytvořit pomocí šablony Azure Resource Manager přidáním uzlu `autoscale` do oddílu `computeProfile` > `workernode`. Uzel `autoscale` obsahuje `recurrence`, který obsahuje `timezone` a `schedule`, které popisují, kdy bude změna provedena.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Povolit a zakázat automatické škálování běžícího clusteru

#### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete povolit automatické škálování na běžícím clusteru, vyberte v části **Nastavení** **Velikost clusteru** . Pak klikněte na **Povolit automatické škálování**. Vyberte typ automatického škálování, který chcete, a zadejte možnosti pro škálování na základě zatížení nebo na základě plánu. Nakonec klikněte na **Uložit**.

![Povolit automatické škálování na základě plánu pracovních uzlů v clusteru](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>S využitím REST API

Pokud chcete povolit nebo zakázat automatické škálování ve spuštěném clusteru pomocí REST API, vytvořte požadavek POST na koncový bod automatického škálování, jak je znázorněno v následujícím fragmentu kódu:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Použijte příslušné parametry v datové části požadavku. K povolení automatického škálování můžete použít datovou část JSON níže. Automatické škálování zakažte pomocí `{autoscale: null}` datové části.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Úplný popis všech parametrů datové části najdete v předchozím oddílu věnovaném [Povolení automatického škálování na základě zatížení](#load-based-autoscaling) .

## <a name="best-practices"></a>Osvědčené postupy

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Výběr škálování na základě zatížení nebo podle plánu

Než se rozhodnete, jaký režim zvolit, zvažte následující faktory:

* Povolí automatické škálování během vytváření clusteru.
* Minimální počet uzlů by měl být alespoň tři.
* Odchylka zatížení: provede zatížení clusteru v určitých časech podle konzistentního vzoru, a to v konkrétní dny. V takovém případě je plánování na základě zatížení lepší volbou.
* Požadavky smlouvy SLA: škálování automatického škálování se znovu aktivuje místo prediktivního. Bude docházet ke zpoždění mezi tím, kdy se zatížení začne zvyšovat a v případě, že cluster potřebuje mít cílovou velikost? Pokud existují přísné požadavky smlouvy SLA a zatížení je pevným známým vzorem, je lepší volbou plánování na základě plánu.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Zvažte latenci operací horizontálního navýšení nebo snížení kapacity.

Dokončení operace škálování může trvat 10 až 20 minut. Při nastavování přizpůsobeného plánu Naplánujte tuto prodlevu. Pokud například potřebujete, aby cluster byl 20 v 9:00 ráno, nastavte aktivační událost plánovače na dřívější čas, například 8:30 dop. to znamená, že operace škálování byla dokončena pomocí 9:00.

### <a name="preparation-for-scaling-down"></a>Příprava na horizontální navýšení kapacity

Během procesu horizontálního navýšení kapacity clusteru bude automatické škálování vyřadit uzly do provozu, aby splňovaly cílovou velikost. Pokud na těchto uzlech běží úlohy, bude automatické škálování čekat na dokončení úkolů. Vzhledem k tomu, že každý pracovní uzel také slouží jako role v HDFS, dočasná data budou přesunuta do zbývajících uzlů. Měli byste se ujistit, že na zbývajících uzlech je dostatek místa pro hostování všech dočasných dat.

Běžící úlohy budou i nadále spouštěny a dokončeny. Čekající úlohy budou čekat na plánování jako normální s menším počtem dostupných pracovních uzlů.

### <a name="minimum-cluster-size"></a>Minimální velikost clusteru

Neměňte rozsah clusteru dolů na méně než tři uzly. Škálování clusteru na méně než tři uzly může vést k zablokování v bezpečném režimu z důvodu nedostatečné replikace souborů. Další informace najdete [v tématu Získání zablokování v nouzovém režimu]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

## <a name="monitoring"></a>Sledování

### <a name="cluster-status"></a>Stav clusteru

Stav clusteru uvedený v Azure Portal vám může pomáhat monitorovat aktivity automatického škálování.

![Povolit stav clusteru automatického škálování na základě zatížení pracovního uzlu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Všechny stavové zprávy clusteru, které se mohou zobrazit, jsou vysvětleny v následujícím seznamu.

| Stav clusteru | Vysvětlení |
|---|---|
| Spuštěno | Cluster pracuje normálně. Všechny předchozí aktivity automatického škálování se úspěšně dokončily. |
| Aktualizace  | Aktualizuje se konfigurace automatického škálování clusteru.  |
| Konfigurace HDInsight  | Probíhá operace škálování a škálování clusteru.  |
| Chyba aktualizace  | Během aktualizace konfigurace automatického škálování zjistila HDInsight problémy. Zákazníci si můžou zvolit, že se má znovu aktualizovat nebo zakázat automatické škálování.  |
| Chyba  | S clusterem je něco špatného a nedá se použít. Odstraňte tento cluster a vytvořte nový.  |

Pokud chcete zobrazit aktuální počet uzlů v clusteru, přejděte na graf **velikosti clusteru** na stránce **Přehled** pro váš cluster nebo klikněte na **Velikost clusteru** v části **Nastavení**.

### <a name="operation-history"></a>Historie operací

Historii škálování a škálování clusteru můžete zobrazit v rámci metriky clusteru. Můžete také zobrazit seznam všech akcí škálování za poslední den, týden nebo jiné časové období.

V části **monitorování**vyberte **metriky** . Pak v rozevíracím seznamu **metrika** klikněte na **Přidat metriku** a **Počet aktivních pracovníků** . Chcete-li změnit časový rozsah, klikněte na tlačítko v pravém horním rohu.

![Povolit metriku automatického škálování na základě plánu pracovního uzlu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si o osvědčených postupech pro ruční škálování clusterů při [škálování osvědčených postupů](hdinsight-scaling-best-practices.md) .
