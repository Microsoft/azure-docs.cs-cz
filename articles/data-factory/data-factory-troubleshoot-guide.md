---
title: Řešení potíží s Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak řešit potíže s externími ovládacími prvky v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 1995ce2a91bfbc115f80c99687cc84b52ef614ec
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950098"
---
# <a name="troubleshoot-azure-data-factory"></a>Řešení potíží s Azure Data Factory

Tento článek popisuje běžné metody řešení potíží pro aktivity externích řízení v Azure Data Factory.

## <a name="azure-databricks"></a>Azure Databricks

| Kód chyby | Chybová zpráva                                          | Popis                             | Doporučení                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Chyba 403.                                                    | Platnost přístupového tokenu datacihly vypršela.                         | Ve výchozím nastavení je token přístupu datacihly platný po dobu 90 dnů.  Vytvořte nový token a aktualizujte propojenou službu. |
| 3201           | Chybí požadované pole: Settings. Task. notebook_task. notebook_path | Špatný vývoj: Cesta k poznámkovému bloku není správně zadaná. | Zadejte cestu k poznámkovému bloku v aktivitě datacihly. |
| 3201           | Cluster... neexistuje.                                 | Chyba při vytváření: Cluster datacihlů neexistuje nebo byl odstraněn. | Ověřte, zda existuje cluster datacihly. |
| 3201           | Neplatný identifikátor URI souboru Pythonu... Pro podporovaná schémata identifikátoru URI prosím navštivte uživatelskou příručku pro datacihly. | Chybný vývoj.                                                | Zadejte buď absolutní cesty pro schémata adresování pracovních prostorů `dbfs:/folder/subfolder/foo.py` , nebo soubory uložené v systému souborů datacihly. |
| 3201           | {0}LinkedService musí mít jako požadované vlastnosti Domain a accessToken. | Chybný vývoj.                                                | Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}LinkedService by měl pro vytvoření zadat buď existující ID clusteru, nebo informace o novém clusteru. | Chybný vývoj.                                                | Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Typ uzlu Standard_D16S_v3 se nepodporuje. Podporované typy uzlů:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Chybný vývoj.                                                | Přečtěte si chybovou zprávu.                                          |
| 3201           | Neplatné notebook_path:... V současné době jsou podporovány pouze absolutní cesty. Cesta musí začínat znakem "/". | Chybný vývoj.                                                | Viz chybová zpráva.                                          |
| 3202           | V posledních 3600 sekundách již byly vytvořeny 1000 úloh, překročení limitu četnosti:   1000 vytvoření úlohy za 3600 sekund. | Příliš mnoho datacihlů běží za hodinu.                         | Ověřte všechny kanály, které používají tento pracovní prostor datacihly pro svou rychlost vytváření úloh.  Pokud kanály spouštějí příliš mnoho datacihlů, migrují se některé kanály do nového pracovního prostoru. |
| 3202           | Nepovedlo se analyzovat objekt žádosti: Očekávalo se klíčové slovo Key a Value, které se má nastavit pro pole mapy JSON base_parameters, přijatý klíč:... takové. | Chyba při vytváření: Pro parametr se nezadala žádná hodnota.         | Zkontrolujte kód JSON kanálu a ujistěte se, že všechny parametry v poznámkovém bloku baseParameters určují neprázdnou hodnotu. |
| 3202           | Uživatel: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` nemá oprávnění k přístupu ke clusteru. | Uživatel, který vygeneroval přístupový token, nemá oprávnění k přístupu ke clusteru datacihlům, který je zadaný v propojené službě. | Ujistěte se, že uživatel má v pracovním prostoru požadovaná oprávnění.   |
| 3203           | Cluster je v ukončeném stavu, není k dispozici pro příjem úloh. Opravte prosím cluster nebo to zkuste znovu později. | Cluster byl ukončen.    U interaktivních clusterů to může být spor. | Nejlepším způsobem, jak se této chybě vyhnout, je použití clusterů úloh.             |
| 3204           | Provedení úlohy se nezdařilo.  | Chybové zprávy indikují různé problémy, jako je neočekávaný stav clusteru nebo konkrétní aktivita. Nezobrazuje se vám většinou žádná chybová zpráva.                                                          | Není k dispozici                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Následující tabulka platí pro U-SQL.

| Kód chyby         | Chybová zpráva                                                | Popis                                          | Doporučení                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Přístupový token je od nesprávného tenanta.                    | Nesprávný tenant Azure Active Directory (Azure AD).                                         | Instanční objekt používaný pro přístup k Azure Data Lake Analytics patří jinému tenantovi služby Azure AD. Vytvořte nový instanční objekt ve stejném tenantovi jako účet Data Lake Analytics. |
| 2711,   2705,   2704 | Zakázán. Ověření seznamu ACL selhalo. Buď prostředek neexistuje, nebo uživatel nemá oprávnění k provedení požadované operace.<br/><br/>Uživatel nemůže získat přístup k Data Lake Store.  <br/><br/>Uživatel nemá oprávnění k použití Data Lake Analytics. | Objekt služby nebo certifikát nemá přístup k souboru v úložišti. | Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky. |
| 2711                 | Nepovedlo se najít soubor nebo složku Azure Data Lake Store.       | Cesta k souboru U-SQL je chybná nebo přihlašovací údaje propojené služby nemají přístup. | Ověřte cestu a přihlašovací údaje, které jsou k dispozici v propojené službě. |
| 2707                 | Nelze přeložit účet AzureDataLakeAnalytics. Zkontrolujte prosím ' Account ' a ' DataLakeAnalyticsUri '. | Účet Data Lake Analytics v propojené službě je nesprávný.                  | Ověřte, jestli je zadaný správný účet.             |
| 2703                 | ID chyby: E_CQO_SYSTEM_INTERNAL_ERROR (nebo jakákoli chyba začínající číslem ID chyby:). | Tato chyba je z Data Lake Analytics.                                    | Chyba jako v příkladu znamená, že úloha byla odeslána do Data Lake Analytics a skript se nezdařil. Prozkoumejte v Data Lake Analytics. Na portálu přejdete na účet Data Lake Analytics a vyhledáte úlohu pomocí ID spuštění Data Factory aktivity (ne ID spuštění kanálu). Úloha obsahuje další informace o této chybě a pomůže vám při odstraňování potíží. Pokud řešení není jasné, obraťte se na tým podpory Data Lake Analytics a zadejte adresu URL úlohy, která zahrnuje název vašeho účtu a ID úlohy. |
| 2709                 | V tuto chvíli nemůžeme vaši úlohu přijmout. Maximální počet úloh zařazených do fronty pro váš účet je 200. | Tato chyba je způsobená omezováním Data Lake Analytics.                                           | Snižte počet odeslaných úloh, které se mají Data Lake Analytics změnou Data Factory triggerů a nastavení souběžnosti u aktivit. Nebo zvyšte limity Data Lake Analytics. |
| 2709                 | Tato úloha byla odmítnuta, protože vyžaduje 24 jednotek Austrálie. Zásada definovaná správcem tohoto účtu brání úloze v používání více než 5 jednotek Austrálie. | Tato chyba je způsobená omezováním Data Lake Analytics.                                           | Snižte počet odeslaných úloh, které se mají Data Lake Analytics změnou Data Factory triggerů a nastavení souběžnosti u aktivit. Nebo zvyšte limity Data Lake Analytics. |



## <a name="azure-functions"></a>Azure Functions

| Kód chyby | Chybová zpráva                           | Popis                                                  | Doporučení                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Obsah odpovědi není platný JObject. | Funkce Azure, která byla volána, nevrátila v odpovědi datovou část JSON. Aktivita funkce Azure v Data Factory podporuje jenom obsah odpovědi JSON. | Aktualizujte funkci Azure functions a vraťte platnou datovou část JSON. C# `(ActionResult)new<OkObjectResult("{`Funkce může například vracet \"ID\":123\".\"`}");` |
| 3600         | Neplatný HttpMethod:...               | Metoda HTTP zadaná v datové části aktivity není podporována aktivitou funkce Azure Functions. | Použijte podporovanou metodu HTTP, například PUT, POST, GET, DELETE, OPTIONS, HEAD nebo TRACE. |



## <a name="custom"></a>Vlastní

Následující tabulka se vztahuje na Azure Batch.

| Kód chyby | Chybová zpráva                                                | Popis                                                  | Doporučení                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Došlo k neočekávané výjimce a spuštění selhalo.             | Příkaz nelze spustit, nebo program vrátil kód chyby. | Ujistěte se, že spustitelný soubor existuje. Pokud se program spustil, ujistěte se, že se do účtu úložiště nahrály *STDOUT. txt* a *stderr. txt* . Je dobrým zvykem generovat protokoly Copious v kódu pro ladění. |
| 2501         | Nejde získat přístup k účtu Batch uživatele; Zkontrolujte prosím nastavení účtu Batch. | Nesprávný klíč pro přístup k dávce nebo název fondu.            | Ověřte název fondu a přístupový klíč Batch v propojené službě. |
| 2502         | Nejde získat přístup k účtu úložiště uživatele; Zkontrolujte prosím nastavení účtu úložiště. | Nesprávný název účtu úložiště nebo přístupový klíč.       | Ověřte název účtu úložiště a přístupový klíč v propojené službě. |
| 2504         | Operace vrátila neplatný stavový kód důvodu chybného požadavku.     | Příliš mnoho souborů v folderPath vlastní aktivity. Celková velikost resourceFiles nemůže být delší než 32 768 znaků. | Odstraňte nepotřebné soubory. Nebo je zip a přidejte příkaz k extrakci, který je rozbalí. Například použijte`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | Sdílený přístupový podpis nejde vytvořit, pokud se nepoužívají přihlašovací údaje klíče účtu. | Vlastní aktivity podporují jenom účty úložiště, které používají přístupový klíč. | Přečtěte si popis chyby.                                            |
| 2507         | Cesta ke složce neexistuje nebo je prázdná:...            | V zadané cestě nejsou v účtu úložiště žádné soubory.       | Cesta ke složce musí obsahovat spustitelné soubory, které chcete spustit. |
| 2508         | Ve složce prostředků jsou duplicitní soubory.               | Více souborů se stejným názvem jsou v různých podsložkách folderPath. | Vlastní aktivity sloučí strukturu složek pod folderPath.  Pokud potřebujete zachovat strukturu složek, soubory zip a extrahujte je v Azure Batch pomocí příkazu Rozbalit. Například použijte`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | Adresa URL dávky... je neplatné; musí být ve formátu identifikátoru URI.         | Adresy URL dávky musí být podobné`https://mybatchaccount.eastus.batch.azure.com` | Přečtěte si popis chyby.                                            |
| 2510         | Při odesílání žádosti došlo k chybě.               | Adresa URL dávky není platná.                                         | Ověřte adresu URL dávky.                                            |

## <a name="hdinsight"></a>HDInsight

Následující tabulka se vztahuje na datové proudy Spark, podregistr, MapReduce, prasete a Hadoop.

| Kód chyby | Chybová zpráva                                                | Popis                                                  | Doporučení                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Odeslání úlohy Hadoop se nezdařilo. Chyba: Vzdálený název se nedá rozpoznat. <br/><br/>Cluster se nenašel. | Poskytnutý identifikátor URI clusteru je neplatný.                              | Ujistěte se, že se cluster neodstranil a že je zadaný identifikátor URI správný. Po otevření identifikátoru URI v prohlížeči by se mělo zobrazit uživatelské rozhraní Ambari. Pokud je cluster ve virtuální síti, identifikátor URI by měl být privátním identifikátorem URI. Pokud ho chcete otevřít, použijte virtuální počítač, který je součástí stejné virtuální sítě. Další informace najdete v tématu [přímé připojení k Apache Hadoop službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Odeslání úlohy Hadoop se nezdařilo. Úloha:..., cluster:.../. Chyba: Úloha byla zrušena. | Vypršel časový limit pro odeslání úlohy.                         | Problémem může být buď obecné připojení HDInsight, nebo připojení k síti. Nejdřív potvrďte, že je uživatelské rozhraní HDInsight Ambari dostupné z libovolného prohlížeče. Potvrďte, že vaše přihlašovací údaje jsou pořád platné. Pokud používáte integrovaný modul runtime (IR) s místním hostováním, ujistěte se, že provedete tento postup z virtuálního počítače nebo počítače, ve kterém je nainstalováno prostředí IR pro místní hostování. Pak zkuste úlohu odeslat z Data Factory znovu. Pokud se to nepodaří, obraťte se na tým Data Factory pro podporu. |
| 2300         | Přístupu   Uživatelské jméno nebo heslo Ambari není správné.  <br/><br/>Přístupu   Správce uživatele je v Ambari uzamčený.   <br/><br/>403 – zakázáno: Přístup byl zamítnut. | Přihlašovací údaje pro HDInsight jsou nesprávné nebo vypršela jejich platnost. | Opravte přihlašovací údaje a znovu nasaďte propojenou službu. Nejdřív ověřte, že přihlašovací údaje fungují v HDInsight tím, že otevřete identifikátor URI clusteru v jakémkoli prohlížeči a zkusíte se přihlásit. Pokud přihlašovací údaje nefungují, můžete je resetovat z Azure Portal. |
| 2300,   2310 | 502 – Webový server obdržel neplatnou odpověď v době, kdy fungoval jako brána nebo proxy server.       <br/>Chybná brána. | Tato chyba je ze služby HDInsight.                               | Tato chyba je z clusteru HDInsight. Další informace najdete v tématu [Chyba Ambari uživatelského rozhraní 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 chyby připojení k serveru spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 chyby při připojování k serveru Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)a řešení chyb chybná [Brána v Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2300         | Odeslání úlohy Hadoop se nezdařilo. Úloha:..., cluster:... Chyba: {\"Error\":\"nelze obsluhovat žádost o odeslání úlohy, protože služba Templeton je zaneprázdněna s příliš velkým počtem žádostí o odeslání úlohy. Než zkusíte operaci zopakovat, počkejte prosím nějakou dobu. Pokud chcete nakonfigurovat souběžné požadavky, přečtěte si prosím konfigurační Templeton. parallellism. job. Submit.  <br/><br/>Odeslání úlohy Hadoop se nezdařilo. Úloha: 161da5d4-6fa8-4ef4-A240-6b6428c5ae2f, cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Chyba: {\"Error\":\"Java. IO. IOException: org. Apache. Hadoop. příze. Exceptions. YarnException: Odeslání application_1561147195099_3730 do PŘÍZe: org. Apache. Hadoop. Security. AccessControlException se nezdařilo: Kořenová složka Queue. joblauncher už má 500 aplikací, nemůže přijmout odeslání aplikace: application_1561147195099_3730 \. | Současně probíhá odeslání příliš velkého počtu úloh do HDInsight. | Zvažte omezení počtu souběžných úloh odeslaných do HDInsight. Pokud jsou úlohy odesílány stejnou aktivitou, přečtěte si téma Data Factory souběžnosti aktivity. Změna aktivačních událostí, aby souběžné běhy byly rozloženy v průběhu času. Informace o tom, `templeton.parallellism.job.submit` jak Chyba naznačuje, najdete v dokumentaci k HDInsight. |
| 2303,   2347 | Úloha Hadoop selhala s ukončovacím kódem "5". wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderrDalší podrobnosti najdete v tématu.  <br/><br/>Spuštění podregistru se nezdařilo s kódem chyby ' UserErrorHiveOdbcCommandExecutionFailure '.   wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.outDalší podrobnosti najdete v tématu. | Úloha byla odeslána do HDInsight a nezdařila se v HDInsight. | Úloha se úspěšně odeslala do HDInsight. Nepovedlo se v clusteru. Buď otevřete úlohu a protokoly v uživatelském rozhraní HDInsight Ambari, nebo soubor otevřete z úložiště, jak naznačuje chybová zpráva. V souboru se zobrazí podrobnosti o chybě. |
| 2328         | Při zpracování požadavku došlo k vnitřní chybě serveru. Opakujte prosím požadavek nebo kontaktujte podporu. | K této chybě dochází v HDInsight na vyžádání.                              | K této chybě dojde ze služby HDInsight, když se zřizování HDInsight nepovede. Kontaktujte tým HDInsight a zadejte název clusteru na vyžádání. |
| 2310         | java.lang.NullPointerException                               | K této chybě dojde, když se úloha odešle do clusteru Spark.      | Tato výjimka pochází ze služby HDInsight. Skrývá skutečný problém. Požádejte o podporu tým HDInsight. Zadejte název clusteru a rozsah doby běhu aktivity. |
|              | Všechny ostatní chyby                                             |                                                              | Přečtěte si téma [řešení potíží pomocí HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) a [HDInsight – Nejčastější dotazy](https://hdinsight.github.io/). |



## <a name="web-activity"></a>Aktivita webu

| Kód chyby | Chybová zpráva                                                | Popis                                                  | Doporučení                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Neplatný HttpMethod:...                                    | Aktivita webu nepodporuje metodu HTTP zadanou v datové části aktivity. | Podporované metody HTTP jsou PUT, POST, GET a DELETE. |
| 2108         | Neplatná chyba serveru 500.                                     | Došlo k vnitřní chybě koncového bodu.                               | Použijte Fiddler nebo post ke kontrole funkčnosti na adrese URL. |
| 2108         | Neautorizováno 401.                                             | V požadavku chybí platné ověření.                      | Je možné, že vypršela platnost tokenu. Zadejte platnou metodu ověřování. Použijte Fiddler nebo post ke kontrole funkčnosti na adrese URL. |
| 2108         | Zakázáno 403.                                                | Chybí požadovaná oprávnění.                                 | Ověřte oprávnění uživatelů pro přístup k prostředku. Použijte Fiddler nebo post ke kontrole funkčnosti na adrese URL.  |
| 2108         | Chybný požadavek 400.                                              | Neplatný požadavek HTTP                                         | Ověřte adresu URL, sloveso a text požadavku. K ověření žádosti použijte Fiddler nebo post.  |
| 2108         | Nenalezeno 404.                                                | Prostředek se nenašel.                                       | K ověření žádosti použijte Fiddler nebo post.  |
| 2108         | Služba není dostupná.                                          | Služba není k dispozici.                                       | K ověření žádosti použijte Fiddler nebo post.  |
| 2108         | Nepodporovaný typ média.                                       | Typ obsahu se neshoduje s tělem webové aktivity.           | Zadejte typ obsahu, který odpovídá formátu datové části. K ověření žádosti použijte Fiddler nebo post. |
| 2108         | Prostředek, který hledáte, byl odebrán, došlo ke změně jeho názvu nebo je dočasně nedostupný. | Prostředek není k dispozici.                                | Ke kontrole koncového bodu použijte Fiddler nebo post. |
| 2108         | Stránku, kterou hledáte, nelze zobrazit, protože je použita neplatná metoda (příkaz HTTP). | V požadavku byla zadána nesprávná metoda webové aktivity.   | Ke kontrole koncového bodu použijte Fiddler nebo post. |
| 2108         | invalid_payload                                              | Tělo aktivity webu je nesprávné.                       | Ke kontrole koncového bodu použijte Fiddler nebo post. |

Použití Fiddler k vytvoření relace HTTP monitorované webové aplikace:

1. Stáhněte, nainstalujte a otevřete [Fiddler](https://www.telerik.com/download/fiddler).

1. Pokud vaše webová aplikace používá protokol HTTPS, použijte možnost **nástroje** > **Fiddler možnosti** > **https**. Vyberte **zachytit protokol HTTPS připojení** a **dešifrování přenosu HTTPS**. 
   
   ![Fiddler možnosti](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Pokud vaše aplikace používá certifikáty SSL, přidejte do svého zařízení certifikát Fiddler. V **nabídce nástroje** > **Fiddler možnosti** > **https** **Akce** **exportovat kořenový certifikát do plochy**. >  > 

1. Vypněte zachytávání pomocí přechodu na**přenos** **souborů** > . Nebo stiskněte klávesu **F12**.

1. Vymažte mezipaměť prohlížeče, aby se odstranily všechny položky v mezipaměti, a je nutné je znovu stáhnout.

1. Vytvořit požadavek: 

   a. Vyberte kartu **Autor** .

   b. Nastavte metodu HTTP a adresu URL.

   c. Pokud potřebujete, přidejte záhlaví a text žádosti.

   d. Vyberte **Provést**.

9. Zapněte znovu zachytávání provozu a dokončete problematickou transakci na stránce.

10. Přejít na **soubor** > **Uložit** > **všechny relace**.

Další informace najdete v tématu [Začínáme s Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Další postup

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)



