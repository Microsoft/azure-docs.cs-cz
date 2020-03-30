---
title: Monitorování diagnostických protokolů mediálních služeb prostřednictvím nástroje Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak směrovat a zobrazit diagnostické protokoly prostřednictvím Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 4d4587c701a054828fc34785e2ae680fef47625d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382915"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorovat diagnostické protokoly služby Media Services

[Azure Monitor](../../azure-monitor/overview.md) umožňuje monitorovat metriky a diagnostické protokoly, které vám pomohou pochopit, jak si vaše aplikace vedou. Podrobný popis této funkce a zjistěte, proč byste chtěli používat protokoly metrik a diagnostiky Azure Media Services, najdete [v tématu Monitor Media Services metriky a diagnostické protokoly](media-services-metrics-diagnostic-logs.md).

Tento článek ukazuje, jak směrovat data do účtu úložiště a potom zobrazit data.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).
- Zkontrolujte [metriky a diagnostické protokoly služby Monitor Media Services](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Směrování dat do účtu úložiště pomocí portálu

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Přejděte na účet mediálních služeb v části Sledování a klepněte na **položku Nastavení** **diagnostiky**. Tady se zobrazí seznam všech prostředků ve vašem předplatném, které generují data monitorování prostřednictvím služby Azure Monitor.

    ![Část Nastavení diagnostiky](media/media-services-diagnostic-logs/logs01.png)

1. Klepněte na **tlačítko Přidat diagnostické nastavení**.

   Nastavení diagnostiky prostředků je definice toho, *jaká* data monitorování se mají směrovat z konkrétního prostředku a *kam* se mají směrovat.

1. V části, která se zobrazí, zadejte **název** nastavení a zaškrtněte políčko u možnosti **Archivovat do účtu úložiště**.

    Vyberte účet úložiště, do kterého chcete odesílat protokoly, a stiskněte **ok**.
1. Zaškrtněte všechna políčka v částech **Protokol** a **Metrika**. V závislosti na typu prostředku může být k dispozici pouze jedna z těchto možností. Tato zaškrtávací políčka řídí, jaké kategorie dat protokolů a metrik dostupné pro daný typ prostředku se odesílají do vybraného cíle, v tomto případě do účtu úložiště.

   ![Část Nastavení diagnostiky](media/media-services-diagnostic-logs/logs02.png)
1. Nastavte posuvník **Doba uchování (dny)** na hodnotu 30. Tento posuvník nastavuje počet dnů, po který se mají data monitorování uchovávat v účtu úložiště. Azure Monitor automaticky odstraní data starší než zadaný počet dnů. Pokud jako dobu uchování nastavíte 0 dnů, data se uloží navždy.
1. Klikněte na **Uložit**.

Data monitorování z vašeho prostředku se teď přenášejí do účtu úložiště.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Směrování dat do účtu úložiště pomocí azure cli

Chcete-li povolit ukládání diagnostických protokolů v `az monitor diagnostic-settings` účtu úložiště, spustíte následující příkaz azure CLI:

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

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Zobrazení dat v účtu úložiště pomocí portálu

Pokud jste postupovali podle předchozích kroků, data se začala přenášet do vašeho účtu úložiště.

Na zobrazení události v účtu úložiště možná budete muset počkat až pět minut.

1. Na portálu přejděte do části **Účty úložiště**, kterou najdete v levém navigačním panelu.
1. Identifikujte účet úložiště, který jste vytvořili v předchozí části, a klikněte na něj.
1. Klikněte na **objekty BLOB**, potom na kontejner s popiskem **insights-logs-keydeliveryrequests**. Toto je kontejner, který má protokoly v něm. Data monitorování jsou rozdělena do kontejnerů podle ID prostředku a potom podle data a času.
1. Kliknutím na kontejnery pro ID prostředku, datum a čas přejděte k souboru PT1H.json. Klikněte na soubor PT1H.json a pak klikněte na **Stáhnout**.

 Teď můžete zobrazit událost JSON, která se uložila do účtu úložiště.

### <a name="examples-of-pt1hjson"></a>Příklady PT1H.json

#### <a name="clear-key-delivery-log"></a>Vymazat protokol doručení klíče

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

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine šifrovaný protokol doručení klíče

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

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="see-also"></a>Viz také

* [Metriky azure monitoru](../../azure-monitor/platform/data-platform.md)
* [Diagnostické protokoly monitorování Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Jak shromažďovat a využívat data protokolu z prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Další kroky

[Monitorování metrik](media-services-metrics-howto.md)
