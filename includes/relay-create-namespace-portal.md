---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210190"
---
1. Přihlaste se k webu [Azure Portal][Azure portal].
1. Vyberte **vytvořit prostředek**. Pak vyberte **Integrace** > **Přenos**. Pokud se v seznamu **Přenos** nezobrazuje, vyberte v pravém horním rohu **Zobrazit vše**.
1. Vyberte **vytvořit**a do pole **název** zadejte název oboru názvů. Azure Portal zkontroluje, jestli je název k dispozici.
1. Vyberte předplatné Azure, ve kterém chcete vytvořit obor názvů.
1. V poli [Skupina prostředků](../articles/azure-resource-manager/manage-resource-groups-portal.md)vyberte existující skupinu prostředků, do které se má obor názvů umístit, nebo vytvořte novou.  
1. Vyberte zemi nebo oblast, ve které by měl být hostovaný obor názvů.

    ![Vytvořit obor názvů][create-namespace]

1. Vyberte **Vytvořit**. Azure Portal vytvoří obor názvů a povolí ho. Po několika minutách systém zřídí prostředky pro váš účet.

### <a name="get-management-credentials"></a>Získání přihlašovacích údajů pro správu

1. Vyberte **všechny prostředky**a pak zvolte nově vytvořený název oboru názvů.
1. Vyberte **zásady sdíleného přístupu**.  
1. V části **Zásady sdíleného přístupu** vyberte **RootManageSharedAccessKey**.
1. V **části zásady SAS: RootManageSharedAccessKey**vyberte tlačítko **Kopírovat** vedle **primárního připojovacího řetězce**. Tato akce zkopíruje připojovací řetězec do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
1. Zopakujte předchozí krok, zkopírujte si hodnotu **primárního klíče** a vložte ji do dočasného umístění pro pozdější použití.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
