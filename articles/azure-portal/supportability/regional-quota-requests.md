---
title: Žádost o zvýšení limitů kvót virtuálních procesorů Azure pro místní virtuální procesory
description: Jak požádat o zvýšení limitu kvóty virtuálních procesorů pro oblast na webu Azure Portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843680"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standardní kvóta: Zvýšení limitů podle regionů

Azure Resource Manager podporuje dva typy kvót virtuálních procesorů pro virtuální počítače:

* Virtuální počítače s *průběžnými platbami* a *rezervované instance virtuálních počítače* podléhají *standardní kvótě virtuálního procesoru*.
* *Virtuální počítače spot* podléhají *kvótě virtuálního procesoru na místě*.

Standardní kvóta virtuálního procesoru pro instance platebních a rezervovaných virtuálních strojů je vynucena na dvou úrovních pro každé předplatné v každé oblasti:

* První vrstva je *celkový limit místních virtuálních procesorů*napříč všemi řadami virtuálních montovny.
* Druhá vrstva je *limit virtuálních procesorů řady virtuálních procesorů*řady vm , například virtuální procesory řady D.

Při každém nasazení nového virtuálního počítače na místě, celkové nové a stávající využití virtuálních procesorů pro tuto řadu virtuálních počítače nesmí překročit schválenou kvótu virtuálních procesorů pro tuto konkrétní řadu virtuálních počítače. Kromě toho celkový počet nových a stávajících virtuálních procesorů, které jsou nasazeny ve všech řadách virtuálních počítače by neměla překročit celkovou schválenou kvótu místní virtuální ch výkonu pro předplatné. Pokud je překročena některá z těchto kvót, nasazení virtuálního počítače není povoleno.

Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro řadu virtuálních počítače pomocí portálu Azure. Zvýšení kvóty řady virtuálních počítače automaticky zvýší celkový limit místní virtuální ho výkonu o stejnou částku.

Při vytváření nového předplatného se výchozí celkový počet místních virtuálních procesorů nemusí rovnat celkové výchozí kvótě virtuálních procesorů pro všechny jednotlivé řady virtuálních počítače. Tento rozdíl může mít za následek předplatné s dostatečnou kvótou pro každou řadu jednotlivých virtuálních aplikací, které chcete nasadit. Ale nemusí být dostatečná kvóta pro celkové místní virtuální procesory pro všechna nasazení. V takovém případě je nutné odeslat žádost o explicitní zvýšení limitu celkového počtu místních virtuálních procesorů. Celkový limit místnívirtuální procesor nesmí překročit celkovou schválenou kvótu pro všechny řady virtuálních počítače pro oblast.

Další informace o standardních kvótách virtuálních procesorů najdete v [tématu Kvóty virtuálních procesorů virtuálních procesorů virtuálních procesorů](../../virtual-machines/windows/quotas.md) virtuálních zařízení a [omezení předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Další informace o zvýšení limitů virtuálních procesorů virtuálních procesorů virtuálních počítače na místě najdete v [tématu Kvóta bodů: Zvýšení limitů pro všechny řady virtuálních počítače](low-priority-quota.md).

Můžete požádat o zvýšení standardní kvóty virtuálního procesoru podle oblasti dvěma způsoby.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Požádat o zvýšení kvóty podle oblastí z nápovědy + podpory

Chcete-li požádat o zvýšení kvóty virtuálního procesoru podle oblasti z **nápovědy + podpory**:

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Nápověda + podpora**.

   ![Odkaz "Nápověda + podpora"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. V **nápovědě + podpoře**vyberte Nový **požadavek na podporu**.

    ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/new-support-request.png)

1. V **případě typu Problém**vyberte možnost Omezení služeb a předplatného **(kvóty).**

   ![Výběr typu problému](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. V **části Předplatné**vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Pro **typ kvóty**vyberte **další požadavky**.

   ![Výběr typu kvóty](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Vyberte **další: Řešení** pro otevření **podrobností o problému**. V **popisu**uveďte následující informace:

    1. V **poli Model nasazení**zadejte Správce **prostředků**.  
    1. V **poli Oblast**zadejte požadovanou oblast, například Východní USA **2**.  
    1. V **poli Nový limit**zadejte nový limit virtuálního procesoru pro danou oblast. Tato hodnota by neměla překročit součet schválených kvót pro jednotlivé řady skladových položk pro toto předplatné.

    ![Zadání podrobností pro požadavek na kvótu](./media/resource-manager-core-quotas-request/regional-details.png)

1. Chcete-li pokračovat ve vytváření žádosti o podporu, vyberte **možnost Revize + vytvořit.**

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Požádat o zvýšení kvóty podle oblastí z odběrů

Chcete-li požádat o zvýšení kvóty virtuálního procesoru podle oblasti z **odběrů**:

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **předplatná**.

   ![Přejděte na Předplatná na webu Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné, které chcete upravit.](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. V levém podokně vyberte **Možnost Využití + kvóty**.

   ![Sledovat odkaz Využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Vpravo nahoře vyberte **Požádat o zvýšení**.

   ![Výběrem zvýšíte kvótu.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. V **části Typ kvóty**vyberte další **požadavky**.

   ![Výběr typu kvóty](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Vyberte **další: Řešení** pro otevření **podrobností o problému**. V poli **Popis** uveďte následující doplňující informace:

    1. V **poli Model nasazení**zadejte Správce **prostředků**.  
    1. V **poli Oblast**zadejte požadovanou oblast, například Východní USA **2**.  
    1. V **poli Nový limit**zadejte nový limit virtuálního procesoru pro danou oblast. Tato hodnota by neměla překročit součet schválených kvót pro jednotlivé řady skladových položk pro toto předplatné.

    ![Zadání informací do podrobností](./media/resource-manager-core-quotas-request/regional-details.png)

1. Chcete-li pokračovat ve vytváření žádosti o podporu, vyberte **možnost Revize + vytvořit.**
