---
title: Pro aplikaci Proxy aplikace nalezeny žádná skupina konektor pracovní | Microsoft Docs
description: Řešení problémů, které se můžete setkat, když není žádný funkční konektor ve skupině pro vaši aplikaci s Azure AD Application Proxy Connector
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: f6baea8c2702fc2a86c75d32c7b4ca958fd913b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591741"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Žádná pracovní skupina konektor najít pro aplikaci Proxy aplikace

Tento článek pomáhá řešit obvyklé problémy potýkají při konektor zjištěna pro aplikaci Proxy aplikace integrované s Azure Active Directory není.

## <a name="overview-of-steps"></a>Přehled kroků
Pokud není žádný funkční konektor ve skupině konektor pro vaši aplikaci, existuje několik způsobů, jak problém vyřešit:

-   Pokud máte žádné konektory ve skupině, můžete:

    -   Stáhněte si nový konektor na pravém místního serveru a přiřaďte ho do této skupiny

    -   Konektor služby active přesuňte do skupiny

-   Pokud máte žádné aktivní konektory ve skupině, můžete:

    -   Z důvodu, kterou vaše konektor je neaktivní identifikovat a vyřešit

    -   Konektor služby active přesuňte do skupiny

A pokuste se zjistit problém, otevřete nabídku "Proxy aplikace" v aplikaci a podívejte se na zprávu upozornění konektor skupiny. Pokud nejsou žádné konektory ve skupině, určuje upozornění, že skupiny musí mít alespoň jeden konektor. Pokud máte žádné aktivní konektory, vysvětluje upozornění, který. Je běžné tak, aby měl neaktivní konektory. 

   ![Výběr skupiny konektoru na portálu Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Podrobnosti o každá z těchto možností najdete v části odpovídající níže. Pokyny předpokládají, že začínáte na stránce Správa konektor. Pokud hledáte na výše uvedené chybová zpráva, můžete přejít na tuto stránku kliknutím na upozornění. Můžete také dostanete na stránku tak, že přejdete na **Azure Active Directory**, kliknutím na na **podnikové aplikace, které**, pak **Proxy aplikace.**

   ![Správa skupin konektoru na portálu Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Stáhněte si nový konektor

Pokud chcete stáhnout nový konektor, použijte tlačítko "Stáhnout konektor" v horní části stránky.

Konektor nainstalujte na počítač s přímé směrem pohledu na back-end aplikace. Obvykle je konektor nainstalovaný na stejném serveru jako aplikace. Po stažení, konektor objevit v této nabídky. Klikněte na konektoru a ujistěte se, že patří do správné skupiny pomocí "Konektor skupinu" rozevíracího seznamu. Uložte změnu.

   ![Stáhnout konektor z portálu Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Přesunout konektoru služby Active

Pokud máte aktivní konektor, musí patřit do skupiny a má směrem pohledu do cílové back-end aplikace, můžete konektor přesunout do skupiny přiřazené. Chcete-li to provést, klikněte na tlačítko konektor. V poli "Konektor skupina" pomocí rozevíracího seznamu vyberte správné skupině, a klikněte na Uložit.

## <a name="resolve-an-inactive-connector"></a>Vyřešte neaktivní konektoru

Pokud se pouze konektory ve skupině jsou neaktivní, jsou pravděpodobně na počítači, který nemá všechny nezbytné porty odblokováno.

na odstranění příčin tohoto problému naleznete v dokumentu Poradce při potížích porty podrobnosti.

## <a name="next-steps"></a>Další postup
[Pochopení konektory proxy aplikace služby Azure AD](manage-apps/application-proxy-connectors.md)


