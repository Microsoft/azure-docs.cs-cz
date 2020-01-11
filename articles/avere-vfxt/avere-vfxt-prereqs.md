---
title: Avere vFXT požadavky – Azure
description: Předpoklady pro avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 0dafef7cf262153ccdb3b490aa0c7bd039b4a89b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889175"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Příprava k vytvoření Avere vFXT

Tento článek popisuje nezbytné úlohy pro vytvoření clusteru avere vFXT.

## <a name="create-a-new-subscription"></a>Vytvoření nového předplatného

Začněte vytvořením nového předplatného Azure. Použijte samostatné předplatné pro každý projekt avere vFXT, abyste mohli snadno sledovat všechny projektové prostředky a výdaje, chránit ostatní projekty před zřizováním a zjednodušit vyčištění.

Pokud chcete vytvořit nové předplatné Azure v Azure Portal:

* Přejít na [okno předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Klikněte na tlačítko **+ Přidat** v horní části.
* Přihlásit se, pokud se zobrazí výzva
* Vyberte nabídku a Projděte si postup vytvoření nového předplatného.

## <a name="configure-subscription-owner-permissions"></a>Konfigurace oprávnění vlastníka předplatného

Uživatel s oprávněním vlastníka pro předplatné by měl vytvořit cluster vFXT. Oprávnění vlastníka předplatného jsou nutná k přijetí licenčních podmínek služby a provádění dalších akcí.

K dispozici je několik scénářů s alternativním řešením, které umožňují nevlastnictví vytvořit avere vFTX pro cluster Azure. Tyto scénáře zahrnují omezení prostředků a přiřazování dalších rolí tvůrci. V obou těchto případech musí vlastník předplatného také [přijmout licenční podmínky pro avere vFXT](#accept-software-terms) před časem.

| Scénář | Omezení | Přístup k rolím vyžadovaným k vytvoření clusteru avere vFXT |
|----------|--------|-------|
| Správce skupiny prostředků | Virtuální síť, řadič clusteru a uzly clusteru se musí vytvořit v rámci skupiny prostředků. | Role [Správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator) a [přispěvatelů](../role-based-access-control/built-in-roles.md#contributor) v oboru pro cílovou skupinu prostředků |
| Externí virtuální síť | Řadič clusteru a uzly clusteru se vytvoří v rámci skupiny prostředků, ale použije se existující virtuální síť v jiné skupině prostředků. | (1) role [Správce přístupu uživatele](../role-based-access-control/built-in-roles.md#user-access-administrator) a [přispěvatelé](../role-based-access-control/built-in-roles.md#contributor) jsou v oboru pro skupinu prostředků vFXT. a (2) [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator)a role [přispěvatele avere](../role-based-access-control/built-in-roles.md#avere-contributor) v oboru pro skupinu prostředků virtuální sítě. |

Alternativou je vytvoření vlastní role řízení přístupu na základě role (RBAC) předem a přiřazení oprávnění uživateli, jak je vysvětleno v [tomto článku](avere-vfxt-non-owner.md). Tato metoda poskytuje pro tyto uživatele významná oprávnění.

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta pro cluster vFXT

Musíte mít dostatečnou kvótu pro následující součásti Azure. V případě potřeby [vyžádejte zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Zde uvedené virtuální počítače a komponenty SSD jsou pro samotný cluster vFXT. Pro virtuální počítače a jednotky SSD, které máte v úmyslu použít pro výpočetní farmu, budete potřebovat dodatečnou kvótu.  Ujistěte se, že je povolená kvóta pro oblast, ve které chcete pracovní postup spustit.

|Komponenta Azure|Kvóta|
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3|
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Back-endové úložiště dat (volitelné) |Jeden nový LRS kontejner objektů BLOB |

## <a name="accept-software-terms"></a>Přijmout softwarové podmínky

> [!NOTE]
> Tento krok není nutný, pokud vlastník předplatného vytvoří cluster avere vFXT.

Během vytváření clusteru musíte přijmout podmínky služby pro software avere vFXT. Pokud nejste vlastníkem předplatného, měli by mít vlastník předplatného podmínky k přijetí podmínek předem. Tento krok je potřeba provést jenom jednou pro každé předplatné.

Chcete-li přijmout podmínky pro software předem:

1. Otevřete Cloud Shell v Azure Portal nebo přejděte na <https://shell.azure.com>. Přihlaste se pomocí ID předplatného.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Vydejte tento příkaz, aby přijímal podmínky služby a povolil programový přístup k imagi softwaru avere vFXT for Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Vytvoření koncového bodu služby úložiště ve vaší virtuální síti (v případě potřeby)

[Koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) udržuje místní provoz Azure Blob namísto směrování mimo virtuální síť. Doporučuje se pro všechny avere vFXT pro clustery Azure, které využívají Azure Blob pro úložiště dat back-endu.

Pokud poskytujete existující virtuální síť a vytvoříte nový kontejner Azure Blob pro úložiště back-endu v rámci vytváření clusteru, musíte mít v síti koncový bod služby pro úložiště Microsoft. Tento koncový bod musí existovat před vytvořením clusteru, nebo se vytvoření nezdaří.

Koncový bod služby úložiště se doporučuje pro všechny avere vFXT pro clustery Azure, které používají úložiště objektů BLOB v Azure, a to i v případě, že úložiště přidáte později.

> [!TIP]
>
>* Pokud vytváříte novou virtuální síť jako součást vytváření clusteru, tento krok přeskočte.
>* Tento krok je nepovinný, pokud při vytváření clusteru nevytváříte úložiště objektů BLOB. V takovém případě můžete koncový bod služby vytvořit později, pokud se rozhodnete použít Azure Blob.

Vytvořte koncový bod služby úložiště z Azure Portal.

1. Na portálu otevřete seznam virtuálních sítí.
1. Vyberte virtuální síť pro svůj cluster.
1. V nabídce vlevo klikněte na **koncové body služby** .
1. V horní části klikněte na **Přidat** .
1. Vyberte ``Microsoft.Storage``služby.
1. Vyberte podsíť clusteru.
1. V dolní části klikněte na **Přidat**.

   ![Postup vytvoření koncového bodu služby Azure Portal snímku obrazovky s poznámkami.](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>Další krok: Vytvoření clusteru vFXT

Po dokončení těchto požadavků můžete přejít k vytvoření samotného clusteru. Pokyny najdete [v tématu Nasazení clusteru vFXT](avere-vfxt-deploy.md) .
