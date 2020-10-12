---
title: Avere vFXT požadavky – Azure
description: Přečtěte si o úlohách, které se mají provést před vytvořením clusteru v avere vFXT pro Azure, včetně práce s předplatnými, kvótami a koncovými body služby úložiště.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: be5c640ef069690c408460b7374aee82c35b3a67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88272412"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Příprava k vytvoření Avere vFXT

Tento článek vysvětluje úlohy požadovaných součástí pro vytvoření clusteru avere vFXT.

## <a name="create-a-new-subscription"></a>Vytvoření nového předplatného

Začněte vytvořením nového předplatného Azure. Použijte samostatné předplatné pro každý projekt avere vFXT pro zjednodušení sledování a vyčištění výdajů a zajistěte, aby při zřizování pracovního postupu clusteru neovlivnily jiné projekty kvóty nebo omezování prostředků.

Pokud chcete vytvořit nové předplatné Azure v Azure Portal:

1. Přejít na [okno předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Klikněte na tlačítko **+ Přidat** v horní části.
1. Přihlásit se, pokud se zobrazí výzva
1. Vyberte nabídku a Projděte si postup vytvoření nového předplatného.

## <a name="configure-subscription-owner-permissions"></a>Konfigurace oprávnění vlastníka předplatného

Uživatel s oprávněním vlastníka pro předplatné by měl vytvořit cluster vFXT. Vytvoření clusteru vyžaduje, aby vlastník přijal licenční podmínky služby a schvaloval změny prostředků sítě a úložiště.

K dispozici je několik alternativních řešení, která umožňují vytvořit avere vFXT pro cluster Azure pomocí nevlastnictví. Tyto scénáře zahrnují omezení prostředků a přiřazování dalších rolí Azure tvůrci. Ve všech těchto případech by měl vlastník předplatného také [přijmout podmínky avere vFXT software](#accept-software-terms) před časem.

| Scénář | Omezení | Přístup k rolím vyžadovaným k vytvoření clusteru avere vFXT |
|----------|--------|-------|
| Správce skupiny prostředků vytvoří vFXT. | V rámci skupiny prostředků musí být vytvořená virtuální síť, řadič clusteru a uzly clusteru. | Role [Správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator) a [přispěvatelů](../role-based-access-control/built-in-roles.md#contributor) jsou vymezeny na cílovou skupinu prostředků. |
| Použít existující externí virtuální síť | Řadič clusteru a uzly clusteru se vytvářejí v rámci skupiny prostředků vFXT, ale používají existující virtuální síť v jiné skupině prostředků. | (1) role [Správce přístupu uživatele](../role-based-access-control/built-in-roles.md#user-access-administrator) a [přispěvatelé](../role-based-access-control/built-in-roles.md#contributor) jsou v oboru pro skupinu prostředků vFXT. a (2) [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator)a role [přispěvatele avere](../role-based-access-control/built-in-roles.md#avere-contributor) v oboru pro skupinu prostředků virtuální sítě. |
| Vlastní role pro tvůrci clusteru | Neexistují žádná omezení umístění prostředků. Tato metoda poskytuje nevlastníky významná oprávnění. | Vlastník předplatného vytvoří vlastní roli Azure, jak je vysvětleno v [tomto článku](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta pro cluster vFXT

Ověřte, zda máte dostatečnou kvótu pro následující součásti Azure. V případě potřeby [vyžádejte zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Zde uvedené virtuální počítače a komponenty SSD jsou pro samotný cluster vFXT. Pamatujte, že budete potřebovat kvótu pro virtuální počítače a SSD, které použijete pro výpočetní farmu.
>
> Ujistěte se, že je povolená kvóta pro oblast, ve které chcete pracovní postup spustit.

|Komponenta Azure|Kvóta|
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3 (jeden na uzel clusteru) |
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Back-endové úložiště dat (volitelné) |Jeden nový LRS kontejner objektů BLOB |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Přijmout softwarové podmínky

> [!TIP]
> Tento krok přeskočte, pokud vlastník předplatného vytvoří cluster avere vFXT.

Během vytváření clusteru musíte přijmout podmínky služby pro software avere vFXT. Pokud nejste vlastníkem předplatného, měli by mít vlastník předplatného podmínky k přijetí podmínek předem.

Tento krok je potřeba provést jenom jednou pro každé předplatné.

Chcete-li přijmout podmínky pro software předem:

1. Otevřete Cloud Shell v Azure Portal nebo přejděte na <https://shell.azure.com> . Přihlaste se pomocí ID předplatného.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Vydejte tento příkaz, aby přijímal podmínky služby a povolil programový přístup k imagi softwaru avere vFXT for Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Vytvoření koncového bodu služby úložiště ve vaší virtuální síti (v případě potřeby)

[Koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) udržuje místní provoz Azure Blob namísto směrování mimo virtuální síť. Doporučuje se pro všechny avere vFXT pro clustery Azure, které využívají Azure Blob pro úložiště dat back-endu.

Pokud při vytváření clusteru vytvoříte novou virtuální síť, koncový bod se vytvoří automaticky. Pokud zadáte existující virtuální síť, musí mít koncový bod služby Microsoft Storage, pokud chcete během vytváření clusteru vytvořit nový kontejner úložiště objektů BLOB.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Pokud vytváříte novou virtuální síť jako součást vytváření clusteru, tento krok přeskočte.
>* Koncový bod je nepovinný, pokud při vytváření clusteru nevytváříte úložiště objektů BLOB. V takovém případě můžete koncový bod služby vytvořit později, pokud se rozhodnete použít Azure Blob.

Vytvořte koncový bod služby úložiště z Azure Portal.

1. Na portálu otevřete seznam virtuálních sítí.
1. Vyberte virtuální síť pro svůj cluster.
1. V nabídce vlevo klikněte na **koncové body služby** .
1. V horní části klikněte na **Přidat** .
1. Vyberte službu ``Microsoft.Storage`` .
1. Vyberte podsíť clusteru.
1. V dolní části klikněte na **Přidat**.

   ![Postup vytvoření koncového bodu služby Azure Portal snímku obrazovky s poznámkami.](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Další kroky

Po dokončení těchto požadavků můžete cluster vytvořit. Pokyny najdete [v tématu Nasazení clusteru vFXT](avere-vfxt-deploy.md) .
