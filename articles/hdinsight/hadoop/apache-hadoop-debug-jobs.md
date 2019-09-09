---
title: 'Apache Hadoop ladění: Zobrazení protokolů a interpretace chybových zpráv – Azure HDInsight'
description: Přečtěte si o chybových zprávách, které se můžou zobrazit při správě HDInsight pomocí PowerShellu, a krocích, které můžete provést pro obnovení.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: e5ae05b2ad1dc03bad210b1f67834865afd49df3
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810876"
---
# <a name="analyze-apache-hadoop-logs"></a>Analýza protokolů Apache Hadoopu

Každý Apache Hadoop cluster v Azure HDInsight má účet úložiště Azure, který se používá jako výchozí systém souborů. Účet úložiště se označuje jako výchozí účet úložiště. Cluster používá úložiště tabulek Azure a BLOB Storage ve výchozím účtu úložiště pro ukládání protokolů.  Pokud chcete zjistit výchozí účet úložiště pro váš cluster, přečtěte si téma [správa Apache Hadoopch clusterů ve službě HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Protokoly se uchovávají v účtu úložiště i po odstranění clusteru.

## <a name="logs-written-to-azure-tables"></a>Protokoly zapsané do tabulek Azure

Protokoly zapsané do tabulek Azure poskytují jednu úroveň přehledu o tom, co se děje s clusterem HDInsight.

Při vytváření clusteru HDInsight se automaticky vytvoří šest tabulek pro clustery se systémem Linux ve výchozím úložišti tabulek:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Název souboru tabulky je **u\<názvu clusteru > TableName >\<DDMonYYYYatHHMMSSsss**.

Tyto tabulky obsahují následující pole:

* ClusterDnsName
* Název komponenty
* EventTimestamp
* Host
* MALoggingHash
* Message
* Ne
* PreciseTimeStamp
* Role
* RowIndex
* Klient
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Nástroje pro přístup k protokolům
K dispozici je řada nástrojů pro přístup k datům v těchto tabulkách:

* Visual Studio
* Azure Storage Explorer
* Power Query pro Excel

#### <a name="use-power-query-for-excel"></a>Použití Power Query pro Excel
Power Query lze nainstalovat z [Microsoft Power Query pro Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Požadavky na systém najdete na stránce pro stažení.

**Pomocí Power Query otevřít a analyzovat protokol služby**

1. Otevřete **aplikaci Microsoft Excel**.
2. V nabídce **Power Query** klikněte na **z Azure**a pak klikněte na **z Microsoft Azure úložiště tabulek**.
   
    ![HDInsight Hadoop v Excelu PowerQuery otevřít Azure Table Storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Zadejte název účtu úložiště, buď krátký název, nebo plně kvalifikovaný název domény.
4. Zadejte klíč účtu úložiště. Zobrazí se seznam tabulek:
   
    ![Protokoly HDInsight Hadoop uložené ve službě Azure Table Storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. V podokně **navigátor** klikněte pravým tlačítkem na tabulku hadoopservicelog a vyberte **Upravit**. Zobrazí se čtyři sloupce. Volitelně můžete odstranit **klíč oddílu**, **klíč řádku**a **časové razítko** , a to tak, že je vyberete a pak kliknete na **odebrat sloupce** z možností na pásu karet.
6. Klikněte na ikonu rozbalit ve sloupci obsah a vyberte sloupce, které chcete importovat do excelové tabulky. V této ukázce zvolím TraceLevel a Component: Může mi poskytnout některé základní informace o tom, které součásti byly problémy.
   
    ![Protokoly HDInsight Hadoop výběr sloupců Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "Protokoly HDInsight Hadoop výběr sloupců Excel")
7. Kliknutím na tlačítko **OK** importujte data.
8. Vyberte sloupce **TraceLevel**, role a **Component** a pak na pásu karet klikněte na **Seskupit podle** ovládacího prvku.
9. Klikněte na **OK** v dialogovém okně Seskupit podle.
10. Klikněte na * * použít & Zavřít * *.

Nyní můžete použít aplikaci Excel k filtrování a řazení podle potřeby. Je možné zahrnout další sloupce (například zprávy), aby bylo možné přejít k podrobnostem o problémech, ale výběr a seskupení výše uvedených sloupců poskytuje dátý přehled o tom, co se děje se službami Hadoop. Stejný nápad lze použít pro tabulky Setuplog a hadoopinstalllog.

#### <a name="use-visual-studio"></a>Použití Visual Studia
**Použití sady Visual Studio**

1. Otevřít Visual Studio.
2. V nabídce **zobrazení** klikněte na **Průzkumník cloudu**. Nebo jednoduše klikněte na **CTRL\, + CTRL + X**.
3. V **Průzkumníku cloudu**vyberte **typy prostředků**.  Další dostupnou možností jsou **skupiny prostředků**.
4. Rozbalte **účty úložiště**, výchozí účet úložiště pro váš cluster a pak **tabulky**.
5. Dvakrát klikněte na **hadoopservicelog**.
6. Přidejte filtr. Příklad:
   
        TraceLevel eq 'ERROR'
   
    ![Protokoly Hadoop HDInsight výběr sloupců vs](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "Protokoly Hadoop HDInsight výběr sloupců vs")
   
    Další informace o sestavování filtrů naleznete v tématu [konstrukce řetězců filtru pro návrháře tabulky](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Protokoly zapsané do Azure Blob Storage
Protokoly zapsané do tabulek Azure poskytují jednu úroveň přehledu o tom, co se děje s clusterem HDInsight. Tyto tabulky ale neposkytují protokoly na úrovni úloh, které mohou být užitečné při procházení dalších problémů, když k nim dojde. Pokud chcete tuto další úroveň podrobností poskytnout, clustery HDInsight se nakonfigurují tak, aby na účet Blob Storage napsaly protokoly úloh pro každou úlohu odeslanou prostřednictvím Templeton. Prakticky to znamená, že se úlohy odeslaly pomocí rutin Microsoft Azure PowerShell nebo rozhraní API pro odeslání úlohy .NET, a ne úlohy odeslané prostřednictvím přístupu RDP/příkazového řádku ke clusteru. 

Pokud chcete zobrazit protokoly, přečtěte si téma [přístup Apache HADOOP nitě protokoly aplikací v HDInsight se systémem Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Další informace o protokolech aplikací naleznete v tématu [zjednodušení správy uživatelských protokolů a přístup v Apache HADOOP nitě](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>Zobrazení protokolů stavu clusteru a úloh
### <a name="access-the-ambari-ui"></a>Přístup k uživatelskému rozhraní Ambari
V Azure Portal kliknutím na název clusteru HDInsight otevřete podokno clusteru. V podokně clusteru klikněte na **řídicí panel**.

![Spustit řídicí panel clusteru](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Přístup k uživatelskému rozhraní příze
V Azure Portal kliknutím na název clusteru HDInsight otevřete podokno clusteru. V podokně clusteru klikněte na **řídicí panel**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. V Ambari v seznamu služeb vlevo vyberte možnost **příze** . Na stránce, která se zobrazí, vyberte možnost **Rychlé odkazy**a pak vyberte položku aktivní hlavní uzel a správce prostředků uživatelské rozhraní.

Pomocí uživatelského rozhraní PŘÍZe můžete provádět následující akce:

* **Získá stav clusteru**. V levém podokně rozbalte **cluster**a klikněte na **o**. Toto jsou podrobné informace o stavu clusteru, jako je celková přidělená paměť, používané jádra, stav Správce prostředků clusteru, verze clusteru atd.
  
    ![Spustit přízi na řídicím panelu clusteru](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "Spustit přízi na řídicím panelu clusteru")
* **Získá stav uzlu**. V levém podokně rozbalte **cluster**a klikněte na **uzly**. Zobrazí se seznam všech uzlů v clusteru, adresa HTTP každého uzlu, prostředky přidělené každému uzlu atd.
* **Monitorovat stav úlohy**. V levém podokně rozbalte **cluster**a potom klikněte na **aplikace** . zobrazí se seznam všech úloh v clusteru. Chcete-li se podívat na úlohy v určitém stavu (například nový, odesláno, spuštěno atd.), klikněte na příslušný odkaz v části **aplikace**. Další informace o úloze získáte tak, že kliknete na název úlohy, včetně výstupu, protokolů atd.

### <a name="access-the-hbase-ui"></a>Přístup k uživatelskému rozhraní HBA
V Azure Portal kliknutím na název clusteru HDInsight HBA otevřete podokno clusteru. V podokně clusteru klikněte na **řídicí panel**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. V Ambari vyberte adaptéry HBA ze seznamu služeb. V horní části stránky vyberte **Rychlé odkazy** , přesuňte ukazatel na aktivní odkaz na uzel Zookeeper a pak klikněte na HBase Master uživatelské rozhraní.

## <a name="hdinsight-error-codes"></a>Kódy chyb HDInsight
Chybové zprávy uvedené v této části jsou k dispozici, aby uživatelům systému Hadoop ve službě Azure HDInsight porozuměli možným chybovým stavům, se kterými se mohou setkat při správě služby pomocí Azure PowerShell a jejich informování o krocích, které je možné učinit. k zotavení z chyby.

Některé z těchto chybových zpráv se taky můžou zobrazit ve Azure Portal, když se používají ke správě clusterů HDInsight. Jiné chybové zprávy, se kterými se můžete setkat, ale mají méně podrobnější vliv na omezení na nápravných akcích, které jsou v tomto kontextu možné. Další chybové zprávy jsou k dispozici v kontextech, ve kterých je toto zmírnění zřejmé. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Popis**: Pokud chcete použít vlastní nastavení pro podregistr a Oozie metaúložiště, poskytněte prosím podrobnosti o službě Azure SQL Database alespoň pro jednu komponentu.
* **Omezení rizik**: Uživatel musí dodat platný SQL Azure metastore a opakovat požadavek.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Popis**: Nepovedlo se vytvořit cluster v oblasti *nameOfYourRegion*. Použijte platnou oblast HDInsight a opakujte požadavek.
* **Omezení rizik**: Zákazník by měl vytvořit oblast clusteru, která je v současné době podporuje: Jihovýchodní Asie, Západní Evropa, Severní Evropa, Východní USA nebo Západní USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Popis**: Serveru se nepovedlo najít požadovaný záznam clusteru.  
* **Omezení rizik**: Zkuste operaci zopakovat.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Popis**: Název DNS clusteru *yourDnsName* je neplatný. Ujistěte se prosím, že název začíná a končí alfanumerickým znakem a může obsahovat jenom speciální znak-.  
* **Omezení rizik**: Ujistěte se, že jste pro cluster použili platný název DNS, který začíná a končí alfanumerickými znaky a neobsahuje žádné speciální znaky jiné než pomlčku "-" a potom operaci opakujte.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Popis**: Název clusteru *yourClusterName* není k dispozici. Vyberte prosím jiný název.  
* **Omezení rizik**: Uživatel by měl zadat název clusteru, který je jedinečný a neexistuje, a zkuste to znovu. Pokud uživatel používá portál, uživatelské rozhraní je upozorní, pokud se název clusteru už používá během postupu vytvoření.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Popis**: Heslo clusteru je neplatné. Heslo musí mít aspoň 10 znaků a musí obsahovat aspoň jedno číslo, velké písmeno, malé písmeno a speciální znak bez mezer a nesmí obsahovat uživatelské jméno v rámci této části.  
* **Omezení rizik**: Zadejte platné heslo clusteru a zkuste operaci zopakovat.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Popis**: Uživatelské jméno clusteru je neplatné. Ujistěte se prosím, že uživatelské jméno neobsahuje speciální znaky nebo mezery.  
* **Omezení rizik**: Zadejte platné uživatelské jméno clusteru a zkuste operaci zopakovat.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Popis**: Název DNS clusteru *yourDnsClusterName* je neplatný. Ujistěte se prosím, že název začíná a končí alfanumerickým znakem a může obsahovat jenom speciální znak-.  
* **Omezení rizik**: Zadejte platné uživatelské jméno clusteru DNS a zkuste operaci zopakovat.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Popis**: Název kontejneru v identifikátoru URI *yourcontainerURI* a název DNS *yourDnsName* v textu požadavku musí být stejný.  
* **Omezení rizik**: Ujistěte se, že název kontejneru a název DNS jsou stejné, a zkuste operaci zopakovat.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Popis**: Neplatná konfigurace clusteru Nepovedlo se najít žádné definice datových uzlů v velikost uzlu.  
* **Omezení rizik**: Zkuste operaci zopakovat.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Popis**: Nepovedlo se odstranit nasazení clusteru.  
* **Omezení rizik**: Opakujte operaci odstranění.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Popis**: Chyba konfigurace služby Požadované informace o mapování DNS se nenašly.  
* **Omezení rizik**: Odstraňte cluster a vytvořte nový cluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Popis**: Pokus o vytvoření kontejneru clusteru je duplicitní. Záznam existuje pro *nameOfYourContainer* , ale značky ETag se neshodují.
* **Omezení rizik**: Zadejte jedinečný název pro kontejner a opakujte operaci vytvoření.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Popis**: *NameOfYourHostedService* hostované služby už cluster obsahuje. Hostovaná služba nemůže obsahovat víc clusterů.  
* **Omezení rizik**: Hostování clusteru v jiné hostované službě.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Popis**: Serveru se nepovedlo aktualizovat stav nasazení clusteru.  
* **Omezení rizik**: Zkuste operaci zopakovat. Pokud k tomu dojde několikrát, kontaktujte šablonu stylů CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Popis**: Clusterový *yourClusterName* se odstranil jako součást údržby. Vytvořte prosím cluster znovu.
* **Omezení rizik**: Vytvořte znovu cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Popis**: Neplatná konfigurace clusteru Požadovaná konfigurace hlavního uzlu nebyla nalezena v velikostech uzlů.
* **Omezení rizik**: Zkuste operaci zopakovat.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Popis**: Nepovedlo se vytvořit *nameOfYourHostedService*hostované služby. Zkuste prosím žádost znovu.  
* **Omezení rizik**: Opakujte požadavek.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Popis**: Hostovaná služba *nameOfYourHostedService* už má produkční nasazení. Hostovaná služba nemůže obsahovat více produkčních nasazení. Opakujte požadavek s jiným názvem clusteru.
* **Omezení rizik**: Použijte jiný název clusteru a opakujte požadavek.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Popis**: *NameOfYourHostedService* hostované služby pro cluster se nepovedlo najít.  
* **Omezení rizik**: Pokud je cluster v chybovém stavu, odstraňte jej a akci opakujte.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Popis**: *NameOfYourHostedService* hostované služby nemá žádné přidružené nasazení.  
* **Omezení rizik**: Pokud je cluster v chybovém stavu, odstraňte jej a akci opakujte.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Popis**: SubscriptionId *yourSubscriptionId* nemá zbývající jádra k vytvoření clusteru *yourClusterName*. Požadováno: *resourcesRequired*, Available: *resourcesAvailable*.  
* **Omezení rizik**: Uvolněte prostředky v předplatném nebo zvyšte prostředky dostupné pro předplatné a pokuste se cluster vytvořit znovu.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Popis**: ID předplatného *yourSubscriptionId* nemá kvótu pro nový hostované službě pro vytvoření clusteru *yourClusterName*.  
* **Omezení rizik**: Uvolněte prostředky v předplatném nebo zvyšte prostředky dostupné pro předplatné a pokuste se cluster vytvořit znovu.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Popis**: Došlo k vnitřní chybě serveru. Zkuste prosím žádost znovu.  
* **Omezení rizik**: Opakujte požadavek.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Popis**: Azure Storage umístění *DataRegion* není platné umístění. Přesvědčte se, zda je oblast správná, a opakujte požadavek.
* **Omezení rizik**: Vyberte umístění úložiště, které podporuje HDInsight, ověřte, jestli je váš cluster umístěný společně, a zkuste operaci zopakovat.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Popis**: Neplatná velikost virtuálního počítače pro datové uzly. Pro všechny datové uzly je podporována pouze velikost "velký virtuální počítač".  
* **Omezení rizik**: Zadejte podporovanou velikost uzlu pro datový uzel a opakujte operaci.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Popis**: Neplatná velikost virtuálního počítače pro hlavní uzel Pro hlavní uzel je podporována pouze velikost virtuálního počítače ExtraLarge.  
* **Omezení rizik**: Zadejte podporovanou velikost uzlu pro hlavní uzel a opakujte operaci.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Popis**: Používané ID předplatného *yourSubscriptionId* nemá dostatečná oprávnění k provedení operace odstranění pro cluster *yourClusterName*.  
* **Omezení rizik**: Pokud je cluster v chybovém stavu, vyřaďte ho a pak to zkuste znovu.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Popis**: Název kontejneru objektů BLOB externího účtu úložiště *yourContainerName* je neplatný. Ujistěte se, že název začíná písmenem a obsahuje jenom malá písmena, číslice a spojovníky.  
* **Omezení rizik**: Zadejte platný název kontejneru objektů BLOB pro účet úložiště a zkuste operaci zopakovat.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Popis**: Aby bylo možné nastavit podrobnosti tajného klíče, musí být konfigurace pro účet externího úložiště *yourStorageAccountName* .  
* **Omezení rizik**: Zadejte platný tajný klíč pro účet úložiště a zkuste operaci zopakovat.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Popis**: Hlavička verze *yourVersionHeader* není v platném formátu yyyy-MM-DD.  
* **Omezení rizik**: Zadejte platný formát pro záhlaví verze a opakujte požadavek.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Popis**: Neplatná konfigurace clusteru Našla se víc než jedna konfigurace hlavního uzlu.  
* **Omezení rizik**: Upravte konfiguraci tak, aby byl zadán pouze jeden hlavní uzel.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Popis**: Operaci nelze dokončit v povoleném čase nebo maximální možné pokusy o opakování. Zkuste prosím žádost znovu.  
* **Omezení rizik**: Opakujte požadavek.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Popis**: Parametr *yourParameterName* nemůže mít hodnotu null ani být prázdný.  
* **Omezení rizik**: Zadejte platnou hodnotu parametru.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Popis**: Nejméně jeden vstup žádosti o vytvoření clusteru není platný. Přesvědčte se, zda jsou vstupní hodnoty správné, a opakujte požadavek.  
* **Omezení rizik**: Přesvědčte se, zda jsou vstupní hodnoty správné, a opakujte požadavek.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Popis**: Možnost oblastí není k dispozici pro oblast *yourRegionName* a ID předplatného *yourSubscriptionId*.  
* **Omezení rizik**: Zadejte oblast, která podporuje clustery HDInsight. Veřejně podporované oblasti: Jihovýchodní Asie, Západní Evropa, Severní Evropa, Východní USA nebo Západní USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Popis**: *YourStorageAccountName* účtu úložiště je v oblasti *currentRegionName*. Měl by být stejný jako *yourClusterRegionName*oblasti clusteru.  
* **Omezení rizik**: Buď zadejte účet úložiště ve stejné oblasti, ve které je váš cluster, nebo pokud jsou vaše data v účtu úložiště, vytvořte nový cluster ve stejné oblasti jako existující účet úložiště. Pokud používáte portál, uživatelské rozhraní je předem upozorní na tento problém.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Popis**: Zadané ID předplatného *yourSubscriptionId* není aktivní.  
* **Omezení rizik**: Opětovná aktivace předplatného nebo získání nového platného předplatného.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Popis**: ID předplatného *yourSubscriptionId* se nepovedlo najít.  
* **Omezení rizik**: Ověřte, jestli je ID vašeho předplatného platné, a zkuste operaci zopakovat.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Popis**: Nepovedlo se přeložit DNS *yourDnsUrl*. Ujistěte se prosím, že je k dispozici plně kvalifikovaná adresa URL koncového bodu objektu BLOB.  
* **Omezení rizik**: Zadejte platnou adresu URL objektu BLOB. Adresa URL musí být plně platná, včetně počátečního řetězce *http://* a končí *. com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Popis**: Nepovedlo se ověřit umístění *yourDnsUrl*prostředků. Ujistěte se prosím, že je k dispozici plně kvalifikovaná adresa URL koncového bodu objektu BLOB.  
* **Omezení rizik**: Zadejte platnou adresu URL objektu BLOB. Adresa URL musí být plně platná, včetně počátečního řetězce *http://* a končí *. com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Popis**: Možnost verze není dostupná pro *specifiedVersion* verzí a ID předplatného *yourSubscriptionId*.  
* **Omezení rizik**: Vyberte verzi, která je k dispozici, a zkuste operaci zopakovat.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Popis**: *SpecifiedVersion* verze není podporována.
* **Omezení rizik**: Vyberte podporovanou verzi a zkuste operaci zopakovat.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Popis**: *SpecifiedVersion* verze není k dispozici v Azure region *specifiedRegion*.  
* **Omezení rizik**: Vyberte verzi, která je podporovaná v zadané oblasti, a zkuste operaci zopakovat.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Popis**: Neplatná konfigurace clusteru Požadovaná konfigurace účtu WASB se v externích účtech nenašla.  
* **Omezení rizik**: Ověřte, že účet existuje a je správně zadaný v konfiguraci, a zkuste operaci zopakovat.

## <a name="next-steps"></a>Další kroky

* [Povolit výpisy haldy pro Apache Hadoop služby v HDInsight se systémem Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
