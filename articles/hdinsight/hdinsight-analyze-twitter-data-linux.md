---
title: Analýza dat Twitteru pomocí Apache Hive – Azure HDInsight
description: Další informace o použití Apache Hivu a Apache Hadoop v HDInsight transformujte nezpracovaná data Twitteru do prohledávání tabulky Hive.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: d037c4fc65bc32ff183ddc7a8925cd2f51411ea0
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034622"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analýza dat Twitteru pomocí Apache Hivu a Apache Hadoop v HDInsight

Informace o používání Apache Hive ke zpracování dat Twitteru. Výsledkem je seznam uživatelů Twitteru, kteří odeslané většina tweety, které obsahují určité slovo.

> [!IMPORTANT]
> Kroky v tomto dokumentu byly testovány v HDInsight 3.6.
>
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Získání dat

Twitter umožňuje načíst data pro každý tweet jako dokument JavaScript Object Notation (JSON) prostřednictvím rozhraní REST API. [OAuth](http://oauth.net) se nedá provést ověření do rozhraní API.

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

1. Z webového prohlížeče, přihlaste se k [ https://apps.twitter.com/ ](https://apps.twitter.com/). Klikněte na tlačítko **zaregistrujte** propojení, pokud nemáte účet na Twitteru.

2. Klikněte na tlačítko **vytvořte novou aplikaci**.

3. Zadejte **název**, **popis**, **webu**. Můžete společně tvoří adresu URL, **webu** pole. V následující tabulce jsou uvedeny některé ukázkové hodnoty pro použití:

   | Pole | Hodnota |
   |:--- |:--- |
   | Název |MyHDInsightApp |
   | Popis |MyHDInsightApp |
   | Web |http://www.myhdinsightapp.com |

4. Zkontrolujte **Ano, souhlasím**a potom klikněte na tlačítko **vytvoření aplikace Twitter**.

5. Klikněte na tlačítko **oprávnění** kartu. Výchozí oprávnění je **jen pro čtení**.

6. Klikněte na tlačítko **klíče a přístupové tokeny** kartu.

7. Klikněte na tlačítko **vytvořit můj přístupový token**.

8. Klikněte na tlačítko **Test OAuth** v pravém horním rohu stránky.

9. Zapište si **uživatelský klíč**, **uživatelský tajný klíč**, **přístupový token**, a **tajný klíč přístupového tokenu**.

### <a name="download-tweets"></a>Stáhněte si tweety

Následující kód Python stáhne 10 000 tweety z Twitteru a uložit je do souboru s názvem **tweets.txt**.

> [!NOTE]
> Následující kroky se provádějí v clusteru HDInsight, protože je již nainstalován Python.

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Pomocí následujících příkazů nainstalujte [Tweepy](http://www.tweepy.org/), [Progressbar](https://pypi.python.org/pypi/progressbar/2.2)a další požadované balíčky:

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

5. Použijte následující text jako obsah **gettweets.py** souboru:

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
    > Informace o aplikaci twitter nahraďte zástupný text pro následující položky:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > Upravte filtr témata na posledním řádku ke sledování oblíbených klíčových slov. Pomocí oblíbených klíčových slov v době spuštění skriptu umožňuje rychlejší zachycení data.

6. Použití **Ctrl + X**, pak **Y** k uložení souboru.

7. Spusťte soubor a stahovat tweety, použijte následující příkaz:

    ```bash
    python gettweets.py
    ```

    Se zobrazuje indikátor průběhu. Až 100 % se počítá jako tweety, které se stáhnou.

   > [!NOTE]
   > Pokud trvá dlouhou dobu indikátoru průběhu pro přechod, měli byste změnit filtr ke sledování populárních témat. Po mnoho tweetů o tématu ve filtru se můžete rychle získat 10000 tweety, které jsou potřeba.

### <a name="upload-the-data"></a>Nahrání dat

Pokud chcete nahrát data do úložiště HDInsight, použijte následující příkazy:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Tyto příkazy uložení dat do umístění, které můžou přistupovat všechny uzly v clusteru.

## <a name="run-the-hiveql-job"></a>Spustit úlohu HiveQL

1. Chcete-li vytvořit soubor, který obsahuje příkazy HiveQL použijte následující příkaz:

   ```bash
   nano twitter.hql
   ```

    Jako obsah souboru, použijte následující text:

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

2. Stisknutím klávesy **Ctrl + X**, stiskněte klávesu **Y** k uložení souboru.
3. Použijte následující příkaz pro spuštění HiveQL obsažené v souboru:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Tento příkaz spustí **twitter.hql** souboru. Po dokončení dotazu se zobrazí `jdbc:hive2//localhost:10001/>` řádku.

4. Z příkazového řádku beeline ověřte, že data byla importována pomocí následujícího dotazu:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Tento dotaz vrátí maximálně 10 tweety, které obsahují slovo **Azure** v textu zprávy.

    > [!NOTE]
    > Pokud jste změnili filtr `gettweets.py` skriptu, nahraďte **Azure** s jeden z filtrů jste použili.

## <a name="next-steps"></a>Další postup

Jste se naučili, jak transformovat nestrukturované datové sady JSON do strukturovaného tabulky Hive. Další informace o Hive v HDInsight, najdete v následujících dokumentech:

* [Začínáme s HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analýza zpoždění letů pomocí HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
