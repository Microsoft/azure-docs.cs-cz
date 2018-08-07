---
title: Konfigurace připojovacího řetězce pro službu Azure Storage | Dokumentace Microsoftu
description: Nakonfigurujte připojovací řetězec pro účet úložiště Azure. Připojovací řetězec obsahuje informace potřebné k autorizaci přístupu k účtu úložiště z aplikace za běhu.
services: storage
author: craigshoemaker
ms.service: storage
ms.topic: article
ms.date: 04/12/2017
ms.author: cshoe
ms.component: common
ms.openlocfilehash: 1c6ba727bf9c19b5da0d0d0313377d5d4ad1e2ac
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530847"
---
# <a name="configure-azure-storage-connection-strings"></a>Nakonfigurování připojovacích řetězců Azure Storage

Připojovací řetězec obsahuje ověřovací informace požadované pro vaší aplikaci přístup k datům v účtu služby Azure Storage za běhu. Můžete nakonfigurovat připojovací řetězce:

* Připojte k emulátoru úložiště Azure.
* Přístup k účtu úložiště v Azure.
* Přístup k zadaným prostředkům v Azure pomocí sdíleného přístupového podpisu (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Uložení připojovacího řetězce
Vaše aplikace potřebuje pro přístup k připojovací řetězec za běhu k autorizaci požadavků na služby Azure Storage. Máte několik možností pro uložení připojovacího řetězce:

* Je spuštěna aplikace na ploše nebo na zařízení můžete uložit připojovací řetězec **app.config** nebo **web.config** souboru. Přidat připojovací řetězec k **AppSettings** části v těchto souborech.
* Aplikace běžící v rámci cloudové služby Azure můžete uložit připojovací řetězec [soubor schématu (.cscfg) konfigurace služby Azure](https://msdn.microsoft.com/library/ee758710.aspx). Přidat připojovací řetězec k **ConfigurationSettings** oddílu služby konfiguračního souboru.
* Připojovací řetězec můžete použít přímo v kódu. Doporučujeme však připojovací řetězec uložit v konfiguračním souboru ve většině scénářů.

Uložení připojovacího řetězce v konfiguračním souboru umožňuje snadno aktualizovat připojovací řetězec přepnutí mezi emulátorem úložiště a účtu služby Azure storage v cloudu. Stačí upravit připojovací řetězec tak, aby odkazoval na vaše cílové prostředí.

Můžete použít [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) pro přístup k připojovacího řetězce za běhu bez ohledu na to, kde je aplikace spuštěná.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Vytvořit připojovací řetězec pro emulátor úložiště
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o emulátoru úložiště najdete v tématu [použití emulátoru úložiště Azure pro vývoj a testování](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Vytvořit připojovací řetězec pro účet úložiště Azure
Vytvoření připojovacího řetězce pro váš účet úložiště Azure, použijte následující formát. Označuje, zda chcete připojit k účtu úložiště prostřednictvím protokolu HTTPS (doporučeno) nebo HTTP, nahraďte `myAccountName` s názvem účtu úložiště, a nahraďte `myAccountKey` přístupovým klíčem vašeho účtu:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Připojovací řetězec může například vypadat podobně jako:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

I když Azure Storage podporuje protokol HTTP i HTTPS v připojovacím řetězci *HTTPS se důrazně doporučuje*.

> [!TIP]
> Můžete najít účet úložiště připojovací řetězce v [webu Azure portal](https://portal.azure.com). Přejděte do **nastavení** > **přístupové klíče** v nabídce okno účet úložiště a zobrazte si připojovací řetězce pro obě primární a sekundární přístupové klíče.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Vytvoření připojovacího řetězce, pomocí sdíleného přístupového podpisu
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Vytvoření připojovacího řetězce pro koncový bod explicitního úložiště
Můžete zadat koncové body služby explicitní v připojovacím řetězci namísto použití výchozí koncové body. Vytvoření připojovacího řetězce, který určuje explicitní koncový bod, zadejte koncový bod dokončení služby pro jednotlivé služby včetně specifikace protokolu (HTTPS (doporučeno) nebo HTTP), v následujícím formátu:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jeden scénář, kde budete chtít zadat explicitní koncový bod je, když jste změnili koncový bod služby Blob storage do [vlastní doménu](../blobs/storage-custom-domain-name.md). V takovém případě můžete zadat vlastní koncový bod pro úložiště objektů Blob v připojovacím řetězci. Volitelně můžete zadat výchozí koncové body pro ostatní služby, pokud je vaše aplikace používá.

Tady je příklad připojovacího řetězce, který určuje explicitní koncový bod služby Blob service pro:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Tento příklad určuje explicitní koncových bodů pro všechny služby, včetně vlastní domény pro službu Blob service:

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

Hodnoty koncového bodu v připojovacím řetězci slouží k vytvoření žádosti o identifikátory URI ke službám úložiště a určovat formu všechny identifikátory URI, které jsou vráceny do vašeho kódu.

Pokud jste změnili koncový bod úložiště na vlastní doménu a vynechat možnost tohoto koncového bodu z připojovacího řetězce, pak nebudete moci použít tento připojovací řetězec pro přístup k datům v dané služby v kódu.

> [!IMPORTANT]
> Hodnoty koncového bodu služby v připojovacích řetězců musí být ve správném formátu identifikátorů URI, včetně `https://` (doporučeno) nebo `http://`. Vzhledem k tomu, že úložiště Azure zatím nepodporuje protokol HTTPS pro vlastní domény, které *musí* zadejte `http://` pro libovolný identifikátor URI, který odkazuje na vlastní doménu pro koncový bod.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Vytvoření připojovacího řetězce s příponou koncového bodu
Vytvoření připojovacího řetězce pro službu storage v oblasti nebo instance s přípony jiný koncový bod jako pro Azure China nebo Azure Government, použijte následující formát připojovacího řetězce. Označuje, zda chcete připojit k účtu úložiště prostřednictvím protokolu HTTPS (doporučeno) nebo HTTP, nahraďte `myAccountName` s názvem účtu úložiště, nahraďte `myAccountKey` se přístupový klíč účtu a nahradit `mySuffix` s příponou identifikátor URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Tady je příklad připojovacího řetězce pro služby storage v Azure Čína:

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
* [Průzkumníci služby Azure Storage](storage-explorers.md)
* [Použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md)

