---
title: Azure Policy jako zdroj Event Grid
description: Tento článek popisuje, jak použít Azure Policy jako zdroj události Event Grid. Poskytuje schéma a odkazy na články týkající se kurzu a postupů.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735057"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události [Azure Policy](../governance/policy/index.yml) . Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](./event-schema.md). Nabízí také seznam rychlých startů a kurzů pro použití Azure Policy jako zdroje událostí.

## <a name="available-event-types"></a>Dostupné typy událostí

Azure Policy emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Je aktivována, když je vytvořen stav dodržování zásad. |
| Microsoft. PolicyInsights. PolicyStateChanged | Je aktivována, když se změní stav dodržování zásad. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Vyvolá se, když se odstraní stav dodržování zásad. |

## <a name="example-event"></a>Příklad události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)
Následující příklad ukazuje schéma události vytvoření stavu zásad: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Schéma pro událost změněné stavu zásad je podobné: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma události vytvoření stavu zásad: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Schéma pro událost změněné stavu zásad je podobné: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Vlastnosti události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Plně kvalifikované ID prostředku, pro který je změna stavu dodržování předpisů, včetně názvu prostředku a typu prostředku. Používá formát, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `eventTime` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Azure Policy data události. |
| `dataVersion` | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| `metadataVersion` | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `source` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Plně kvalifikované ID prostředku, pro který je změna stavu dodržování předpisů, včetně názvu prostředku a typu prostředku. Používá formát, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `time` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Azure Policy data události. |
| `specversion` | řetězec | Verze specifikace schématu CloudEvents |

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `timestamp` | řetězec | Čas (ve standardu UTC), který byl prostředek zkontrolován nástrojem Azure Policy. Pro řazení událostí použijte tuto vlastnost namísto nejvyšší úrovně `eventTime` nebo `time` vlastností. |
| `policyAssignmentId` | řetězec | ID prostředku přiřazení zásady |
| `policyDefinitionId` | řetězec | ID prostředku definice zásady. |
| `policyDefinitionReferenceId` | řetězec | Referenční ID pro definici zásady v definici iniciativy, pokud je přiřazení zásady pro iniciativu. Může být prázdné. |
| `complianceState` | řetězec | Stav dodržování předpisů prostředku s ohledem na přiřazení zásady. |
| `subscriptionId` | řetězec | ID předplatného prostředku |
| `complianceReasonCode` | řetězec | Kód důvodu dodržování předpisů. Může být prázdné. |

## <a name="next-steps"></a>Další kroky

- Návod pro směrování Azure Policych událostí změny stavu naleznete v tématu [použití Event Grid pro oznamování změn stavu zásad](../governance/policy/tutorials/route-state-change-events.md).
- Přehled Integrace Azure Policy s Event Grid naleznete v tématu [reakce na Azure Policy události pomocí Event Grid](../governance/policy/concepts/event-overview.md).
- Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](./overview.md)
- Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](./subscription-creation-schema.md).