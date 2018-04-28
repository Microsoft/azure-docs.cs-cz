---
title: Omezení přístupu pomocí sdílené přístupové podpisy - Azure HDInsight | Microsoft Docs
description: Naučte se používat sdílené přístupové podpisy omezit HDInsight přístup k datům uloženým v objektů BLOB služby Azure storage.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 328f052e1ccad3ac26cce62c858e050253192ae8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Použití Azure sdílené přístupové podpisy úložiště omezit přístup k datům v HDInsight

HDInsight má úplný přístup k datům v účtech úložiště Azure, který je přidružen ke clusteru. Sdílené přístupové podpisy na kontejner objektů blob můžete použít k omezení přístupu k datům. Podpisy sdíleného přístupu (SAS) jsou funkce účtů úložiště Azure, která vám umožní omezit přístup k datům. Například poskytuje přístup jen pro čtení k datům.

> [!IMPORTANT]
> Řešení pomocí Apache škálu zvažte použití HDInsight připojený k doméně. Další informace najdete v tématu [konfigurace připojený k doméně HDInsight](./domain-joined/apache-domain-joined-configure.md) dokumentu.

> [!WARNING]
> HDInsight musí mít plný přístup k výchozí úložiště pro cluster.

## <a name="requirements"></a>Požadavky

* Předplatné Azure
* C# nebo Python. Příklad kódu C# je k dispozici jako řešení sady Visual Studio.

  * Visual Studio musí být ve verzi 2013, 2015 nebo 2017
  * Python musí být verze 2.7 nebo vyšší

* Cluster HDInsight se systémem Linux nebo [prostředí Azure PowerShell] [ powershell] – Pokud máte existující cluster založený na Linuxu, můžete použít Ambari přidat sdíleného přístupového podpisu pro clusteru. Pokud ne, Azure PowerShell můžete použít k vytvoření clusteru a přidejte sdíleného přístupového podpisu při vytváření clusteru.

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* V příkladu souborů z [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Toto úložiště obsahuje následující položky:

  * Projekt sady Visual Studio, který můžete vytvořit kontejner úložiště, uložené zásady a SAS pro použití s HDInsight
  * Skript v jazyce Python, můžete vytvořit kontejner úložiště, uložené zásady a SAS pro použití s HDInsight
  * Skript prostředí PowerShell, který můžete vytvořit HDInsight cluster a nakonfigurujte ho na používání sdíleného přístupového podpisu.

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Existují dvě formy sdílené přístupové podpisy:

* Ad hoc: čas zahájení, čas vypršení platnosti a oprávnění pro SAS se všechny zadaný v identifikátoru URI SAS.

* Uložené zásady přístupu: zásady uložené přístupu je definován na kontejner prostředků, jako je například kontejner objektů blob. Zásady můžete použít ke správě omezení pro jeden nebo více sdílených přístupových podpisů. Pokud přidružíte SAS se zásadami přístupu uložené, zdědí SAS omezení – čas spuštění, čas vypršení platnosti a - definována pro zásady uložené přístupu oprávnění.

Rozdíl mezi dvěma formuláři je důležité pro jeden klíč scénář: odvolaných certifikátů. SAS je adresu URL, takže každý, kdo získává SAS, můžete použít bez ohledu na to, kdo je požadována na začátku. Pokud veřejně publikována SAS, můžete použít kdokoli na světě. SAS, který je distribuován je platný, dokud jednu ze čtyř akcí se stane:

1. Je dosaženo času vypršení platnosti, zadaný na SAS.

2. Je dosaženo času vypršení platnosti, zadaný v zásadách přístupu uložené odkazuje SAS. Následující scénáře způsobit čas vypršení platnosti nelze připojit:

    * Časový interval uplynul.
    * Zásady uložené přístupu se upravují tak, aby měl čas vypršení platnosti v minulosti. Změna čas vypršení platnosti je jeden způsob odvolání SAS.

3. Zásady přístupu uložené odkazuje SAS je odstranit, což je další způsob odvolání SAS. Pokud budete chtít znovu vytvořit zásady uložené přístupu se stejným názvem, všechny tokeny SAS pro předchozí zásady jsou platné (Pokud neuplynul čas vypršení platnosti na sdíleného přístupového podpisu). Pokud máte v úmyslu odvolání SAS, je nutné použít jiný název, pokud je znovu vytvořit zásady přístupu, doba vypršení platnosti v budoucnu.

4. Znovu vygeneruje klíč účtu, který byl použit k vytvoření přidružení zabezpečení. Obnovuje se klíč způsobí, že všechny aplikace, které používají předchozí klíč vč. ověřování. Aktualizujte všechny součásti na nový klíč.

> [!IMPORTANT]
> Sdílený přístupový podpis identifikátor URI je spojena s účet klíč používaný k vytvoření podpisu a přidruženého uložené zásady přístupu (pokud existuje). Pokud je zadána žádná zásada uložené přístup, jediný způsob, jak odvolat sdílený přístupový podpis je změnit klíč účtu.

Doporučujeme vždy použít zásady uložené přístupu. Pokud používáte uložené zásady, můžete odvolat podpisy nebo prodloužit datum vypršení platnosti, podle potřeby. Kroky v tomto dokumentu uložené zásady přístupu k vygenerování SAS.

Další informace o sdílených přístupových podpisů najdete v tématu [vysvětlení modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Vytvoření uložené zásady a SAS pomocí jazyka C\#

1. Otevřete řešení v sadě Visual Studio.

2. V Průzkumníku řešení klikněte pravým tlačítkem na **SASToken** projektu a vyberte **vlastnosti**.

3. Vyberte **nastavení** a přidejte hodnoty pro následující položky:

   * StorageConnectionString: Připojovací řetězec pro účet úložiště, který chcete vytvořit uložené zásady a SAS pro. Musí být ve formátu `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` kde `myaccount` je název účtu úložiště a `mykey` je klíč pro účet úložiště.

   * ContainerName: Kontejneru v účtu úložiště, který chcete omezit přístup k datům.

   * SASPolicyName: Název vytvořit pomocí uložené zásady.

   * FileToUpload: Cesta k souboru, který je odeslat do kontejneru.

4. Spusťte projekt. Po vygenerování SAS, zobrazí se informace podobná následující text:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Uložte zásadu token SAS, název účtu úložiště a název kontejneru. Tyto hodnoty se používají při přidružení účtu úložiště k vašemu clusteru HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Vytvoření uložené zásady a SAS používá Python

1. Otevřete soubor SASToken.py a změňte následující hodnoty:

   * zásady\_název: název, který má použít k vytvoření uložené zásady.

   * úložiště\_účet\_název: název účtu úložiště.

   * úložiště\_účet\_klíč: klíč pro účet úložiště.

   * úložiště\_kontejneru\_název: kontejneru v účtu úložiště, který chcete omezit přístup k datům.

   * Příklad\_souboru\_cesta: cesta k souboru, který je odeslat do kontejneru.

2. Spusťte skript. Po dokončení skriptu zobrazí tokenu SAS, podobně jako následující text:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Uložte zásadu token SAS, název účtu úložiště a název kontejneru. Tyto hodnoty se používají při přidružení účtu úložiště k vašemu clusteru HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Použití SAS s HDInsight

Při vytváření clusteru služby HDInsight, musíte zadat účet primárního úložiště a volitelně můžete zadat další účty úložiště. Obě tyto metody přidávání úložiště vyžadují úplný přístup k účtům úložiště a kontejnerů, které se používají.

Použití sdíleného přístupového podpisu a omezit přístup do kontejneru, přidat vlastní položku k **core-site** konfiguraci pro daný cluster.

* Pro **založené na Windows** nebo **systémem Linux** clusterů HDInsight, můžete přidat položku při vytváření clusteru pomocí prostředí PowerShell.
* Pro **systémem Linux** clusterů HDInsight, změnit konfiguraci po vytvoření clusteru pomocí nástroje Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Vytvoření clusteru, který používá SAS

Příklad vytvoření clusteru služby HDInsight, který používá SAS je součástí `CreateCluster` adresáři úložiště. Pokud chcete použít, použijte následující kroky:

1. Otevřete `CreateCluster\HDInsightSAS.ps1` soubor v textovém editoru a upravte následující hodnoty na začátku dokumentu.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Můžete například změnit `'mycluster'` na název clusteru, kterou chcete vytvořit. Hodnoty SAS by měl odpovídat hodnoty z předchozí kroky při vytváření účtu úložiště a tokenu SAS.

    Po změně hodnoty se uložte soubor.

2. Otevření nového příkazového řádku Azure PowerShell. Pokud nejste obeznámeni s prostředím Azure PowerShell nebo ho nenainstalovali, přečtěte si téma [nainstalovat a nakonfigurovat Azure PowerShell][powershell].

1. Na řádku použijte následující příkaz k ověření vašeho předplatného Azure:

    ```powershell
    Connect-AzureRmAccount
    ```

    Pokud budete vyzváni, přihlaste se pomocí účtu pro vaše předplatné Azure.

    Pokud váš účet je spojen s několika předplatných Azure, budete možná muset použít `Select-AzureRmSubscription` vyberte předplatné, které chcete použít.

4. Na řádku přejděte do adresáře `CreateCluster` adresář, který obsahuje soubor HDInsightSAS.ps1. Pak použijte následující příkaz pro spuštění skriptu

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Jako skript se spustí, zaprotokoluje výstup do příkazového řádku prostředí PowerShell jako vytvoří prostředek skupiny a úložiště účtů. Zobrazí se výzva k zadání uživatele HTTP pro HDInsight cluster. Tento účet slouží k zabezpečení přístup HTTP/s pro cluster.

    Při vytváření clusteru se systémem Linux, zobrazí se výzva pro název účtu uživatele SSH a heslo. Tento účet slouží pro vzdálené přihlášení do clusteru.

   > [!IMPORTANT]
   > Po zobrazení výzvy k protokolu HTTP/s nebo SSH uživatelské jméno a heslo, je nutné zadat heslo, které splňuje následující kritéria:
   >
   > * Musí být minimálně 10 znaků.
   > * Musí obsahovat nejméně jednu číslici
   > * Musí obsahovat alespoň jeden jiný než alfanumerický znak
   > * Musí obsahovat aspoň jedno velké nebo malé písmeno.

Jak dlouho trvá chvíli pro tento skript k dokončení, obvykle přibližně 15 minut. Po dokončení skriptu bez chyb, byl vytvořen cluster.

### <a name="use-the-sas-with-an-existing-cluster"></a>Použití SAS s stávajícího clusteru

Pokud máte existující cluster založený na Linuxu, můžete přidat SAS k **core-site** konfigurace pomocí následujících kroků:

1. Otevřete webovému uživatelskému rozhraní Ambari pro váš cluster. Adresa pro tuto stránku https://YOURCLUSTERNAME.azurehdinsight.net. Po zobrazení výzvy ověřování ke clusteru pomocí jméno správce (správce) a heslo použité při vytváření clusteru.

2. Na levé straně webovému uživatelskému rozhraní Ambari, vyberte **HDFS** a pak vyberte **konfigurací** kartě uprostřed stránky.

3. Vyberte **Upřesnit** kartě a poté vyhledejte **vlastní základní site** části.

4. Rozbalte **vlastní základní site** oddíl a potom přejděte na koncové a vyberte **přidat vlastnost...**  odkaz. Použijte následující hodnoty pro **klíč** a **hodnotu** pole:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Hodnota**: SAS vrácený jazyka C# nebo Python aplikace byla spuštěna dříve

     Nahraďte **CONTAINERNAME** s název kontejneru, který jste použili k aplikaci C# nebo SAS. Nahraďte **STORAGEACCOUNTNAME** názvem účtu úložiště, který jste použili.

5. Klikněte **přidat** tlačítko Uložit tento klíč a hodnotu a pak klikněte na **Uložit** tlačítko Uložit změny konfigurace. Po zobrazení výzvy, přidejte popis změny ("Přidat přístup k úložišti SAS" třeba) a potom klikněte na **Uložit**.

    Klikněte na tlačítko **OK** když byly dokončeny změny.

   > [!IMPORTANT]
   > Změna se projeví až po restartování několik služeb.

6. V Ambari webového uživatelského rozhraní, vyberte **HDFS** ze seznamu na levé straně a potom vyberte **restartujte všechny vliv** z **služby akce** rozevíracím seznamu na pravé straně. Po zobrazení výzvy vyberte __potvrďte restartujte všechny__.

    Tento postup opakujte pro MapReduce2 a YARN.

7. Po restartování služby, vyberte každé z nich a zakázat režimu údržby od **služby akce** rozevírací nabídku.

## <a name="test-restricted-access"></a>Testování s omezeným přístupem

Pokud chcete ověřit, že mají omezený přístup, použijte následující metody:

* Pro **založené na Windows** clustery HDInsight k připojení ke clusteru pomocí vzdálené plochy. Další informace najdete v tématu [připojení k HDInsight pomocí protokolu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Po připojení používat **Hadoop příkazového řádku** ikony na ploše otevřete příkazový řádek.

* Pro **systémem Linux** clusterů HDInsight, připojte se ke clusteru pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení ke clusteru pomocí následujících kroků ověřte, můžete na účtu úložiště SAS pouze pro čtení a seznam položek:

1. K zobrazení seznamu obsahu kontejneru, použijte následující příkaz z příkazového řádku: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Nahraďte **SASCONTAINER** s názvem kontejneru vytvořili pro účet úložiště SAS. Nahraďte **SASACCOUNTNAME** s názvem účet úložiště používané pro SAS.

    Seznam obsahuje soubor nahrát při vytvoření kontejneru a SAS.

2. Použijte následující příkaz k ověření, že si můžete přečíst obsah souboru. Nahraďte **SASCONTAINER** a **SASACCOUNTNAME** jako v předchozím kroku. Nahraďte **FILENAME** s názvem souboru se zobrazí v předchozí příkaz:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Tento příkaz vypíše obsah souboru.

3. Stáhněte soubor do místního systému souborů pomocí následujícího příkazu:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Tento příkaz stáhne soubor do místního souboru s názvem **testfile.txt**.

4. Použít následující příkaz k nahrání do nový soubor s názvem místního souboru **testupload.txt** na úložiště SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zobrazí zpráva podobná následující text:

        put: java.io.IOException

    K této chybě dojde, protože umístění úložiště je pro čtení + pouze seznam. Umístění dat na výchozí úložiště pro cluster, který lze zapisovat, použijte následující příkaz:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Tento čas by měl úspěšně dokončit operaci.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="a-task-was-canceled"></a>Úloha byla zrušena.

**Příznaky**: při vytváření clusteru pomocí skriptu prostředí PowerShell, zobrazí se následující chybová zpráva:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Příčina**: k této chybě může dojít, pokud použijete heslo pro uživatele správce nebo HTTP pro cluster nebo (pro clustery se systémem Linux) uživatel SSH.

**Řešení**: použít heslo, které splňuje následující kritéria:

* Musí být minimálně 10 znaků.
* Musí obsahovat nejméně jednu číslici
* Musí obsahovat alespoň jeden jiný než alfanumerický znak
* Musí obsahovat aspoň jedno velké nebo malé písmeno.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postup přidání úložiště omezený přístup ke svému clusteru HDInsight, se naučíte další způsoby, jak pracovat s daty v clusteru:

* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Používání nástroje MapReduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
