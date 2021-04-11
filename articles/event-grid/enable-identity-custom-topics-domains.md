---
title: Povolení spravované identity na Azure Event Grid vlastní témata a domény
description: Tento článek popisuje, jak povolit identitu spravované služby pro Azure Event Grid vlastní téma nebo doménu.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278214"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Přiřazení identity spravované systémem k Event Grid vlastnímu tématu nebo doméně 
V tomto článku se dozvíte, jak povolit systémově spravovanou identitu pro Event Grid vlastní téma nebo doménu. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Povolit identitu v době vytvoření

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Můžete povolit identitu přiřazenou systémem pro vlastní téma nebo doménu při jejím vytváření v Azure Portal. Následující obrázek ukazuje, jak povolit identitu spravovanou systémem pro vlastní téma. V podstatě vyberte možnost **Povolit identitu přiřazenou systému** na stránce **Upřesnit** v Průvodci vytvořením tématu. Tato možnost se zobrazí také na stránce **Upřesnit** v Průvodci vytvořením domény. 

![Povolit identitu při vytváření vlastního tématu](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Pomocí rozhraní příkazového řádku Azure můžete také vytvořit vlastní téma nebo doménu s identitou přiřazenou systémem. Použijte `az eventgrid topic create` příkaz s `--identity` parametrem nastaveným na `systemassigned` . Pokud hodnotu pro tento parametr nezadáte, použije se výchozí hodnota `noidentity` . 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Podobně můžete pomocí `az eventgrid domain create` příkazu vytvořit doménu s identitou spravovanou systémem.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Povolení identity pro existující vlastní téma nebo doménu
V této části se dozvíte, jak povolit identitu spravovanou systémem pro existující vlastní téma nebo doménu. 

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Následující postup ukazuje, jak povolit identitu spravovanou systémem pro vlastní téma. Postup pro povolení identity pro doménu je podobný. 

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V horní části panelu hledání vyhledejte **témata Event gridu** .
3. Vyberte **vlastní téma** , pro které chcete spravovanou identitu povolit. 
4. Přepněte na kartu **Identita** . 
5. **Zapnutím přepínače povolte identitu** . 
1. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Stránka identita pro vlastní téma"::: 

Podobný postup můžete použít k povolení identity pro doménu služby Event Grid.

### <a name="use-the-azure-cli"></a>Použití Azure CLI
Pomocí `az eventgrid topic update` příkazu s `--identity` nastavením na `systemassigned` Povolte identitu přiřazenou systémem pro existující vlastní téma. Pokud chcete identitu zakázat, zadejte `noidentity` hodnotu. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Příkaz pro aktualizaci existující domény je podobný ( `az eventgrid domain update` ).


## <a name="next-steps"></a>Další kroky
Přidejte identitu do příslušné role (například Service Bus odesílatel dat) v cíli (například Service Bus Queue). Podrobný postup najdete v tématu [udělení spravované identity přístup k Event Grid cíli](add-identity-roles.md). 
