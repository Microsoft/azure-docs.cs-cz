---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254594"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Vytvoření koncového bodu TCP pro virtuální počítač
Aby měl virtuální počítač přístup k SQL Serveru z internetu, musí mít koncový bod pro poslech příchozí komunikace TCP. Tento krok konfigurace Azure směruje příchozí provoz portu TCP na port TCP, který je pro virtuální počítač přístupný.

> [!NOTE]
> Pokud se chcete připojit v rámci stejné cloudové službě nebo virtuální sítě, není nutné vytvořit veřejně přístupném koncovém bodu. V takovém případě můžete pokračovat dalším krokem. Další informace najdete v tématu věnovaném [scénářům připojení](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. Na webu Azure Portal vyberte **Virtuální počítače (Classic)** .
2. Potom vyberte virtuální počítač SQL Server.
3. Vyberte **Koncové body** a potom v horní části okna Koncové body klikněte na tlačítko **Přidat**.
   
    ![Kroky pro vytvoření koncového bodu na portálu](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. V okně **Přidat koncový bod** zadejte **název**, například SQLEndpoint.
5. Vyberte **TCP** v poli **Protokol**.
6. V poli **Veřejný port** zadejte číslo portu, například **57500**.
7. V poli **Privátní port** zadejte port SQL Serveru pro naslouchání. Výchozí nastavení je **1433**.
8. Kliknutím na tlačítko **OK** vytvořte koncový bod.

