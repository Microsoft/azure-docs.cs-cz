---
title: Monitorování diagnostických protokolů prostřednictvím Azure Monitor
description: Tento článek ukazuje, jak směrovat a zobrazovat diagnostické protokoly prostřednictvím Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 290b68b90deb669f70fa708e6c3bda83cb7187c0
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227785"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorování diagnostických protokolů Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure monitor](/azure/azure-monitor/overview) vám umožní monitorovat metriky a diagnostické protokoly, které vám pomůžou pochopit, jak vaše aplikace provádí. Podrobný popis této funkce a informace o tom, proč byste chtěli použít Azure Media Services metriky a diagnostické protokoly, najdete v tématu [monitorování metrik Media Services a diagnostických protokolů](monitor-media-services.md).

V tomto článku se dozvíte, jak směrovat data do účtu úložiště a pak je zobrazit.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet Media Services](../account-create-how-to.md).
- Zkontrolujte  [Media Services monitorování](monitor-media-services.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Směrování dat do účtu úložiště pomocí portálu

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte na účet Media Services v části **monitorování** a klikněte na **nastavení diagnostiky** . Tady se zobrazí seznam všech prostředků ve vašem předplatném, které generují data monitorování prostřednictvím služby Azure Monitor.

    ![Snímek obrazovky, který zvýrazní nastavení diagnostiky v části monitorování.](../media/media-services-diagnostic-logs/logs01.png)

1. Klikněte na **Přidat nastavení diagnostiky**.

   Nastavení diagnostiky prostředků je definice toho, *jaká* data monitorování se mají směrovat z konkrétního prostředku a *kam* se mají směrovat.

1. V části, která se zobrazí, zadejte **název** nastavení a zaškrtněte políčko u možnosti **Archivovat do účtu úložiště**.

    Vyberte účet úložiště, do kterého chcete odeslat protokoly, a stiskněte **OK**.
1. Zaškrtněte všechna políčka v částech **Protokol** a **Metrika**. V závislosti na typu prostředku může být k dispozici pouze jedna z těchto možností. Tato zaškrtávací políčka řídí, jaké kategorie dat protokolů a metrik dostupné pro daný typ prostředku se odesílají do vybraného cíle, v tomto případě do účtu úložiště.

   ![Část Nastavení diagnostiky](../media/media-services-diagnostic-logs/logs02.png)
1. Nastavte posuvník **Doba uchování (dny)** na hodnotu 30. Tento posuvník nastavuje počet dnů, po který se mají data monitorování uchovávat v účtu úložiště. Azure Monitor automaticky odstraní data starší než zadaný počet dnů. Pokud jako dobu uchování nastavíte 0 dnů, data se uloží navždy.
1. Klikněte na **Uložit**.

Data monitorování z vašeho prostředku se teď přenášejí do účtu úložiště.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Směrování dat do účtu úložiště pomocí Azure CLI

Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště, spusťte následující `az monitor diagnostic-settings` příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Například:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Zobrazit data v účtu úložiště pomocí portálu

Pokud jste postupovali podle předchozích kroků, data se začala přenášet do vašeho účtu úložiště.

Na zobrazení události v účtu úložiště možná budete muset počkat až pět minut.

1. Na portálu přejděte do části **Účty úložiště**, kterou najdete v levém navigačním panelu.
1. Identifikujte účet úložiště, který jste vytvořili v předchozí části, a klikněte na něj.
1. Klikněte na **objekty blob** a potom na kontejneru s názvem **Insights-logs-keydeliveryrequests**. Toto je kontejner, který obsahuje vaše protokoly. Data monitorování se rozdělí do kontejnerů podle ID prostředku a pak podle data a času.
1. Kliknutím na kontejnery pro ID prostředku, datum a čas přejděte k souboru PT1H.json. Klikněte na soubor PT1H.json a pak klikněte na **Stáhnout**.

 Teď můžete zobrazit událost JSON, která se uložila do účtu úložiště.

### <a name="examples-of-pt1hjson"></a>Příklady PT1H.js

#### <a name="clear-key-delivery-log"></a>Vymazat protokol doručení klíčů

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Protokol doručení šifrovaného klíče Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="see-also"></a>Viz také

* [Metriky Azure Monitoru](/azure/azure-monitor/data-platform)
* [Protokoly diagnostiky Azure Monitor](/azure/azure-monitor/essentials/platform-logs-overview)
* [Jak shromažďovat a využívat data protokolu z vašich prostředků Azure](/azure/azure-monitor/essentials/platform-logs-overview)

## <a name="next-steps"></a>Další kroky

[Monitorování metrik](media-services-metrics-howto.md)
