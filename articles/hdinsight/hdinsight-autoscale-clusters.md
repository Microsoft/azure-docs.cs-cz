---
title: Automatické škálování clusterů Azure HDInsight
description: Automatické škálování Azure HDInsight slouží k automatickému škálování clusterů Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 68bc30d08d95fe8e3d20a8ecb7af6c9710951921
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399718"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatické škálování clusterů Azure HDInsight

> [!Important]
> 7. listopadu 2019 byla vydána funkce automatického škálování Azure HDInsight pro obecnou dostupnost pro clustery Spark a Hadoop a zahrnovala vylepšení, která nejsou k dispozici ve verzi preview této funkce. Pokud jste vytvořili cluster Spark před 7.
>
> Automatické škálování pro interaktivní dotaz (LLAP) a HBase clustery je stále ve verzi preview. Automatické škálování je k dispozici pouze v clusterech Spark, Hadoop, Interaktivní dotaz a HBase.

Funkce automatického škálování clusteru Azure HDInsight automaticky škáluje počet pracovních uzlů v clusteru nahoru a dolů. Jiné typy uzlů v clusteru nelze aktuálně škálovat.  Při vytváření nového clusteru HDInsight lze nastavit minimální a maximální počet pracovních uzlů. Automatické škálování pak sleduje požadavky na prostředky zatížení analytics a škáluje počet pracovních uzlů nahoru nebo dolů. Za tuto funkci se neplatí žádné další poplatky.

## <a name="cluster-compatibility"></a>Kompatibilita clusteru

Následující tabulka popisuje typy a verze clusteru, které jsou kompatibilní s funkcí Automatické škálování.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 bez ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |
| HDInsight 4.0 bez ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |
| HDInsight 3.6 s ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |
| HDInsight 4.0 s ESP | Ano | Ano | Ano | Ano* | Ne | Ne | Ne |

\*Clustery HBase lze nakonfigurovat pouze pro škálování založené na plánu, nikoli na zatížení.

## <a name="how-it-works"></a>Jak to funguje

Pro váš cluster HDInsight můžete zvolit škálování založené na zatížení nebo měřítko na základě plánu. Škálování založené na zatížení mění počet uzlů v clusteru v nastaveném rozsahu, aby bylo zajištěno optimální využití procesoru a minimalizovány náklady na provoz.

Škálování založené na plánu změní počet uzlů v clusteru na základě podmínek, které se projeví v určitých časech. Tyto podmínky škálovat clusteru na požadovaný počet uzlů.

### <a name="metrics-monitoring"></a>Monitorování metrik

Automatické škálování nepřetržitě monitoruje cluster a shromažďuje následující metriky:

|Metrika|Popis|
|---|---|
|Celkový počet čekajících procesorů|Celkový počet jader potřebných ke spuštění spuštění všech čekajících kontejnerů.|
|Celkový počet nevyřízených paměti|Celková paměť (v MB) potřebné ke spuštění spuštění všech čekajících kontejnerů.|
|Celkový volný procesor|Součet všech nevyužitých jader v aktivních pracovních uzlech.|
|Celková volná paměť|Součet nevyužité paměti (v MB) v aktivních pracovních uzlech.|
|Použitá paměť na uzel|Zatížení pracovního uzlu. Pracovní uzel, na kterém se používá 10 GB paměti, je považován za více zatížení než pracovník s 2 GB použité paměti.|
|Počet předloh aplikací na uzel|Počet kontejnerů Hlavního použití (AM) spuštěných v pracovním uzlu. Pracovní uzel, který je hostitelem dvou kontejnerů AM, je považován za důležitější než pracovní uzel, který je hostitelem nulové kontejnery AM.|

Výše uvedené metriky jsou kontrolovány každých 60 sekund. Automatické škálování umožňuje škálování nahoru a škálování rozhodnutí na základě těchto metrik.

### <a name="load-based-scale-conditions"></a>Podmínky stupnice založené na zatížení

Po zjištění následujících podmínek vystaví automatické škálování požadavek na škálování:

|Vertikální navýšení kapacity|Zmenšení rozsahu|
|---|---|
|Celkový procesor čekající na vyřízení je větší než celkový volný procesor po dobu delší než 3 minuty.|Celkový procesor čekající na vyřízení je menší než celkový volný procesor po dobu delší než 10 minut.|
|Celkový počet čekajících paměti je větší než celková volná paměť po dobu delší než 3 minuty.|Celkový počet čekajících paměti je menší než celková volná paměť po dobu delší než 10 minut.|

Pro škálování služby HDInsight vypočítá, kolik nových pracovních uzlů jsou potřebné ke splnění aktuální požadavky na procesor a paměť a pak vydá požadavek na navýšení kapacity přidat požadovaný počet uzlů.

Pro škálování dolů, na základě počtu kontejnerů AM na uzel a aktuální požadavky na procesor a paměť, automatické škálování vydá požadavek na odebrání určitého počtu uzlů. Služba také zjistí, které uzly jsou kandidáty pro odebrání na základě aktuálního spuštění úlohy. Horizontální navýšení kapacity operace nejprve vyřazuje z provozu uzly a potom je odebere z clusteru.

## <a name="get-started"></a>Začínáme

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Vytvoření clusteru s automatickým škálováním založeným na zatížení

Chcete-li povolit funkci automatického škálování s škálováním založeným na zatížení, proveďte následující kroky jako součást normálního procesu vytváření clusteru:

1. Na kartě **Konfigurace + ceny** zaškrtněte políčko **Povolit automatické škálování.**
1. V části **Typ automatického škálování**vyberte **Možnost Načíst.**
1. Zadejte požadované hodnoty pro následující vlastnosti:  

    * Počáteční **počet uzlů** pro uzel **pracovníka**.
    * **Min** počet pracovních uzlů.
    * **Maximální** počet pracovních uzlů.

    ![Povolení automatického škálování založené na zatížení pracovního uzlu](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Počáteční počet uzlů pracovníka musí spadat mezi minimální a maximální včetně. Tato hodnota definuje počáteční velikost clusteru při jeho vytvoření. Minimální počet pracovních uzlů by měl být nastaven na tři nebo více. Změna velikosti clusteru na méně než tři uzly může vést k jeho uvíznutí v nouzovém režimu z důvodu nedostatečné replikace souborů.  Další informace naleznete [v tématu Uvíznutí v nouzovém režimu](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Vytvoření clusteru s automatickým škálováním na základě plánu

Chcete-li povolit funkci automatického škálování s měřítkem založeným na plánu, proveďte následující kroky v rámci normálního procesu vytváření clusteru:

1. Na kartě **Konfigurace + ceny** zaškrtněte políčko **Povolit automatické škálování.**
1. Zadejte **počet uzlů** pro pracovní **uzel**, který řídí limit pro škálování clusteru.
1. Vyberte možnost **Naplánovat na základě** v části **Typ automatického škálování**.
1. Vyberte **Konfigurovat,** **chcete-li otevřít konfigurační okno automatického škálování.**
1. Vyberte časové pásmo a klikněte na **+ Přidat podmínku**
1. Vyberte dny v týdnu, na které se má vztahovat nová podmínka.
1. Upravte čas, kdy by se měla podmínka projevit, a počet uzlů, na které by měl být cluster škálován.
1. V případě potřeby přidejte další podmínky.

    ![Povolit vytváření na základě plánu pracovního uzlu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Počet uzlů musí být mezi 3 a maximální počet pracovních uzlů, které jste zadali před přidáním podmínky.

### <a name="final-creation-steps"></a>Konečné kroky vytvoření

Pro škálování založené na zatížení i na plánu vyberte typ virtuálního počítače pro pracovní uzly výběrem virtuálního počítače z rozevíracího seznamu v části **Velikost uzlu**. Po výběru typu virtuálního virtuálního virtuálního clusteru pro každý typ uzlu uvidíte odhadovaný rozsah nákladů pro celý cluster. Upravte typy virtuálních počítačů tak, aby odpovídaly vašemu rozpočtu.

![Povolit velikost uzlu založenéna automatického škálování pracovních uzlů](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader hlavních uzlů v kombinaci s maximálním počtem pracovních uzlů nesmí překročit kvótu kapacity. Tato kvóta je však měkkým limitem; vždy můžete vytvořit lístek podpory, abyste jej snadno zvýšili.

> [!Note]  
> Pokud překročíte celkový limit kvóty jádra, zobrazí se chybová zpráva "maximální počet uzlů překročil dostupná jádra v této oblasti, zvolte jinou oblast nebo se obraťte na podporu pro zvýšení kvóty."

Další informace o vytváření clusteru HDInsight pomocí portálu Azure najdete v tématu [Vytváření clusterů založených na Linuxu ve službě HDInsight pomocí portálu Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Vytvoření clusteru pomocí šablony Správce prostředků

#### <a name="load-based-autoscaling"></a>Automatické škálování založené na zatížení

Cluster HDInsight můžete vytvořit s automatickým škálováním na základě zatížení `autoscale` šablony Azure `computeProfile`  >  `workernode` Resource Manager, `minInstanceCount` `maxInstanceCount` přidáním uzlu do oddílu s vlastnostmi a jak je znázorněno v fragmentu json níže.

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

Další informace o vytváření clusterů pomocí šablon Správce prostředků najdete v tématu [Vytváření clusterů Apache Hadoop v HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Automatické škálování založené na plánu

Cluster HDInsight můžete vytvořit pomocí automatického škálování na základě plánu `autoscale` šablony Azure `computeProfile`  >  `workernode` Resource Manager, přidáním uzlu do sekce. Uzel `autoscale` obsahuje, `recurrence` který má `timezone` `schedule` a který popisuje, kdy dojde ke změně.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Povolení a zakázání automatického škálování pro spuštěný cluster

#### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li povolit automatické škálování v běžícím clusteru, vyberte v části **Nastavení** **možnost Velikost clusteru** . Pak vyberte **Povolit automatické škálování**. Vyberte požadovaný typ automatického škálování a zadejte možnosti pro změnu měřítka založenou na zatížení nebo na plánu. Nakonec vyberte **uložit**.

![Povolení spuštění automatického škálování na základě plánu pracovních uzlů](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>S využitím REST API

Chcete-li povolit nebo zakázat automatické škálování v běžícím clusteru pomocí rozhraní REST API, vytvořte požadavek POST do koncového bodu automatického škálování, jak je znázorněno ve fragmentu kódu níže:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Použijte příslušné parametry v datové části požadavku. Následující datová část json by mohla být použita k povolení automatického škálování. Pomocí datové `{autoscale: null}` části zakažte automatické škálování.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Úplný popis všech parametrů datové části naleznete v předchozí části o [povolení automatického škálování založeného](#load-based-autoscaling) na zatížení.

## <a name="best-practices"></a>Osvědčené postupy

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Volba škálování založené na zatížení nebo na plánu

Než se rozhodnete, který režim zvolit, zvažte následující faktory:

* Povolte automatické škálování během vytváření clusteru.
* Minimální počet uzlů by měl být alespoň tři.
* Odchylka zatížení: sleduje zatížení clusteru konzistentní vzorek v určitých časech v určitých dnech. Pokud tomu tak není, plánování založené na zatížení je lepší volbou.
* Požadavky s la: Automatické škálování škálování je reaktivní namísto prediktivní. Bude existovat dostatečná prodleva mezi začátkem načítání a kdy cluster musí mít cílovou velikost? Pokud existují přísné požadavky sla a zatížení je pevný známý vzor, "na základě plánu" je lepší volbou.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Zvažte latenci vertikálně navýšit kapacitu nebo vertikálně snížit kapacitu operací

Dokončení operace škálování může trvat 10 až 20 minut. Při nastavování přizpůsobeného plánu naplánujte toto zpoždění. Například pokud potřebujete velikost clusteru 20 v 9:00, nastavte aktivační událost plánu na dřívější čas, například 8:30 am tak, aby operace škálování byla dokončena do 9:00.

### <a name="preparation-for-scaling-down"></a>Příprava na zmenšení

Během procesu škálování clusteru automatické škálování vyřazuje z provozu uzly tak, aby splňovaly cílovou velikost. Pokud jsou spuštěny úlohy na těchto uzlech, automatické škálování bude čekat na dokončení úloh. Vzhledem k tomu, že každý pracovní uzel také slouží roli v HDFS, temp data budou přesunuta do zbývajících uzlů. Takže byste se měli ujistit, že je ve zbývajících uzlech dostatek místa pro hostování všech dočasných dat.

Spuštěné úlohy budou nadále spuštěny a dokončeny. Čekající úlohy budou čekat na normální plánování s menším počtem dostupných pracovních uzlů.

### <a name="minimum-cluster-size"></a>Minimální velikost clusteru

Nezmenšujte velikost clusteru na méně než tři uzly. Změna velikosti clusteru na méně než tři uzly může vést k jeho uvíznutí v nouzovém režimu z důvodu nedostatečné replikace souborů.  Další informace naleznete [v tématu Uvíznutí v nouzovém režimu](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="monitoring"></a>Monitorování

### <a name="cluster-status"></a>Stav clusteru

Stav clusteru uvedený na portálu Azure vám může pomoct sledovat aktivity automatického škálování.

![Povolení stavu clusteru automatického škálování založeného na načtení pracovního uzlu](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Všechny zprávy o stavu clusteru, které se mohou zobrazit, jsou vysvětleny v seznamu níže.

| Stav clusteru | Popis |
|---|---|
| Spuštěno | Cluster pracuje normálně. Všechny předchozí aktivity automatického škálování byly úspěšně dokončeny. |
| Aktualizace  | Probíhá aktualizace konfigurace automatického škálování clusteru.  |
| Konfigurace HDInsight  | Probíhá operace vertikálně navýšit kapacitu nebo vertikálně navýšit kapacitu clusteru.  |
| Chyba aktualizace  | HdInsight došlo k problémům během aktualizace konfigurace automatického škálování. Zákazníci se mohou rozhodnout, zda aktualizaci zopakují, nebo zakáže automatické škálování.  |
| Chyba  | Něco je špatně s clusterem, a to není použitelné. Odstraňte tento cluster a vytvořte nový.  |

Chcete-li zobrazit aktuální počet uzlů v clusteru, přejděte do **velikostního** grafu clusteru na stránce **Přehled** clusteru nebo vyberte **velikost clusteru** v části **Nastavení**.

### <a name="operation-history"></a>Historie provozu

Můžete zobrazit historii škálování nahoru a škálování dolů jako součást metrikclusteru. Můžete také uvést všechny akce škálování za poslední den, týden nebo jiné časové období.

V části **Sledování**vyberte **metriky** . Pak v rozevíracím poli **Metrika** vyberte **Přidat metriku** a **počet aktivních pracovníků.** Chcete-li změnit časový rozsah, vyberte tlačítko v pravém horním horním.

![Povolení metriky automatického škálování založené na plánu pracovních uzlů](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si o doporučených postupech pro ruční škálování clusterů v [doporučených postupech škálování](hdinsight-scaling-best-practices.md)
