---
title: Správa virtuálních sítí – Azure Portal-Azure Database for MySQL-flexibilní Server
description: Vytvoření a správa virtuálních sítí pro Azure Database for MySQL flexibilní Server pomocí Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4906ce9f562910f0a087cd25167457ec1fb301ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109987"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Vytvoření a správa virtuálních sítí pro Azure Database for MySQL flexibilní Server pomocí Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Flexibilní server Azure Database for MySQL podporuje dva typy vzájemně se vylučujících metod síťového připojení k flexibilnímu serveru. Tyto dvě možnosti jsou následující:

- Veřejný přístup (povolené IP adresy)
- Privátní přístup (integrace virtuální sítě)

V tomto článku se zaměříme na vytvoření serveru MySQL s **privátním přístupem (Integration VNET)** pomocí Azure Portal. Pomocí privátního přístupu (Integration VNet) můžete nasadit flexibilní Server do svého prostředí [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. V případě privátního přístupu se připojení k serveru MySQL omezují jenom na vaši virtuální síť. Další informace o této službě najdete v tématu [privátní přístup (Integration VNET)](./concepts-networking.md#private-access-vnet-integration).

Flexibilní server můžete nasadit do virtuální sítě a podsítě při vytváření serveru. Po nasazení flexibilního serveru ho nemůžete přesunout do jiné virtuální sítě ani podsítě ani pro něj nastavit *veřejný přístup (povolené IP adresy)* .

## <a name="prerequisites"></a>Požadavky
K vytvoření flexibilního serveru ve virtuální síti potřebujete:
- [Virtuální síť](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Virtuální síť a podsíť by měly být ve stejné oblasti a předplatném jako flexibilní Server.

-  [Delegovat podsíť](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) na **Microsoft.DBforMySQL/flexibleServers**. Toto delegování znamená, že danou podsíť můžou využívat pouze flexibilní servery Azure Database for MySQL. V delegované podsíti nemůžou být žádné jiné typy prostředků Azure.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Vytvoření Azure Database for MySQL flexibilního serveru v již existující virtuální síti

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).
2. Vyberte **databáze**  >  **Azure Database for MySQL**. Službu můžete vyhledat také zadáním **MySQL** do vyhledávacího pole.
3. Jako možnost nasazení vyberte **flexibilní Server** .
4. Vyplňte formulář **základy** .
5. Chcete-li nakonfigurovat, jak se chcete připojit k serveru, přejdete na kartu **sítě** .
6. V části **způsob připojení** vyberte **privátní přístup (integrace virtuální sítě)**. Přejít na **Virtual Network** a vyberte už existující *virtuální síť* a *podsíť* , která se vytvořila v rámci výše uvedených požadavků.
7. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte flexibilní konfiguraci serveru.
8. Vyberte **Vytvořit**, aby se server zřídil. Zřizování může trvat několik minut.

>[!Note]
> Po nasazení flexibilního serveru do virtuální sítě a podsítě ho nemůžete přesunout do veřejného přístupu (povolených IP adres).

## <a name="next-steps"></a>Další kroky
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md)
- Další informace o [Azure Database for MySQL flexibilní serverové virtuální síti](./concepts-networking.md#private-access-vnet-integration).
