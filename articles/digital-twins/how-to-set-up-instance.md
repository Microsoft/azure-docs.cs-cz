---
title: Vytvoření instance digitálních vláken Azure
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby Azure Digital revlákens.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f097861f97e7ec62019fa631e2ac608d2e0c5024
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612870"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

Tento článek vás provede základními kroky pro nastavení nové instance digitálních vláken Azure. To zahrnuje vytvoření instance a přiřazení oprávnění [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) k instanci pro sebe sama.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

Potom spuštěním následujících příkazů vytvořte novou skupinu prostředků Azure, kterou použijete v tomto postupu, a pak vytvořte novou instanci digitálních vláken Azure v této skupině prostředků.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

> [!TIP]
> Pokud chcete vypsat seznam názvů oblastí Azure, které se dají předávat do příkazů v Azure CLI, spusťte tento příkaz:
> ```azurecli
> az account list-locations -o table
> ```
> Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Výsledek těchto příkazů vypadá nějak takto a vypisuje informace o prostředcích, které jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

Poznamenejte si název *hostitele*, *název*a *zdroj zdrojové* instance služby Azure Digital revlákens z výstupu. Jedná se o všechny klíčové hodnoty, které možná budete potřebovat, když budete pokračovat v práci s instancí digitálních vláken Azure, abyste mohli nastavit ověřování a související prostředky Azure.

> [!TIP]
> Tyto vlastnosti můžete zobrazit spolu se všemi vlastnostmi instance kdykoli spuštěním `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Přiřadit Azure Active Directory oprávnění

Digitální vlákna Azure používá [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) pro řízení přístupu na základě role (RBAC). To znamená, že před tím, než můžete vytvořit datovou rovinu vaší instance digitálního vlákna Azure, je nutné nejprve přiřadit roli s těmito oprávněními.

Aby bylo možné používat digitální vlákna Azure v rámci klientské aplikace, musíte také zajistit, aby se klientská aplikace mohla ověřit proti digitálním vazbám Azure. K tomu je potřeba nastavit registraci aplikace Azure Active Directory (AAD), kterou si můžete přečíst v tématu [Postupy: ověření klientské aplikace](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Přiřazení role

Pomocí e-mailu přidruženého k tenantovi služby AAD v předplatném Azure vytvořte přiřazení role pro sebe sama. Nejprve se ujistěte, že jste ve svém předplatném Azure klasifikováni jako vlastník. Pak můžete k přiřazení uživatele k roli vlastníka pro instanci digitálního vlákna Azure použít následující příkaz:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Výsledkem tohoto příkazu jsou informace o přiřazení role, kterou jste vytvořili.

> [!TIP]
> Pokud se zobrazí chyba *400: důvodu chybného požadavku* , spusťte následující příkaz, který získá *identifikátor objectID* pro uživatele:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Pak opakujte příkaz přiřazení role, a to pomocí *ID objektu* uživatele místo vašeho e-mailu.

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit a ověřit klientskou aplikaci pro práci s vaší instancí:
* [Postupy: ověření klientské aplikace](how-to-authenticate-client.md)
