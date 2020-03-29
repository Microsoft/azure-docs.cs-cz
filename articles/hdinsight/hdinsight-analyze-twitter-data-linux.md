---
title: Analýza dat twitteru pomocí Apache Hive – Azure HDInsight
description: Přečtěte si, jak používat Apache Hive a Apache Hadoop na HDInsight k transformaci nezpracovaných dat TWitter u tabulky Hive, kterou lze prohledávat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435617"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analyzujte data twitteru pomocí Apache Hive a Apache Hadoop na HDInsight

Přečtěte si, jak používat [Apache Hive](https://hive.apache.org/) ke zpracování dat na Twitteru. Výsledkem je seznam uživatelů Twitteru, kteří poslali nejvíce tweetů, které obsahují určité slovo.

> [!IMPORTANT]  
> Kroky v tomto dokumentu byly testovány na HDInsight 3.6.

## <a name="get-the-data"></a>Získání dat

Twitter umožňuje načíst data pro každý tweet jako dokument JavaScript Object Notation (JSON) prostřednictvím REST API. [OAuth](https://oauth.net) je vyžadován pro ověřování rozhraní API.

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

1. Ve webovém prohlížeči [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)se přihlaste k aplikaci . Pokud nemáte účet na Twitteru, vyberte nyní odkaz **Registrace.**

2. Vyberte **Vytvořit novou aplikaci**.

3. Zadejte **název**, **popis**, **web**. Můžete vytvořit adresu URL pro pole **Web.** V následující tabulce jsou uvedeny některé ukázkové hodnoty, které mají být používány:

   | Pole | Hodnota |
   |--- |--- |
   | Name (Název) |Aplikace MyHDInsight |
   | Popis |Aplikace MyHDInsight |
   | Web |`https://www.myhdinsightapp.com` |

4. Vyberte **Ano, souhlasím**a pak vyberte **Vytvořit aplikaci Twitter**.

5. Vyberte kartu **Oprávnění.** Výchozí oprávnění je **jen pro čtení**.

6. Vyberte kartu **Klíče a přístupové tokeny.**

7. Vyberte **vytvořit přístupový token**.

8. V pravém horním rohu stránky vyberte **Testovat OAuth.**

9. Poznamenejte si **spotřebitelský klíč**, **tajný klíč příjemce**, **přístupový token**a tajný klíč **tokenu aplikace Access**.

### <a name="download-tweets"></a>Stáhnout tweety

Následující kód Pythonu stáhne 10 000 tweetů z Twitteru a uloží je do souboru s názvem **tweets.txt**.

> [!NOTE]  
> Následující kroky se provádějí v clusteru HDInsight, protože Python je již nainstalován.

1. Pomocí [příkazu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. K instalaci [tweepy](https://www.tweepy.org/), [indikátoru průběhu](https://pypi.python.org/pypi/progressbar/2.2)a dalších požadovaných balíčků použijte následující příkazy:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Pomocí následujícího příkazu vytvořte soubor s názvem **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

1. Upravte níže uvedený `Your consumer secret`kód `Your consumer key` `Your access token`nahrazením `Your access token secret` , , , a s příslušnými informacemi z vaší aplikace twitter. Poté vložte upravený kód jako obsah **gettweets.py** souboru.

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
   max_tweets=100

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

    > [!TIP]  
    > Upravte filtr témat na posledním řádku a sledujte oblíbená klíčová slova. Použití klíčových slov populární v době spuštění skriptu umožňuje rychlejší sběr dat.

1. Soubor uložte pomocí **kláves Ctrl + X**a potom pomocí **y.**

1. Ke spuštění souboru a stažení tweetů použijte následující příkaz:

    ```bash
    python gettweets.py
    ```

    Zobrazí se indikátor průběhu. To se počítá až 100% jako tweets jsou staženy.

   > [!NOTE]  
   > Pokud trvá dlouho, než indikátor průběhu posune, měli byste změnit filtr tak, aby sledoval aktuální témata. Když existuje mnoho tweets o tématu ve filtru, můžete rychle získat 100 tweets potřebné.

### <a name="upload-the-data"></a>Nahrání dat

Chcete-li data nahrát do úložiště HDInsight, použijte následující příkazy:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Tyto příkazy ukládají data do umístění, ke kterému mají přístup všechny uzly v clusteru.

## <a name="run-the-hiveql-job"></a>Spuštění úlohy HiveQL

1. Pomocí následujícího příkazu vytvořte soubor obsahující příkazy [HiveQL:](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

   ```bash
   nano twitter.hql
   ```

    Jako obsah souboru použijte následující text:

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

1. Stisknutím **kláves Ctrl + X**a stisknutím **klávesy Y** soubor uložte.

1. Ke spuštění hiveql obsaženého v souboru použijte následující příkaz:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Tento příkaz spustí soubor **twitter.hql.** Po dokončení dotazu se `jdbc:hive2//localhost:10001/>` zobrazí výzva.

1. V řádku s příkazem řádku ověřte, zda byla data importována, pomocí následujícího dotazu:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Tento dotaz vrátí maximálně 10 tweets, které obsahují slovo **Azure** v textu zprávy.

    > [!NOTE]  
    > Pokud jste změnili `gettweets.py` filtr ve skriptu, nahraďte **Azure** jedním z filtrů, které jste použili.

## <a name="next-steps"></a>Další kroky

Naučili jste se transformovat nestrukturovanou datovou sadu JSON do strukturované tabulky [Apache Hive.](https://hive.apache.org/) Další informace o Hive na HDInsight, najdete v následujících dokumentech:

* [Začínáme se službou HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analýza dat zpoždění letu pomocí HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
