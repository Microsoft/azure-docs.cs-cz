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
ms.topic: get-started-article
ms.custom: mvc
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: c57d662d8754353777638137c9ae7e97a5c7de15
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980973"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack doplňkové plány

Jako operátory Azure stacku, vytvoříte doplňkové plány upravit [základní plán](azure-stack-create-plan.md) kdy budete chtít nabízet další služby nebo rozšířit *počítače*, *úložiště*, nebo *sítě* kvóty nad rámec nabídky počáteční základní plán. Doplňkové plány upravit základní plán a jsou volitelná rozšíření, které uživatelé mohou přihlásit k odběru.

Existují situace, kdy kombinování všechno v jednom plánu je optimální. Jindy můžete chtít mít základnu plán a pak nabízet další služby pomocí doplňkové plány. Může například rozhodnout, že nabízí služby IaaS jako součást základní plán službami PaaS považován za doplňkové plány.

Dalším důvodem pro použití doplňkové plány je k poskytování pomoci uživatelům mějte jejich využití prostředků. Uděláte to tak, můžete začít s základní plán, který obsahuje poměrně málo početnému kvóty (v závislosti na požadované služby). Poté jako uživatelé kontaktovat kapacitu, jejich by Generovat výstrahy, které spotřebovali přidělení prostředků na základě jejich přiřazené plánu. Odtud můžete vybrat uživatele doplňkový plán, který poskytuje další prostředky.

> [!NOTE]
> Pokud nechcete použít doplňkový plán pro rozšíření kvóty, můžete také do [upravit původní konfiguraci kvóty](azure-stack-quota-types.md#edit-a-quota).

Když uživatel přidá doplňkový plán k existujícímu předplatnému nabídky, další prostředky může trvat až hodinu, se zobrazí.

## <a name="create-an-add-on-plan"></a>Vytvoření plánu služby doplněk

Doplňkové plány jsou vytvořeny tak, že upravíte stávající nabídky:

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