---
title: Služby zprovoznění ML ve službě HDInsight – Azure
description: Naučte se, jak zprovoznění datový model a vytvořit předpovědi se službami ML ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 1a5a46957c92fb2c14907db728216481f3f57aac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087686"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Cluster služeb zprovoznění ML ve službě Azure HDInsight

Po použití clusteru ML Services ve službě HDInsight k dokončení modelování dat můžete zprovoznění model a vytvořit předpovědi. Tento článek poskytuje pokyny k provedení této úlohy.

## <a name="prerequisites"></a>Požadavky

* Cluster služeb ML v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a výběr **služeb ml** pro **typ clusteru**.

* Klient Secure Shell (SSH): Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Cluster služeb zprovoznění ML s jedním polem konfigurace

> [!NOTE]  
> Následující postup se týká R Server 9,0 a ML Server 9,1. Informace o ML Server 9,3 najdete v tématu [Správa konfigurace provozu pomocí nástroje pro správu](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Připojte se přes SSH k hraničnímu uzlu.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Pokyny k použití SSH se službou Azure HDInsight najdete v tématu [Použití SSH se službou HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Změňte adresář pro příslušnou verzi a sudo síťovou knihovnu DLL s tečkou: 

    - Pro Microsoft ML Server 9,1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Pro Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. Zobrazí se možnosti, ze kterých si můžete vybrat. Vyberte první možnost, jak je znázorněno na následujícím snímku obrazovky, abyste **nakonfigurovali ml Server pro provozuschopnost**.

    ![Výběr nástroje pro správu R serveru](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Nyní máte k dispozici možnost zvolit způsob, jakým se má zprovoznění ML Server. Z prezentovaných možností vyberte **první zadáním.**

    ![Zprovoznění nástroje pro správu R serveru](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Po zobrazení výzvy zadejte a znovu zadejte heslo pro místního uživatele s oprávněními správce.

1. Měli byste vidět výstupy naznačující, že operace byla úspěšná. Také se zobrazí výzva, abyste v nabídce vybrali jinou možnost. Vyberte E a vraťte se do hlavní nabídky.

    ![Nástroj pro správu R serveru byl úspěšný](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Volitelně můžete provádět diagnostické kontroly spuštěním diagnostického testu následujícím způsobem:

    a. V hlavní nabídce vyberte **6** a spusťte diagnostické testy.

    ![Diagnostika nástroje pro správu R serveru](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. **V nabídce diagnostické testy vyberte.** Po zobrazení výzvy zadejte heslo, které jste zadali pro místního uživatele s oprávněními správce.

    ![Test nástroje pro správu R serveru](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Ověřte, že výstup ukazuje, že celkový stav je Pass.

    ![Průchod nástrojem pro správu R serveru](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. V zobrazených možnostech nabídky zadejte **E** pro návrat do hlavní nabídky a pak zadáním **8** ukončete nástroj pro správu.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Dlouhá prodlevy při využívání webové služby v Apache Spark

Pokud dojde k dlouhým prodlevám při pokusu o využívání webové služby vytvořené pomocí funkcí mrsdeploy ve výpočetním kontextu Apache Spark, možná budete muset přidat některé chybějící složky. Pokaždé, když je aplikace Spark vyvolaná z webové služby pomocí funkcí mrsdeploy, patří uživateli *rserve2*. Náhradní řešení tohoto problému:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

V této fázi je konfigurace operacionalizace dokončena. Nyní můžete použít `mrsdeploy` balíček na klientovi rclient pro připojení k provozu na hraničním uzlu a začít používat jeho funkce, jako je [vzdálené spuštění](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) a [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). V závislosti na tom, jestli je váš cluster nastavený ve virtuální síti, může být potřeba nastavit přesměrování portu tunelovým propojením přes přihlášení SSH. Následující části vysvětlují, jak tento tunel nastavit.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster služeb ML ve virtuální síti

Ověřte, že je na hraničním uzlu povolený provoz přes port 12800. Tímto způsobem můžete hraniční uzel použít pro připojení k funkci operacionalizace.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Pokud se metoda `remoteLogin()` nemůže připojit k hraničnímu uzlu, ale můžete se k němu připojit přes SSH, budete muset ověřit, jestli je správně nastavené pravidlo pro povolení provozu na portu 12800. Pokud bude tento problém přetrvávat, můžete jako alternativní řešení použít nastavení přesměrování portu tunelovým propojením přes SSH. Pokyny najdete v následující části:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster služeb ML není nastavený ve virtuální síti

Pokud váš cluster není nastavený ve virtuální síti nebo máte potíže s připojením přes virtuální síť, můžete použít přesměrování portu tunelovým propojením přes SSH:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Jakmile bude relace SSH aktivní, provoz z portu 12800 vašeho místního počítače se přepošle na port 12800 hraničního uzlu prostřednictvím relace SSH. Nezapomeňte v metodě `remoteLogin()` použít adresu `127.0.0.1:12800`. Tato operace se zaznamená do provozu hraničního uzlu prostřednictvím předávání portů.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Škálování provozních výpočetních uzlů na pracovních uzlech HDInsight

Chcete-li škálovat výpočetní uzly, nejprve vyřaďte z provozu pracovní uzly a pak nakonfigurujte výpočetní uzly na vyřazených pracovních uzlech.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: vyřazení pracovních uzlů z provozu

Cluster služeb ML není spravován prostřednictvím [Apache HADOOP příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Pokud se pracovní uzly nevyřadí z provozu, Správce prostředků PŘÍZe nefunguje podle očekávání, protože neví o prostředcích, které server zabere. Abyste této situaci zabránili, doporučujeme před horizontálním navýšením kapacity výpočetních uzlů vyřadit z provozu pracovní uzly.

Pomocí těchto kroků vyřaďte z provozu pracovní uzly:

1. Přihlaste se ke konzole Ambari clusteru a klikněte na kartu **hostitelé** .

1. Vyberte pracovní uzly (budou vyřazeny z provozu).

1. Klikněte na **Akce**  >  **vybrané hostitelé**  >  **hostitelé**  >  **zapnout režim údržby**. Například na následujícím obrázku jsme vybrali k vyřazení z provozu uzly wn3 a wn4.  

   ![Režim údržby pro Apache Ambari](./media/r-server-operationalize/get-started-operationalization.png)  

* Vyberte **Akce**  >  **Vybraní hostitelé**– datové  >  **uzly** > klikněte na **vyřadit z provozu**.
* Vyberte **Akce**  >  **vybrané hostitelé**  >  **NodeManagers** > klikněte na **vyřadit z provozu**.
* Vyberte **Akce**, které jsou  >  **Vybraní hostitelé**  >  :**datanode** > klikněte na **zastavit**.
* Vyberte **Akce**  >  **vybrané hostitele**  >  **NodeManagers** > klikněte na **zastavit**.
* Vyberte **Akce**  >  **vybrané hostitele**  >  **hostitelé** > klikněte na **Zastavit všechny součásti**.
* Zrušte výběr pracovních uzlů a vyberte hlavní uzly.
* Vyberte **Akce**  >  **vybrané hostitele** > "**hostitelé**  >  **restartují všechny součásti**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurace výpočetních uzlů pro každý vyřazený pracovní uzel (y)

1. Přihlaste se přes SSH do každého vyřazeného pracovního uzlu.

1. Spusťte nástroj pro správu pomocí příslušné knihovny DLL pro cluster služeb ML, který máte. Pro ML Server 9,1 Spusťte následující příkaz:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Zadejte **1** pro výběr možnosti **Konfigurovat ml Server pro provozuschopnost**.

1. Zadejte **C** pro výběr možnosti `C. Compute node` . Tím se na pracovním uzlu nakonfiguruje výpočetní uzel.

1. Ukončete nástroj pro správu.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Přidání podrobností výpočetních uzlů na webový uzel

Jakmile budou všechny vyřazené pracovní uzly nakonfigurované tak, aby spouštěly výpočetní uzel, vraťte se do hraničního uzlu a přidejte IP adresy vyřazených pracovních uzlů do konfigurace ML Server webového uzlu:

1. Připojte se přes SSH k hraničnímu uzlu.

1. Spusťte příkaz `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Vyhledejte část URI a přidejte podrobnosti o IP a portech pracovního uzlu.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Další kroky

* [Správa clusteru služby ML Services v HDInsight](r-server-hdinsight-manage.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)
