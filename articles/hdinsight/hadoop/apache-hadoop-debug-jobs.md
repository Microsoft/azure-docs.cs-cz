---
title: 'Ladění systému Hadoop v HDInsight: Zobrazit protokoly a interpretace chybových zpráv – Azure '
description: Další informace o chybové zprávy, které se může zobrazit při správě HDInsight pomocí Powershellu a kroky, které můžete provést při obnově.
services: hdinsight
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 7600ff1a11435ee079fef80ecfa5ad88de1b4750
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569018"
---
# <a name="analyze-hadoop-logs"></a>Analýza protokolů Hadoop

Každý cluster Hadoop v HDInsight Azure má účet úložiště Azure používat jako výchozí systém souborů. Účet úložiště se označuje jako výchozí účet úložiště. Cluster používá služby Azure Table storage a Blob storage na výchozí účet úložiště k ukládání svých protokolů.  Výchozí účet úložiště pro váš cluster najdete v tématu [spravovat Hadoop clusterů v HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Protokoly uchovávat v účtu úložiště i po odstranění clusteru.

## <a name="logs-written-to-azure-tables"></a>Protokoly se zapisují do tabulek Azure

Protokoly zapisují do tabulek Azure poskytují jednu úroveň přehled o tom, co se děje s clusterem HDInsight.

Při vytváření clusteru služby HDInsight šest tabulky vytvářejí automaticky pro clustery založené na Linuxu ve výchozí tabulce úložiště:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Soubor názvy tabulek jsou **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Tyto tabulky obsahují následující pole:

* ClusterDnsName
* Název komponenty
* eventTimestamp
* Hostitel
* MALoggingHash
* Zpráva
* Ne
* PreciseTimeStamp
* Role
* RowIndex
* Tenant
* ČASOVÉ RAZÍTKO
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Nástroje pro přístup k protokoly
Celá řada nástrojů, k dispozici pro přístup k datům v těchto tabulkách:

* Visual Studio
* Azure Storage Explorer
* Power Query pro Excel

#### <a name="use-power-query-for-excel"></a>Pomocí Power Query pro Excel
Power Query lze instalovat z [Microsoft Power Query pro Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Zobrazit stránku ke stažení pro požadavky na systém.

**Pomocí Power Query k otevření a analýza protokolů služby**

1. Otevřít **aplikaci Microsoft Excel**.
2. Z **Power Query** nabídky, klikněte na tlačítko **z Azure**a potom klikněte na tlačítko **z Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery otevřete Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Zadejte název účtu úložiště, krátký název nebo plně kvalifikovaný název.
4. Zadejte klíč účtu úložiště. Zobrazí seznam tabulek:
   
    ![Protokoly HDInsight Hadoop ve službě Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Klikněte pravým tlačítkem na tabulku hadoopservicelog v **Navigátor** podokně a vyberte **upravit**. Zobrazí se čtyři sloupce. Volitelně můžete odstranit **klíč oddílu**, **klíč řádku**, a **časové razítko** sloupců a že je vyberete a pak kliknete **odebrat sloupce** z Možnosti na pásu karet.
6. Klikněte na ikonu rozbalení v obsahu sloupce vybrat sloupce, které chcete importovat do Excelové tabulky. Pro tuto ukázku volba TraceLevel a název komponenty: To lze uvést některé základní informace, na kterém součásti vyskytly problémy.
   
    ![HDInsight Hadoop protokoly zvolit sloupce](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Klikněte na tlačítko **OK** importovat data.
8. Vyberte **TraceLevel**, Role, a **ComponentName** sloupce a pak klikněte na tlačítko **Group** ovládacího prvku na pásu karet.
9. Klikněte na tlačítko **OK** v dialogovém okně Group By
10. Klikněte na ** použít & Zavřít **.

Teď můžete aplikaci Excel k filtrování a řazení podle potřeby. Můžete zahrnout další sloupce (jako je například zpráva) Pokud chcete přejít na problémy, když k nim dojde, ale výběru a seskupování sloupců výše popsané poskytuje vrazíme přehled o co se děje s služby Hadoop. Stejné nápad lze použít pro setuplog a hadoopinstalllog tabulky.

#### <a name="use-visual-studio"></a>Použití Visual Studia
**Použití sady Visual Studio**

1. Otevřete sadu Visual Studio.
2. Z **zobrazení** nabídky, klikněte na tlačítko **Průzkumníka cloudu**. Nebo jednoduše klikněte na tlačítko **CTRL +\, CTRL + X**.
3. Z **Průzkumníka cloudu**vyberte **typy prostředků**.  Je k dispozici možnost **skupiny prostředků**.
4. Rozbalte **účty úložiště**, výchozí účet úložiště pro váš cluster a potom **tabulky**.
5. Dvakrát klikněte na panel **hadoopservicelog**.
6. Přidejte filtr. Příklad:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop protokoly zvolit sloupce](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Další informace o vytváření filtrů najdete v tématu [vytváření řetězce filtru pro návrháře tabulky](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Protokoly se zapisují do úložiště objektů Blob v Azure
[Protokoly zapisují do tabulek Azure](#log-written-to-azure-tables) poskytuje jednu úroveň přehled o tom, co se děje s clusterem HDInsight. Tyto tabulky však neposkytují úroveň úkolu protokoly, které mohou být užitečné při procházení k dalším potížím, které se objeví. Kvůli této další úroveň podrobností clustery HDInsight umožňují zapisovat protokoly úlohy do svého účtu úložiště objektů Blob pro úlohy, které je odeslána prostřednictvím Templeton. Prakticky to znamená, že pomocí rutin prostředí Azure PowerShell nebo rozhraní .NET API odeslání úlohy, není odeslaných přes RDP/příkazového řádku přístup ke clusteru úloh odeslaných úloh. 

Pokud chcete zobrazit protokoly, naleznete v tématu [protokolům aplikací YARN přístupu na základě Linux HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Další informace o aplikačních protokolů najdete v tématu [zjednodušuje správu přihlášení uživatele a přístup v YARNU](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Zobrazit protokoly stavu a úlohy clusteru
### <a name="access-the-ambari-ui"></a>Přístup k rozhraní Ambari
Na webu Azure Portal klikněte na název clusteru HDInsight a otevřete tak podokno clusteru. V podokně clusteru, klikněte na tlačítko **řídicí panel**.

![Spustit řídicí panel clusteru](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Přístup k Yarn uživatelského rozhraní
Na webu Azure Portal klikněte na název clusteru HDInsight a otevřete tak podokno clusteru. V podokně clusteru, klikněte na tlačítko **řídicí panel**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. V Ambari, vyberte **YARN** ze seznamu služeb na levé straně. Na stránce, která se zobrazí, vyberte **rychlé odkazy**, vyberte položku aktivní hlavní uzel a uživatelské rozhraní správce prostředků.

Rozhraní YARN můžete provádět následující akce:

* **Získat stav clusteru**. V levém podokně rozbalte **clusteru**a klikněte na tlačítko **o**. Toto současné clusteru podrobnosti o stavu, jako je Celková přidělená paměť, jader využívaných, stav cluster resource Manageru, verze clusteru a tak dále.
  
    ![Spustit řídicí panel clusteru](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Získat stav uzlu**. V levém podokně rozbalte **clusteru**a klikněte na tlačítko **uzly**. Vypíšou se všechny uzly v clusteru, adresu HTTP každého uzlu prostředky přidělené na každý uzel atd.
* **Monitorovat stav úlohy**. V levém podokně rozbalte **clusteru**a potom klikněte na tlačítko **aplikací** zobrazte seznam všech úloh v clusteru. Pokud se chcete podívat na úloh v určitém stavu (jako je například nový, odeslané, spuštění, atd.), klikněte na příslušný odkaz pod **aplikací**. Dále můžete kliknout na název úlohy zobrazíte další informace o úloze takové včetně výstup, protokoly atd.

### <a name="access-the-hbase-ui"></a>Přístup k rozhraní HBase
Na webu Azure Portal klikněte na název clusteru HDInsight HBase a otevřete tak podokno clusteru. V podokně clusteru, klikněte na tlačítko **řídicí panel**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. Vyberte HBase v Ambari, v seznamu služeb. Vyberte **rychlé odkazy** nahoře na stránce, přejděte na odkaz aktivní uzel Zookeeper a potom klikněte na hlavní uživatelské rozhraní HBase.

## <a name="hdinsight-error-codes"></a>Kódy chyb HDInsight
Chybové zprávy najdete v této části jsou k dispozici k poskytování pomoci uživatelům systému Hadoop v Azure HDInsight, pochopit možné chybové stavy, se kterými se můžete setkat při správě služby pomocí Azure Powershellu a upozorňují na kroky, které je možné provést Obnovit z chyby.

Některé z těchto chybových zpráv může také zobrazit na webu Azure Portal při se používá ke správě clusterů HDInsight. Další chybové zprávy, které můžete narazit, ale jsou méně granulární z důvodu omezení na nápravné akce v tomto kontextu je to možné. Další chybové zprávy jsou k dispozici v kontextech, ve kterém je zřejmý omezení rizik. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Popis**: Chcete-li použít vlastní nastavení u metaúložiště Hive a Oozie zadejte podrobnosti databáze Azure SQL pro nejméně jednu součást.
* **Zmírnění dopadů**: uživatel musí zadat platné metastore SQL Azure a opakujte žádost.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Popis**: Nelze vytvořit cluster v oblasti *nameOfYourRegion*. Použijte platný oblasti HDInsight a opakujte žádost.
* **Zmírnění dopadů**: Zákazník vytvořit oblasti clusteru, který je aktuálně podporuje: jihovýchodní Asie, západní Evropa, Severní Evropa, USA – východ nebo USA – západ.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Popis**: server nemohl najít záznam požadovaný cluster.  
* **Zmírnění dopadů**: Zkuste operaci zopakovat.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Popis**: název DNS clusteru *yourDnsName* je neplatný. Ujistěte se prosím název začíná a končí alfanumerický a může obsahovat pouze '-' speciální znak  
* **Zmírnění dopadů**: Ujistěte se, zda používáte platný název DNS pro váš cluster, který začíná a končí alfanumerické znaky a žádné speciální obsahuje i jiné znaky než čárka "-" a pak zkuste operaci zopakovat.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Popis**: název clusteru *yourClusterName* není k dispozici. Vyberte prosím jiný název.  
* **Zmírnění dopadů**: uživatel by neměl zadejte název clusteru, který je jedinečný a existují a zkuste to znovu. Pokud uživatel je na portálu, rozhraní se upozorní je, pokud název clusteru je již používán během kroků vytvořit.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Popis**: heslo clusteru je neplatné. Heslo musí být minimálně 10 znaků a musí obsahovat alespoň jednu číslici, velké písmeno, malé písmeno a speciální znaky bez mezer a nesmí obsahovat uživatelské jméno jako jeho součástí.  
* **Zmírnění dopadů**: Zadejte platný cluster heslo a zkuste operaci zopakovat.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Popis**: uživatelské jméno clusteru je neplatné. Ujistěte se prosím, že uživatelské jméno nemůže obsahovat speciální znaky ani mezery.  
* **Zmírnění dopadů**: Zadejte uživatelské jméno platným clusterem a zkuste operaci zopakovat.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Popis**: název DNS clusteru *yourDnsClusterName* je neplatný. Ujistěte se prosím název začíná a končí alfanumerický a může obsahovat pouze '-' speciální znak  
* **Zmírnění dopadů**: Zadejte platné uživatelské jméno clusteru DNS a zkuste operaci zopakovat.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Popis**: název kontejneru v identifikátoru URI *yourcontainerURI* a název DNS *yourDnsName* v žádosti subjekt musí být stejné.  
* **Zmírnění dopadů**: Ujistěte se, že název kontejneru a název DNS jsou stejné a zkuste operaci zopakovat.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Popis**: Konfigurace clusteru neplatný. Nepovedlo se najít žádné definice data uzlu v velikost uzlu.  
* **Zmírnění dopadů**: Zkuste operaci zopakovat.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Popis**: nepovedlo se odstranit nasazení clusteru  
* **Zmírnění dopadů**: Zkuste operaci odstranění.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Popis**: Chyba v konfiguraci služby. Požadované informace mapování DNS nebyl nalezen.  
* **Zmírnění dopadů**: odstranění clusteru a vytvoření nového clusteru.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Popis**: Duplicitní pokus o vytvoření clusteru kontejneru. Existuje záznam pro *nameOfYourContainer* ale značek etag se neshodují.
* **Zmírnění dopadů**: Zadejte jedinečný název kontejneru a zkuste operaci vytvoření.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Popis**: hostovanou službu *nameOfYourHostedService* již obsahuje cluster. Hostovaná služba nemůže obsahovat více clusterů  
* **Zmírnění dopadů**: hostování clusteru v jiné hostované služby.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Popis**: server nemohl aktualizovat stav nasazení clusteru.  
* **Zmírnění dopadů**: Zkuste operaci zopakovat. Pokud se to stane více než jednou, obraťte se na šablony stylů CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Popis**: clusteru *yourClusterName* byla odstraněna jako součást údržby. Znovu prosím vytvořte cluster.
* **Zmírnění dopadů**: znovu vytvořte cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Popis**: Konfigurace clusteru neplatný. Požadované konfigurace hlavního uzlu se nenašla v velikosti uzlů.
* **Zmírnění dopadů**: Zkuste operaci zopakovat.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Popis**: nejde vytvořit hostovanou službu *nameOfYourHostedService*. Zkuste prosím žádost.  
* **Zmírnění dopadů**: Zkuste požadavek zopakovat.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Popis**: hostovanou službu *nameOfYourHostedService* už má produkčního nasazení. Hostovaná služba nemůže obsahovat více nasazení v produkčním prostředí. Zkuste požadavek s jiný název clusteru.
* **Zmírnění dopadů**: použijte jiný název clusteru a opakujte žádost.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Popis**: hostovanou službu *nameOfYourHostedService* pro cluster se nenašel.  
* **Zmírnění dopadů**: Pokud se cluster nachází v chybovém stavu, odstraňte ho a pak to zkuste znovu.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Popis**: hostovanou službu *nameOfYourHostedService* nemá žádné přidružené nasazení.  
* **Zmírnění dopadů**: Pokud se cluster nachází v chybovém stavu, odstraňte ho a pak to zkuste znovu.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Popis**: The SubscriptionId *yourSubscriptionId* nemá vlevo jádra k vytvoření clusteru *yourClusterName*. Požadováno: *resourcesRequired*, k dispozici: *resourcesAvailable*.  
* **Zmírnění dopadů**: uvolnit tak prostředky v rámci vašeho předplatného, nebo zvyšte prostředky dostupné pro předplatné a zkuste to znovu vytvořte cluster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Popis**: ID předplatného *yourSubscriptionId* nemá žádné kvóty pro novou hostovanou službu a vytvořte cluster *yourClusterName*.  
* **Zmírnění dopadů**: uvolnit tak prostředky v rámci vašeho předplatného, nebo zvyšte prostředky dostupné pro předplatné a zkuste to znovu vytvořte cluster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Popis**: na serveru došlo k vnitřní chybě. Zkuste prosím žádost.  
* **Zmírnění dopadů**: Zkuste požadavek zopakovat.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Popis**: umístění úložiště Azure *dataRegionName* není platné umístění. Ujistěte se, že oblast je správný a opakujte žádost.
* **Zmírnění dopadů**: Vyberte umístění úložiště, který podporuje HDInsight, zkontrolujte, jestli je váš cluster společně a zkuste operaci zopakovat.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Popis**: velikost neplatný virtuálního počítače pro datové uzly. Pro všechny datové uzly se podporuje jenom velikost velký virtuální počítač.  
* **Zmírnění dopadů**: určení velikosti podporované uzel pro datový uzel a zkuste operaci zopakovat.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Popis**: velikost neplatný virtuálního počítače pro hlavní uzel. Pro hlavní uzel je podporována pouze velikost "ExtraLarge virtuálního počítače".  
* **Zmírnění dopadů**: Zadejte velikost uzlu podporované pro hlavní uzel a opakujte operaci

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Popis**: ID předplatného *yourSubscriptionId* používá nemá dostatečná oprávnění k provedení operace odstranění clusteru *yourClusterName*.  
* **Zmírnění dopadů**: Pokud se cluster nachází v chybovém stavu, vyřaďte ho a pak to zkuste znovu.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Popis**: název kontejneru objektů blob v účtu externího úložiště *yourContainerName* je neplatný. Ujistěte se, že název začíná písmenem a obsahuje jenom malá písmena, číslice a pomlčky.  
* **Zmírnění dopadů**: Zadejte název kontejneru objektů blob v účtu úložiště platný a zkuste operaci zopakovat.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Popis**: konfigurace pro externí účet úložiště *yourStorageAccountName* musí mít tajného klíče podrobnosti o nastavení.  
* **Zmírnění dopadů**: Zadejte platný tajný klíč pro účet úložiště a zkuste operaci zopakovat.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Popis**: Hlavička verze *yourVersionHeader* není v platném formátu rrrr mm-dd.  
* **Zmírnění dopadů**: Zadejte platný formát pro hlavičku verze a opakujte žádost.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Popis**: Konfigurace clusteru neplatný. Nalezeno více než jednu konfiguraci hlavního uzlu.  
* **Zmírnění dopadů**: Upravte konfiguraci tak, že je zadán pouze jeden hlavní uzel.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Popis**: operaci nelze dokončit v povoleném čase nebo maximální počet opakovaných pokusů možné. Zkuste prosím žádost.  
* **Zmírnění dopadů**: Zkuste požadavek zopakovat.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Popis**: Parametr *yourParameterName* nemůže být null ani prázdný.  
* **Zmírnění dopadů**: Zadejte platnou hodnotu parametru.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Popis**: jeden nebo více vstupů žádost o vytvoření clusteru není platná. Ujistěte se, že vstupní hodnoty jsou správné a opakujte žádost.  
* **Zmírnění dopadů**: Ujistěte se, že vstupní hodnoty jsou správné a opakujte žádost.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Popis**: oblast funkce není k dispozici pro oblast *yourRegionName* a ID předplatného *yourSubscriptionId*.  
* **Zmírnění dopadů**: Určete oblast, která podporuje clustery HDInsight. Jsou veřejně podporovaných oblastí: jihovýchodní Asie, západní Evropa, Severní Evropa, USA – východ nebo USA – západ.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Popis**: účet úložiště *yourStorageAccountName* je v oblasti *currentRegionName*. By měla být stejná jako oblast clusteru *yourClusterRegionName*.  
* **Zmírnění dopadů**: Zadejte účet úložiště ve stejné oblasti, ve které váš cluster nebo pokud máte už data v účtu úložiště, vytvořte nový cluster ve stejné oblasti jako existující účet úložiště. Pokud používáte na portálu, rozhraní upozorní je tento problém předem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Popis**: zadané ID předplatného *yourSubscriptionId* není aktivní.  
* **Zmírnění dopadů**: znovu aktivovat vaše předplatné nebo si nové předplatné platný.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Popis**: ID předplatného *yourSubscriptionId* nebyl nalezen.  
* **Zmírnění dopadů**: Zkontrolujte, že ID vašeho předplatného je platný a zkuste operaci zopakovat.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Popis**: nejde přeložit DNS *yourDnsUrl*. Ujistěte se prosím, že je k dispozici plně kvalifikovanou adresu URL pro koncový bod objektu blob.  
* **Zmírnění dopadů**: Zadejte adresu URL platný objekt blob. Adresa URL musí být plně platný, včetně počínaje *http://* a končící na *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Popis**: Nepodařilo se ověřit umístění prostředku *yourDnsUrl*. Ujistěte se prosím, že je k dispozici plně kvalifikovanou adresu URL pro koncový bod objektu blob.  
* **Zmírnění dopadů**: Zadejte adresu URL platný objekt blob. Adresa URL musí být plně platný, včetně počínaje *http://* a končící na *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Popis**: verze funkce není k dispozici pro verzi *specifiedVersion* a ID předplatného *yourSubscriptionId*.  
* **Zmírnění dopadů**: Zvolte verzi, která je k dispozici a zkuste operaci zopakovat.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Popis**: verze *specifiedVersion* není podporován.
* **Zmírnění dopadů**: Vyberte verzi, která je podporována a zkuste operaci zopakovat.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Popis**: verze *specifiedVersion* není k dispozici v oblasti Azure *specifiedRegion*.  
* **Zmírnění dopadů**: Vyberte verzi, která je podporována v zadanou oblast a zkuste operaci zopakovat.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Popis**: Konfigurace clusteru neplatný. Nebyl nalezen v externí účty požadované konfigurace s účtu WASB.  
* **Zmírnění dopadů**: Ověřte, že účet existuje a je správně zadaný v konfiguraci a zkuste operaci zopakovat.

## <a name="next-steps"></a>Další postup

* [Použití zobrazení Ambari k ladění úloh Tez v HDInsight](../hdinsight-debug-ambari-tez-view.md)
* [Povolení výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md)
