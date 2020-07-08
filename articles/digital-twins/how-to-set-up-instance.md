---
title: Vytvoření instance služby Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby Azure Digital revlákens.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4cac7a3f663d9ede966b8d6e5753c48629049dcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057479"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

Tento článek vás provede základními kroky pro nastavení nové instance digitálních vláken Azure. To zahrnuje vytvoření instance a přiřazení oprávnění [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) k instanci pro sebe sama.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

V dalším kroku vytvoříte novou skupinu prostředků Azure, která bude používat v tomto postupu. Pak můžete **vytvořit novou instanci digitálních vláken Azure** v rámci této skupiny prostředků. 

Budete taky muset zadat název vaší instance a vybrat oblast pro nasazení. Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> Název nové instance musí být v rámci oblasti jedinečný (to znamená, že pokud se zvolený název už používá v jiné instanci digitálních vláken Azure v této oblasti, je nutné vybrat jiný název).

Vytvořte skupinu prostředků a instanci pomocí následujících příkazů:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

Výsledek těchto příkazů vypadá nějak takto a vypisuje informace o prostředcích, které jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

Poznamenejte si název *hostitele*, *název*a *zdroj zdrojové* instance služby Azure Digital revlákens z výstupu. Jedná se o všechny klíčové hodnoty, které možná budete potřebovat, když budete pokračovat v práci s instancí digitálních vláken Azure, abyste mohli nastavit ověřování a související prostředky Azure.

> [!TIP]
> Tyto vlastnosti můžete zobrazit spolu se všemi vlastnostmi instance kdykoli spuštěním `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Přiřadit Azure Active Directory oprávnění

Digitální vlákna Azure používá [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) pro řízení přístupu na základě role (RBAC). To znamená, že před tím, než můžete vytvořit datovou rovinu vaší instance digitálního vlákna Azure, je nutné nejprve přiřadit roli s těmito oprávněními.

Aby bylo možné používat digitální vlákna Azure v rámci klientské aplikace, musíte také zajistit, aby se klientská aplikace mohla ověřit proti digitálním vazbám Azure. K tomu je potřeba nastavit registraci aplikace Azure Active Directory (AAD), kterou si můžete přečíst v tématu [Postupy: ověření klientské aplikace](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Přiřazení role

Pomocí e-mailu přidruženého k tenantovi služby AAD ve vašem předplatném Azure můžete vytvořit přiřazení role pro sebe sami v instanci digitálních vláken Azure. 

Abyste to mohli udělat, musíte být ve svém předplatném Azure klasifikováni jako vlastník. Můžete to ověřit spuštěním `az role assignment list --assignee <your-Azure-email>` příkazu a ověřením výstupu, který je *RoleDefinitionName* hodnotou *Owner (vlastník*). Pokud zjistíte, že je hodnota *Přispěvatel* nebo jiný než *vlastník*, obraťte se na správce předplatného s výkonem, abyste udělili oprávnění ve vašem předplatném, aby bylo možné roli zvýšit.

Jako vlastník předplatného můžete k přiřazení uživatele k roli vlastníka pro instanci digitálního vlákna Azure použít následující příkaz:

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
