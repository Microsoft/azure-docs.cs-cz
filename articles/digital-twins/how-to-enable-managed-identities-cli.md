---
title: Povolení spravované identity pro události směrování (Preview) – CLI
titleSuffix: Azure Digital Twins
description: Podívejte se, jak povolit systémově přiřazenou identitu pro digitální vlákna Azure a použít ji k přeposílání událostí pomocí Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202956"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Povolit spravovanou identitu pro směrování událostí digitálních vláken Azure (Preview): Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Tento článek popisuje, jak povolit [identitu přiřazenou systémem pro instanci digitálních vláken Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (aktuálně ve verzi Preview) a používat identitu při předávání událostí do podporovaných cílů, jako je [centrum událostí](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   umístění a [kontejner Azure Storage](../storage/blobs/storage-blobs-introduction.md).

Tento článek vás provede procesem pomocí [**Azure CLI**](/cli/azure/what-is-azure-cli).

Tady je postup, který je popsaný v tomto článku: 

1. Vytvořte instanci digitálních vláken Azure s identitou systému nebo povolte identitu přiřazenou systémem v existující instanci digitálních vláken Azure. 
1. Přidejte k identitě příslušnou roli nebo role. Například přiřaďte roli **odesilatele dat centra událostí Azure** k identitě, pokud je koncový bod centrem událostí, nebo **Azure Service Bus role odesílatel dat** , pokud je koncový bod Service Bus.
1. Vytvořte koncový bod v digitálních událostech Azure, které můžou k ověřování používat identity přiřazené systémem.

## <a name="enable-system-managed-identities-for-an-instance"></a>Povolení identit spravovaných systémem pro instanci 

Když v instanci digitálního vlákna Azure povolíte identitu přiřazenou systémem, Azure pro ni automaticky vytvoří identitu ve službě [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Tuto identitu pak můžete použít k ověření koncových bodů digitálních vláken Azure pro předávání událostí.

V **rámci počátečního nastavení instance** můžete povolit identitu spravované systémem pro instanci digitálního vlákna Azure, nebo **ji později povolit v instanci, která již existuje**.

Kterákoli z těchto metod vytváření poskytne stejné možnosti konfigurace a stejný konečný výsledek pro vaši instanci. Tato část popisuje, jak provést obojí.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Přidání identity spravované systémem během vytváření instance

V této části se dozvíte, jak povolit systémově spravovanou identitu na instanci digitálních vláken Azure, která se právě vytváří. 

To se provádí přidáním `--assign-identity` parametru do `az dt create` příkazu, který se používá k vytvoření instance. (Další informace o tomto příkazu najdete v [referenční dokumentaci](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) nebo v [obecných pokynech k nastavení instance digitálního vlákna Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).)

Chcete-li vytvořit instanci s identitou spravované systémem, přidejte  `--assign-identity` parametr takto:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Přidání identity spravované systémem do existující instance

V této části přidáte identitu spravovanou systémem do instance digitálního vlákna Azure, která už existuje.

To je také provedeno s `az dt create` příkazem a `--assign-identity` parametrem. Namísto zadání nového názvu instance, kterou chcete vytvořit, můžete zadat název instance, která již existuje, chcete-li aktualizovat hodnotu `--assign-identity` pro danou instanci.

Příkaz pro **Povolení** spravované identity je stejný jako příkaz pro vytvoření instance s identitou spravované systémem. Všechny tyto změny jsou hodnotou parametru název instance:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Chcete-li **Zakázat** spravovanou identitu na instanci, kde je aktuálně povolena, použijte následující podobný příkaz k nastavení `--assign-identity` na `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Přiřazení rolí Azure k identitě 

Jakmile se pro instanci digitálních vláken Azure vytvoří identita přiřazená systémem, bude nutné přiřadit příslušné role k ověřování pomocí různých typů [koncových bodů](concepts-route-events.md) pro předávání událostí do podporovaných cílů. Tato část popisuje možnosti role a jejich přiřazení k identitě přiřazené systémem.

>[!NOTE]
> Toto je důležitý krok – bez něj nebude mít identita přístup k vašim koncovým bodům a události nebudou doručeny.

### <a name="supported-destinations-and-azure-roles"></a>Podporované cíle a role Azure 

Tady jsou minimální role, které identita potřebuje pro přístup ke koncovému bodu v závislosti na typu cíle. Budou fungovat i role s vyššími oprávněními (jako jsou role vlastníka dat).

| Cíl | Role Azure |
| --- | --- |
| Azure Event Hubs | Odesilatel dat Event Hubs Azure |
| Azure Service Bus | Azure Service Bus odesílatel dat |
| Kontejner úložiště Azure | Přispěvatel dat v objektech blob služby Storage |

Další informace o koncových bodech, trasách a typech cílů podporovaných pro směrování v rámci digitálních vláken Azure najdete v tématu [*Koncepty: trasy událostí*](concepts-route-events.md).

### <a name="assign-the-role"></a>Přiřazení role

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Můžete přidat parametr do `--scopes` `az dt create` příkazu, aby bylo možné přiřadit identitu k jednomu nebo více oborům se zadanou rolí. To lze použít při prvním vytvoření instance nebo později předáním názvu instance, která již existuje.

Tady je příklad, který vytvoří instanci se systémem spravovanou identitou a přiřadí tuto identitu vlastní roli volanou `MyCustomRole` v centru událostí.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Další příklady přiřazení rolí pomocí tohoto příkazu najdete v [dokumentaci **AZ DT Create** reference](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create).

Alternativně můžete k vytváření a správě rolí použít také skupinu příkazů [**AZ role Assignment**](/cli/azure/role/assignment) . Tato funkce se dá použít k podpoře dalších scénářů, u kterých nechcete seskupovat přiřazení role pomocí příkazu CREATE.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Vytvoření koncového bodu s ověřováním na základě identity

Po nastavení systému spravované identity pro instanci digitálních vláken Azure a přiřazení odpovídajících rolí můžete vytvořit [koncové body](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) digitálních vláken Azure, které umožňují použití identity pro ověřování. Tato možnost je k dispozici pouze pro centra událostí a koncové body typu Service Bus (není podporováno pro Event Grid).

>[!NOTE]
> Nelze upravit koncový bod, který již byl vytvořen s identitou založenou na klíči, aby bylo možné změnit ověřování na základě identity. Typ ověřování je nutné zvolit při prvním vytvoření koncového bodu.

K tomu je potřeba přidat `--auth-type` parametr do `az dt endpoint create` příkazu, který se používá k vytvoření koncového bodu. (Další informace o tomto příkazu najdete v [referenční dokumentaci](/cli/azure/ext/azure-iot/dt/endpoint/create) nebo v [obecných pokynech k nastavení koncového bodu digitálních vláken Azure](how-to-manage-routes-apis-cli.md#create-the-endpoint).)

Chcete-li vytvořit koncový bod, který používá ověřování na základě identity, zadejte `IdentityBased` typ ověřování s  `--auth-type` parametrem. Následující příklad znázorňuje tuto chybu pro Event Hubs koncový bod.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Předpoklady pro zakázání identit spravovaných systémem

Vzhledem k tomu, že identita je spravovaná odděleně od koncových bodů, které ji používají, je důležité vzít v úvahu důsledky, které mohou mít změny identity nebo její role na koncových bodech v instanci digitálních vláken Azure. Pokud je identita zakázaná nebo je z ní odebrána potřebná role koncového bodu, koncový bod může být nepřístupný a tok událostí bude přerušen.

Pokud chcete pokračovat v používání koncového bodu, který byl nastavený se spravovanou identitou, která je teď zakázaná, musíte koncový bod odstranit a [znovu ho vytvořit](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) s jiným typem ověřování. Po této změně může trvat až hodinu, než se události obnoví v doručení do koncového bodu.

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných identitách v Azure AD: 
* [*Spravované identity pro prostředky Azure*](../active-directory/managed-identities-azure-resources/overview.md)