---
title: 'Ladění Hadoop v HDInsight: Zobrazit protokoly a interpretovat chybové zprávy - Azure | Microsoft Docs'
description: Další informace o chybových zpráv, které vám mohou být při správě HDInsight pomocí prostředí PowerShell a kroky, které můžete provést obnovení.
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: ashishthaps
documentationcenter: ''
ms.assetid: 7e6ceb0e-8be8-4911-bc80-20714030a3ad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashish
ms.openlocfilehash: a5db3848eda2dbb6f117562e059b909575966993
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-hadoop-logs"></a>Analýza protokolů Hadoop

Každý cluster Hadoop v prostředí Azure HDInsight má účet úložiště Azure používat jako výchozí systém souborů. Účet úložiště se označuje jako výchozí účet úložiště. Cluster využívá Azure Table storage a úložiště objektů Blob na výchozí účet úložiště pro ukládání protokoly.  Výchozí účet úložiště pro váš cluster naleznete v tématu [spravovat Hadoop clusterů v HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Protokoly uchovávat v účtu úložiště i po odstranění clusteru.

## <a name="logs-written-to-azure-tables"></a>Protokoly zapisují do tabulek Azure

Protokoly zapisují do tabulek Azure poskytnout jednu úroveň přehled o dění pomocí clusteru služby HDInsight.

Při vytváření clusteru HDInsight šesti tabulky se vytvářejí automaticky pro clustery se systémem Linux ve službě Table storage výchozí:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Názvy souborů tabulky jsou **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Tyto tabulky obsahovat následující pole:

* ClusterDnsName
* ComponentName
* eventTimestamp
* Hostitel
* MALoggingHash
* Zpráva
* N
* PreciseTimeStamp
* Role
* RowIndex
* Tenant
* ČASOVÉ RAZÍTKO
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Nástroje pro přístup k protokoly
Nejsou k dispozici pro přístup k datům v těchto tabulkách celou řadu nástrojů:

* Visual Studio
* Azure Storage Explorer
* Power Query pro Excel

#### <a name="use-power-query-for-excel"></a>Použijte Power Query pro Excel
Power Query lze nainstalovat z [Microsoft Power Query pro Excel](http://www.microsoft.com/en-us/download/details.aspx?id=39379). Naleznete na stránce stahování pro požadavky na systém.

**Pomocí doplňku Power Query můžete otevřít a analyzovat protokol služby**

1. Otevřete **aplikace Microsoft Excel**.
2. Z **Power Query** nabídky, klikněte na tlačítko **z Azure**a potom klikněte na **z Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery otevřete Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Zadejte název účtu úložiště, krátký název nebo plně kvalifikovaný název domény.
4. Zadejte klíč účtu úložiště. Zobrazí se seznam tabulek:
   
    ![HDInsight Hadoop protokolů uložených v Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Klikněte pravým tlačítkem na tabulky hadoopservicelog **Navigátor** panelu a vyberte **upravit**. Zobrazí se čtyři sloupce. Volitelně můžete odstranit **klíč oddílu**, **klíč řádku**, a **časové razítko** sloupce je vyberete a poté na **odebrat sloupce** z možností na pásu karet.
6. Klikněte na ikonu rozbalení na sloupci obsahu vybrat sloupce, které chcete importovat do tabulky aplikace Excel. Pro tento ukázkový vybrali jste TraceLevel a ComponentName: ho můžete mě některých základních informací, na kterém byl součástí problémy.
   
    ![Protokoly HDInsight Hadoop zvolit sloupce](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Klikněte na tlačítko **OK** importovat data.
8. Vyberte **TraceLevel**, Role, a **ComponentName** sloupce a pak klikněte na tlačítko **Group By** ovládacího prvku na pásu karet.
9. Klikněte na tlačítko **OK** v dialogovém okně Seskupit podle
10. Klikněte na tlačítko ** Použít & Zavřít **.

Nyní můžete aplikaci Excel k filtrování a řazení podle potřeby. Můžete rozbalit problémy, když k nim dojde, ale výběru a seskupování sloupců popsané výše poskytuje dostatečnou přehled o co se děje s služby Hadoop zařadit ostatních sloupců (například zprávy). Na stejné nápad může být použita setuplog a hadoopinstalllog tabulky.

#### <a name="use-visual-studio"></a>Použití Visual Studia
**Použijte sadu Visual Studio**

1. Otevřete sadu Visual Studio.
2. Z **zobrazení** nabídky, klikněte na tlačítko **Průzkumník cloudu**. Nebo jednoduše klikněte na tlačítko **CTRL +\, CTRL + X**.
3. Z **Průzkumník cloudu**, vyberte **typy prostředků**.  Je k dispozici možnost **skupiny prostředků**.
4. Rozbalte položku **účty úložiště**, výchozí účet úložiště pro cluster a potom **tabulky**.
5. Klikněte dvakrát na **hadoopservicelog**.
6. Přidání filtru. Příklad:
   
        TraceLevel eq 'ERROR'
   
    ![Protokoly HDInsight Hadoop zvolit sloupce](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Další informace o vytváření filtrů najdete v tématu [vytvořit filtr řetězce pro návrháře tabulky](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Protokoly zapisují do Azure Blob Storage
[Protokoly zapisují do tabulek Azure](#log-written-to-azure-tables) zadejte jednu úroveň přehled o dění pomocí clusteru služby HDInsight. Tyto tabulky však neposkytuje protokoly úrovni úkolů, které mohou být užitečné při procházení další problémy, když k nim dojde. Zadejte tento další úroveň podrobností, clustery HDInsight nastavené zápis úloh protokolů k účtu úložiště objektů Blob pro všechny úlohy, které je odeslána prostřednictvím Templeton. Prakticky to znamená, úlohy, odeslané pomocí rutin prostředí Microsoft Azure PowerShell nebo rozhraní .NET API odeslání úlohy, není úlohy, odeslané prostřednictvím protokolu RDP nebo příkazového-řádku přístup ke clusteru. 

K zobrazení protokolů, najdete v části [protokolů YARN přístup k aplikaci v HDInsight se systémem Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Další informace o protokoly aplikací najdete v tématu [zjednodušit správu přihlášení uživatele a přístup v YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Zobrazení stavu a úlohu protokoly clusteru
### <a name="access-the-ambari-ui"></a>Přístup k rozhraní Ambari
Z portálu Azure klikněte na název clusteru HDInsight a otevřete podokno clusteru. V podokně clusteru, klikněte na tlačítko **řídicí panel**.

![Spusťte řídicí panel clusteru](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Přístup k Yarn uživatelského rozhraní
Z portálu Azure klikněte na název clusteru HDInsight a otevřete podokno clusteru. V podokně clusteru, klikněte na tlačítko **řídicí panel**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. V Ambari, vyberte **YARN** ze seznamu služeb na levé straně. Na stránce, který se zobrazí, vyberte možnost **rychlé odkazy**, vyberte položku aktivní hlavní uzel a uživatelské rozhraní správce prostředků.

Rozhraní YARN můžete provést následující akce:

* **Získat stav clusteru**. V levém podokně rozbalte **clusteru**a klikněte na tlačítko **o**. Tato přítomen clusteru podrobnosti o stavu jako přidělené paměti jádra používá, celkový stav správce prostředků clusteru, verze clusteru a tak dále.
  
    ![Spusťte řídicí panel clusteru](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Získat stav uzlu**. V levém podokně rozbalte **clusteru**a klikněte na tlačítko **uzly**. Toto jsou uvedeny všechny uzly v clusteru, adresa HTTP každého uzlu prostředky přidělené na každém uzlu, atd.
* **Monitorovat stav úlohy**. V levém podokně rozbalte **clusteru**a potom klikněte na **aplikace** zobrazte seznam všech úloh v clusteru. Pokud chcete, podívejte se na úloh v určitém stavu (například nové, odeslaná, spuštěná atd.), klikněte na příslušný odkaz v části **aplikace**. Dále můžete kliknout na název úlohy, který získáte další informace o úlohy takové včetně výstup, protokoly, atd.

### <a name="access-the-hbase-ui"></a>Přístup k rozhraní HBase
Z portálu Azure klikněte na název clusteru HDInsight HBase a otevřete podokno clusteru. V podokně clusteru, klikněte na tlačítko **řídicí panel**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. V Ambari vyberte ze seznamu služeb HBase. Vyberte **rychlé odkazy** nahoře na stránce, přejděte na aktivní uzel odkaz Zookeeper a pak klikněte na hlavní HBase uživatelského rozhraní.

## <a name="hdinsight-error-codes"></a>Kódy chyb HDInsight
Chybové zprávy uvedeno v této části jsou k dispozici na Hadoop v prostředí Azure HDInsight vysvětlení možné chybové stavy, které narazí při správě služby pomocí prostředí Azure PowerShell uživatele a jejich upozorňují na kroky, které můžete provést Chcete-li zotavit z chyby.

Některé z těchto chybových zpráv může také zobrazit na portálu Azure při se používá ke správě clusterů HDInsight. Další chybové zprávy, které můžete narazit, ale jsou méně granulární z důvodu omezení na nápravné akce v tomto kontextu. Další chybové zprávy jsou uvedeny v kontextu, kde je zřejmé toto řešení. 

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Popis**: Chcete-li použít vlastní nastavení pro metaúložiště Hive a Oozie zadejte podrobnosti databáze Azure SQL pro alespoň jedna součást.
* **Zmírnění dopadů**: uživatel musí zadat platný metaúložiště SQL Azure a opakujte žádost.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Popis**: Nepodařilo se vytvořit cluster v oblasti *nameOfYourRegion*. Použijte platný oblast Hdinsightu a opakujte žádost.
* **Zmírnění dopadů**: zákazníka měli vytvořit oblasti clusteru, který je aktuálně podporuje: jihovýchodní Asie, západní Evropa, Severní Evropa, východní USA nebo západní USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Popis**: server nelze najít záznam požadovaný cluster.  
* **Zmírnění dopadů**: operaci opakujte.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Popis**: název DNS clusteru *yourDnsName* je neplatný. Zkontrolujte, zda název spustí a končí alfanumerické znaky a může obsahovat pouze '-' speciální znak  
* **Zmírnění dopadů**: Ujistěte se, zda používáte platný název DNS pro váš cluster, který začíná a končí alfanumerické znaky a obsahuje žádné speciální znaky jiné než čárka '-' a operaci opakujte.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Popis**: název clusteru *yourClusterName* není k dispozici. Vyberte prosím jiný název.  
* **Zmírnění dopadů**: uživatel by neměl zadejte název clusteru, který je jedinečný a existují a opakujte. Pokud uživatel používá portálu, uživatelské rozhraní bude upozornit je, pokud název clusteru, je již používán během kroků vytvořit.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Popis**: clusteru heslo je neplatné. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jednu číslici, velké písmeno, malé písmeno a speciální znak bez mezer a nesmí obsahovat uživatelské jméno jako jeho součástí.  
* **Zmírnění dopadů**: Zadejte platný clusteru heslo a operaci opakujte.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Popis**: uživatelské jméno clusteru je neplatná. Zkontrolujte, zda uživatelské jméno nebude obsahovat speciální znaky ani mezery.  
* **Zmírnění dopadů**: zadat uživatelské jméno, platné clusteru a operaci opakujte.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Popis**: název DNS clusteru *yourDnsClusterName* je neplatný. Zkontrolujte, zda název spustí a končí alfanumerické znaky a může obsahovat pouze '-' speciální znak  
* **Zmírnění dopadů**: Zadejte platné uživatelské jméno DNS clusteru a operaci opakujte.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Popis**: název kontejneru v identifikátoru URI *yourcontainerURI* a název DNS *yourDnsName* v požadavku obsah musí být stejné.  
* **Zmírnění dopadů**: Ujistěte se, že váš název kontejneru a název DNS jsou stejné a operaci opakujte.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Popis**: Konfigurace clusteru neplatný. Nepodařilo se najít žádné definice uzlu dat v velikost uzlu.  
* **Zmírnění dopadů**: operaci opakujte.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Popis**: odstranění nasazení selhalo pro Cluster  
* **Zmírnění dopadů**: operaci odstranění.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Popis**: služby Chyba konfigurace. Požadované informace mapování DNS nebyl nalezen.  
* **Zmírnění dopadů**: odstranění clusteru a vytvoření nového clusteru.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Popis**: Duplicitní pokusu o vytvoření clusteru kontejneru. Existuje záznam pro *nameOfYourContainer* ale značky etag binárním rozsáhlým se neshodují.
* **Zmírnění dopadů**: Zadejte jedinečný název kontejneru a opakujte operaci vytvoření.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Popis**: hostovaná služba *nameOfYourHostedService* již obsahuje clusteru. Hostovaná služba nemůže obsahovat více clusterů  
* **Zmírnění dopadů**: hostitele clusteru, jiné hostované služby.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Popis**: serveru se nepovedlo aktualizovat stav nasazení clusteru.  
* **Zmírnění dopadů**: operaci opakujte. V takovém případě vícekrát, kontaktujte CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Popis**: clusteru *yourClusterName* byla odstraněna jako součást údržby. Vytvořte cluster.
* **Zmírnění dopadů**: znovu vytvořte cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Popis**: Konfigurace clusteru neplatný. Požadované konfigurace hlavního uzlu nebyla nalezena v velikostí uzlu.
* **Zmírnění dopadů**: operaci opakujte.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Popis**: Nelze vytvořit hostovanou službu *nameOfYourHostedService*. Opakujte žádost.  
* **Zmírnění dopadů**: opakujte žádost.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Popis**: hostované služby *nameOfYourHostedService* již produkčním nasazení. Hostovaná služba nemůže obsahovat více nasazení v produkčním prostředí. Opakujte požadavek s jiný název clusteru.
* **Zmírnění dopadů**: použijte jiný název clusteru a opakujte žádost.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Popis**: hostované služby *nameOfYourHostedService* pro cluster se nepodařilo najít.  
* **Zmírnění dopadů**: Pokud je cluster v chybovém stavu, odstraňte jej a akci opakujte.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Popis**: hostované služby *nameOfYourHostedService* nemá žádné přidružené nasazení.  
* **Zmírnění dopadů**: Pokud je cluster v chybovém stavu, odstraňte jej a akci opakujte.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Popis**: ID předplatného *yourSubscriptionId* nemá jader vlevo k vytvoření clusteru *yourClusterName*. Požadováno: *resourcesRequired*, k dispozici: *resourcesAvailable*.  
* **Zmírnění dopadů**: uvolní prostředky ve vašem předplatném nebo zvýšení dostupné k předplatnému prostředky a pokuste se znovu vytvořit cluster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Popis**: ID předplatného *yourSubscriptionId* nemá kvótu pro nové HostedService k vytvoření clusteru *yourClusterName*.  
* **Zmírnění dopadů**: uvolní prostředky ve vašem předplatném nebo zvýšení dostupné k předplatnému prostředky a pokuste se znovu vytvořit cluster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Popis**: došlo k vnitřní chybě serveru. Opakujte žádost.  
* **Zmírnění dopadů**: opakujte žádost.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Popis**: umístění úložiště Azure *dataRegionName* není platné umístění. Ujistěte se, že oblast je správný a opakujte žádost.
* **Zmírnění dopadů**: Vyberte umístění úložiště, který podporuje HDInsight, zkontrolujte, jestli je váš cluster umístit do stejného umístění a zkuste operaci zopakovat.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Popis**: velikost neplatný virtuálního počítače pro datové uzly. Pro všechny uzly dat je podporována pouze velikost "Velký virtuálního počítače".  
* **Zmírnění dopadů**: Zadejte velikost podporované uzlu pro datový uzel a opakujte operaci.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Popis**: velikost neplatný virtuálního počítače pro hlavního uzlu. Pouze velikost 'ExtraLarge virtuálního počítače, je podporována pro hlavního uzlu.  
* **Zmírnění dopadů**: Zadejte velikost podporované uzlu pro hlavní uzel a opakujte operaci

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Popis**: ID předplatného *yourSubscriptionId* používá nemá dostatečná oprávnění k provedení operace delete pro cluster *yourClusterName*.  
* **Zmírnění dopadů**: Pokud je cluster v chybovém stavu, zahoďte ho a potom akci opakujte.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Popis**: název kontejneru objektu blob účtu externího úložiště *yourContainerName* je neplatný. Zajistěte, aby název začíná písmenem a obsahuje jenom malá písmena, číslice a pomlčky.  
* **Zmírnění dopadů**: Zadejte název kontejneru objektu blob účet platný úložiště a operaci opakujte.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Popis**: konfigurace pro externí účet úložiště *yourStorageAccountName* musí mít tajný klíč podrobnosti o nastavení.  
* **Zmírnění dopadů**: Zadejte platný tajný klíč pro účet úložiště a operaci opakujte.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Popis**: Hlavička verze *yourVersionHeader* není v platném formátu rrrr mm-dd.  
* **Zmírnění dopadů**: Zadejte platný formát pro hlavičku verze a opakujte žádost.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Popis**: Konfigurace clusteru neplatný. Nalezena minimálně jedna konfigurace hlavního uzlu.  
* **Zmírnění dopadů**: Upravte konfiguraci tak, že pouze jeden hlavního uzlu je zadán.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Popis**: operaci nelze dokončit v povoleném čase nebo maximální počet opakování pokusů o zadání možné. Opakujte žádost.  
* **Zmírnění dopadů**: opakujte žádost.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Popis**: Parametr *yourParameterName* nemůže být null nebo prázdný.  
* **Zmírnění dopadů**: Zadejte platnou hodnotu pro parametr.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Popis**: jeden nebo více vstupů žádost o vytvoření clusteru není platný. Zajistěte, aby vstupní hodnoty jsou správné a opakujte žádost.  
* **Zmírnění dopadů**: Zkontrolujte, zda vstupní hodnoty jsou správné a opakujte žádost.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Popis**: oblast funkce není k dispozici pro oblast *yourRegionName* a ID předplatného *yourSubscriptionId*.  
* **Zmírnění dopadů**: určit oblasti, která podporuje clustery HDInsight. Jsou veřejně podporovaných oblastí: jihovýchodní Asie, západní Evropa, Severní Evropa, východní USA nebo západní USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Popis**: účet úložiště *yourStorageAccountName* je v oblasti *currentRegionName*. Musí být stejné jako oblasti clusteru *yourClusterRegionName*.  
* **Zmírnění dopadů**: Zadejte účet úložiště ve stejné oblasti, který je cluster v nebo pokud vašich dat je již v účtu úložiště, vytvořte nový cluster ve stejné oblasti jako stávající účet úložiště. Pokud používáte portál, uživatelské rozhraní vás upozorní, je tento problém předem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Popis**: zadané ID předplatného *yourSubscriptionId* není aktivní.  
* **Zmírnění dopadů**: znova si předplatné aktivovat nebo získat nové platné předplatné.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Popis**: ID předplatného *yourSubscriptionId* nebyl nalezen.  
* **Zmírnění dopadů**: Zkontrolujte, že svoje ID předplatného je platný a operaci opakujte.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Popis**: nelze přeložit DNS *yourDnsUrl*. Zkontrolujte, zda že je zadaný plně kvalifikovanou adresu URL pro koncový bod objektu blob.  
* **Zmírnění dopadů**: Zadejte adresu URL platný objekt blob. Adresa URL musí být plně platný, včetně počínaje *http://* a končící na *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Popis**: Nepodařilo se ověřit umístění prostředku *yourDnsUrl*. Zkontrolujte, zda že je zadaný plně kvalifikovanou adresu URL pro koncový bod objektu blob.  
* **Zmírnění dopadů**: Zadejte adresu URL platný objekt blob. Adresa URL musí být plně platný, včetně počínaje *http://* a končící na *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Popis**: verze funkce není k dispozici pro verzi *specifiedVersion* a ID předplatného *yourSubscriptionId*.  
* **Zmírnění dopadů**: Zvolte verzi, která je k dispozici a opakujte operaci.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Popis**: verze *specifiedVersion* není podporována.
* **Zmírnění dopadů**: Zvolte verzi, která je podporována a operaci opakujte.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Popis**: verze *specifiedVersion* není k dispozici v oblasti Azure *specifiedRegion*.  
* **Zmírnění dopadů**: Vyberte verzi, která je podporována v oblasti zadaný a operaci opakujte.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Popis**: Konfigurace clusteru neplatný. Požadované WASB účet konfigurace nebyla nalezena v externích účtů.  
* **Zmírnění dopadů**: Ověřte, zda účet existuje a zda je správně zadaný v konfiguraci a operaci opakujte.

## <a name="next-steps"></a>Další postup

* [Použití zobrazení Ambari k ladění úlohách Tez v HDInsight](../hdinsight-debug-ambari-tez-view.md)
* [Povolit výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní](../hdinsight-hadoop-manage-ambari.md)
