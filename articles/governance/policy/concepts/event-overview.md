---
title: Reakce na události změny stavu Azure Policy
description: Použijte Azure Event Grid k přihlášení k odběru událostí zásad aplikací, které umožňují aplikacím reagovat na změny stavu bez nutnosti složitosti kódu.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735072"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Reakce na události změny stavu Azure Policy

Události Azure Policy umožňují aplikacím reagovat na změny stavu. Tato integrace se provádí bez nutnosti složitosti kódu nebo nákladných a neefektivních služeb cyklického dotazování. Místo toho se události odesílají prostřednictvím [Azure Event Grid](../../../event-grid/index.yml) předplatitelům, jako je [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml)nebo dokonce i vlastní naslouchací proces http.
V kritickém případě platíte jenom za to, co využijete.

Události Azure Policy jsou odesílány do Azure Event Grid, který poskytuje vašim aplikacím spolehlivé služby doručování prostřednictvím zásad opakovaného opakování a doručování nedoručených zpráv. Další informace najdete v tématu [Event Grid doručování zpráv a zkuste to znovu](../../../event-grid/delivery-and-retry.md).

Běžný scénář Azure Policy události je sledován při změně stavu dodržování předpisů u prostředku během vyhodnocování zásad. Architektura založená na událostech představuje účinný způsob, jak reagovat na tyto změny namísto kontroly stavu dodržování předpisů v rámci pevného plánu.

> [!NOTE]
> Azure Policy události změny stavu se odesílají do Event Grid poté, co [aktivační událost vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers) dokončí vyhodnocení prostředků.

Úplný kurz najdete v tématu [události změny stavu zásad směrování na Event Grid pomocí Azure CLI](../tutorials/route-state-change-events.md) .

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Event Grid model zdrojů a obslužných rutin" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Dostupné události Azure Policy

Event Grid používá [odběry událostí](../../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Odběry událostí Azure Policy můžou zahrnovat tři typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Je aktivována, když je vytvořen stav dodržování zásad. |
| Microsoft. PolicyInsights. PolicyStateChanged | Je aktivována, když se změní stav dodržování zásad. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Vyvolá se, když se odstraní stav dodržování zásad. |

## <a name="event-schema"></a>Schéma událostí

Azure Policy události obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Událost Azure Policy můžete identifikovat, pokud `eventType` vlastnost začíná na "Microsoft. PolicyInsights".
Další informace o použití vlastností Event Grid události jsou zdokumentovány v  
[Event Grid schéma událostí](../../../event-grid/event-schema.md).

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `id` | řetězec | Jedinečný identifikátor události |
| `topic` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Plně kvalifikované ID prostředku, pro který je změna stavu dodržování předpisů, včetně názvu prostředku a typu prostředku. Používá formát, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | object | Azure Policy data události. |
| `data.timestamp` | řetězec | Čas (ve standardu UTC), který byl prostředek zkontrolován nástrojem Azure Policy. Pro řazení událostí použijte tuto vlastnost namísto nejvyšší úrovně `eventTime` nebo `time` vlastností. |
| `data.policyAssignmentId` | řetězec | ID prostředku přiřazení zásady |
| `data.policyDefinitionId` | řetězec | ID prostředku definice zásady. |
| `data.policyDefinitionReferenceId` | řetězec | Referenční ID pro definici zásady v definici iniciativy, pokud je přiřazení zásady pro iniciativu. Může být prázdné. |
| `data.complianceState` | řetězec | Stav dodržování předpisů prostředku s ohledem na přiřazení zásady. |
| `data.subscriptionId` | řetězec | ID předplatného prostředku |
| `data.complianceReasonCode` | řetězec | Kód důvodu dodržování předpisů. Může být prázdné. |
| `eventType` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `eventTime` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `dataVersion` | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| `metadataVersion` | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Tady je příklad události změny stavu zásad:

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

Další informace najdete v tématu [schéma Azure Policych událostí](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají Azure Policy události, by měly dodržovat tyto Doporučené postupy:

> [!div class="checklist"]
> - Více předplatných lze nakonfigurovat tak, aby směrovalo události do stejné obslužné rutiny události, takže nemusíte předpokládat události z konkrétního zdroje. Místo toho zkontrolujte téma zprávy a ujistěte se, že přiřazení zásad, definice zásad a prostředek události změny stavu jsou pro.
> - `eventType`Zajistěte, aby všechny události, které jste obdrželi, byly typu a nepředpokládaly, které jsou očekávané.
> - Použijte `data.timestamp` k určení pořadí událostí v Azure Policy místo vlastností nejvyšší úrovně `eventTime` nebo `time` .
> - Pro přístup k prostředku, který má změnu stavu zásad, použijte pole Subject.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a podělte se o Azure Policy události změny stavu:

- [Události změny stavu zásad směrování pro Event Grid s využitím Azure CLI](../tutorials/route-state-change-events.md)
- [Podrobnosti Azure Policy schématu pro Event Grid](../../../event-grid/event-schema-policy.md)
- [Informace o službě Event Grid](../../../event-grid/overview.md)