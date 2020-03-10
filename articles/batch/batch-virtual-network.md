---
title: Zřízení fondu ve virtuální síti – Azure Batch | Microsoft Docs
description: Vytvoření fondu služby Batch ve službě Azure Virtual Network, aby výpočetní uzly mohly bezpečně komunikovat s jinými virtuálními počítači v síti, jako je třeba souborový server.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: bbe38a9dc7be749b8e138ff3ca9ec4f06255b389
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355360"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Vytvoření fondu Azure Batch ve virtuální síti

Když vytváříte fond Azure Batch, můžete fond zřídit v podsíti [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) , kterou zadáte. Tento článek vysvětluje, jak nastavit fond dávek ve virtuální síti. 

## <a name="why-use-a-vnet"></a>Proč používat virtuální síť?

Fond Azure Batch obsahuje nastavení, která umožňují komunikačním uzlům vzájemně komunikovat – například pro spouštění úkolů s více instancemi. Tato nastavení nevyžadují samostatnou virtuální síť. Ve výchozím nastavení ale uzly nemůžou komunikovat s virtuálními počítači, které nejsou součástí fondu dávek, například licenčního serveru nebo souborového serveru. Pokud chcete, aby výpočetní uzly fondu umožňovaly zabezpečeně komunikovat s jinými virtuálními počítači nebo s místní sítí, můžete fond zřídit v podsíti virtuální sítě Azure. 

## <a name="prerequisites"></a>Požadavky

* **Ověřování**: Pokud chcete použít virtuální síť Azure klientské rozhraní API služby Batch musí používat ověřování pomocí Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md). 

* **Virtuální síť Azure**. V následující části najdete informace o požadavcích a konfiguraci virtuální sítě. Pokud chcete připravit virtuální síť s jednou nebo více podsítěmi předem, můžete použít Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure (CLI) nebo jiné metody.  
  * Pokud chcete vytvořit virtuální síť založenou na Azure Resource Manager, přečtěte si téma [vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Pro nová nasazení se doporučuje použít virtuální síť založená na Správce prostředků a je podporovaná jenom ve fondech v konfiguraci virtuálního počítače.
  * Pokud chcete vytvořit klasickou virtuální síť, přečtěte si téma [vytvoření virtuální sítě (klasické) s několika podsítěmi](../virtual-network/create-virtual-network-classic.md). Klasická virtuální síť je podporovaná jenom ve fondech v konfiguraci Cloud Services.

## <a name="vnet-requirements"></a>Požadavky na virtuální síť

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Vytvoření fondu s virtuální sítí na portálu

Jakmile vytvoříte virtuální síť a přiřadíte k ní podsíť, můžete vytvořit fond služby Batch s touto virtuální sítí. Pomocí těchto kroků vytvořte fond z Azure Portal: 

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupina prostředků obsahující virtuální síť, kterou hodláte použít. 
2. V okně **Nastavení** na levé straně vyberte položku nabídky **fondy** .
3. V okně **fondy** vyberte příkaz **Přidat** .
4. V okně **Přidat fond** vyberte v rozevíracím seznamu **typ obrázku** možnost, kterou hodláte použít. 
5. Vyberte pro vlastní image správného **vydavatele/nabídku/SKU** .
6. Zadejte zbývající požadovaná nastavení, včetně **velikosti uzlu**, **cílových vyhrazených uzlů**a uzlů s **nízkou prioritou**, a také všech požadovaných volitelných nastavení.
7. V **Virtual Network**vyberte virtuální síť a podsíť, které chcete použít.
  
   ![Přidat fond do virtuální sítě](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelování

Ve vaší organizaci můžete mít požadavky na přesměrování (vynucení) internetového provozu vázaného na Internet z podsítě zpátky do místního umístění pro kontrolu a protokolování. Možná jste povolili vynucené tunelování pro podsítě ve vaší virtuální síti. 

Aby bylo zajištěno, že výpočetní uzly fondu Azure Batch fungují ve virtuální síti s povoleným vynuceným tunelovým propojením, je nutné pro tuto podsíť přidat následující [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) :

* Služba Batch potřebuje komunikovat s výpočetními uzly fondu pro úlohy plánování. Pokud chcete povolit tuto komunikaci, přidejte uživatelsky definovanou trasu pro každou IP adresu, kterou používá služba Batch, v oblasti, ve které existuje účet Batch. Informace o tom, jak získat seznam IP adres služby Batch, najdete v tématu [značky služeb v](../virtual-network/service-tags-overview.md) místním prostředí.

* Zajistěte, aby odchozí přenosy do Azure Storage (konkrétně adresy URL formuláře `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`a `<account>.blob.core.windows.net`) nejsou blokované prostřednictvím místního síťového zařízení.

Když přidáte trasu definovanou uživatelem, definujte trasu pro každou související předponu IP adresy dávky a nastavte **typ dalšího segmentu směrování** na **Internet**. Prohlédněte si následující příklad:

![Trasa definovaná uživatelem](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Další kroky

- Podrobný přehled služby Batch najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
- Další informace o vytváření tras definovaných uživatelem najdete v tématu [vytvoření trasy definované uživatelem Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).
