---
title: Analýza dat Twitteru pomocí Hadoop v HDInsight – Azure
description: Naučte se pomocí Hivu analyzovat data Twitteru platformy hadoop v HDInsight cílem zjistit frekvenci použití určitého slova.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 8ac97c14b4abaa1c07e8f982edb53e0acac3692f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597616"
---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analýza dat Twitteru pomocí Hivu ve službě HDInsight
Sociální weby jsou jedním z hlavních dodávala vynutí pro velké objemy dat přijetí. Veřejné rozhraní API pomocí Twitteru, jako jsou k dispozici jsou užitečné zdroje dat pro analýzu a pochopení trendů Oblíbené.
V tomto kurzu bude dostávat tweety pomocí Twitteru streamovacího rozhraní API a pak pomocí Apache Hive v Azure HDInsight získat seznam uživatelů Twitteru, kteří odeslané většina tweety, které obsahovala určité slovo.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Konkrétní kroky do clusteru se systémem Linux najdete v tématu [analýza Twitteru dat pomocí Hivu ve službě HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Pracovní stanice** s prostředím Azure PowerShell nainstalovaný a nakonfigurovaný.

    Ke spuštění skriptů Windows Powershellu, musíte spustit prostředí Azure PowerShell jako správce a nastavte zásady spouštění na *RemoteSigned*. Zobrazit [skripty Windows Powershellu spusťte][powershell-script].

    Před spuštěním skriptů Windows Powershellu, ujistěte se, že jste připojeni k předplatnému Azure pomocí následující rutiny:

    ```powershell
    Connect-AzureRmAccount
    ```

    Pokud máte více předplatných Azure, použijte následující rutinu nastavit aktuální předplatné:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a k 1. lednu 2017 jsme ji odebrali. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Podle postupu v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) si nainstalujte nejnovější verzi prostředí Azure PowerShell. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

* **Cluster Azure HDInsight**. Pokyny týkající se zřizování clusteru, naleznete v tématu [začněte používat HDInsight] [ hdinsight-get-started] nebo [clusterů HDInsight zřízení][hdinsight-provision]. Později v tomto kurzu budete potřebovat název clusteru.

Následující tabulka uvádí soubory používané v tomto kurzu:

| Soubory | Popis |
| --- | --- |
| /tutorials/Twitter/data/tweets.txt |Zdroj dat pro úlohy Hive. |
| /tutorials/Twitter/Output |Výstupní složka pro úlohy Hive. Název výstupního souboru výchozí Hive úlohy je **000000_0**. |
| tutorials/Twitter/Twitter.hql |Soubor skriptu HiveQL. |
| /tutorials/Twitter/JobStatus |Stav úlohy systému Hadoop. |

## <a name="get-twitter-feed"></a>Informační kanál Twitteru GET
V tomto kurzu budete používat [rozhraní API pro streamování na Twitteru][twitter-streaming-api]. Konkrétní Twitter streamovacího rozhraní API použijete je [stavy nebo bloku filtru][twitter-statuses-filter].

> [!NOTE]
> Soubor, který obsahuje 10 000 tweety a soubor skriptu Hive (popsané v další části) se nahrály do veřejného kontejneru objektů Blob. Pokud chcete použít nahraných souborů, můžete tuto část přeskočit.

Tweety data se ukládají ve formátu JavaScript Object Notation (JSON), který obsahuje komplexní vnořené struktury. Místo psaní spousty řádků kódu s použitím konvenčních programovací jazyk, můžete transformovat tento vnořené struktury do tabulky Hive, tak, aby může být dotázán pomocí jazyk SQL (Structured Query) – například jazyka nazývaného HiveQL.

Twitter používá OAuth pro zajištění autorizovaný přístup k jeho rozhraní API. OAuth je ověřovací protokol, který umožňuje uživatelům, abyste mohli schválit aplikace tak, aby fungoval bez sdílení hesla jejich jménem. Další informace najdete v [oauth.net](http://oauth.net/) nebo vynikající [Průvodce pro začátečníky OAuth](http://hueniverse.com/oauth/) z Hueniverse.

Prvním krokem při používání OAuth je vytvoření nové aplikace na webu vývojáře služby Twitter.

**Vytvoření aplikace Twitter**

1. Přihlaste se k [ https://apps.twitter.com/ ](https://apps.twitter.com/). Klikněte na tlačítko **zaregistrujte se hned teď** propojení, pokud nemáte účet na Twitteru.
2. Klikněte na tlačítko **vytvořte novou aplikaci**.
3. Zadejte **název**, **popis**, **webu**. Můžete společně tvoří adresu URL, **webu** pole. V následující tabulce jsou uvedeny některé ukázkové hodnoty pro použití:

   | Pole | Hodnota |
   | --- | --- |
   |  Název |MyHDInsightApp |
   |  Popis |MyHDInsightApp |
   |  Web |http://www.myhdinsightapp.com |
4. Zkontrolujte **Ano, souhlasím**a potom klikněte na tlačítko **vytvoření aplikace Twitter**.
5. Klikněte na tlačítko **oprávnění** kartu. Výchozí oprávnění je **jen pro čtení**. To je dostatečná pro účely tohoto kurzu.
6. Klikněte na tlačítko **klíče a přístupové tokeny** kartu.
7. Klikněte na tlačítko **vytvořit můj přístupový token**.
8. Klikněte na tlačítko **Test OAuth** v pravém horním rohu stránky.
9. Zapište si **uživatelský klíč**, **uživatelský tajný klíč**, **přístupový token**, a **tajný klíč přístupového tokenu**. Hodnoty budete potřebovat v pozdější části kurzu.

V tomto kurzu použijete prostředí Windows PowerShell provádět volání webové služby. Další oblíbené nástroje pro volání webové služby je [ *Curl*][curl]. Curl je možné stáhnout z [tady][curl-download].

> [!NOTE]
> Při použití příkazu curl ve Windows pro hodnoty možnosti použijte dvojité uvozovky místo jednoduché uvozovky.

**Chcete-li získat tweetů**

1. Otevřete Windows PowerShell Integrované skriptovací prostředí (ISE). (Na obrazovce Start systému Windows 8, zadejte **PowerShell_ISE** a potom klikněte na tlačítko **Windows PowerShell ISE**. Zobrazit [spusťte prostředí Windows PowerShell v systému Windows 8 a Windows](https://docs.microsoft.com/en-us/powershell/scripting/setup/starting-windows-powershell?view=powershell-6)
2. V podokně skriptu zkopírujte následující skript:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Connect-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Nastaví prvních pět až osm proměnné ve skriptu:

    Proměnná|Popis
    ---|---
    $clusterName|Toto je název clusteru HDInsight, ve které chcete spustit aplikaci.
    $oauth_consumer_key|Jde o aplikaci Twitter **uživatelský klíč** jste si poznamenali dříve při vytvoření aplikace Twitter.
    $oauth_consumer_secret|Jde o aplikaci Twitter **uživatelský tajný klíč** jste si poznamenali dříve.
    $oauth_token|Jde o aplikaci Twitter **přístupový token** jste si poznamenali dříve.
    $oauth_token_secret|Jde o aplikaci Twitter **tajný klíč přístupového tokenu** jste si poznamenali dříve.
    $destBlobName|Toto je název výstupního objektu blob. Výchozí hodnota je **tutorials/twitter/data/tweets.txt**. Pokud změníte výchozí hodnotu, je potřeba aktualizovat skripty prostředí Windows PowerShell odpovídajícím způsobem.
    $trackString|Webová služba bude vracet tweety týkající se těchto klíčových slov. Výchozí hodnota je **HDInsight Azure, cloudu,**. Pokud změníte výchozí hodnotu, bude odpovídajícím způsobem aktualizovat skripty prostředí Windows PowerShell.
    $lineMax|Hodnota určuje, kolik tweety, které budou číst skript. Čtení 100 tweetů trvá přibližně 3 minuty. Můžete nastavit větší číslo, ale bude trvat delší dobu na stáhnout.

1. Stisknutím klávesy **F5** spusťte skript. Pokud narazíte na problémy, jako alternativní řešení, vyberte všechny řádky a potom stiskněte klávesu **F8**.
2. Měly by se zobrazit "Dokončení!" na konci výstupu. Chybové zprávy se zobrazí červeně.

Jako postup ověření, můžete zkontrolovat soubor výstup **/tutorials/twitter/data/tweets.txt**, ve vašem úložišti objektů Blob v Azure pomocí Průzkumníka služby Azure storage nebo Azure Powershellu. Ukázkový skript prostředí Windows PowerShell pro zobrazení seznamu souborů, najdete v části [použití Blob storage s HDInsight][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Vytvořte skript HiveQL
Pomocí Azure Powershellu, můžete spustit více příkazy HiveQL jeden po druhém nebo balíček příkaz HiveQL do souboru skriptu. V tomto kurzu vytvoříte skript HiveQL. Soubor skriptu musí být odeslán do služby Azure Blob storage. V další části se spustí soubor skriptu pomocí Azure Powershellu.

> [!NOTE]
> Soubor skriptu Hive a soubor, který obsahuje 10 000 tweety se nahrály do veřejného kontejneru objektů Blob. Pokud chcete použít nahraných souborů, můžete tuto část přeskočit.

Skript HiveQL provede následující:

1. **Odstranit tabulku tweets_raw** v případě, že tabulka již existuje.
2. **Vytvoří tabulku Hive tweets_raw**. Tuto dočasnou tabulku Hive strukturovaných uchovává data pro další extrakce, transformace a načítání (ETL) zpracování. Informace o oddílech najdete v tématu [Hive kurzu][apache-hive-tutorial].
3. **Načtení dat** ze zdrojové složky /tutorials/twitter/data. Velké tweety datovou sadu ve vnořených formátu JSON má nyní převedeny na dočasné struktura tabulky Hive.
4. **Odstranit tabulku tweety** v případě, že tabulka již existuje.
5. **Vytvoření tabulky tweety**. Předtím, než datovou sadu tweetů můžete dotazovat pomocí Hive, potřebujete spustit jiný proces ETL. Tento proces ETL definuje podrobnější schéma tabulky pro data, která jste uložili v tabulce "twitter_raw".
6. **Vložit tabulku přepsat**. Tento komplexní skript Hive se spustí řízený sada dlouhý úlohy mapreduce je možné Hadoop cluster. V závislosti na vaší datové sadě a velikost vašeho clusteru to může trvat přibližně 10 minut.
7. **Vložit přepsat directory**. Spustit dotaz a výstupní datové sady do souboru. Tento dotaz vrátí seznam Twitteru, kteří odeslané většina tweety, obsahující slovo "Azure".

**Vytvoříte skript Hivu a odešlete ji do Azure**

1. Otevřete Windows PowerShell ISE.
2. V podokně skriptu zkopírujte následující skript:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Nastavte první dvě proměnné ve skriptu:

   | Proměnná | Popis |
   | --- | --- |
   |  $clusterName |Zadejte název clusteru HDInsight, ve které chcete spustit aplikaci. |
   |  $subscriptionID |Zadejte ID svého předplatného Azure. |
   |  $sourceDataPath |Umístění úložiště objektů Blob v Azure, kde dotazy Hive bude číst data z. Nemusíte změnit tuto proměnnou. |
   |  $outputPath |Umístění úložiště objektů Blob v Azure, kde dotazy Hive se vypíše výsledky. Nemusíte změnit tuto proměnnou. |
   |  $hqlScriptFile |Umístění a název souboru souboru skript HiveQL. Nemusíte změnit tuto proměnnou. |
4. Stisknutím klávesy **F5** spusťte skript. Pokud narazíte na problémy, jako alternativní řešení, vyberte všechny řádky a potom stiskněte klávesu **F8**.
5. Měly by se zobrazit "Dokončení!" na konci výstupu. Chybové zprávy se zobrazí červeně.

Jako postup ověření, můžete zkontrolovat soubor výstup **/tutorials/twitter/twitter.hql**, ve vašem úložišti objektů Blob v Azure pomocí Průzkumníka služby Azure storage nebo Azure Powershellu. Ukázkový skript prostředí Windows PowerShell pro zobrazení seznamu souborů, najdete v části [použití Blob storage s HDInsight][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Zpracování dat Twitteru pomocí podregistru
Dokončili jste všechny přípravné kroky. Nyní jste vyvolání skriptu Hivu a zkontrolovat výsledky.

### <a name="submit-a-hive-job"></a>Odeslání úlohy Hive
Pomocí následujícího skriptu prostředí Windows PowerShell pro spuštění skriptu Hive. Budete muset nastavit první proměnné.

> [!NOTE]
> Pokud chcete použít v posledních dvou částech tweety a skript HiveQL jste nahráli, nastavte $hqlScriptFile "/ tutorials/twitter/twitter.hql". Pokud chcete použít ty, které byly nahrány do veřejných objektů blob za vás, nastavte $hqlScriptFile "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace �\s*$� -replace �.*\s�
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Kontrola výsledků
Pomocí následujícího skriptu prostředí Windows PowerShell zkontrolovat výstup úlohy Hive. Je potřeba nastavit první dvě proměnné.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> V tabulce Hive \001 používá jako oddělovač. Oddělovač není ve výstupu.

Po výsledky analýzy byly umístěny do úložiště objektů Blob v Azure, můžete exportovat data do serveru Azure SQL database a SQL, exportovat data do aplikace Excel pomocí doplňku Power Query nebo připojení aplikace k datům pomocí ovladače ODBC Hive. Další informace najdete v tématu [Sqoop použití s HDInsight][hdinsight-use-sqoop], [analyzovat zpoždění letů pomocí HDInsight][hdinsight-analyze-flight-delay-data], [ Připojení Excelu k HDInsight pomocí Power Query][hdinsight-power-query], a [připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu][hdinsight-hive-odbc].

## <a name="next-steps"></a>Další postup
V tomto kurzu jsme viděli, jak transformovat nestrukturované datové sady JSON na tabulku Hive strukturovaných dotazů, zkoumat a analyzovat data z Twitteru s využitím HDInsight v Azure. Další informace naleznete v tématu:

* [Začínáme s HDInsight][hdinsight-get-started]
* [Analýza zpoždění letů pomocí HDInsight][hdinsight-analyze-flight-delay-data]
* [Připojení Excelu k HDInsight pomocí Power Query][hdinsight-power-query]
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu][hdinsight-hive-odbc]
* [Použití Sqoopu se službou HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-power-query]:hadoop/apache-hadoop-connect-excel-power-query.md
[hdinsight-hive-odbc]:hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md
