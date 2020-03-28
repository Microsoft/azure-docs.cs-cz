---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76020840"
---
1. Přihlaste se k [portálu Azure][Azure portal].
1. Vyberte **Vytvořit prostředek**. Potom vyberte **integrační** > **relé**. Pokud se v seznamu **Přenos** nezobrazuje, vyberte v pravém horním rohu **Zobrazit vše**.
1. Vyberte **Vytvořit**a do pole **Název** zadejte název oboru názvů. Azure Portal zkontroluje, jestli je název k dispozici.
1. Zvolte předplatné Azure, ve kterém chcete vytvořit obor názvů.
1. Ve [skupině prostředků](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)zvolte existující skupinu prostředků, do které chcete umístit obor názvů, nebo vytvořte novou.  
1. Vyberte zemi nebo oblast, ve které má být obor názvů hostován.

    ![Vytvoření oboru názvů][create-namespace]

1. Vyberte **Vytvořit**. Portál Azure vytvoří obor názvů a povolí ho. Po několika minutách systém zřídí prostředky pro váš účet.

### <a name="get-management-credentials"></a>Získání přihlašovacích údajů pro správu

1. Vyberte **Všechny prostředky**a pak zvolte nově vytvořený název oboru názvů.
1. Vyberte **zásady sdíleného přístupu**.  
1. V části **Zásady sdíleného přístupu** vyberte **RootManageSharedAccessKey**.
1. V části **Zásady SAS: RootManageSharedAccessKey**vyberte tlačítko **Kopírovat** vedle **primárního připojovacího řetězce**. Tato akce zkopíruje připojovací řetězec do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
1. Zopakujte předchozí krok, zkopírujte si hodnotu **primárního klíče** a vložte ji do dočasného umístění pro pozdější použití.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
