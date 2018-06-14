---
title: Zřízení fondu Azure Batch ve virtuální síti | Microsoft Docs
description: Fondu služby Batch můžete vytvořit ve virtuální síti, aby výpočetní uzly bezpečně komunikovat s ostatních virtuálních počítačů v síti, jako je například souborový server.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/05/2018
ms.author: danlep
ms.openlocfilehash: 5a06ad5086a42bb00147e085227f3c71c357544e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846804"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Vytvoření fondu Azure Batch ve virtuální síti


Při vytváření fondu Azure Batch můžete zřídit fondu v podsíti [virtuální síť Azure](../virtual-network/virtual-networks-overview.md) (VNet), které zadáte. Tento článek vysvětluje, jak nastavit fondu služby Batch ve virtuální síti. 



## <a name="why-use-a-vnet"></a>Proč používat virtuální síť?


Fondu Azure Batch má nastavení, které povolí výpočetní uzly komunikaci mezi sebou – například můžete spouštět úlohy s více instancemi. Tato nastavení nevyžadují samostatné virtuální sítě. Ve výchozím nastavení, ale uzlů nemůže komunikovat s virtuálních počítačů, které nejsou součástí fondu Batch, například licenčního serveru nebo souborového serveru. Povolit fondu výpočetních uzlů pro bezpečnou komunikaci s jinými virtuálními počítači nebo k místní síti, můžete zřídit fondu v podsíti virtuální sítí VNet Azure. 



## <a name="prerequisites"></a>Požadavky

* **Ověřování**: Pokud chcete použít virtuální síť Azure klientské rozhraní API služby Batch musí používat ověřování pomocí Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md). 

* **Síť Azure**. Předem připravit virtuální síť, jednu nebo více podsítí, můžete portál Azure, Azure PowerShell, rozhraní příkazového řádku Azure (CLI) nebo jiné metody. Vytvoření virtuální sítě založené na Azure Resource Manager naleznete v tématu [vytvořit virtuální síť](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Vytvoření klasické virtuální sítě, naleznete v části [vytvoření virtuální sítě (klasické) s několika podsítěmi](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>Požadavky na virtuální síť
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Vytvoření fondu pomocí virtuální sítě na portálu

Po vytvoření virtuální sítě a přiřazené podsítě, můžete vytvořit fondu služby Batch pomocí této virtuální sítě. Postupujte podle těchto kroků můžete vytvořit fond z portálu Azure: 



1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném, oblasti, skupinu prostředků obsahující virtuální sítě, který chcete použít. 
2. V **nastavení** okno na levé straně vyberte **fondy** položku nabídky.
3. V **fondy** vyberte **přidat** příkaz.
4. Na **přidat fond** okně možnost, že máte v úmyslu použít z **typ obrázku** rozevíracího seznamu. 
5. Vyberte správný **vydavatele nebo nabídka nebo Sku** pro vlastní bitovou kopii.
6. Zadejte zbývající požadované nastavení, včetně **velikost uzlu**, **cílové uzly vyhrazené**, a **nízkou prioritu uzly**, stejně jako všechny potřeby volitelná nastavení.
7. V **virtuální sítě**, vyberte virtuální síť a podsíť, které chcete použít.
  
  ![Přidejte fond s virtuální sítě](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Trasy definované uživatelem pro vynucené tunelování

Můžete chtít požadavky ve vaší organizaci přesměrování (vynutit) internetový provoz z podsítě zpět do místního umístění pro kontrolu a protokolování. Může povolíte vynucené tunelování pro podsítě ve vaší virtuální síti. 

Aby se zajistilo, že výpočetní uzly fondu Azure Batch fungovat ve virtuální síti, která obsahuje vynucené tunelování povolena, je nutné přidat následující [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) pro tuto podsíť:

* Služba Batch musí komunikovat s fondu výpočetních uzlů pro plánování úloh. Chcete-li povolit tuto komunikaci, přidejte trasu uživatelem definované pro každou IP adresu, používá služba Batch v oblasti, kde existuje vašeho účtu Batch. Pokud chcete získat seznam IP adres služby Batch, kontaktujte prosím podporu Azure.

* Ujistěte se, že odchozí přenosy do služby Azure Storage (konkrétně adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, a `<account>.blob.core.windows.net`) není blokován prostřednictvím vaší místní síťové zařízení.

Když přidáte trasy definované uživatelem, definujte trasy pro každou relaci předponu adresy Batch IP a nastavte **typ dalšího směrování** k **Internet**. Podívejte se na následující příklad:

![Trasa definovaná uživatelem](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Další postup

- Podrobnější přehled služby Batch, najdete v tématu [rozsáhlé paralelní vývoj výpočetní řešení pomocí služby Batch](batch-api-basics.md).
- Další informace o vytváření trasy definované uživatelem, naleznete v části [vytvořit trasy definované uživatelem – portál Azure](../virtual-network/tutorial-create-route-table-portal.md).
