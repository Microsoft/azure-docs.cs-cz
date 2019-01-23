---
title: Pro aplikaci Proxy aplikací se nenašly žádné pracovní skupina konektorů | Dokumentace Microsoftu
description: Řešení problémů, které se můžete setkat, když není žádný funkční konektor ve skupině konektorů pro vaši aplikaci s Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6e4f481ccefd8f583c197fc367419a4e6aaf8a5d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464438"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Žádné pracovní skupina konektorů pro aplikaci Proxy aplikací se nenašly

Tento článek pomůže při řešení běžných problémů čelí při konektor zjistila aplikace Proxy aplikací integrovaná s Azure Active Directory není.

## <a name="overview-of-steps"></a>Přehled kroků
Pokud není žádný funkční konektor ve skupině konektorů pro vaši aplikaci, existuje několik způsobů, jak problém vyřešit:

-   Pokud máte ve skupině žádné konektory, můžete:

    -   Stáhněte si nový konektor na pravém v místním serveru a přiřaďte ho do této skupiny

    -   Přesunout do skupiny pro konektor služby active

-   Pokud máte ve skupině žádné aktivní konektory, můžete:

    -   Z důvodů, proč svůj konektor je neaktivní identifikovat a vyřešit

    -   Přesunout do skupiny pro konektor služby active

Chcete-li problém pochopit, otevřete nabídku "Proxy aplikace" ve vaší aplikaci a podívejte se na upozornění skupina konektorů. Pokud ve skupině nejsou žádné konektory, určuje toto upozornění že skupina musí aspoň jeden konektor. Pokud máte k dispozici žádné aktivní konektory, upozornění, která vysvětluje. Je běžné mít neaktivní konektory. 

   ![Výběr skupiny konektor na webu Azure portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Podrobnosti o každé z těchto možností najdete v části odpovídající níže. Pokyny předpokládají, se spouští ze stránky správy konektoru. Pokud chcete na výše uvedené chybová zpráva, můžete přejít na tuto stránku kliknutím na upozornění. Můžete také dostanete na stránku tak, že přejdete do **Azure Active Directory**, pak kliknete na **podnikové aplikace**, pak **Proxy aplikací.**

   ![Správa skupin konektor na webu Azure portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Stáhněte si nový konektor

Chcete-li si stáhli nový konektor, použijte tlačítko "Stáhnout konektor" v horní části stránky.

Nainstalujte connector na počítači s přístupem na dohled back-end aplikace. Obvykle je konektor nainstalovaný na stejném serveru jako aplikace. Po stažení si konektor by se zobrazit v této nabídce. Klepněte na konektoru a ujistěte se, že patří do správné skupiny pomocí "Skupina konektorů" rozevíracího seznamu. Uložte změny.

   ![Stáhnout konektor z portálu Azure portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Přesunutí aktivního konektoru

Pokud máte aktivní konektor, by měly patřit do skupiny, který má dohled cílové aplikace back-end, můžete přesunout do přiřazené skupině konektoru. Uděláte to tak, klikněte na tento konektor. V poli "Skupina konektorů" pomocí rozevíracího seznamu vyberte správnou skupinu a pak klikněte na Uložit.

## <a name="resolve-an-inactive-connector"></a>Vyřešit neaktivní konektoru

Pokud nejsou aktivní konektory pouze ve skupině, jsou pravděpodobně na počítači, který nemá všechny nezbytné porty odblokováno.

na zkoumání tohoto problému naleznete v dokumentu porty Poradce při potížích podrobnosti.

## <a name="next-steps"></a>Další postup
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)


