---
title: Konfigurace připojovacího řetězce
titleSuffix: Azure Storage
description: Konfigurace připojovacího řetězce pro účet služby Azure Storage. Připojovací řetězec obsahuje informace potřebné k autorizaci přístupu k účtu úložiště z vaší aplikace za běhu s použitím autorizace pomocí sdíleného klíče.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d7ca1707c89f03683960822591065143d3f8aa4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783874"
---
# <a name="configure-azure-storage-connection-strings"></a>Nakonfigurování připojovacích řetězců Azure Storage

Připojovací řetězec zahrnuje autorizační informace požadované pro vaši aplikaci pro přístup k datům v účtu Azure Storage za běhu pomocí autorizace pomocí sdíleného klíče. Připojovací řetězce můžete nakonfigurovat na:

* Připojte se k emulátoru úložiště Azurite.
* Přístup k účtu úložiště v Azure.
* Přístup k určeným prostředkům v Azure získáte prostřednictvím sdíleného přístupového podpisu (SAS).

Informace o tom, jak zobrazit přístupové klíče účtu a zkopírovat připojovací řetězec, najdete v tématu [Správa přístupových klíčů účtu úložiště](storage-account-keys-manage.md).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>Uložení připojovacího řetězce

Vaše aplikace potřebuje přístup k připojovacímu řetězci za běhu, aby bylo možné autorizovat požadavky na Azure Storage. Máte několik možností pro uložení připojovacího řetězce:

* Připojovací řetězec můžete uložit do proměnné prostředí.
* Aplikace spuštěná na ploše nebo v zařízení může uložit připojovací řetězec do souboru **app.config** nebo **web.config** . Přidejte připojovací řetězec do oddílu **appSettings** v těchto souborech.
* Aplikace, která běží v cloudové službě Azure, může uložit připojovací řetězec do [souboru schématu konfigurace služby Azure (. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)). Přidejte připojovací řetězec do části **ConfigurationSettings** konfiguračního souboru služby.

Uložení připojovacího řetězce do konfiguračního souboru usnadňuje aktualizaci připojovacího řetězce pro přepínání mezi [emulátorem úložiště Azurite](../common/storage-use-azurite.md) a účtem služby Azure Storage v cloudu. Musíte upravit připojovací řetězec tak, aby odkazoval na cílové prostředí.

Můžete použít [Configuration Manager Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) pro přístup k připojovacímu řetězci za běhu bez ohledu na to, kde je aplikace spuštěná.

## <a name="configure-a-connection-string-for-azurite"></a>Konfigurace připojovacího řetězce pro Azurite

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o Azurite najdete v tématu [použití emulátoru Azurite pro vývoj místních Azure Storage](../common/storage-use-azurite.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurace připojovacího řetězce pro účet služby Azure Storage

Pro vytvoření připojovacího řetězce pro účet úložiště Azure použijte následující formát. Určete, jestli se chcete k účtu úložiště připojit prostřednictvím protokolu HTTPS (doporučeno) nebo HTTP, nahraďte `myAccountName` názvem svého účtu úložiště a nahraďte `myAccountKey` ho klíčem pro přístup k účtu:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Například váš připojovací řetězec může vypadat podobně jako:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

I když Azure Storage podporuje HTTP i HTTPS v připojovacím řetězci, *důrazně se doporučuje protokol HTTPS*.

> [!TIP]
> Připojovací řetězce účtu úložiště najdete v [Azure Portal](https://portal.azure.com). V okně nabídky účtu úložiště přejděte na **Nastavení**  >  **přístupové klíče** a zobrazte připojovací řetězce pro primární i sekundární přístupový klíč.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Vytvoření připojovacího řetězce pomocí sdíleného přístupového podpisu

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Vytvoření připojovacího řetězce pro explicitní koncový bod úložiště

Místo používání výchozích koncových bodů můžete v připojovacím řetězci zadat explicitní koncové body služby. Chcete-li vytvořit připojovací řetězec, který určuje explicitní koncový bod, zadejte úplný koncový bod služby pro každou službu, včetně specifikace protokolu (HTTPS (doporučeno) nebo HTTP) v následujícím formátu:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jedním z situací, kdy byste měli chtít zadat explicitní koncový bod, je, že jste namapovali koncový bod služby Blob Storage na [vlastní doménu](../blobs/storage-custom-domain-name.md). V takovém případě můžete v připojovacím řetězci zadat vlastní koncový bod pro úložiště objektů BLOB. Volitelně můžete zadat výchozí koncové body pro ostatní služby, pokud je vaše aplikace používá.

Tady je příklad připojovacího řetězce, který určuje explicitní koncový bod pro Blob service:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Tento příklad určuje explicitní koncové body pro všechny služby, včetně vlastní domény pro Blob service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Hodnoty koncového bodu v připojovacím řetězci slouží k vytvoření identifikátorů URI žádosti pro služby úložiště a k diktování formuláře všech identifikátorů URI, které jsou vráceny do kódu.

Pokud jste namapovali koncový bod úložiště na vlastní doménu a tento koncový bod vynecháte z připojovacího řetězce, nebudete moct tento připojovací řetězec použít pro přístup k datům v této službě z vašeho kódu.

Další informace o konfiguraci vlastní domény pro Azure Storage najdete v tématu [Mapování vlastní domény na koncový bod Azure Blob Storage](../blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Hodnoty koncového bodu služby v připojovacích řetězcích musí být identifikátory URI ve správném formátu, včetně `https://` (doporučeno) nebo `http://` .

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Vytvoření připojovacího řetězce s příponou koncového bodu

K vytvoření připojovacího řetězce pro službu úložiště v oblastech nebo instancích s různými příponami koncových bodů, například pro Azure Čína 21Vianet nebo Azure Government, použijte následující formát připojovacího řetězce. Určete, jestli se chcete k účtu úložiště připojit prostřednictvím protokolu HTTPS (doporučeno) nebo HTTP, nahraďte `myAccountName` názvem svého účtu úložiště, nahraďte `myAccountKey` klíčem pro přístup k účtu a nahraďte `mySuffix` příponou URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Tady je příklad připojovacího řetězce pro služby úložiště v Azure Čína 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analýza připojovacího řetězce

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Další kroky

* [Použití emulátoru Azurite pro vývoj místních Azure Storage](../common/storage-use-azurite.md)
* [Azure Storage Explorer](storage-explorers.md)
* [Použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md)