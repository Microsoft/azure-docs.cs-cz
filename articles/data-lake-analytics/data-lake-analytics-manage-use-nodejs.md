---
title: Správa Azure Data Lake Analytics pomocí sady Azure SDK pro Node.js
description: Tento článek popisuje, jak používat sadu Azure SDK pro Node.js ke správě účtů, zdrojů dat, úloh a uživatelů Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: get-started-article
ms.date: 12/05/2016
ms.openlocfilehash: 7af56c7770de7eef0fcf33b2c29f02d3b9fe7170
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624307"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Správa Azure Data Lake Analytics pomocí sady Azure SDK pro Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak můžete spravovat účty, zdroje dat, uživatele a úlohy Azure Data Lake Analytics prostřednictvím aplikace napsané pomocí sady Azure SDK pro Node.js. 

Podporují se následující verze:
* **Verze Node.js: 0.10.0 nebo vyšší**
* **Verze rozhraní REST API pro Účet: 2015-10-01-preview**
* **Verze rozhraní REST API pro Katalog: 2015-10-01-preview**
* **Verze rozhraní REST API pro Úlohu: 2016-03-20 preview**

## <a name="features"></a>Funkce
* Správa účtů: vytvoření, získání, seznam, aktualizace a odstranění.
* Správa úloh: odeslání, získání, seznam a zrušení.
* Správa katalogů: získání a seznam.

## <a name="how-to-install"></a>Postup instalace
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Ověření pomocí služby Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Vytvoření klienta Data Lake Analytics
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Získání seznamu úloh
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Získání seznamu databází v katalogu Data Lake Analytics
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Viz také
* [Microsoft Azure SDK pro Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK pro Node.js – správa Data Lake Storu](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

