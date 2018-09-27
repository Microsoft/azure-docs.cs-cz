---
title: Načtení dat pomocí služby Azure Machine Learning Prep sady SDK pro Data – Python
description: Další informace o načítání dat pomocí sady SDK služby Azure Machine Learning Data Prep. Můžete načíst různé typy vstupních dat, určit typy souborů dat a parametry nebo použít funkci inteligentního čtení sady SDK automaticky rozpoznat typ souboru.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 436ff9d318dc311efe27352a8b2ac91cfb5be618
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221317"
---
#<a name="load-and-read-data-with-azure-machine-learning"></a>Načtení a čtení dat pomocí Azure Machine Learning

Použití [sady SDK služby Azure Machine Learning Data Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) načíst různé druhy vstupní data. 

Jak načíst vaše data, máte dva přístupy:
+ Zadejte datový typ souboru a jeho parametry
+ Pomocí funkce Inteligentní čtení sady SDK automaticky rozpoznat typ souboru

## <a name="use-text-line-data"></a>Použít data řádku textu 
Jedním z nejjednodušších způsobů načítání dat je číst jako řádky textu.

Tady je ukázkový kód:
```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Perokresba|
|----|-----|
|0|Datum \| \| minimální teploty \| \| nejvyšší teplota|
|1|2015-07-1 \| \| -4.1 \| \| 10.0.|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|
|3|2015-07-3 \| \| -7.0 \| \| 10.5|
|4|2015-07-4 \| \| -5.5 \| \| 9.3|

Jakmile data ingestují, můžete načíst pandas DataFrame pro úplnou datovou sadu.

Tady je ukázkový kód:
```python
df = dataflow.to_pandas_dataframe()
df
```
Ukázkový výstup:
||Perokresba|
|----|-----|
|0|Datum\| \| minimální teploty\| \| nejvyšší teplota|
|1|2015-07-1\| \| 4.1\| \| 10.0.|
|2|2015-07-2\| \| 0,8\| \| 10.8|
|3|2015-07-3\| \| 7.0\| \| 10.5|
|4|2015-07-4\| \| 5.5\| \| 9.3|

## <a name="use-csv-data"></a>Použití dat ve formátu CSV
Při čtení textových souborů s oddělovači, můžete nechat odvodit analýzy parametry (například oddělovač, kódování, jestli se má použít, hlavičky atd.) místo poskytování je základní modul runtime. V tomto příkladu pokus o čtení souboru tak, že zadáte jenom jeho umístění. 

Tady je ukázkový kód:
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

Ukázkový výstup:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale kraj|10171002158| |
|2|ALABAMA|1|101710|Hale kraj|10171002162| |
|3|ALABAMA|1|101710|Hale kraj|10171002156| |
|4|ALABAMA|1|101710|Hale kraj|10171000588|2|

Je jeden z parametrů, které můžete určit počet řádků pro přeskočení v souborech, které právě čtete. Použijte následující kód k vyfiltrování řádku duplicitní.
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

Ukázkový výstup:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale kraj|10171002158|29|
|1|ALABAMA|1|101710|Hale kraj|10171002162|40 |
|2|ALABAMA|1|101710|Hale kraj|10171002156| 43|
|3|ALABAMA|1|101710|Hale kraj|10171000588|2|
|4|ALABAMA|1|101710|Hale kraj|10171000589|23 |

V dalším kroku můžete si prohlédnout datové typy sloupců.
Tady je ukázkový kód:
```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

Bohužel náš sloupce se nevrátili jako řetězce. Důvodem je, že ve výchozím nastavení, sady SDK pro Azure Machine Learning Data Prep nemění datový typ. Zdroj dat, které jsme se čtení z je textový soubor, takže sady SDK čte všechny hodnoty jako řetězce. V tomto příkladu je však chceme analyzovat číselné sloupce jako čísla. K tomuto účelu můžete nastavit parametr inference_arguments k current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Několik sloupců byly správně rozpozná jako čísla a jejich typ je nastavený na float64. Pomocí provádí ingestování můžete načíst pandas DataFrame pro úplnou datovou sadu.
Tady je ukázkový kód:
```python
df = dataflow.to_pandas_dataframe()
df
```

Ukázkový výstup:
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|49.0|
|1|ALABAMA|Hale kraj|1.017100e + 10|40.0|
|2|ALABAMA|Hale kraj|1.017100e + 10|hodnotu 43,0|
|3|ALABAMA|Hale kraj|1.017100e + 10|2.0|
|4|ALABAMA|Hale kraj|1.017100e + 10|23.0|

## <a name="use-excel-data"></a>Použití Excelových dat
Obsahuje sadu SDK pro Azure Machine Learning Data Prep `read_excel` funkce načíst soubory aplikace Excel. Tady je ukázkový kód:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

Ukázkový výstup:
||Sloupec1|Column2|Sloupec3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Železa IV b|60000000.0|Nalezeno|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19.58333|17.91667|
|1|Cabo York|Železa IIIAB|58200000.0|Nalezeno|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76.13333|-64.93333|
|2|Campo del Cielo|Železa, IAB MG|50000000.0|Nalezeno|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27.46667|-60.58333|
|3|Diablo údolí|Železa, IAB MG|30000000.0|Nalezeno|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35.05000|-111.03333|
|4|Armanty|Železa IIIE|28000000.0|Nalezeno|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47.00000|88.00000|

Načetli jste první tabulky Excelového souboru. Stejného výsledku dosáhnout explicitním zadáním názvu na listu, které chcete načíst. Pokud chcete místo toho zatížení druhý list, můžete zadat jeho název jako argument. Příklad:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

Ukázkový výstup:
||Sloupec1|Column2|Sloupec3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|1|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|2|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|3|pořadí|Titul|Studio|Po celém světě|Domácí / %|Sloupec1|Zámořské / %|Column2|Rok ^|
|4|1|Miniatury|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

Jak je vidět, došlo v tabulce v druhé tabulce záhlaví a tři prázdné řádky. Budete muset upravit argumenty funkce odpovídajícím způsobem. Příklad:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

Ukázkový výstup:
||pořadí|Titul|Studio|Po celém světě|Domácí / %|Sloupec1|Zámořské / %|Column2|Rok ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Miniatury|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Pamětích.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|
|2|3|Společnosti Marvel Avengers|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|Harry Potter a Deathly Hallows 2. část|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|Zmrazené|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="use-fixed-width-data-files"></a>Použít pevnou délkou datových souborů
Pro soubory s pevnou šířkou můžete zadat seznam posunutí. První sloupec vždy předpokládá, že je třeba spustit na posunu 0:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

Ukázkový výstup:
||010000|99999|NEPLATNÉ NORSKO|NE|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|NEPLATNÉ NORSKO|NE|NE|ENSO||||
|1|010010|99999|JAN MAYEN|NE|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NE|NE|||||
|3|010014|99999|SOERSTOKKEN|NE|NE|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NE|NE|ENBL|+61383|+005867|+03270|


Pokud neexistují žádné hlavičky v souborech, je vhodné na první řádek považovat za data. Předáním `PromoteHeadersMode.NONE` argument – klíčové slovo záhlaví, můžete vyhnout detekci záhlaví a získat správná data. Příklad:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

Ukázkový výstup:

||Sloupec1|Column2|Sloupec3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|NEPLATNÉ NORSKO|NE|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|NEPLATNÉ NORSKO|NE|NE|ENSO||||
|2|010010|99999|JAN MAYEN|NE|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NE|NE|||||
|4|010014|99999|SOERSTOKKEN|NE|NE|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NE|NE|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>Použití dat SQL
Sady SDK pro Azure Machine Learning Data Prep můžete také načíst data ze serverů SQL. V současné době se podporuje jenom Microsoft SQL Server.
Chcete-li čtení dat z SQL serveru, vytvořte objekt zdroje dat, který obsahuje informace o připojení. Příklad:
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Jak je vidět, parametr hesla `MSSQLDataSource` přijímá objekt tajného kódu. Objekt tajného kódu můžete získat dvěma způsoby:
-   Prováděcí modul zaregistrujte tajný kód a její hodnotu. 
-   Vytvoření tajného kódu s pouze ID (užitečné v případě tajná hodnota je už zaregistrovaný v prostředí pro spouštění).

Jakmile vytvoříte objekt zdroje dat, můžete přejít k číst data. Příklad:
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

Ukázkový výstup:
||ProductID|Název|ProductNumber|Barva|StandardCost|ListPrice|Velikost|Hmotnost|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|Thumbnailphoto nastavuje|ThumbnailPhotoFileName|ROWGUID|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame – Black, 58|FR-R92B 58|Černá|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|HL Road Frame – Red, 58|FR-R92R 58|Červená|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Sport – 100 Helmet, Red|HL-U509-R|Červená|13.0863|34.99|Žádný|Žádný|35|33|2005-07-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|
|3|708|Sport – 100 Helmet, Black|HL U509|Černá|13.0863|34.99|Žádný|Žádný|35|33|2005-07-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000 + 00:00|
|4|709|Socks kolo Horská oblast, M|TAK B909-M|Bílá|3.3963|9.50|M|Žádný|27|18|2005-07-01: 00:00:00 + 00:00|2006-06-30 00:00:00 + 00:00|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000 + 00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

Ukázkový výstup:
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Použití Azure Data Lake Storage
Existují dva způsoby, jak sada SDK můžete získat potřebné token OAuth pro přístup k Azure Data Lake Storage:
-   Získání přístupového tokenu z poslední relace přihlášení z rozhraní příkazového řádku Azure přihlášení uživatele.
-   Pomocí hlavního názvu služby (SP) a certifikát jako tajný klíč

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Pomocí přístupového tokenu z poslední relace příkazového řádku Azure
Na místním počítači spusťte následující příkaz:

> [!NOTE] 
> Pokud váš uživatelský účet je členem více než jednoho tenanta Azure, budete muset zadat tenanta ve formátu název hostitele adresy URL AAD.


Příklad:
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Vytvoření instančního objektu pomocí Azure CLI
Můžete použít rozhraní Azure CLI k vytvoření instančního objektu služby a odpovídající certifikát. Tento konkrétní instanční nastavená na čtečka otisku prstů, kde je její obor omezit na jenom účet úložiště Azure Data Lake "dpreptestfiles".  Příklad:
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Tento příkaz vysílá `appId` a cesta k souboru certifikátu (obvykle ve složce home). Soubor .crt obsahuje veřejný certifikát a privátní klíč ve formátu PEM.

Extrahujte kryptografický otisk:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Ke konfiguraci seznamu ACL pro systém souborů Azure Data Lake Storage, použijte ID objektu uživatele nebo instanční objekt v tomto příkladu. Příklad:
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Ke konfiguraci `Read` a `Execute` přístup pro systém souborů Azure Data Lake Storage, budete muset nakonfigurovat seznam ACL pro soubory a složky jednotlivě. Toto je skutečnost, že základní model řízení přístupu HDFS nepodporuje dědičnosti. Příklad:
```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
### <a name="acquire-an-oauth-access-token"></a>Získání přístupového tokenu OAuth
Použití `adal` balíčku (přes: `pip install adal`) vytvořit kontext ověřování na MSFT na úrovni tenanta a získání přístupového tokenu OAuth. ADLS, musí být prostředek v žádosti o token pro 'https://datalake.azure.net", která se liší od nejvíce dalších prostředků Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Web|Ulice|city|Kraj|
|----|------|-----|----|----|----|----|
|0|1012063|Přidružení - Danville Kaledonie Farmářům uvedení na trh|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledonie|
|1|1011871|Věci odvál čas Stearns Farmářům ' trhu|http://Stearnshomestead.com |Silniční ridge 6975|Parma|Cuyahoga|
|2|1011878|100 přepočet mílí na trhu|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. hlavní ulice Farmářům trhu|http://thetownofsixmile.wordpress.com/ |106 S. hlavní ulice|Šest míle|||
|4|1010691|Společnost 10th trhu Farmářům Steet komunity|http://agrimissouri.com/mo-grown/grodetail.php... |Ulice 10 a topolů|Lamar|Barton|

## <a name="use-smart-reading"></a>Použití "inteligentní čtení"

Pomocí funkce Inteligentní čtení sady SDK automaticky rozpoznat typ souboru.
