---
title: Zprovoznění služby ML na HDInsight – Azure
description: Zjistěte, jak zprovoznit služby ML v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: d0cae4210fa313c5d6e1f33987422e7b0d53e76a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579953"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Zprovoznění služby ML clusteru v Azure HDInsight

Po použití služby ML clusteru v HDInsight k dokončení modelování vašich dat, které můžete model operacionalizovat za účelem provádění předpovědí. Tento článek obsahuje pokyny k provedení této úlohy.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby ML v HDInsight**: Pokyny najdete v tématu [začít pracovat se službami ML na HDInsight](r-server-get-started.md).

* **Klient Secure Shell (SSH)**: Klient SSH slouží ke vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo na clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Zprovoznění clusteru služby ML pomocí konfigurace

> [!NOTE]  
> Následující postup platí pro R Server 9.0 a ML serveru 9.1. ML Server 9.3, najdete v tématu [použít nástroje pro správu ke správě konfigurace operacionalizace](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Připojte se přes SSH k hraničnímu uzlu.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Pokyny k použití SSH se službou Azure HDInsight, naleznete v tématu [použití SSH s HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Přejděte do adresáře příslušné verze a sudo dot net knihovny dll: 

    - Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Pro Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Zobrazí se možnosti lze vybírat. Zvolte první možnost, jak je znázorněno na následujícím snímku obrazovky, na **ML konfigurovat Server pro Operacionalizaci**.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Nyní se zobrazí možnost zvolte, jak chcete zprovoznit ML Server. Z uvedených možností, vyberte první z nich tak, že zadáte **A**.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Po zobrazení výzvy zadejte a znovu zadejte heslo místního správce.

1. Měli byste vidět výstupy navrhuje, že operace byla úspěšná. Také budete vyzváni k výběru jiné možnosti z nabídky. Vyberte E přejděte zpět do hlavní nabídky.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Volitelně můžete provést diagnostické kontroly spuštěním diagnostického testu následujícím způsobem:

    a. V hlavní nabídce vyberte **6** spustit diagnostické testy.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/diagnostic-1.png)

    b. V nabídce diagnostické testy, zvolte **A**. Po zobrazení výzvy zadejte heslo, které jste zadali pro místního správce.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/diagnostic-2.png)

    c. Ověřte, že výstup ukazuje, že celkový stav z hlediska pass.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/diagnostic-3.png)

    d. V možnostech nabídky zobrazí, zadejte **E** vrátit do hlavní nabídky a pak zadejte **8** ukončete nástroj pro správu.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Dlouhá zpoždění při využívání webové služby na Apache Sparku

Pokud dochází k dlouhým zpožděním při pokusech o využívání webové služby vytvořené pomocí funkcí mrsdeploy ve Apache Spark výpočetním kontextu, budete muset přidat některé chybějící složky. Pokaždé, když je aplikace Spark vyvolaná z webové služby pomocí funkcí mrsdeploy, patří uživateli *rserve2*. Náhradní řešení tohoto problému:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


V této fázi je konfigurace operacionalizace dokončena. Teď můžete použít `mrsdeploy` balíček na vašem klientovi RClient připojit k operacionalizaci na hraničním uzlu a začít používat funkce operacionalizace, například [vzdálené spuštění](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) a [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). V závislosti na tom, jestli je váš cluster nastavený ve virtuální síti, může být potřeba nastavit přesměrování portu tunelovým propojením přes přihlášení SSH. Následující části vysvětlují, jak tento tunel nastavit.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster se službou ML Services ve virtuální síti

Ověřte, že je na hraničním uzlu povolený provoz přes port 12800. Tímto způsobem můžete hraniční uzel použít pro připojení k funkci operacionalizace.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Pokud se metoda `remoteLogin()` nemůže připojit k hraničnímu uzlu, ale můžete se k němu připojit přes SSH, budete muset ověřit, jestli je správně nastavené pravidlo pro povolení provozu na portu 12800. Pokud bude tento problém přetrvávat, můžete jako alternativní řešení použít nastavení přesměrování portu tunelovým propojením přes SSH. Pokyny najdete v tématu v následující části:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster se službou ML Services nastavený mimo virtuální síť

Pokud váš cluster není nastavený ve virtuální síti nebo máte potíže s připojením přes virtuální síť, můžete použít přesměrování portu tunelovým propojením přes SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Jakmile je aktivní relaci SSH, provoz z portu 12800 vašeho místního počítače je předán hraničního uzlu prostřednictvím relace SSH port 12800. Nezapomeňte v metodě `remoteLogin()` použít adresu `127.0.0.1:12800`. Přihlásí operacionalizaci hraničního uzlu prostřednictvím přesměrování portu.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Škálování zprovozněný výpočetních uzlů na pracovní uzly HDInsight

Pokud chcete škálovat výpočetní uzly, nejprve vyřadit z provozu pracovní uzly a pak konfigurace výpočetních uzlů na vyřazených pracovních uzlů.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: Vyřazení pracovních uzlů z provozu

Cluster se službou ML Services není spravován přes [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Pokud pracovní uzly nevyřadí, správce prostředků YARN nebude fungovat podle očekávání, protože nebude vědět o prostředcích, které server. Abyste této situaci zabránili, doporučujeme před horizontálním navýšením kapacity výpočetních uzlů vyřadit z provozu pracovní uzly.

Použijte následující postup vyřazení pracovních uzlů z provozu:

1. Přihlaste se ke konzole Ambari clusteru a klikněte na **hostitele** kartu.

1. Výběr pracovních uzlů (Vyřazená z provozu).

1. Klikněte na tlačítko **akce** > **Selected Hosts** > **hostitele** > **zapnout režim údržby**. Například na následujícím obrázku jsme vybrali k vyřazení z provozu uzly wn3 a wn4.  

   ![vyřazení pracovních uzlů z provozu](./media/r-server-operationalize/get-started-operationalization.png)  

* Vyberte **akce** > **Selected Hosts** > **DataNodes** > klikněte na tlačítko **Decommission**.
* Vyberte **akce** > **Selected Hosts** > **NodeManagers** > klikněte na tlačítko **Decommission**.
* Vyberte **akce** > **Selected Hosts** > **DataNodes** > klikněte na tlačítko **Zastavit**.
* Vyberte **akce** > **Selected Hosts** > **NodeManagers** > klikněte na **Zastavit**.
* Vyberte **akce** > **Selected Hosts** > **hostitele** > klikněte na tlačítko **zastavit všechny komponenty**.
* Zrušte výběr pracovních uzlů a vyberte hlavní uzly.
* Vyberte **akce** > **Selected Hosts** > "**hostitele** > **restartovat všechny komponenty**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurace výpočetních uzlů na všech vyřazených pracovních uzlech

1. Přihlaste se přes SSH do každého vyřazeného pracovního uzlu.

1. Spusťte nástroj pro správu používající knihovnu DLL relevantní pro cluster služby ML, který máte. Pro ML serveru 9.1 Spusťte následující příkaz:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Zadejte **1** možnost **ML konfigurovat Server pro Operacionalizaci**.

1. Zadejte **C** možnost `C. Compute node`. Tím se na pracovním uzlu nakonfiguruje výpočetní uzel.

1. Ukončete nástroj pro správu.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Přidání podrobností o výpočetních uzlech do webového uzlu

Jakmile budou všechny vyřazené pracovní uzly jsou nakonfigurovány na spuštění výpočetního uzlu, přejděte zpět hraničního uzlu a přidejte IP adresy vyřazených pracovních uzlů do konfigurace webového uzlu ML serveru:

1. Připojte se přes SSH k hraničnímu uzlu.

1. Spusťte `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Vyhledejte část "Uris" a přidejte pracovní uzel IP adresy a porty.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Další postup

* [Správa clusteru služby ML Services v HDInsight](r-server-hdinsight-manage.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)
