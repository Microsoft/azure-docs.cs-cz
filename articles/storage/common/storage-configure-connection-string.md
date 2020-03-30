---
title: Konfigurace připojovacího řetězce
titleSuffix: Azure Storage
description: Nakonfigurujte připojovací řetězec pro účet úložiště Azure. Připojovací řetězec obsahuje informace potřebné k autorizaci přístupu k účtu úložiště z vaší aplikace za běhu pomocí autorizace sdíleného klíče.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268350"
---
# <a name="configure-azure-storage-connection-strings"></a>Nakonfigurování připojovacích řetězců Azure Storage

Připojovací řetězec obsahuje informace o autorizaci potřebné pro vaši aplikaci pro přístup k datům v účtu Úložiště Azure za běhu pomocí autorizace sdíleného klíče. Připojovací řetězce můžete nakonfigurovat takto:

* Připojte se k emulátoru úložiště Azure.
* Přístup k účtu úložiště v Azure.
* Přístup k určeným prostředkům v Azure prostřednictvím sdíleného přístupového podpisu (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Zobrazení a kopírování připojovacího řetězce

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Uložení připojovacího řetězce

Vaše aplikace potřebuje přístup k připojovacímu řetězci za běhu, aby autorizovala požadavky na Azure Storage. Máte několik možností pro uložení připojovacího řetězce:

* Připojovací řetězec můžete uložit do proměnné prostředí.
* Aplikace spuštěná na ploše nebo v zařízení může připojovací řetězec uložit do souboru **app.config** nebo **web.config.** Přidejte připojovací řetězec do části **AppSettings** v těchto souborech.
* Aplikace spuštěná v cloudové službě Azure může ukládat připojovací řetězec do [souboru schématu konfigurace služby Azure (.cscfg).](https://msdn.microsoft.com/library/ee758710.aspx) Přidejte připojovací řetězec do části **ConfigurationSettings** v konfiguračním souboru služby.

Uložení připojovacího řetězce v konfiguračním souboru usnadňuje aktualizaci připojovacího řetězce pro přepínání mezi emulátorem úložiště a účtem úložiště Azure v cloudu. Stačí upravit připojovací řetězec, aby přejděte na cílové prostředí.

Nástroj Microsoft [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) můžete použít k přístupu k připojovacímu řetězci za běhu bez ohledu na to, kde je vaše aplikace spuštěná.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Konfigurace připojovacího řetězce pro emulátor úložiště

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o emulátoru úložiště najdete v [tématu Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurace připojovacího řetězce pro účet úložiště Azure

Chcete-li vytvořit připojovací řetězec pro účet úložiště Azure, použijte následující formát. Určete, jestli se chcete připojit k účtu úložiště pomocí `myAccountName` protokolu HTTPS (doporučeno) nebo `myAccountKey` HTTP, nahraďte název účtu úložiště a nahraďte klíčem pro přístup k účtu:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Připojovací řetězec může například vypadat podobně jako:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Přestože Azure Storage podporuje protokol HTTP i HTTPS v připojovacím *řetězci, https se důrazně doporučuje*.

> [!TIP]
> Připojovací řetězce účtu úložiště najdete na [webu Azure Portal](https://portal.azure.com). Přejděte na **nastavení** > **Přístupové klávesy** v okně nabídky účtu úložiště zobrazíte připojovací řetězce pro primární i sekundární přístupové klíče.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Vytvoření připojovacího řetězce pomocí sdíleného přístupového podpisu

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Vytvoření připojovacího řetězce pro explicitní koncový bod úložiště

Můžete zadat explicitní koncové body služby v připojovacím řetězci namísto použití výchozích koncových bodů. Chcete-li vytvořit připojovací řetězec, který určuje explicitní koncový bod, zadejte koncový bod úplné služby pro každou službu, včetně specifikace protokolu (HTTPS (doporučeno) nebo HTTP), v následujícím formátu:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jeden scénář, kde můžete chtít zadat explicitní koncový bod je, když jste namapovali koncový bod úložiště objektů Blob na [vlastní doménu](../blobs/storage-custom-domain-name.md). V takovém případě můžete zadat vlastní koncový bod pro úložiště objektů Blob v připojovacím řetězci. Volitelně můžete zadat výchozí koncové body pro ostatní služby, pokud je vaše aplikace používá.

Tady je příklad připojovacího řetězce, který určuje explicitní koncový bod pro službu Objektů blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Tento příklad určuje explicitní koncové body pro všechny služby, včetně vlastní domény pro službu objektů Blob:

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

Hodnoty koncového bodu v připojovacím řetězci se používají k vytvoření identifikátorů URI požadavku na služby úložiště a k diktování formuláře všech identifikátorů URI, které jsou vráceny do kódu.

Pokud jste namapovali koncový bod úložiště na vlastní doménu a vynechejte tento koncový bod z připojovacího řetězce, nebudete moci tento připojovací řetězec použít k přístupu k datům v této službě z vašeho kódu.

> [!IMPORTANT]
> Hodnoty koncového bodu služby v připojovacích `https://` řetězcích musí `http://`být dobře tvarované identifikátory URI, včetně (doporučeno) nebo . Vzhledem k tomu, že Azure Storage *must* ještě `http://` nepodporuje protokol HTTPS pro vlastní domény, musíte zadat pro libovolný identifikátor URI koncového bodu, který odkazuje na vlastní doménu.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Vytvoření připojovacího řetězce s příponou koncového bodu

Chcete-li vytvořit připojovací řetězec pro službu úložiště v oblastech nebo instancích s různými příponami koncových bodů, například pro Azure China 21Vianet nebo Azure Government, použijte následující formát připojovacího řetězce. Určete, zda se chcete připojit k účtu úložiště prostřednictvím protokolu HTTPS (doporučeno) nebo HTTP, nahraďte `myAccountName` názvem účtu úložiště, nahraďte `myAccountKey` klíčem pro přístup k účtu a nahraďte `mySuffix` příponou URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Tady je příklad připojovacího řetězce pro služby úložiště v Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analýza připojovacího řetězce

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Další kroky

* [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md)
* [Průzkumníci azure úložiště](storage-explorers.md)
* [Použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
