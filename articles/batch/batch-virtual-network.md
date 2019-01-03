---
title: Zřízení fondu ve virtuální síti – Azure Batch | Dokumentace Microsoftu
description: Postup vytvoření fondu služby Batch ve virtuální síti Azure tak, aby výpočetních uzlů můžete zabezpečeně komunikovat s jiným virtuálním počítačům v síti, například souborový server.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 840c9f4115c13052c65e5975ed22aab7737ea2fe
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543901"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Vytvoření fondu služby Azure Batch ve virtuální síti

Při vytváření fondu Azure Batch můžete zřídit fond v podsíti [virtuální síť Azure](../virtual-network/virtual-networks-overview.md) (VNet), který zadáte. Tento článek vysvětluje, jak vytvořit fond služby Batch ve virtuální síti. 

## <a name="why-use-a-vnet"></a>Proč používat virtuální síť?

Fond služby Azure Batch má nastavení, které povolí výpočetních uzlů komunikovat mezi sebou – například, spustíte úkoly s více instancemi. Tato nastavení nevyžadují samostatnou virtuální síť. Ve výchozím nastavení, ale uzlů nemůže komunikovat s virtuální počítače, které nejsou součástí fondu služby Batch, jako je například licenční server nebo souborovým serverem. Povolit fondu výpočetních uzlů k bezpečné komunikaci s jinými virtuálními počítači, nebo v místní síti, můžete zřídit fond v podsíti virtuální síť Azure. 

## <a name="prerequisites"></a>Požadavky

* **Ověřování**: Pokud chcete použít virtuální síť Azure klientské rozhraní API služby Batch musí používat ověřování pomocí Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md). 

* **Virtuální síť Azure**. Následující části pro požadavky na virtuální síť a konfigurace. Virtuální síť, jednu nebo více podsítí připravit předem, můžete na webu Azure portal, prostředí Azure PowerShell, rozhraní příkazového řádku Azure (CLI) nebo jiné metody.  
  * Vytvoření virtuální sítě založené na Azure Resource Manageru najdete v tématu [vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Virtuální sítě založené na Resource Manager se doporučuje pro nová nasazení a je podporován pouze ve fondech v konfiguraci virtuálního počítače.
  * Pokud chcete vytvořit klasickou virtuální síť, naleznete v tématu [vytvoření virtuální sítě (klasické) s několika podsítěmi](../virtual-network/create-virtual-network-classic.md). Klasické virtuální sítě je podporována pouze ve fondech v konfiguraci cloudové služby.

## <a name="vnet-requirements"></a>Požadavky na virtuální síť

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Vytvoření fondu pomocí virtuální sítě na portálu

Po vytvoření virtuální sítě a přiřazené podsítě, můžete vytvořit fond služby Batch pomocí této virtuální síti. Postupujte podle těchto kroků můžete vytvořit fond z portálu Azure portal: 

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupinu prostředků obsahující virtuální síť, kterou chcete použít. 
2. V **nastavení** na levé straně vyberte okno **fondy** položky nabídky.
3. V **fondy** okna, vyberte **přidat** příkazu.
4. Na **přidat fond** okna, vyberte možnost máte v úmyslu použít z **typ obrázku** rozevíracího seznamu. 
5. Vyberte správné **vydavatel/nabídka/Sku** pro vaši vlastní image.
6. Zadejte zbývající požadovaná nastavení, včetně **velikost uzlu**, **cílové vyhrazené uzly**, a **s nízkou prioritou uzly**, stejně jako všechny požadované volitelná nastavení.
7. V **virtuální sítě**, vyberte virtuální síť a podsíť, kterou chcete použít.
  
  ![Přidat fond s virtuální sítí](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Trasy definované uživatelem pro vynucené tunelování

Budete muset požadavky ve vaší organizaci přesměrování (vynutit) internetový provoz z podsítě zpět na vaše místní umístění pro účely zkoumání a protokolování. Možná jste povolili, vynucené tunelové propojení podsítí ve virtuální síti. 

Pokud chcete mít jistotu, že vaše výpočetní uzly fondu Azure Batch fungovat ve virtuální síti, která má vynucené tunelování povolena, je nutné přidat následující [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) pro tuto podsíť:

* Služba Batch potřebuje ke komunikaci s fondu výpočetních uzlů pro plánování úloh. Pokud chcete povolit tuto komunikaci, přidejte trasy definované uživatelem pro každou IP adresu, která používá službu Batch v oblasti, které se nachází váš účet Batch. Pokud chcete získat seznam IP adres služby Batch, kontaktujte podporu Azure.

* Zkontrolujte, že odchozí provoz do služby Azure Storage (konkrétně adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, a `<account>.blob.core.windows.net`) není blokovaný prostřednictvím vaší místní síti zařízení.

Při přidání trasy definované uživatelem, definovat trasu pro každou související předponu adresy IP služby Batch a nastavit **typem dalšího segmentu směrování** k **Internet**. Prohlédněte si následující příklad:

![Trasa definovaná uživatelem](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Další postup

- Podrobný přehled služby Batch, najdete v části [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
- Další informace o vytváření trasy definované uživatelem, najdete v článku [trasy definované uživatelem – Azure portal vytvořit](../virtual-network/tutorial-create-route-table-portal.md).
