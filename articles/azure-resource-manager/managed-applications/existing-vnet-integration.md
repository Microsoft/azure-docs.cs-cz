---
title: Nasadit do existující virtuální sítě
description: Popisuje, jak povolit uživatelům spravované aplikace výběr existující virtuální sítě. Virtuální síť může být mimo spravovanou aplikaci.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84261286"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Použití existující virtuální sítě s Azure Managed Applications

V tomto článku se dozvíte, jak definovat spravovanou aplikaci Azure, která se integruje se stávající virtuální sítí v rámci předplatného uživatele. Spravovaná aplikace umožňuje příjemci rozhodnout se, jestli má vytvořit novou virtuální síť, nebo použít existující. Existující virtuální síť může být mimo spravovanou skupinu prostředků.

## <a name="main-template"></a>Hlavní šablona

Nejprve se podíváme na **mainTemplate.js** souboru. Níže je uvedena celá šablona pro nasazení virtuálního počítače a přidružených prostředků. Později budete podrobněji prozkoumávat části šablony, které souvisejí s používáním existující virtuální sítě.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Všimněte si, že virtuální síť je [podmíněně nasazená](../templates/conditional-resource-deployment.md). Příjemce předává hodnotu parametru, která indikuje, jestli se má vytvořit nová nebo použít stávající virtuální síť. Pokud příjemce vybere novou virtuální síť, prostředek se nasadí. V opačném případě se prostředek během nasazování přeskočí.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

Proměnná pro ID virtuální sítě má dvě vlastnosti. Jedna vlastnost vrací ID prostředku při nasazení nové virtuální sítě. Druhá vlastnost vrací ID prostředku při použití existující virtuální sítě. ID prostředku pro existující virtuální síť zahrnuje název skupiny prostředků, která obsahuje virtuální síť.

ID podsítě je tvořeno hodnotou ID virtuální sítě. Používá hodnotu pro výběr zákazníků.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

Síťové rozhraní je nastaveno na proměnnou ID podsítě.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Definice uživatelského rozhraní

Teď se podívejme na **createUiDefinition.js** souboru. Celý soubor je:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

Tento soubor obsahuje prvek virtuální sítě.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Tento prvek umožňuje uživateli vybrat buď novou, nebo existující virtuální síť.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Nová nebo existující virtuální síť":::

Do výstupů zahrnete hodnotu, která indikuje, jestli uživatel vybral novou nebo existující virtuální síť. Je zde také spravovaná hodnota identity.

> [!NOTE]
> Výstupní hodnota pro spravovanou identitu musí mít název **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Další kroky

Další informace o vytváření definičního souboru uživatelského rozhraní najdete v tématu [CreateUiDefinition.jspro prostředí pro vytváření spravované aplikace Azure](create-uidefinition-overview.md).
