---
title: V tomto článku se dozvíte, jak aktualizovat Azure Stack nabídky a plány | Dokumentace Microsoftu
description: Tento článek popisuje, jak zobrazit a upravit existující služby Azure Stack nabídky a plány.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 00bb17eadfee32e9b0d006ac76bb8e1cd614f13e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780384"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack doplňkové plány

Jako operátory Azure stacku, vytvoříte doplňkové plány upravit [základní plán](azure-stack-create-plan.md) kdy budete chtít nabízet další služby nebo rozšířit *počítače*, *úložiště*, nebo *sítě* kvóty nad rámec nabídky počáteční základní plán. Doplňkové plány upravit základní plán a jsou volitelná rozšíření, které uživatelé mohou přihlásit k odběru.

Existují situace, kdy kombinování všechno v jednom plánu je optimální. Jindy můžete chtít mít základnu plán a pak nabízet další služby pomocí doplňkové plány. Může například rozhodnout, že nabízí služby IaaS jako součást základní plán službami PaaS považován za doplňkové plány.

Dalším důvodem pro použití doplňkové plány je usnadnit monitorování využití prostředků. Uděláte to tak, můžete začít s základní plán, který obsahuje poměrně málo početnému kvóty (v závislosti na požadované služby). Poté jako uživatelé kontaktovat kapacitu, jejich by Generovat výstrahy, které spotřebovali přidělení prostředků na základě jejich přiřazené plánu. Odtud můžete vybrat uživatele doplňkový plán, který poskytuje další prostředky.

> [!NOTE]
> Pokud nechcete použít doplňkový plán pro rozšíření kvóty, můžete také do [upravit původní konfiguraci kvóty](azure-stack-quota-types.md#edit-a-quota).

Když přidáte doplňkový plán k existujícímu předplatnému nabídky, další prostředky může trvat až hodinu, se zobrazí.

Doplňkové plány jsou vytvořeny tak, že upravíte existující nabídky.

## <a name="create-an-add-on-plan-1902-and-later"></a>Vytvoření plánu služby doplněk (1902 a novější)

1. Přihlaste se k portálu Správce služby Azure Stack jako správce cloudu.
2. Postupujte stejným způsobem umožňuje [vytvořit nový plán základní](azure-stack-create-plan.md) k vytvoření nového plánu nabídky služeb, které nebyly nabízeny dříve.
3. Na portálu správce, klikněte na tlačítko **nabízí** a pak vyberte nabídku se doplňkový plán aktualizace.

   ![Vytvoření doplňkový plán](media/create-add-on-plan/add-on1.png)

4. Přejděte do dolní části nabídky vlastnosti a vyberte **doplňkové plány**. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvoření doplňkový plán](media/create-add-on-plan/add-on2.png)

5. Vyberte plán, který chcete přidat. V tomto příkladu se nazývá plánu **20 storageaccounts**. Po výběru plánu, klikněte na **vyberte** plánu přidat do nabídky. Měli byste obdržet oznámení, že tento plán byl úspěšně přidán do nabídky.

    ![Vytvoření doplňkový plán](media/create-add-on-plan/add-on3.png)

6. Projděte si seznam doplňkové plány zahrnutou částku, chcete-li ověřit, že je uvedený nový doplňkový plán.

    [![Doplňkový plán vytvořit](media/create-add-on-plan/add-on4.png "vytvořit doplňkový plán")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Vytvoření plánu služby doplněk (1901 a starší)

1. Přihlaste se k portálu Správce služby Azure Stack jako správce cloudu.
2. Postupujte stejným způsobem umožňuje [vytvořit nový plán základní](azure-stack-create-plan.md) k vytvoření nového plánu nabídky služeb, které nebyly nabízeny dříve. V tomto příkladu služby Key Vault (**Microsoft.KeyVault**) služby bude zahrnuté v novém plánu.
3. Na portálu správce, klikněte na tlačítko **nabízí** a pak vyberte nabídku se doplňkový plán aktualizace.

   ![Vytvoření doplňkový plán](media/create-add-on-plan/1.PNG)

4. Přejděte do dolní části nabídky vlastnosti a vyberte **doplňkové plány**. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvoření doplňkový plán](media/create-add-on-plan/2.PNG)

5. Vyberte plán, který chcete přidat. V tomto příkladu se nazývá plánu **Key vault plán**. Po výběru plánu, klikněte na **vyberte** plánu přidat do nabídky. Měli byste obdržet oznámení, že tento plán byl úspěšně přidán do nabídky.

    ![Vytvoření doplňkový plán](media/create-add-on-plan/3.PNG)

6. Projděte si seznam doplňkové plány zahrnutou částku, chcete-li ověřit, že je uvedený nový doplňkový plán.

    ![Vytvoření doplňkový plán](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Další postup

* [Vytvoření nabídky](azure-stack-create-offer.md)