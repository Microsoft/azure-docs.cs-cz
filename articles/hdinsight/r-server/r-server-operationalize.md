---
title: Zprovoznit R serverem v HDInsight - Azure | Microsoft Docs
description: Informace o zprovoznění R Server v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 6de6e78d9b4ad68d268b59cff18c75fbdd7be757
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412837"
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Zprovoznit cluster R serverem v Azure HDInsight

Po použití cluster R serverem v HDInsight dokončete data modelování, můžete zprovoznit model, který má provádět předpovědi. Tento článek obsahuje pokyny k provedení této úlohy.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby R serverem v HDInsight**: pokyny najdete v tématu [začít pracovat s R serverem v HDInsight](r-server-get-started.md).

* **Klient Secure Shell (SSH):** Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Zprovoznit R Server clusteru s jedním pole Konfigurace

1. Připojte se přes SSH k hraničnímu uzlu.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Pokyny týkající se používání SSH s Azure HDInsight najdete v tématu [použití SSH s HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Změňte adresář pro příslušné verze a sudo net dll tečkou: 

    - Pro Microsoft R Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Pro Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Zobrazí se s možnostmi můžete vybírat. Zvolte první možnost, jak je znázorněno na následujícím snímku obrazovky na **konfigurace R Server pro Operationalization**.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Nyní se zobrazí možnost vyberte, jakým způsobem chcete zprovoznit R Server. Z možností vidění, zvolte první z nich tak, že zadáte **A**.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Po zobrazení výzvy zadejte a zadejte znovu heslo pro uživatele místního správce.

6. Měli byste vidět výstupů, které naznačují, že operace byla úspěšná. Také budete vyzváni k z nabídky vyberte jinou možnost. Vyberte E přejděte zpět do hlavní nabídky.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Volitelně můžete provádět diagnostické kontroly spuštěním diagnostický test následujícím způsobem:

    a. V hlavní nabídce vyberte **6** ke spuštění diagnostických testů.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/diagnostic-1.png)

    b. V nabídce diagnostické testy, vyberte **A**. Po zobrazení výzvy zadejte heslo, které jste zadali pro uživatele místního správce.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/diagnostic-2.png)

    c. Ověřte, že výstup ukazuje, že celkový stav je do průchodu.

    ![jednotná konfigurace operacionalizace](./media/r-server-operationalize/diagnostic-3.png)

    d. Z nabídky možnost zobrazí, zadejte **E** chcete vrátit do hlavní nabídky a pak zadejte **8** ukončete nástroj Správce.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Velká zpoždění při využívání webovou službu na Spark

Pokud dochází k dlouhým zpožděním při pokusech o využívání webové služby vytvořené pomocí funkcí mrsdeploy ve výpočetním kontextu Sparku, možná budete muset přidat některé chybějící složky. Pokaždé, když je aplikace Spark vyvolaná z webové služby pomocí funkcí mrsdeploy, patří uživateli *rserve2*. Náhradní řešení tohoto problému:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


V této fázi je konfigurace operacionalizace dokončena. Nyní můžete pomocí `mrsdeploy` balíček na vaše RClient pro připojení k operationalization na hraniční uzel a začít používat jeho funkce, jako jsou [vzdálené spuštění](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) a [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). V závislosti na tom, jestli je váš cluster nastavený ve virtuální síti, může být potřeba nastavit přesměrování portu tunelovým propojením přes přihlášení SSH. Následující části vysvětlují, jak tento tunel nastavit.

### <a name="r-server-cluster-on-virtual-network"></a>R Server clusteru ve virtuální síti

Ověřte, že je na hraničním uzlu povolený provoz přes port 12800. Tímto způsobem můžete hraniční uzel použít pro připojení k funkci operacionalizace.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Pokud se metoda `remoteLogin()` nemůže připojit k hraničnímu uzlu, ale můžete se k němu připojit přes SSH, budete muset ověřit, jestli je správně nastavené pravidlo pro povolení provozu na portu 12800. Pokud bude tento problém přetrvávat, můžete jako alternativní řešení použít nastavení přesměrování portu tunelovým propojením přes SSH. Pokyny najdete v následující části:

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>R Server clusteru nejsou nastaveny ve virtuální síti

Pokud váš cluster není nastavený ve virtuální síti nebo máte potíže s připojením přes virtuální síť, můžete použít přesměrování portu tunelovým propojením přes SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Jakmile máte aktivní relaci SSH, provoz z portu 12800 vašeho počítače se prostřednictvím relace SSH přesměruje na port 12800 hraničního uzlu. Nezapomeňte v metodě `remoteLogin()` použít adresu `127.0.0.1:12800`. To zaznamená do uzlu edge operationalization prostřednictvím portu předávání.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Škálování operationalized výpočetních uzlů na HDInsight uzlů pracovního procesu

Škálovat výpočetní uzly, nejprve vyřadit z provozu uzly pracovních procesů a poté proveďte konfiguraci výpočetních uzlů na vyřazený pracovním uzlům.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: Vyřazení z provozu uzlů pracovního procesu

R Server clusteru není spravován pomocí YARN. Pokud pracovní uzly nejsou vyřazení, YARN Resource Manager nefunguje podle očekávání, protože nemá žádné informace o prostředky se zabírá serverem. Abyste této situaci zabránili, doporučujeme před horizontálním navýšením kapacity výpočetních uzlů vyřadit z provozu pracovní uzly.

Postupujte podle těchto kroků vyřazení z provozu uzlů pracovního procesu:

1. Přihlaste se ke konzole Ambari clusteru a klikněte na **hostitele** kartě.

2. Vyberte pracovní uzly (Chcete-li být vyřazení).

3. Klikněte na tlačítko **akce** > **vybrané hostitele** > **hostitele** > **zapnout režim údržby**. Například na následujícím obrázku jsme vybrali k vyřazení z provozu uzly wn3 a wn4.  

   ![vyřazení pracovních uzlů z provozu](./media/r-server-operationalize/get-started-operationalization.png)  

* Vyberte **akce** > **vybrané hostitele** > **DataNodes** > klikněte na tlačítko **Decommission**.
* Vyberte **akce** > **vybrané hostitele** > **NodeManagers** > klikněte na tlačítko **Decommission**.
* Vyberte **akce** > **vybrané hostitele** > **DataNodes** > klikněte na tlačítko **Zastavit**.
* Vyberte **akce** > **vybrané hostitele** > **NodeManagers** > klikněte na **Zastavit**.
* Vyberte **akce** > **vybrané hostitele** > **hostitele** > klikněte na tlačítko **ukončit všechny součásti**.
* Zrušte výběr pracovních uzlů a vyberte hlavní uzly.
* Vyberte **akce** > **vybrané hostitele** > "**hostitele** > **restartujte všechny součásti**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurace výpočetní uzly na každý vyřazení pracovní uzly

1. Přihlaste se přes SSH do každého vyřazeného pracovního uzlu.

2. Spusťte nástroj Správce pomocí příslušných knihovny DLL pro cluster R serverem, který máte. R Server 9.1 Spusťte následující příkaz:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Zadejte **1** a vyberte možnost **konfigurace R Server pro Operationalization**.

4. Zadejte **C** a vyberte možnost `C. Compute node`. Tím se na pracovním uzlu nakonfiguruje výpočetní uzel.

5. Ukončete nástroj pro správu.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Přidání výpočetní uzly podrobnosti na uzel webového

Jakmile se všechny uzly pracovního procesu vyřazení jsou nakonfigurovány na spuštění výpočetního uzlu, vraťte se na uzlu edge a přidejte vyřazení pracovní uzly IP adresy v konfiguraci uzlu serveru R webové:

1. Připojte se přes SSH k hraničnímu uzlu.

2. Spusťte `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Vyhledejte v části "Identifikátory URI" a přidejte IP pracovního uzlu a podrobnosti o portu.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Další postup

* [Správa clusteru R Serveru v HDInsight](r-server-hdinsight-manage.md)
* [Možnosti výpočetního kontextu pro cluster R Serveru v HDInsight](r-server-compute-contexts.md)
* [Možnosti služby Azure Storage pro cluster R Serveru v HDInsight](r-server-storage.md)
