---
title: Pro aplikaci Proxy aplikace nebyla nalezena žádná pracovní skupina konektorů.
description: Řešení problémů, se kterými se můžete setkat, když neexistuje žádný pracovní konektor ve skupině konektorů pro vaši aplikaci s proxy aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ab0d1b3bbab9c97c04da4f918f3aaa2f1d07e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275625"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Pro aplikaci Proxy aplikace nebyla nalezena žádná pracovní skupina konektorů.

Tento článek pomáhá vyřešit běžné problémy, kterým čelí, když není zjištěn konektor pro aplikaci proxy aplikace integrované s Azure Active Directory.

## <a name="overview-of-steps"></a>Přehled kroků
Pokud neexistuje žádný pracovní konektor ve skupině konektorů pro vaši aplikaci, existuje několik způsobů, jak problém vyřešit:

-   Pokud ve skupině nemáte žádné konektory, můžete:

    -   Stáhněte si nový konektor na pravém místním serveru a přiřaďte jej této skupině

    -   Přesunutí aktivní spojnice do skupiny

-   Pokud ve skupině nemáte žádné aktivní konektory, můžete:

    -   Identifikujte důvod, proč je konektor neaktivní a vyřešte

    -   Přesunutí aktivní spojnice do skupiny

Chcete-li zjistit problém, otevřete v aplikaci nabídku "Proxy aplikace" a podívejte se na varovnou zprávu skupiny konektorů. Pokud ve skupině nejsou žádné konektory, varovná zpráva určuje, že skupina potřebuje alespoň jeden konektor. Pokud nemáte žádné aktivní konektory, varovná zpráva vysvětluje, že. Je běžné mít neaktivní konektory. 

   ![Výběr skupinkonektorů na webu Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Podrobnosti o každé z těchto možností naleznete v příslušné části níže. Pokyny předpokládají, že začínáte ze stránky správy konektoru. Pokud se díváte na výše uvedenou chybovou zprávu, můžete přejít na tuto stránku kliknutím na varovnou zprávu. Na stránku se taky dostanete tak, že přejdete do **služby Azure Active Directory**, kliknete na **podnikové aplikace**a potom na **proxy aplikace.**

   ![Správa skupin konektorů na webu Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Stažení nového konektoru

Chcete-li stáhnout nový konektor, použijte tlačítko "Stáhnout konektor" v horní části stránky.

Nainstalujte konektor na stroj s přímým zorným polem do back-endové aplikace. Konektor je obvykle nainstalován na stejném serveru jako aplikace. Po stažení by se měl v této nabídce zobrazit konektor. klikněte na konektor a pomocí rozevíracího souboru "Skupina konektorů" zkontrolujte, zda patří do správné skupiny. Uložte změnu.

   ![Stažení konektoru z webu Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Přesunutí aktivní hospojnice

Pokud máte aktivní konektor, který by měl patřit do skupiny a má zorné pole do cílové back-endové aplikace, můžete přesunout spojnice do přiřazené skupiny. Chcete-li tak učinit, klepněte na konektor. V poli Skupina konektorů vyberte správnou skupinu pomocí rozevíracího seznamu a klikněte na Uložit.

## <a name="resolve-an-inactive-connector"></a>Řešení neaktivní spojnice

Pokud pouze konektory ve skupině jsou neaktivní, jsou pravděpodobně v počítači, který nemá všechny potřebné porty odblokovány.

Podrobnosti o zkoumání tohoto problému naleznete v dokumentu pro řešení potíží s porty.

## <a name="next-steps"></a>Další kroky
[Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)


