---
title: Avere vFXT předpoklady - Azure
description: Předpoklady pro Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252542"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Příprava k vytvoření Avere vFXT

Tento článek vysvětluje nezbytné úkoly pro vytvoření clusteru Avere vFXT.

## <a name="create-a-new-subscription"></a>Vytvoření nového předplatného

Začněte vytvořením nového předplatného Azure. Použijte samostatné předplatné pro každý projekt Avere vFXT zjednodušit sledování výdajů a vyčištění a ujistěte se, že ostatní projekty nejsou ovlivněny kvóty nebo omezení prostředků při zřizování pracovního postupu clusteru.

Vytvoření nového předplatného Azure na webu Azure Portal:

1. Přejděte do [okna Předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Klikněte na tlačítko **+ Přidat** v horní části
1. Pokud se zobrazí výzva, přihlaste se
1. Vyberte nabídku a projděte si kroky k vytvoření nového předplatného.

## <a name="configure-subscription-owner-permissions"></a>Konfigurace oprávnění vlastníka předplatného

Uživatel s oprávněními vlastníka pro odběr by měl vytvořit cluster vFXT. Vytvoření clusteru vyžaduje, aby vlastník přijal softwarové podmínky služby a povolil změny síťových a úložných prostředků.

Existují určitá řešení, která umožňují nevlastníkovi vytvořit cluster Avere vFXT pro cluster Azure. Tyto scénáře zahrnují omezení prostředků a přiřazení dalšírole řízení přístupu na základě rolí (RBAC) tvůrce. Ve všech těchto případech musí vlastník předplatného také [předem přijmout softwarové podmínky Avere vFXT.](#accept-software-terms)

| Scénář | Omezení | Přístup k rolím potřebným k vytvoření clusteru Avere vFXT |
|----------|--------|-------|
| Správce skupiny prostředků vytvoří vFXT | Virtuální síť, řadič clusteru a uzly clusteru musí být vytvořeny v rámci skupiny prostředků. | [Role správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator) a [přispěvatele,](../role-based-access-control/built-in-roles.md#contributor) obě s vymezenou skupinou cílových prostředků. |
| Použití existující externí virtuální sítě | Řadič clusteru a uzly clusteru jsou vytvořeny v rámci skupiny prostředků vFXT, ale používají existující virtuální síť v jiné skupině prostředků. | (1) Role [správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator) a [přispěvatele](../role-based-access-control/built-in-roles.md#contributor) s vymezenou skupinou prostředků vFXT; a (2) [Role přispěvatele virtuálního počítače](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator)a [přispěvatele Avere](../role-based-access-control/built-in-roles.md#avere-contributor) s rozsahem skupiny prostředků virtuální sítě. |
| Vlastní role pro tvůrce clusteru | Žádná omezení umístění zdrojů. Tato metoda poskytuje oprávnění bez vlastníků. | Vlastník předplatného vytvoří vlastní roli RBAC, jak je vysvětleno v [tomto článku](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta pro cluster vFXT

Zkontrolujte, zda máte dostatečnou kvótu pro následující součásti Azure. V případě potřeby [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Virtuální počítače a součásti SSD zde uvedené jsou pro samotný cluster vFXT. Nezapomeňte, že potřebujete také kvótu pro virtuální počítače a ssd, které budete používat pro vaši výpočetní farmu.
>
> Ujistěte se, že kvóta je povolena pro oblast, ve které chcete pracovní postup spustit.

|Komponenta Azure|Kvóta|
|----------|-----------|
|Virtual Machines|3 nebo více E32s_v3 (jeden na uzel clusteru) |
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Úložiště back-end dat (volitelné) |Jeden nový LRS Blob kontejner |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Přijmout softwarové podmínky

> [!TIP]
> Tento krok přeskočte, pokud vlastník předplatného vytvoří cluster Avere vFXT.

Během vytváření clusteru musíte přijmout smluvní podmínky pro software Avere vFXT. Pokud nejste vlastníkem předplatného, vlastník předplatného musí podmínky předem přijmout.

Tento krok je třeba provést pouze jednou za předplatné.

Chcete-li softwarové podmínky přijmout předem:

1. Otevřete cloudové prostředí na webu <https://shell.azure.com>Azure Portal nebo procházením aplikace . Přihlaste se pomocí ID předplatného.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Vyvolte tento příkaz pro přijetí podmínek služby a povolení programového přístupu pro bitovou kopii softwaru Avere vFXT pro Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Vytvoření koncového bodu služby úložiště ve virtuální síti (v případě potřeby)

[Koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) udržuje provoz objektů blob Azure místní místo směrování mimo virtuální síť. Doporučuje se pro všechny Avere vFXT pro cluster Azure, který používá Azure Blob pro back-end ové úložiště dat.

Pokud při vytváření clusteru vytvoříte novou virtuální síť, vytvoří se koncový bod automaticky. Pokud poskytujete existující virtuální síť, musí mít koncový bod služby úložiště Microsoft, pokud chcete vytvořit nový kontejner úložiště objektů Blob během vytváření clusteru.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Tento krok přeskočte, pokud vytváříte novou virtuální síť jako součást vytváření clusteru.
>* Koncový bod je volitelný, pokud nevytváříte úložiště objektů Blob během vytváření clusteru. V takovém případě můžete vytvořit koncový bod služby později, pokud se rozhodnete použít objekt blob Azure.

Vytvořte koncový bod služby úložiště z webu Azure Portal.

1. Na portálu otevřete seznam virtuálních sítí.
1. Vyberte virtuální síť pro váš cluster.
1. V levé nabídce klikněte na **Servisní koncové body.**
1. Nahoře klikněte na **Přidat.**
1. Zvolte ``Microsoft.Storage``službu .
1. Vyberte podsíť clusteru.
1. V dolní části klikněte na **Přidat**.

   ![Snímek obrazovky portálu Azure s anotacemi pro kroky vytváření koncového bodu služby](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Další kroky

Po dokončení těchto požadavků můžete vytvořit cluster. Přečtěte [si nasazení clusteru vFXT](avere-vfxt-deploy.md) pokyny.
