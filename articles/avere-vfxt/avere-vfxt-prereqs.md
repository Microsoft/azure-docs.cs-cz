---
title: Avere vFXT – požadavky na Azure
description: Předpoklady pro Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056737"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Příprava k vytvoření Avere vFXT

Tento článek vysvětluje požadované úkoly pro vytváření Avere vFXT clusteru.

## <a name="create-a-new-subscription"></a>Vytvořte nové předplatné

Začněte vytvořením nového předplatného Azure. Použití samostatné předplatné pro každý projekt vFXT Avere vám umožní snadno sledovat všechny prostředky projektu a výdaje, chránit jiné projekty z možných omezování během zřizování prostředků a zjednodušení vyčištění.  

Vytvoření nového předplatného Azure na webu Azure Portal:

* Přejděte [okně předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Klikněte na tlačítko **+ přidat** tlačítko v horní části
* Přihlášení po zobrazení výzvy
* Vyberte nabídky a projdete si kroky k vytvoření nového předplatného

## <a name="configure-subscription-owner-permissions"></a>Konfigurace předplatného oprávnění vlastníka

Uživatel s oprávněními vlastníka předplatného by měl vytvořit vFXT cluster. Přijměte podmínky pro software služby a provádět další akce jsou potřeba oprávnění vlastníka předplatného. 

Existují některé řešení scénářů, které umožňují jiných uživatelů k vytvoření Avere vFTX pro Azure cluster. Tyto scénáře zahrnují omezení prostředků a přiřazení dalších rolí Tvůrce. V obou těchto případech se vlastník předplatného také musí [přijměte podmínky pro software vFXT Avere](#accept-software-terms) předem. 

| Scénář | Omezení | Potřebný k vytvoření clusteru vFXT Avere role zabezpečeného přístupu | 
|----------|--------|-------|
| Správce skupiny prostředků | Virtuální síť, řadič clusteru a uzly clusteru je nutné vytvořit ve skupině prostředků | [Správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) role, i s rozsahem cílová skupina prostředků | 
| Externí virtuální sítě | Kontroler clusteru a uzly clusteru se vytvoří v rámci skupiny prostředků, ale používá se existující virtuální sítě v jiné skupině prostředků. | (1) [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) role omezená na skupinu prostředků vFXT; a (2) [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [přístup uživatelů Správce](../role-based-access-control/built-in-roles.md#user-access-administrator), a [Avere Přispěvatel](../role-based-access-control/built-in-roles.md#avere-contributor) role vymezeny na skupinu prostředků virtuální sítě. |
 
Další možností je vytvořit roli řízení (RBAC) vlastní přístup na základě rolí předem domluvili a přiřadit oprávnění pro uživatele, jak je popsáno v [v tomto článku](avere-vfxt-non-owner.md). Tato metoda poskytuje významná oprávnění pro tyto uživatele. 

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta pro vFXT clusteru

Musíte mít dostatečnou kvótu pro následující komponenty Azure. V případě potřeby [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Pro samotný cluster vFXT jsou virtuální počítače a komponenty SSD zde uvedené. Budete potřebovat další kvótu pro virtuální počítače a SSD, kterou chcete použít pro výpočetní farmy.  Ujistěte se, že je povolená kvótu pro oblast, ve kterém chcete spustit pracovní postup.

|Komponenta Azure|Kvóta|
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3|
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Back-endové úložiště dat (volitelné) |Jeden nový kontejner objektů Blob LRS |

## <a name="accept-software-terms"></a>Přijměte podmínky pro software

> [!NOTE] 
> Tento krok není povinný, pokud Avere vFXT cluster vytvoří vlastník předplatného.

Při vytváření clusteru musíte přijmout podmínky pro Avere vFXT software služby. Pokud nejste vlastníkem předplatného, jste vlastníkem předplatného, přijímáte předem. Tento krok pouze je nutné provést jednou na jedno předplatné.

Přijmout podmínky předem software: 

1. Otevřete cloud shell na webu Azure Portal nebo procházením <https://shell.azure.com>. Přihlaste se pomocí ID vašeho předplatného.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Přijměte podmínky služby a povolit programový přístup pro vFXT Avere pro image Azure softwaru používat tento příkaz: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Vytvoření koncového bodu služby úložiště ve vaší virtuální síti (v případě potřeby)

A [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) udržuje objektů Blob v Azure provoz místní namísto směrování mimo virtuální síť. Doporučuje se pro všechny Avere vFXT pro cluster Azure, která využívá back endovým datům úložiště objektů Blob v Azure. 

Pokud poskytujete existující virtuální síť a vytvořili nový kontejner objektů Blob v Azure pro váš back endové úložné jako součást vytváření clusteru, musí mít koncový bod služby ve virtuální síti pro úložiště od Microsoftu. Tento koncový bod, musí existovat před vytvořením clusteru nebo vytváření se nezdaří. 

Koncový bod služby storage se doporučuje pro všechny Avere vFXT pro cluster Azure, která používá úložiště objektů Blob v Azure i v případě, že přidáte úložiště později. 

> [!TIP] 
> * Tento krok přeskočte, pokud vytváříte nové virtuální sítě jako součást vytváření clusteru. 
> * Tento krok je volitelný, pokud nevytváříte úložiště objektů Blob při vytváření clusteru. V takovém případě můžete vytvořit koncový bod služby později Pokud se rozhodnete používat Azure Blob.

Vytvoření koncového bodu služby úložiště na webu Azure Portal. 

1. Z portálu, klikněte na tlačítko **virtuální sítě** na levé straně.
1. Vyberte virtuální síť pro cluster. 
1. Klikněte na tlačítko **koncové body služby** na levé straně.
1. Klikněte na tlačítko **přidat** v horní části.
1. Nechte službu jako ``Microsoft.Storage`` a zvolte podsíť clusteru.
1. V dolní části, klikněte na tlačítko **přidat**.

   ![Azure portal – snímek obrazovky s poznámkami postup vytvoření koncového bodu služby](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Další krok: Vytvoření clusteru vFXT

Po dokončení těchto nezbytných podmínkách, můžete přejít do vytváření samotného clusteru. Čtení [nasazení clusteru vFXT](avere-vfxt-deploy.md) pokyny.