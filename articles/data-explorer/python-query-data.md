---
title: 'Rychlý start: Dotazování dat pomocí knihovny Python Azure Data Exploreru'
description: V tomto rychlém startu se naučíte dotazovat data z Azure Data Exploreru pomocí Pythonu.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: 38a7a4fa3ac0b9bfb554deeb8086dca398e5cdc1
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956166"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Rychlý start: Dotazování dat pomocí knihovny Python Azure Data Exploreru

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer poskytuje [datovou klientskou knihovnu pro Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Pomocí této knihovny můžete data dotazovat z kódu. V tomto rychlém startu se připojíte k tabulce na *clusteru nápovědy*, který jsme zřídili pro podporu výuky. Pak provedete dotaz na tabulku v tomto clusteru a vrátíte výsledky.

Tento rychlý start je dostupný také v podobě [poznámkového bloku Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Požadavky

* E-mailový účet organizace, který je členem služby Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) nainstalovaný na počítači pro vývoj

## <a name="install-the-data-library"></a>Instalace datové knihovny

Nainstalujte *azure-kusto-data*.

```
pip install azure-kusto-data==0.0.13
```

## <a name="add-import-statements-and-constants"></a>Přidání příkazů a konstant pro import

Naimportujte z knihovny třídy a *pandas*, což je knihovna pro analýzu dat.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

K ověření aplikace používá Azure Data Explorer ID vašeho tenanta AAD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

ID tenanta je v tomto případě `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Před spuštěním tohoto kódu nastavte hodnotu AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Teď sestavte připojovací řetězec. V tomto příkladu se pro přístup ke clusteru používá ověřování pomocí zařízení. Můžete také použít certifikát aplikace AAD, klíč aplikace AAD a uživatele a heslo AAD.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Připojení k Azure Data Exploreru a spuštění dotazu

Spusťte dotaz vůči clusteru a uložte výstup do datového rámce. Když se tento kód spustí, vrátí podobnou zprávu: *Pokud se chcete přihlásit, otevřete ve webovém prohlížeči stránku https://microsoft.com/devicelogin a zadejte kód F3W4VWZDM k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Zkoumání dat v datovém rámci

Po přihlášení vrátí tento dotaz výsledky, které jsou uložené v datovém rámci. S výsledky můžete pracovat stejně jako s jakýmkoli jiným datovým rámcem.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Měli byste vidět prvních deset výsledků z tabulky StormEvents.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý start: Ingestování dat pomocí knihovny Python Azure Data Exploreru](python-ingest-data.md)