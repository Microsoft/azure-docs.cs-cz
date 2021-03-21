---
title: Zřízení fondu ve virtuální síti
description: Vytvoření fondu služby Batch ve službě Azure Virtual Network, aby výpočetní uzly mohly bezpečně komunikovat s jinými virtuálními počítači v síti, jako je třeba souborový server.
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: d6e5de75164e098fc95f6c086d9f98a652dcee4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561905"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Vytvoření fondu Azure Batch ve virtuální síti

Když vytváříte fond Azure Batch, můžete fond zřídit v podsíti [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) , kterou zadáte. Tento článek vysvětluje, jak nastavit fond dávek ve virtuální síti.

## <a name="why-use-a-vnet"></a>Proč používat virtuální síť?

Výpočetní uzly ve fondu můžou vzájemně komunikovat, například spouštět úlohy s více instancemi, aniž by vyžadovaly samostatnou virtuální síť. Ve výchozím nastavení ale uzly ve fondu nemůžou komunikovat s virtuálními počítači, které jsou mimo fond, jako jsou například licenční servery nebo souborové servery.

Pokud chcete, aby výpočetní uzly komunikovaly zabezpečeně s ostatními virtuálními počítači nebo v místní síti, můžete fond zřídit v podsíti virtuální sítě Azure.

## <a name="prerequisites"></a>Předpoklady

- **Ověřování**. Pokud chcete použít virtuální síť Azure klientské rozhraní API služby Batch musí používat ověřování pomocí Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md).

- **Virtuální síť Azure**. V následující části najdete informace o požadavcích a konfiguraci virtuální sítě. Pokud chcete předem připravit virtuální síť s jednou nebo více podsítěmi, můžete použít Azure Portal, Azure PowerShell, rozhraní příkazového řádku (CLI) Azure Command-Line nebo jiné metody.
  - Pokud chcete vytvořit virtuální síť založenou na Azure Resource Manager, přečtěte si téma [vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Pro nová nasazení se doporučuje použít virtuální síť založená na Správce prostředků a je podporovaná jenom u fondů, které používají konfiguraci virtuálních počítačů.
  - Pokud chcete vytvořit klasickou virtuální síť, přečtěte si téma [vytvoření virtuální sítě (klasické) s několika podsítěmi](/previous-versions/azure/virtual-network/create-virtual-network-classic). Klasická virtuální síť je podporovaná jenom u fondů, které používají konfiguraci Cloud Services.

## <a name="vnet-requirements"></a>Požadavky na virtuální síť

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Vytvoření fondu pomocí virtuální sítě v Azure Portal

Jakmile vytvoříte virtuální síť a přiřadíte k ní podsíť, můžete vytvořit fond služby Batch s touto virtuální sítí. Pomocí těchto kroků vytvořte fond z Azure Portal: 

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupina prostředků obsahující virtuální síť, kterou hodláte použít.
2. V okně **Nastavení** na levé straně vyberte položku nabídky **fondy** .
3. V okně **fondy** vyberte **Přidat**.
4. V okně **Přidat fond** vyberte v rozevíracím seznamu **typ obrázku** možnost, kterou hodláte použít.
5. Vyberte pro vlastní image správného **vydavatele/nabídku/SKU** .
6. Zadejte zbývající požadovaná nastavení, včetně **velikosti uzlu**, **cílových vyhrazených uzlů** a uzlů s **nízkou prioritou**, a také všech požadovaných volitelných nastavení.
7. V **Virtual Network** vyberte virtuální síť a podsíť, které chcete použít.

   ![Přidat fond do virtuální sítě](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelování

Ve vaší organizaci můžete mít požadavky na přesměrování (vynucení) internetového provozu vázaného na Internet z podsítě zpátky do místního umístění pro kontrolu a protokolování. Kromě toho jste mohli povolit vynucené tunelování pro podsítě ve vaší virtuální síti.

Aby se zajistilo, že uzly ve vašem fondu fungují ve virtuální síti s povoleným vynuceným tunelovým propojením, musíte pro tuto podsíť přidat tyto [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) (udr):

- Služba Batch potřebuje komunikovat s uzly pro plánování úloh. Pokud chcete povolit tuto komunikaci, přidejte UDR pro každou IP adresu, kterou služba Batch používá, v oblasti, ve které existuje účet Batch. Seznam IP adres služby Batch najdete v tématu věnovaném místním [značkám služby](../virtual-network/service-tags-overview.md).

- Zajistěte, aby odchozí přenosy do Azure Storage (konkrétně adresy URL formuláře `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net` ) nejsou blokované vaší místní sítí.

Když přidáte UDR, definujte trasu pro každou související předponu IP adresy dávky a nastavte **typ dalšího segmentu směrování** na **Internet**.

![Trasa definovaná uživatelem](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> IP adresy služby Batch se můžou v průběhu času měnit. Aby nedocházelo k výpadkům z důvodu změny IP adresy, vytvořte proces pro automatické aktualizace IP adres služby Batch a udržujte je v tabulce směrování v aktuálním stavu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Naučte se [vytvořit trasu definovanou uživatelem v Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).
