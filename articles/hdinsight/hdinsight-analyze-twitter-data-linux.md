---
title: Analýza dat Twitteru pomocí Apache Hive - Azure HDInsight | Microsoft Docs
description: Další informace o použití použití Hive a Hadoop v HDInsight k transformaci dat ve formátu raw TWitter do prohledávatelné tabulku Hive.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1e3a9b12a9621baf91d49be48fb79a5d6bca1326
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>Analýza dat Twitteru pomocí Hive a Hadoop v HDInsight

Naučte se používat Apache Hive ke zpracování dat služby Twitter. Výsledkem je seznam Twitter uživatelů, kteří odeslané většina tweetů, které obsahují určité slovo.

> [!IMPORTANT]
> Kroky v tomto dokumentu byly testovány na HDInsight 3.6.
>
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Získání dat

Twitter umožňuje načíst [data pro každou tweet](https://dev.twitter.com/docs/platform-objects/tweets) jako dokument JavaScript Object Notation (JSON) přes REST API. [OAuth](http://oauth.net) je vyžadován pro ověření rozhraní API.

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

1. Z webového prohlížeče, přihlaste se k [ https://apps.twitter.com/ ](https://apps.twitter.com/). Klikněte na tlačítko **registrace nyní** odkaz, pokud nemáte účet služby Twitter.

2. Klikněte na tlačítko **vytvořte novou aplikaci**.

3. Zadejte **název**, **popis**, **webu**. Můžete použít pro adresu URL **webu** pole. Následující tabulka uvádí některé ukázkové hodnoty použít:

   | Pole | Hodnota |
   |:--- |:--- |
   | Název |MyHDInsightApp |
   | Popis |MyHDInsightApp |
   | Web |http://www.myhdinsightapp.com |

4. Zkontrolujte **souhlasím**a potom klikněte na **vytvořit aplikaci služby Twitter**.

5. Klikněte **oprávnění** kartě. Výchozí oprávnění je **jen pro čtení**.

6. Klikněte **klíče a přístupové tokeny** kartě.

7. Klikněte na tlačítko **vytvořit můj přístupový token**.

8. Klikněte na tlačítko **testovací OAuth** v pravém horním rohu stránky.

9. Zapište **uživatelský klíč**, **uživatelský tajný klíč**, **přístupový token**, a **tajný klíč přístupového tokenu**.

### <a name="download-tweets"></a>Stáhnout tweetů

Následující kód Python stahování 10 000 tweetů z Twitteru a uložte je do souboru s názvem **tweets.txt**.

> [!NOTE]
> Následující kroky jsou v clusteru HDInsight, provést, protože je již nainstalován jazyk Python.

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Použijte následující příkazy pro instalaci [Tweepy](http://www.tweepy.org/), [Progressbar](https://pypi.python.org/pypi/progressbar/2.2)a další požadované balíčky:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Pomocí následujícího příkazu vytvořte soubor s názvem **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Použít následující text jako obsah **gettweets.py** souboru:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]
    > Informace z vaší aplikace twitter nahraďte zástupný text pro následující položky:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > Upravte filtr témata na posledním řádku ke sledování oblíbených klíčových slov. Pomocí Oblíbené klíčová slova v době, kdy spustíte skript umožňuje rychlejší sběru dat.

6. Použití **kombinaci kláves Ctrl + X**, pak **Y** k uložení souboru.

7. Pomocí následujícího příkazu spusťte soubor a stáhnete tweetů:

    ```bash
    python gettweets.py
    ```

    Se zobrazí indikátor průběhu. Až o 100 % počítá jako tweetů staženy.

   > [!NOTE]
   > Pokud to trvá dlouho indikátor průběhu posunut, měli byste změnit filtr pro sledování trendů témata. Pokud nejsou k dispozici mnoho tweety o téma do filtru, můžete rychle získat 10000 tweetů potřeby.

### <a name="upload-the-data"></a>Odeslání dat

Pokud chcete nahrát data do úložiště HDInsight, použijte následující příkazy:

   ```bash
   hdfs dfs -mkdir -p /tutorials/twitter/data
   hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Tyto příkazy ukládání dat v umístění, ke kterému mají přístup všechny uzly v clusteru.

## <a name="run-the-hiveql-job"></a>Spustit úlohu HiveQL

1. K vytvoření souboru, který obsahuje příkazy HiveQL použijte následující příkaz:

   ```bash
   nano twitter.hql
   ```

    Použijte následující text jako obsah souboru:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
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
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
   ```

2. Stiskněte klávesu **kombinaci kláves Ctrl + X**, stiskněte **Y** k uložení souboru.
3. Ke spuštění HiveQL obsažené v souboru použijte následující příkaz:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Tento příkaz spustí **twitter.hql** souboru. Po dokončení dotazu se zobrazí `jdbc:hive2//localhost:10001/>` řádku.

4. Na příkazový řádek beeline ověřte, že importu dat pomocí následujícího dotazu:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Tento dotaz vrací maximálně 10 tweetů, které obsahují slovo **Azure** v textu zprávy.

    > [!NOTE]
    > Pokud jste změnili filtru v `gettweets.py` skriptu, nahraďte **Azure** s jedním z filtrů jste použili.

## <a name="next-steps"></a>Další postup

Jste se naučili postup transformace datové sadě služby nestrukturovaných JSON do strukturovaných tabulku Hive. Další informace o Hive v HDInsight, najdete v následujících dokumentech:

* [Začínáme s HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analýza dat zpoždění letu pomocí HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
