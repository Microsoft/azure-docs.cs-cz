---
title: Nakonfigurovat připojovací řetězec pro Azure Storage | Microsoft Docs
description: Nakonfigurujte připojovací řetězec pro účet úložiště Azure. Připojovací řetězec obsahuje informace potřebné k ověření přístupu k účtu úložiště z vaší aplikace za běhu.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: dee0fd68ccaf80ff6e44ee10fa24bb19e220d0d5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240825"
---
# <a name="configure-azure-storage-connection-strings"></a>Nakonfigurování připojovacích řetězců Azure Storage

Připojovací řetězec obsahuje informace o ověřování, které jsou potřebné pro vaši aplikaci pro přístup k datům v účtu Azure Storage za běhu. Můžete nakonfigurovat připojovací řetězce pro:

* Připojte k emulátoru úložiště Azure.
* Přístup k účtu úložiště v Azure.
* Přístup k zadané prostředky v Azure pomocí sdíleného přístupového podpisu (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Ukládání připojovací řetězec
Aplikace musí pro přístup k připojovací řetězec za běhu k ověřování žádostí odeslaných do služby Azure Storage. Máte několik možností pro ukládání připojovací řetězec:

* Je spuštěna aplikace na ploše nebo na zařízení můžete uložit připojovací řetězec **app.config** nebo **web.config** souboru. Přidat připojovací řetězec k **AppSettings** část v těchto souborech.
* Aplikace běžící v cloudové službě Azure můžete uložit připojovací řetězec [konfigurační soubor schématu (.cscfg) Azure služby](https://msdn.microsoft.com/library/ee758710.aspx). Přidat připojovací řetězec k **ConfigurationSettings** oddíl konfiguračního souboru služby.
* Můžete vytvořit připojovací řetězec přímo v kódu. Doporučujeme však připojovací řetězec uložit v konfiguračním souboru ve většině scénářů.

Ukládání připojovací řetězec v konfiguračním souboru usnadňuje aktualizovat připojovací řetězec pro přepínání emulátor úložiště a účet úložiště Azure v cloudu. Potřebujete upravit připojovací řetězec tak, aby odkazoval na cílovém prostředí.

Můžete použít [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) pro přístup k připojovací řetězec za běhu bez ohledu na to, kde je aplikace spuštěna.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Vytvořit připojovací řetězec pro emulátor úložiště
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o emulátoru úložiště najdete v tématu [pomocí emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Vytvoření připojovacího řetězce pro účet úložiště Azure
Vytvoření připojovacího řetězce pro váš účet úložiště Azure, použijte následující formát. Označuje, zda se chcete připojit k účtu úložiště prostřednictvím protokolu HTTPS (doporučeno) nebo protokol HTTP, nahraďte `myAccountName` s názvem účtu úložiště a nahraďte `myAccountKey` vaším přístupovým klíčem účet:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Například připojovací řetězec může vypadat podobně jako:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

I když Azure Storage podporuje protokoly HTTP a HTTPS v připojovacím řetězci, *HTTPS se důrazně doporučuje*.

> [!TIP]
> Můžete najít váš účet úložiště připojovací řetězce v [portál Azure](https://portal.azure.com). Přejděte na **nastavení** > **přístupové klíče** v okně účtu úložiště nabídky Zobrazit připojovací řetězce pro obě primární a sekundární přístupové klíče.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Vytvoření připojovacího řetězce pomocí sdíleného přístupového podpisu
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Vytvoření připojovacího řetězce pro koncový bod explicitní úložiště
Koncové body explicitní služby můžete zadat v připojovací řetězec místo použití výchozí koncové body. Pokud chcete vytvořit připojovací řetězec, který určuje explicitní koncový bod, zadejte koncový bod dokončení služby pro jednotlivé služby včetně specifikace protokolu (HTTPS (doporučeno) nebo HTTP), v následujícím formátu:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jeden scénář, kde můžete chtít zadat explicitní koncový bod je, když jste změnili koncový bod úložiště objektů Blob [vlastní domény](../blobs/storage-custom-domain-name.md). V takovém případě můžete zadat svůj vlastní koncový bod pro úložiště objektů Blob v připojovacím řetězci. Volitelně můžete zadat výchozí koncové body pro jiné služby, pokud je vaše aplikace používá.

Tady je příklad připojovacího řetězce, který určuje explicitní koncový bod služby objektů Blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Tento příklad určuje explicitní koncových bodů pro všechny služby, včetně vlastní doménu pro služby objektů Blob:

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

Hodnoty koncového bodu v připojovacím řetězci slouží k vytvoření požadavku identifikátory URI služby storage a stanovují formu všechny identifikátory URI, které jsou vráceny do vašeho kódu.

Pokud jste změnili koncový bod úložiště vlastní domény a vynechejte tohoto koncového bodu z připojovacího řetězce, pak nebude možné použít tento připojovací řetězec pro přístup k datům v této službě z vašeho kódu.

> [!IMPORTANT]
> Hodnoty koncového bodu služby v připojovací řetězce musí být ve správném formátu identifikátory URI, včetně `https://` (doporučeno) nebo `http://`. Protože Azure Storage ještě nepodporuje protokol HTTPS pro vlastní domény, můžete *musí* zadejte `http://` pro libovolný koncový bod identifikátor URI, který odkazuje na vlastní doménu.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Vytvoření připojovacího řetězce s příponou koncový bod
Vytvoření připojovacího řetězce pro službu úložiště v oblasti nebo instancí s přípon jinému koncovému bodu, například pro Azure China nebo Azure Government, použijte následující formát řetězec připojení. Označuje, zda se chcete připojit k účtu úložiště prostřednictvím protokolu HTTPS (doporučeno) nebo protokol HTTP, nahraďte `myAccountName` s názvem účtu úložiště, nahraďte `myAccountKey` se přístupový klíč účtu a nahraďte `mySuffix` s příponou URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Tady je příklad řetězce připojení pro služby úložiště v Číně Azure:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analýza připojovacího řetězce
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Další postup
* [Použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md)
* [Azure průzkumníci úložiště](storage-explorers.md)
* [Použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md)

