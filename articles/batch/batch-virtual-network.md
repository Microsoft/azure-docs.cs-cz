---
title: Zřízení fondu ve virtuální síti – Azure Batch | Dokumenty společnosti Microsoft
description: Jak vytvořit fond dávek ve virtuální síti Azure tak, aby výpočetní uzly můžete bezpečně komunikovat s jinými virtuálními počítači v síti, jako je například souborový server.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: bbe38a9dc7be749b8e138ff3ca9ec4f06255b389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247745"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Vytvoření fondu dávek Azure ve virtuální síti

Když vytvoříte fond Azure Batch, můžete zřídit fond v podsíti [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) (VNet), kterou zadáte. Tento článek vysvětluje, jak nastavit fond dávek ve virtuální síti. 

## <a name="why-use-a-vnet"></a>Proč používat virtuální síť?

Fond Azure Batch má nastavení, která umožňují výpočetním uzlům vzájemnou komunikaci – například ke spuštění úloh s více instancemi. Tato nastavení nevyžadují samostatnou virtuální síť. Ve výchozím nastavení však uzly nemohou komunikovat s virtuálními počítači, které nejsou součástí fondu batch, jako je například licenční server nebo souborový server. Chcete-li povolit výpočetní uzly fondu bezpečně komunikovat s jinými virtuálními počítači nebo s místní sítí, můžete zřídit fond v podsíti virtuální sítě Azure. 

## <a name="prerequisites"></a>Požadavky

* **Ověřování .** Pokud chcete použít virtuální síť Azure klientské rozhraní API služby Batch musí používat ověřování pomocí Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md). 

* **Virtuální síť Azure**. Požadavky a konfiguraci virtuální sítě najdete v následující části. K přípravě virtuální sítě s jednou nebo více podsítěmi předem, můžete použít portál Azure, Azure PowerShell, Rozhraní příkazového řádku Azure (CLI) nebo jiné metody.  
  * Pokud chcete vytvořit virtuální síť založenou na Azure Resource Manageru, [přečtěte si, že najdete v tématu Vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Virtuální síť založená na Správci prostředků se doporučuje pro nová nasazení a je podporovaná jenom ve fondech v konfiguraci virtuálního počítače.
  * Pokud chcete vytvořit klasickou virtuální síť, přečtěte [si tématu Vytvoření virtuální sítě (klasické) s více podsítěmi](../virtual-network/create-virtual-network-classic.md). Klasická virtuální síť se podporuje jenom ve fondech v konfiguraci cloudových služeb.

## <a name="vnet-requirements"></a>Požadavky na virtuální síť

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Vytvoření fondu s virtuální sítí na portálu

Po vytvoření virtuální sítě a přiřazení podsítě k ní můžete vytvořit fond dávek s tímto virtuální mašalem. Chcete-li vytvořit fond z webu Azure Portal, postupujte takto: 

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupina prostředků obsahující virtuální síť, kterou chcete použít. 
2. V okně **Nastavení** vlevo vyberte položku nabídky **Bazény.**
3. V okně **Fondy** vyberte příkaz **Přidat.**
4. V okně **Přidat fond** vyberte možnost, kterou chcete použít v rozevíracím souboru **Typ obrazu.** 
5. Vyberte správnou **aplikaci Publisher/Offer/Sku** pro vlastní obrázek.
6. Zadejte zbývající požadovaná nastavení, včetně **velikosti uzlu**, **cílových uzly**a **uzlů s nízkou prioritou**, a také všechna požadovaná volitelná nastavení.
7. Ve **virtuální síti**vyberte virtuální síť a podsíť, kterou chcete použít.
  
   ![Přidání fondu s virtuální sítí](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelové propojení

Ve vaší organizaci můžete mít požadavky na přesměrování (vynuceného) internetového provozu z podsítě zpět do místního umístění pro kontrolu a protokolování. Je možné, že jste povolili vynucené tunelové propojení pro podsítě ve virtuální síti. 

Chcete-li zajistit, aby výpočetní uzly fondu Azure Batch fungovaly ve virtuální síti, která má povolené vynucené tunelové propojení, musíte pro tuto podsíť přidat následující [uživatelem definované trasy:](../virtual-network/virtual-networks-udr-overview.md)

* Služba Batch potřebuje komunikovat s výpočetními uzly fondu pro plánování úloh. Chcete-li tuto komunikaci povolit, přidejte uživatelem definovanou trasu pro každou adresu IP používanou službou Batch v oblasti, kde existuje váš účet Batch. Informace o tom, jak získat seznam IP adres služby Batch, najdete [v tématu Service tags v místním prostředí.](../virtual-network/service-tags-overview.md)

* Ujistěte se, že odchozí provoz do služby `<account>.table.core.windows.net`Azure `<account>.queue.core.windows.net`Storage `<account>.blob.core.windows.net`(konkrétně adresy URL formuláře , a ) není blokován prostřednictvím místního síťového zařízení.

Když přidáte uživatelem definovanou trasu, definujte trasu pro každou související předponu IP dávky a nastavte **další typ směrování** na **Internet**. Prohlédněte si následující příklad:

![Trasa definovaná uživatelem](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Další kroky

- Podrobný přehled batch, najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení s Batch](batch-api-basics.md).
- Další informace o vytvoření uživatelem definované trasy najdete [v tématu Vytvoření uživatelem definované trasy – portálu Azure](../virtual-network/tutorial-create-route-table-portal.md).
