---
title: Vytváření vlastních aktivačních událostí v Azure Data Factory
description: Naučte se, jak vytvořit vlastní Trigger v Azure Data Factory, který spouští kanál, v reakci na vlastní událost publikovanou do Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e372808250b1d5b21cd5f6c5226922d5d6ad0f77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609293"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Vytvoření triggeru, který spouští kanál v reakci na vlastní událost (Preview)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje vlastní triggery událostí, které můžete vytvořit ve svých Data Factorych kanálech.

Architektura založená na událostech (EDA) je běžný vzor pro integraci dat, který zahrnuje produkční, detekci, spotřebu a reakci na události. Scénáře integrace dat často vyžadují Data Factory zákazníky, aby mohli aktivovat kanály na základě určitých událostí. Data Factory nativní integrace s [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) teď pokrývá [vlastní události](../event-grid/custom-topics.md): zákazníci odesílají libovolné události do tématu Event gridu a Data Factory se přihlásili a naslouchají v tématu a triggery se odpovídajícím způsobem.

> [!NOTE]
> Integrace popsaná v tomto článku závisí na [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Ujistěte se, že vaše předplatné je zaregistrované u poskytovatele prostředků Event Grid. Další informace najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Musíte být schopni provést akci *Microsoft. EventGrid/eventSubscriptions/**. Tato akce je součástí předdefinované role EventGrid EventSubscription přispěvatele.

Kromě toho můžou zákazníci s kombinací parametrů kanálu a triggeru vlastních událostí analyzovat a odkazovat na _vlastní datovou datovou část v_ průběhu spuštění kanálu. _datové_ pole ve vlastní datové části události je struktura hodnot klíč-hodnota, která poskytuje zákazníkům maximální kontrolu nad spuštěním kanálu řízených událostmi.

> [!IMPORTANT]
> V datové části vlastní události může být vždy chybějící klíč, na který se odkazuje v Parametrizace. _Spuštění triggeru_ se nezdaří s chybou, což uvádí, že výraz nelze vyhodnotit, protože neexistuje vlastnost _KeyName_ . Událost neaktivuje __žádné__ _spuštění kanálu_ .

## <a name="setup-event-grid-custom-topic"></a>Nastavení Event Grid vlastní téma

Chcete-li použít aktivační událost vlastní události v Data Factory, je třeba _nejprve_ nastavit [vlastní téma v Event Grid](../event-grid/custom-topics.md). Pracovní postup se liší od triggeru události úložiště, kde Data Factory nastaví téma za vás. Tady potřebujete přejít na Azure Event Grid a vytvořit téma sami. Další informace o tom, jak vytvořit vlastní téma, najdete v tématu kurzy Azure Event Gridového [portálu](../event-grid/custom-topics.md#azure-portal-tutorials) a kurzy k rozhraní příkazového [řádku](../event-grid/custom-topics.md#azure-cli-tutorials) .

Datové továrny očekávají, že události budou následovat [Event Grid schématu události](../event-grid/event-schema.md). Ujistěte se, že datové části události obsahují následující pole.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

V této části se dozvíte, jak vytvořit Trigger události úložiště v uživatelském rozhraní Azure Data Factory.

1. Přepněte na kartu **Upravit** , která je zobrazená se symbolem tužky.

1. V nabídce vyberte **aktivační událost** a pak vyberte **Nová/upravit**.

1. Na stránce **Přidat aktivační události** vyberte **zvolit aktivační událost...** a pak vyberte **+ Nová**.

1. Vybrat **vlastní události** typu Trigger

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Snímek obrazovky se stránkou autora pro vytvoření nového vlastního triggeru událostí v Data Factory uživatelském rozhraní." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. V rozevíracím seznamu předplatné Azure vyberte vlastní téma nebo ručně zadejte obor témat události.

   > [!NOTE]
   > Pokud chcete vytvořit novou nebo upravit existující Trigger vlastní události, účet Azure použitý k přihlášení do Data Factory a publikování triggeru události úložiště musí mít v tématu příslušné oprávnění řízení přístupu na základě role (Azure RBAC). Žádná další oprávnění nejsou vyžadována: instanční objekt pro Azure Data Factory _nepotřebuje zvláštní_ oprávnění pro Event Grid. Další informace o řízení přístupu najdete v části [řízení přístupu na základě rolí](#role-based-access-control) .

1. **Předmět začíná** na konci a **předmět s** vlastnostmi umožňuje filtrovat události, pro které chcete aktivovat kanál. Obě vlastnosti jsou volitelné.

1. Pomocí **+ New** přidejte **typy událostí** , které chcete filtrovat. Vlastní Trigger události: zaměstnanec nebo vztah pro seznam: Pokud vlastní událost má vlastnost _EventType_ , která odpovídá jakémukoli uvedenému seznamu, aktivuje se spuštění kanálu. Typ události rozlišuje velká a malá písmena. Například na následujícím snímku obrazovky se Trigger shoduje se všemi událostmi _copycompleted_ nebo _copysucceeded_ s předmětem, který začíná na _faktorech_ .

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Snímek stránky pro úpravu triggeru pro vysvětlení typů událostí a filtrování předmětu v Data Factory uživatelském rozhraní.":::

1. Vlastní Trigger událostí může analyzovat a odesílat vlastní _datovou_ datovou část vašeho kanálu. Nejprve vytvořte parametry kanálu a vyplňte hodnoty na stránce **parametry** . Použijte format **@triggerBody (). Event. data._keyName_** k analýze datové části dat a předání hodnot do parametrů kanálu. Podrobné vysvětlení najdete v tématu [metadata triggeru odkazů v Prokanálech](how-to-use-trigger-parameterization.md) a [systémových proměnných ve vlastní aktivační](control-flow-system-variables.md#custom-event-trigger-scope) události.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Snímek obrazovky s nastavením parametrů kanálu":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Snímek obrazovky se stránkou parametrů pro referenční datovou část v vlastní události":::

1. Až budete hotovi, klikněte na **OK** .

## <a name="json-schema"></a>Schéma JSON

Následující tabulka poskytuje přehled prvků schématu, které souvisejí s vlastními triggery událostí:

| **Element JSON** | **Popis** | **Typ** | **Povolené hodnoty** | **Povinné** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **oboru** | ID prostředku Azure Resource Manager tématu Event gridu. | Řetězec | ID Azure Resource Manager | Yes |
| **událost** | Typ událostí, které způsobují, že se aktivační událost aktivuje. | Pole řetězců    |  | Ano, je očekávána alespoň jedna hodnota. |
| **subjectBeginsWith** | Pole předmětu musí začínat vzorem poskytnutým pro aktivaci triggeru. Například `factories` aktivuje Trigger jenom pro událost, která začíná na `factories` . | Řetězec   | | No |
| **subjectEndsWith** | Pole předmětu musí končit vzorem poskytnutým pro aktivaci triggeru. | Řetězec   | | No |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure Data Factory používá řízení přístupu na základě role Azure (Azure RBAC) k zajištění toho, aby neautorizovaný přístup mohl naslouchat, odebírat aktualizace a aktivovat kanály propojené s vlastními událostmi, jsou čistě zakázané.

* Aby bylo možné úspěšně vytvořit novou nebo aktualizovat existující Trigger vlastní události, účet Azure přihlášený k Data Factory musí mít odpovídající přístup k příslušnému účtu úložiště. V opačném případě se operace nezdařila se _zakázaným přístupem_.
* Data Factory nepotřebuje žádná zvláštní oprávnění k vašemu Event Grid a vy _nemusíte_ přiřazovat zvláštní oprávnění Azure RBAC pro Data Factory instančního objektu pro tuto operaci.

Konkrétně zákazník potřebuje oprávnění _Microsoft. EventGrid/EventSubscriptions/Write_ pro _/Subscriptions/# # # #/resourceGroups//# # # #/Providers/Microsoft.EventGrid/topics/someTopics_

## <a name="next-steps"></a>Další kroky

* Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#trigger-execution).
* Informace o tom, jak odkazovat na metadata triggeru v kanálu, najdete v tématu [metadata triggeru odkazů v běhu kanálu](how-to-use-trigger-parameterization.md) .
