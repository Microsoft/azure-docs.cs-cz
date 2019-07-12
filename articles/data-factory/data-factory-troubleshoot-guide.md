---
title: Řešení potíží s Azure Data Factory | Dokumentace Microsoftu
description: Řešení potíží s Azure Data Factory. Běžné dokumentu pro všechny externí a aktivity řízení.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: d220730bb2e93e32d00e56ed98f4962ad89eda5a
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626323"
---
# <a name="troubleshooting-azure-data-factory"></a>Řešení potíží s Azure Data Factory
Tento článek uvádí běžné otázky k řešení problémů.

- [Azure Databricks (Poznámkový blok, kromě souborů JAR, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [Vlastní (služba Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, Hadoop Streaming)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Webová aktivita](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Kód chyby | Chybová zpráva                                          | Popis problému                             | Je to možné opravy / Doporučená akce                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Chyba 403                                                    | Databricks přístupového tokenu vypršela.                         | Ve výchozím nastavení Databricks přístupový token je platný po dobu 90 dnů.  Vytvořit nový token a aktualizovat propojené služby. |
| 3201           | Chybí povinné pole: settings.task.notebook_task.notebook_path | Chybný vytváření: Cestu k poznámkovému bloku není zadán správně. | Zadejte prosím cestu k poznámkovému bloku v Databricks aktivity. |
| 3201           | Cluster... neexistuje                                 | Vytváření chyb: Databricks pro cluster neexistuje nebo byl odstraněn. | Ověřte prosím, že Databricks pro cluster existuje. |
| 3201           | Identifikátor URI souboru neplatný python:... Navštivte prosím uživatelská příručka k Databricks pro podporované schémata identifikátoru URI. | Chybný pro tvorbu                                                | Zadejte absolutní cesty pro režimy adresování pracovní prostor, nebo "dbfs:/folder/subfolder/foo.py" DBFS uložených souborů. |
| 3201           | {0}   Propojené služby by měly mít domény a accessToken jako požadované vlastnosti | Chybný pro tvorbu                                                | Zkontrolujte, zda [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   Propojené služby by měl určit existující cluster Id nebo pro vytvoření nové informace o clusteru | Chybný pro tvorbu                                                | Zkontrolujte, zda [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Typ uzlu Standard_D16S_v3 není podporován. Podporované typy uzlů:   Standard_DS3_v2 Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2 Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, standard_ds15_v2 urychlení sítě, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s Standard_F8s, Standard_F16s, typu Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3 Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2 | Chybný pro tvorbu                                                | Přečtěte si chybovou zprávu                                          |
| 3201           | Neplatný notebook_path:... Aktuálně jsou podporovány pouze absolutní cesty. Cesty musí začínat lomítkem (/). | Chybný pro tvorbu                                                | Přečtěte si chybovou zprávu                                          |
| 3202           | Již byly k 1 000 úloh vytvořených v posledních 3 600 sekund, překročení limitu přenosové rychlosti:   1 000 úloh vytvoření za 3 600 sekund. | Spouští se příliš mnoho Databricks za hodinu.                         | Zkontrolujte všechny kanály, které používají tento pracovní prostor Databricks pro kurz vytvoření úlohy.  Pokud kanály spustili příliš mnoho spuštění Databricks v agregaci, migrate na nový pracovní prostor některé kanály. |
| 3202           | Nepovedlo se analyzovat objekt žádosti: Očekávaný 'klíč' a 'value' nastavit pro base_parameters mapování pole JSON, je teď "klíč:"...". Místo toho. | Vytváření chyb: Žádná hodnota zadaná pro parametr         | Kontrola kódu json kanálu a ujistěte se, že všechny parametry v poznámkovém bloku baseParameters mít neprázdnou hodnotu zadat. |
| 3202           | Uživatel: SimpleUserContext{userId=..., name=user@company.com, orgId=…} nemá oprávnění pro přístup ke clusteru | Uživatel, který vygeneroval přístupový token není povolen přístup ke clusteru Databricks zadaná v propojené službě. | Ujistěte se, že uživatel má požadovaná oprávnění v pracovním prostoru.   |
| 3203           | Cluster je ve stavu ukončeno, není k dispozici pro příjem úlohy. Odstranit cluster nebo opakujte akci později. | Cluster se ukončilo.    Pro interaktivní cluster může to být časování. | Nejlepší způsob, jak tomu předešli, je použití úlohy clustery.             |
| 3204           | Provádění úlohy se nezdařilo. Může existovat libovolný počet chybových zpráv ze stavu neočekávané clusteru na zprávy specifické pro aktivitu.  Nejčastějším vůbec není žádná chybová zpráva. | Není k dispozici                                                          | Není k dispozici                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| Kód chyby:         | Chybová zpráva                                                | Popis problému                                          | Je to možné opravy / Doporučená akce                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Přístupový token pochází od nesprávného tenanta                    | Nesprávný tenanta AAD                                         | Instanční objekt služby pro přístup k ADLA patří do jiného tenanta AAD. Vytvořte prosím nový instanční objekt ve stejném tenantovi jako i účet ADLA. |
| 2711,   2705,   2704 | Je zakázané. Seznam ACL ověření se nezdařilo. Prostředek neexistuje nebo uživatel nemá oprávnění provádět požadovanou operaci<br/><br/>Uživatel není možné přistupovat k datalake store  <br/><br/>Uživatel nemá oprávnění ke službě data lake analytics | Instanční objekt nebo poskytnutý certifikát nemá přístup k souboru v úložišti | Zkontrolujte prosím, že instanční objekt služby nebo certifikát, který poskytuje pro úlohy v ADLA má přístup k účtu ADLA i výchozí úložiště ADLS, od kořenové složky. |
| 2711                 | 'Azure Data Lake Store' soubor nebo složku nelze nalézt       | Cesta k souboru USQL je chybná nebo propojené služby, přihlašovací údaje nemáte přístup | Ověřte prosím cestu a přihlašovací údaje zadané v propojené službě |
| 2707                 | Nelze vyřešit účet AzureDataLakeAnalytics. Zkontrolujte prosím "AccountName" a "DataLakeAnalyticsUri". | V propojené službě i účet ADLA je nesprávný                  | Ověřte prosím, že se zadal správný účet             |
| 2703                 | Id chyby: E_CQO_SYSTEM_INTERNAL_ERROR nebo všechny chyby začíná "Id chyby:" | Chyba pochází ze služby ADLA                                    | Všechny chyby, který bude vypadat jako v příkladu znamená, že úloha byla odeslána ADLA a existuje skriptu se nezdařilo. Šetření je nutné provést v ADLA. Pokud otevřete portál a přejděte do účtu ADLA, hledat pomocí ADF aktivity Id (ne Id spuštění kanálu) spuštění úlohy. Úloha existuje bude mít další informace o této chybě a pomůže vám to řešení potíží. Pokud řešení není jasné, prosím požádejte tým podpory ADLA a zadejte adresu URL úlohy, který obsahuje název účtu a ID úlohy. |
| 2709                 | V tuto chvíli jsme nemůže přijmout vaší úlohy. Maximální počet úloh zařazených do fronty pro váš účet je 200. | Omezení šířky pásma na ADLA                                           | Snižte počet odeslaných úloh na ADLA změnou ADF aktivačních událostí a nastavení souběžnosti v aktivity nebo navýšení limitů na ADLA |
| 2709                 | Tato úloha byla odmítnuta, protože vyžaduje 24 jednotek analýzy a tento účet má zásady definované správce, který brání úlohu pomocí více než 5 au. | Omezení šířky pásma na ADLA                                           | Snižte počet odeslaných úloh na ADLA změnou ADF aktivačních událostí a nastavení souběžnosti v aktivity nebo navýšení limitů na ADLA |



## <a name="azure-functions"></a>Azure Functions

| Kód chyby: | Chybová zpráva                           | Popis                                                  | Je to možné opravy / Doporučená akce                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Obsah odpovědi není platný JObject | To znamená, že funkce Azure Functions se volalo v odpovědi nevrátil datovou část JSON. Aktivita funkce ADF Azure podporuje pouze obsah odpovědi JSON. | Aktualizovat funkci Azure, třeba vrátit platný datovou část JSON C# funkce může vrátit (ActionResult) nový < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | Neplatná vlastnost HttpMethod: '..'.               | To znamená, že metoda Http zadaná v datové části aktivita nepodporuje aktivita funkce Azure. | Metody Http, které jsou podporovány jsou:  <br/>PUT, POST, ZÍSKÁVÁNÍ, ODSTRAŇOVÁNÍ, OPTIONS, HEAD, TRASOVÁNÍ |



## <a name="custom-azure-batch"></a>Vlastní (služba Azure Batch)
| Kód chyby: | Chybová zpráva                                                | Popis                                                  | Je to možné opravy / Doporučená akce                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Spuštění došlo k neočekávané výjimce a provádění se nezdařilo.             | Nejde spustit příkaz nebo program vrátil chybový kód. | Zkontrolujte, jestli existuje spustitelný soubor. Je-li program spuštěn, zkontrolujte stdout.txt a stderr.txt nahráli do účtu úložiště. Je vhodné vysílala protokoly velkým ve vašem kódu pro ladění. |
| 2501         | Nedaří se uživatelský účet batch, Zkontrolujte prosím nastavení účtu batch. | Nesprávný Batch přístup klíč nebo fond je zadaný název.            | Třeba ověřit název fondu a přístupový klíč služby Batch v propojené službě. |
| 2502         | Můžete není přístup uživatele účtu úložiště, prosím zkontrolujte nastavení účtu úložiště. | Nesprávný klíč účtu úložiště název nebo přístup k dispozici.       | Třeba ověřit klíč název a přístup k účtu úložiště v propojené službě. |
| 2504         | Operace vrátil neplatný stavový kód "chybného požadavku.     | Příliš mnoho souborů v folderPath Pokud vlastní aktivity.  (Celková velikost resourceFiles nemůže být více než 32768 znaků.) | Odeberte nepotřebné soubory, nebo je zip a přidat unzip příkaz k extrakci, například: powershell.exe - nologo - noprofile-příkaz "& {Add-Type - A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Nelze vytvořit sdílený přístupový podpis, pokud se používají přihlašovací údaje klíč účtu. | Vlastní aktivity se podporují jenom účty úložiště, které používají přístupový klíč. | Přečtěte si popis                                            |
| 2507         | Cesta ke složce neexistuje nebo je prázdný:...            | Žádné soubory v účtu úložiště v zadané cestě.       | V cestě folderPath musí obsahovat spustitelné soubory, které chcete spustit. |
| 2508         | Existují duplicitní soubory ve složce resource.               | Existuje více souborů se stejným názvem v různých podsložky folderPath. | Vlastní aktivity sloučit strukturu složek v rámci folderPath.  Pokud struktura složek musí být zachována, soubory zip a rozbalte je na Azure Batch příkazem unzip, například: powershell.exe - nologo - noprofile-příkaz "& {Add-Type - A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | Batch adresu url... je neplatný, musí být ve formátu Uri.         | Adresy URL služby batch musí být podobně jako na protokol https:\//mybatchaccount.eastus.batch.azure.com | Přečtěte si popis                                            |
| 2510         | Při odesílání požadavku došlo k chybě.               | Adresa URL služby batch je neplatná                                         | Ověřte adresu URL služby batch.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, Hadoop Streaming)

| Kód chyby: | Chybová zpráva                                                | Popis                                                  | Je to možné opravy / Doporučená akce                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Odeslání úlohy Hadoop se nezdařilo. Chyba: Vzdálený název nelze rozpoznat. <br/><br/>Cluster se nenašel. | Zadaný identifikátor URI clusteru není platný                              | Zkontrolujte, zda nebyla odstraněna clusteru a je správně zadaný identifikátor URI. Identifikátor URI můžete otevřít v jakémkoli prohlížeči a měla by se zobrazit uživatelské rozhraní Ambari. Pokud se cluster nachází ve virtuální síti, pak identifikátoru URI by měl být privátní identifikátor URI a snažte se ji otevřít z virtuálního počítače, který je součástí stejné virtuální síti. Další informace o [virtuální sítě v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Odeslání úlohy Hadoop se nezdařilo. Job: …, Cluster: …/. Chyba: Úloha byla zrušena. | Vypršel časový limit odeslání úlohy.                         | To může být obecné potíže s připojením HDInsight nebo problém síťového připojení. Zkontrolujte, že uživatelské rozhraní Ambari HDInsight je dostupná prostřednictvím libovolného prohlížeče a vaše přihlašovací údaje jsou pořád platné. Ujistěte se, že k tomu z virtuálního počítače nebo počítače, kde je nainstalovaný místní prostředí IR, pokud používáte v místním prostředí IR. Zkuste to znovu odeslání úlohy z ADF. Pokud se přesto nezdaří, požádejte o podporu týmu ADF. |
| 2300         | Neautorizováno:   Ambari uživatelské jméno nebo heslo není správné  <br/><br/>Neautorizováno:   Správce uživatelů je uzamčen v Ambari   <br/><br/>403 – Zakázáno: Přístup byl odepřen | Poskytnuté přihlašovací údaje pro HDInsight, nejsou správné nebo jejichž platnost vypršela | Opravte je a znovu nasaďte propojenou službu. Ujistěte se, že přihlašovací údaje pracovat na HDInsight nejprve otevřením URI clusteru v jakémkoli prohlížeči a zkusíte se přihlásit. Pokud nefungují, můžete je resetovat z portálu Azure Portal. |
| 2300,   2310 | 502 – Webový server obdržel neplatnou odpověď v době, kdy fungoval jako brána nebo proxy server       <br/>Chybná brána | Chyba pochází z HDInsight                               | Tato chyba pochází z clusteru HDInsight. Přečtěte si [Poradce při potížích s HDInsight](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) s běžnými chybami.    <br/>Pro clustery Spark je příčinou může být také způsobena [to](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Dodatečný odkaz](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | Odeslání úlohy Hadoop se nezdařilo. Úloha:..., clusteru:... Chyba: {\"chyba\":\"nelze zpracovat žádost o úlohu Odeslat jako templeton služba je zaneprázdněná s příliš mnoho žádostí o odeslání úlohy. Počkejte prosím chvíli, než se budete pokoušet operaci zopakovat. Najdete templeton.parallellism.job.submit config ke konfiguraci souběžných požadavků. \  <br/><br/>Odeslání úlohy Hadoop se nezdařilo. Úlohy: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, clusteru: https: \/ /abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Chyba: {\"chyba\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException: Nepovedlo se odeslat application_1561147195099_3730 do YARN: org.apache.hadoop.security.AccessControlException: Fronty root.joblauncher již má 500 aplikace, nemůže přijmout podání žádosti o: application_1561147195099_3730\ | Příliš mnoho úloh se odesílají na HDInsight ve stejnou dobu | Vezměte v úvahu omezíte počet souběžných úloh HDI odeslání. Najdete ADF aktivity souběžnost Pokud jsou právě odeslal do stejné aktivity. Aktivační události změňte, takže spuštění souběžných kanálu jsou umístěny v čase. Aby bylo možné upravit "templeton.parallellism.job.submit" jak navrhuje chybová také najdete na webu docs HDInsight. |
| 2303,   2347 | Úloha Hadoop selhal s ukončovacím kódem "5". V tématu "wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' Další podrobnosti.  <br/><br/>Spuštění selhalo s kódem chyby "UserErrorHiveOdbcCommandExecutionFailure" Hive.   V tématu "wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' Další podrobnosti | Úloha byla odeslána na HDInsight a jeho selhání v HDInsight | Úloha byla odeslána do HDInsight úspěšně. Selhání v clusteru. Prosím buď otevřete úlohu na uživatelské rozhraní Ambari HDInsight a otevřete protokoly existuje nebo otevřete soubor z úložiště jako body zpráva Chyba navýšení kapacity. Podrobnosti o chybě bude v tomto souboru. |
| 2328         | Při zpracování požadavku došlo k vnitřní chybě serveru. Prosím zkuste požadavek zopakovat, nebo se obraťte na podporu | Probíhá na HDInsight na vyžádání.                              | Tato chyba pochází ze služby HDInsight při zřizování HDInsight se nezdaří. Obraťte se na tým HDInsight a uveďte název clusteru na vyžádání. |
| 2310         | java.lang.NullPointerException                               | Došlo k chybě při odesílání úlohy ke clusteru Spark      | Tato výjimka pochází z HDInsight a je skrytí skutečného problému.   Obraťte se na tým HDInsight pro podporu a zadejte název clusteru a spuštění časový rozsah aktivity. |
|              | Všechny ostatní chyby                                             |                                                              | Najdete [Poradce při potížích s HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) a [HDInsight – nejčastější dotazy](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Aktivita webu

| Kód chyby: | Chybová zpráva                                                | Popis                                                  | Je to možné opravy / Doporučená akce                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Neplatná vlastnost HttpMethod: '..'.                                    | To znamená, že metoda Http zadaná v datové části aktivita nepodporuje aktivitu webu. | Metody Http, které jsou podporovány jsou: <br/>PUT, POST, ZÍSKÁVÁNÍ, ODSTRAŇOVÁNÍ |
| 2108         | Neplatný Server chyba 500                                     | Vnitřní chyba v koncovém bodě                               | Zkontrolujte funkčnost na adresu URL (pomocí nástroje Fiddler nebo Postman): [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Unauthorized 401                                             | Chybí platný ověřování na požádání                      | Zadejte platný ověřovací metodu (token možná vypršela platnost).   <br/><br/>Zkontrolujte funkčnost na adresu URL (pomocí nástroje Fiddler nebo Postman): [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Forbidden 403                                                | Chybí požadovaná oprávnění                                 | Zkontrolujte oprávnění uživatele na využívaných prostředků.   <br/><br/>Zkontrolujte funkčnost na adresu URL (pomocí nástroje Fiddler nebo Postman): [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Chybný požadavek 400                                              | Neplatný požadavek Http                                         | Zkontrolujte adresu URL, operace a text žádosti.   <br/><br/>Použití aplikace Fiddler nebo Postman pro ověření žádosti: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 Nenalezeno                                                | Prostředek se nenašel.                                       | Použití aplikace Fiddler nebo Postman pro ověření žádosti: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Služba není k dispozici                                          | Služba je neaktivní                                       | Použití aplikace Fiddler nebo Postman pro ověření žádosti: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Nepodporovaný typ média                                       | Neodpovídající Content-Type s tělem webové aktivity           | Zadejte správný typ obsahu, který odpovídá formátu datové části Použití aplikace Fiddler nebo Postman pro ověření žádosti: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Prostředek, který chcete pro byl odebrán, přejmenován nebo je dočasně nedostupná. | Prostředek není k dispozici                                | Pomocí nástroje Fiddler nebo Postman zkontrolujte koncový bod: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Stránka, kterou jste hledali nelze zobrazit, protože se používá neplatné metody (příkazu HTTP). | V požadavku byl zadán nesprávný metodu webové aktivity   | Pomocí nástroje Fiddler nebo Postman zkontrolujte koncový bod: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Text pro aktivitu webu je nesprávná                       | Pomocí nástroje Fiddler nebo Postman zkontrolujte koncový bod: [Použití aplikace Fiddler k vytvoření relace HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Použití aplikace Fiddler k vytvoření relace HTTP monitorované webové aplikace

1. Stáhněte a nainstalujte [Fiddleru](https://www.telerik.com/download/fiddler)

2. Pokud vaše webová aplikace používá protokol HTTPS:

   1. Otevřete Fiddler

   2. Přejděte na **nástroje > Možnosti Fiddleru** a vyberte jako v následující snímek obrazovky. 

      ![fiddler-options](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Pokud vaše aplikace používá certifikáty SSL, musíte také přidat certifikát Fiddler k zařízení.

4. Přidání certifikátu aplikace Fiddler k zařízení, přejděte na **nástroje** > **možnosti Fiddleru** > **HTTPS**  >   **Akce** > **Export kořenového certifikátu do Desktopu** k získání certifikátu Fiddleru.

5. Vypněte zachytávání tak, aby mezipaměť prohlížeče můžete vymazat zahájíte nové trasování.

6. 1. Přejděte na **souboru** > **zachycování provozu** nebo stiskněte klávesu **F12**.
   2. Vymažte mezipaměť prohlížeče tak, aby všechny položky v mezipaměti se odeberou a musí být znovu stáhnou.

7. Vytvořte žádost o: 

8. 1. Klikněte na kartu Composer
   2. Nastavte metodu Http a adresy URL
   3. Přidat záhlaví a text žádosti v případě potřeby
   4. Klepněte na tlačítko Spustit

9. Spustit zachytávání provozu znovu a dokončení problematické transakce na stránce.

10. Po dokončení, přejděte na **souboru** > **Uložit** > **všechny relace**.

Další informace o aplikaci Fiddler [zde](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>Další postup

Pro další pomoc při hledání řešení problému tady jsou některé další prostředky, můžete to zkusit.

*  [Blogy](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o funkce](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



