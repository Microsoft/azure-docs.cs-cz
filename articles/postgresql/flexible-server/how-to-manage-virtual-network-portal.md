---
title: Správa virtuálních sítí – Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Vytvoření a správa virtuálních sítí pro Azure Database for PostgreSQL flexibilní Server pomocí Azure Portal
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d5b60cfeb3bfce1253ece3708e6b48ce6bcb0b4f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226901"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Vytvoření a správa virtuálních sítí pro Azure Database for PostgreSQL flexibilní Server pomocí Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní Server podporuje dva typy vzájemně se vylučujících metod síťového připojení, které se připojují k flexibilnímu serveru. Tyto dvě možnosti jsou následující:

* Veřejný přístup (povolené IP adresy)
* Privátní přístup (integrace virtuální sítě)

V tomto článku se zaměříme na vytváření PostgreSQL serveru pomocí **privátního přístupu (Integration VNET)** pomocí Azure Portal. Pomocí privátního přístupu (Integration VNet) můžete nasadit flexibilní Server do svého prostředí [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. V případě privátního přístupu jsou připojení k serveru PostgreSQL omezená na vaši virtuální síť. Další informace o této službě najdete v tématu [privátní přístup (Integration VNET)](./concepts-networking.md#private-access-vnet-integration).

Flexibilní server můžete nasadit do virtuální sítě a podsítě při vytváření serveru. Po nasazení flexibilního serveru ho nemůžete přesunout do jiné virtuální sítě ani podsítě ani pro něj nastavit *veřejný přístup (povolené IP adresy)* .

## <a name="prerequisites"></a>Požadavky
K vytvoření flexibilního serveru ve virtuální síti potřebujete:
- [Virtuální síť](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Virtuální síť a podsíť by měly být ve stejné oblasti a předplatném jako flexibilní Server.

-  [Delegování podsítě](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) do **Microsoft. DBforPostgreSQL/flexibleServers**. Toto delegování znamená, že tuto podsíť můžou používat jenom Azure Database for PostgreSQL flexibilní servery. V delegované podsíti nemůžou být žádné jiné typy prostředků Azure.
-  Přidejte `Microsoft.Storage` do koncového bodu služby pro podsíť delegovanou na flexibilní servery. K tomu je potřeba provést následující kroky:
     1. Přejít na stránku Virtual Network.
     2. Vyberte virtuální síť, ve které plánujete nasazení flexibilního serveru.
     3. Vyberte podsíť delegovanou pro flexibilní Server.
     4. Na obrazovce vyžádané replikace v části **koncový bod služby** vyberte možnost `Microsoft.storage` z rozevíracího seznamu.
     5. Uložte změny.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Vytvoření Azure Database for PostgreSQL-flexibilního serveru v už existující virtuální síti

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).
2. Vyberte **databáze**  >  **Azure Database for PostgreSQL**. Službu můžete vyhledat také zadáním **PostgreSQL** do vyhledávacího pole.
3. Jako možnost nasazení vyberte **flexibilní Server** .
4. Vyplňte formulář **základy** .
5. Chcete-li nakonfigurovat, jak se chcete připojit k serveru, přejdete na kartu **sítě** .
6. V části **způsob připojení** vyberte **privátní přístup (integrace virtuální sítě)**. Přejít na **Virtual Network** a vyberte už existující *virtuální síť* a *podsíť* , která se vytvořila v rámci výše uvedených požadavků.
7. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte flexibilní konfiguraci serveru.
8. Vyberte **Vytvořit**, aby se server zřídil. Zřizování může trvat několik minut.

>[!Note]
> Po nasazení flexibilního serveru do virtuální sítě a podsítě ho nemůžete přesunout do veřejného přístupu (povolených IP adres).
## <a name="next-steps"></a>Další kroky
- [Vytvářejte a spravujte Azure Database for PostgreSQLově flexibilní serverovou serverovou síť pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Další informace o [sítích v Azure Database for PostgreSQL-flexibilním serveru](./concepts-networking.md)
- Další informace o [Azure Database for PostgreSQL flexibilní serverovou virtuální síť](./concepts-networking.md#private-access-vnet-integration).