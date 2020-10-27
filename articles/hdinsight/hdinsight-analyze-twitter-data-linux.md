---
title: Analýza dat Twitteru pomocí Apache Hive – Azure HDInsight
description: Naučte se, jak pomocí Apache Hive a Apache Hadoop v HDInsight transformovat nezpracovaná data TWitteru do tabulky s možností prohledávání.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: fe511ed2d6b724c1215f9986c9d6c50aae076935
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533287"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analýza dat Twitteru pomocí Apache Hive a Apache Hadoop v HDInsight

Naučte se používat [Apache Hive](https://hive.apache.org/) ke zpracování dat Twitteru. Výsledkem je seznam uživatelů Twitteru, kteří poslali nejvíce tweety, která obsahují určité slovo.

> [!IMPORTANT]  
> Kroky v tomto dokumentu byly testovány v HDInsight 3,6.

## <a name="get-the-data"></a>Získání dat

Twitter umožňuje načíst data pro každý disk ve formátu. v podobě dokumentu JavaScript Object Notation (JSON) prostřednictvím REST API. Pro ověřování do rozhraní API je vyžadován protokol [OAuth](https://oauth.net) .

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

1. Z webového prohlížeče se přihlaste [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) . Pokud nemáte účet na Twitteru, vyberte odkaz **registrace nyní** .

2. Vyberte **vytvořit novou aplikaci** .

3. Zadejte **název** , **Popis** , **Web** . Pro pole **Web** můžete vytvořit adresu URL. V následující tabulce jsou uvedeny některé ukázkové hodnoty, které se mají použít:

   | Pole | Hodnota |
   |--- |--- |
   | Název |MyHDInsightApp |
   | Popis |MyHDInsightApp |
   | Web |`https://www.myhdinsightapp.com` |

4. Vyberte **Ano,** souhlasím a pak vyberte **vytvořit aplikaci Twitter** .

5. Vyberte kartu **oprávnění** . Výchozí oprávnění je jen **pro čtení** .

6. Vyberte kartu **klíče a přístupové tokeny** .

7. Vyberte **vytvořit přístupový token** .

8. V pravém horním rohu stránky vyberte **test OAuth** .

9. Zapište **klíč příjemce** , **tajný klíč příjemce** , **přístupový token** a **tajný klíč přístupového tokenu** .

### <a name="download-tweets"></a>Stáhnout tweety

Následující kód Pythonu stáhne 10 000 tweety z Twitteru a uloží je do souboru s názvem **tweets.txt** .

> [!NOTE]  
> Následující kroky se provádějí v clusteru HDInsight, protože Python je už nainstalovaný.

1. Připojte se ke clusteru pomocí [příkazu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pomocí následujících příkazů nainstalujte [Tweepy](https://www.tweepy.org/), [indikátor průběhu](https://pypi.python.org/pypi/progressbar/2.2)a další požadované balíčky:

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

1. Pomocí následujícího příkazu vytvořte soubor s názvem **gettweets.py** :

   ```bash
   nano gettweets.py
   ```

1. Níže uvedený kód nahraďte nahrazením `Your consumer secret` , `Your consumer key` , a `Your access token` `Your access token secret` s příslušnými informacemi z vaší aplikace Twitter. Pak vložte upravený kód jako obsah souboru **gettweets.py** .

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
    > Upravte témata filtr na posledním řádku, abyste mohli sledovat oblíbená klíčová slova. Používání klíčových slov oblíbená v době spuštění skriptu umožňuje rychlejší zachytávání dat.

1. Použijte **kombinaci kláves CTRL + X** a potom kliknutím na **Y** soubor uložte.

1. Pomocí následujícího příkazu spusťte soubor a stáhněte tweety:

    ```bash
    python gettweets.py
    ```

    Zobrazí se indikátor průběhu. Počítá až 100% po stažení tweety.

   > [!NOTE]  
   > Pokud má indikátor průběhu trvat dlouhou dobu, měli byste změnit filtr tak, aby sledoval témata týkající se trendů. Pokud existuje mnoho tweety o tématu ve vašem filtru, můžete rychle získat 100 tweety, který potřebujete.

### <a name="upload-the-data"></a>Nahrajte data

Pokud chcete nahrát data do úložiště HDInsight, použijte následující příkazy:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Tyto příkazy ukládají data do umístění, ke kterému mají přístup všechny uzly v clusteru.

## <a name="run-the-hiveql-job"></a>Spuštění úlohy HiveQL

1. Pomocí následujícího příkazu vytvořte soubor obsahující příkazy [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) :

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

1. Stiskněte klávesy **CTRL + X** a potom stisknutím klávesy **Y** soubor uložte.

1. Pomocí následujícího příkazu spusťte HiveQL obsaženou v souboru:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Tento příkaz spustí soubor **Twitter. HQL** . Po dokončení dotazu se zobrazí `jdbc:hive2//localhost:10001/>` výzva.

1. Z příkazového řádku Beeline použijte následující dotaz k ověření, že data byla importována:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Tento dotaz vrátí maximálně 10 tweety, které obsahují slovo **Azure** v textu zprávy.

    > [!NOTE]  
    > Pokud jste filtr změnili ve `gettweets.py` skriptu, nahraďte **Azure** jedním z filtrů, které jste použili.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak transformovat nestrukturované datové sady JSON do strukturované [Apache Hive](https://hive.apache.org/) tabulky. Další informace o podregistru v HDInsight najdete v následujících dokumentech:

* [Začínáme se službou HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analýza dat zpoždění letů pomocí HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)