---
title: Zprovoznění služeb ML na HDInsight – Azure
description: Zjistěte, jak zprovoznit datový model a vytvořit předpovědi pomocí ml služeb v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71123565"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Zprovoznění clusteru služeb ML na Azure HDInsight

Po použití clusteru ML Services v HDInsight k dokončení modelování dat, můžete zprovoznit model, aby předpovědi. Tento článek obsahuje pokyny k provedení tohoto úkolu.

## <a name="prerequisites"></a>Požadavky

* Cluster služeb ML na hdinsightu. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **služby ML** pro typ **clusteru**.

* Klient Secure Shell (SSH): Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Zprovoznit cluster služby ML s konfigurací s jedním schránkovým boxem

> [!NOTE]  
> Následující kroky platí pro r server 9.0 a ML Server 9.1. V části ML Server 9.3 naleznete [v části Správa konfigurace operačního systému pomocí nástroje pro správu](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Připojte se přes SSH k hraničnímu uzlu.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Pokyny, jak používat SSH ve službě Azure HDInsight, najdete v tématu [Použití SSH s HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Změňte adresář pro příslušnou verzi a sudo tečka net dll: 

    - Pro server Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Pro Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Zobrazí se možnosti, ze kterých si můžete vybrat. Zvolte první možnost, jak je znázorněno na následujícím snímku obrazovky, pro **konfiguraci serveru ML pro zprovoznění**.

    ![Možnost Správa serveru R vybrat](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Nyní se zobrazí možnost zvolit způsob zprovoznění serveru ML. Z prezentovaných možností zvolte první zadáním **a**.

    ![Nástroj pro správu serveru R zprovoznění](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Po zobrazení výzvy zadejte a znovu zadejte heslo pro místního správce.

1. Měli byste vidět výstupy naznačující, že operace byla úspěšná. Budete také vyzváni k výběru jiné možnosti z nabídky. Výběrem možnosti E se vrátíte do hlavní nabídky.

    ![R server Správa nástroj úspěch](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Volitelně můžete provádět diagnostické kontroly spuštěním diagnostického testu následujícím způsobem:

    a. V hlavní nabídce vyberte **6** a spusťte diagnostické testy.

    ![Diagnostika nástroje správy serveru R](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. V nabídce Diagnostické testy vyberte **a**. Po zobrazení výzvy zadejte heslo, které jste zadali místnímu uživateli správce.

    ![R test nástroje pro správu serveru R](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Ověřte, zda výstup ukazuje, že celkový stav je průchod.

    ![R server Správa nástroj projít](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Z prezentovaných možností menu zadejte **E,** abyste se vrátili do hlavního menu a poté zadali **8,** abyste opustili nástroj admin.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Dlouhá zpoždění při využívání webové služby na Apache Spark

Pokud narazíte na dlouhá zpoždění při pokusu o využití webové služby vytvořené pomocí funkcí mrsdeploy v výpočetním kontextu Apache Spark, možná budete muset přidat některé chybějící složky. Pokaždé, když je aplikace Spark vyvolaná z webové služby pomocí funkcí mrsdeploy, patří uživateli *rserve2*. Náhradní řešení tohoto problému:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


V této fázi je konfigurace operacionalizace dokončena. Nyní můžete použít `mrsdeploy` balíček na vašem RClient pro připojení k operationalization na hraničníuzel a začít používat jeho funkce, jako [je vzdálené spuštění](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) a webové [služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). V závislosti na tom, jestli je váš cluster nastavený ve virtuální síti, může být potřeba nastavit přesměrování portu tunelovým propojením přes přihlášení SSH. Následující části vysvětlují, jak tento tunel nastavit.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster služby ML ve virtuální síti

Ověřte, že je na hraničním uzlu povolený provoz přes port 12800. Tímto způsobem můžete hraniční uzel použít pro připojení k funkci operacionalizace.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Pokud se metoda `remoteLogin()` nemůže připojit k hraničnímu uzlu, ale můžete se k němu připojit přes SSH, budete muset ověřit, jestli je správně nastavené pravidlo pro povolení provozu na portu 12800. Pokud bude tento problém přetrvávat, můžete jako alternativní řešení použít nastavení přesměrování portu tunelovým propojením přes SSH. Pokyny naleznete v následující části:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster služby ML není nastaven ve virtuální síti

Pokud váš cluster není nastavený ve virtuální síti nebo máte potíže s připojením přes virtuální síť, můžete použít přesměrování portu tunelovým propojením přes SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Jakmile je vaše relace SSH aktivní, provoz z portu místního počítače 12800 je předán na port hraničního uzlu 12800 prostřednictvím relace SSH. Nezapomeňte v metodě `remoteLogin()` použít adresu `127.0.0.1:12800`. To přihlásí do provozuschopnosti hraničního uzlu prostřednictvím předávání portů.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Škálování zprovozněné výpočetní uzly na pracovních uzlech HDInsight

Chcete-li škálovat výpočetní uzly, nejprve vyřadit z provozu pracovní uzly a potom nakonfigurovat výpočetní uzly na vyřazených pracovních uzlů.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: Vyřazení pracovních uzlů z provozu

Cluster služby ML není spravován prostřednictvím [apache hadoopové příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Pokud pracovní uzly nejsou vyřazeny z provozu, Správce prostředků YARN nefunguje podle očekávání, protože si není vědom prostředků, které server zabere do provozu. Abyste této situaci zabránili, doporučujeme před horizontálním navýšením kapacity výpočetních uzlů vyřadit z provozu pracovní uzly.

Chcete-li vyřadit pracovní uzly z provozu, postupujte takto:

1. Přihlaste se ke konzoli Ambari clusteru a klikněte na kartu **Hosts.**

1. Vyberte pracovní uzly (chcete-li být vyřazeni z provozu).

1. Klikněte na **akce** > **Vybraní hostitelé** > **Hostitelé** > **zapnout režim údržby**. Například na následujícím obrázku jsme vybrali k vyřazení z provozu uzly wn3 a wn4.  

   ![Apache Ambari zapnout režim údržby](./media/r-server-operationalize/get-started-operationalization.png)  

* Vybrat **akce** > **Vybrané hosty** > **DataNodes** > klepněte na **vyřadit z provozu**.
* Vyberte **akce** > **Vybrané zástupy** > **NodeManagers** > klepněte na tlačítko **Vyřadit z provozu**.
* Vybrat **akce** > **Vybrané hosty** > **DataNodes** > klepněte na **tlačítko Zastavit**.
* Vyberte **akce** > **Vybrané zástupy** > **NodeManažeři** > klikněte na **Stop**.
* Vybrat **akce** > **Vybrané hostitele** > **Hostitelé** > klepněte na **tlačítko Zastavit všechny součásti**.
* Zrušte výběr pracovních uzlů a vyberte hlavní uzly.
* Vyberte **akce** > **Vybraní hostitelé** > "**Hosts** > **Restartuje všechny součásti**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurace výpočetních uzlů na každém vyřazeném pracovním uzlu

1. Přihlaste se přes SSH do každého vyřazeného pracovního uzlu.

1. Spusťte nástroj pro správce pomocí příslušné služby DLL pro cluster služby ML Services, který máte. Pro ml server 9.1 spusťte následující:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Zadáním **1** vyberte možnost **Konfigurovat server ML pro zprovoznění**.

1. Chcete-li vybrat `C. Compute node`možnost , zadejte **C** . Tím se na pracovním uzlu nakonfiguruje výpočetní uzel.

1. Ukončete nástroj pro správu.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Přidání podrobností výpočetních uzlů na webovém uzlu

Jakmile jsou všechny vyřazené pracovní uzly nakonfigurovány pro spuštění výpočetního uzlu, vraťte se do hraničního uzlu a přidejte ip adresy vyřazených pracovních uzlů v konfiguraci webového uzlu serveru ML:

1. Připojte se přes SSH k hraničnímu uzlu.

1. Spusťte `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Vyhledejte část "Uris" a přidejte ip adresu a podrobnosti o portu pracovního uzlu.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Další kroky

* [Správa clusteru služby ML Services v HDInsight](r-server-hdinsight-manage.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)
