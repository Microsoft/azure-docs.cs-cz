---
title: Nenašla se žádná skupina pracovních konektorů pro aplikaci proxy aplikace.
description: Problémy s adresou, se kterými se můžete setkat, když ve skupině konektorů není žádný pracovní konektor pro vaši aplikaci s Proxy aplikací služby AD Azure
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257030"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenašla se žádná skupina pracovních konektorů pro aplikaci proxy aplikace.

Tento článek vám pomůže vyřešit běžné problémy s tím, že se nezjistil konektor pro aplikaci proxy aplikace, která je integrovaná s Azure Active Directory.

## <a name="overview-of-steps"></a>Přehled kroků
Pokud ve skupině konektorů není žádný pracovní konektor pro vaši aplikaci, existuje několik způsobů, jak tento problém vyřešit:

-   Pokud ve skupině nemáte žádné konektory, můžete:

    -   Stáhněte si nový konektor na pravé straně na místním serveru a přiřaďte ho k této skupině.

    -   Přesunout aktivní konektor do skupiny

-   Pokud ve skupině nemáte žádné aktivní konektory, můžete:

    -   Identifikace důvodu neaktivního a přeloženého konektoru

    -   Přesunout aktivní konektor do skupiny

Chcete-li zjistit problém, otevřete v aplikaci nabídku "proxy aplikací" a podívejte se na zprávu upozornění skupiny konektoru. Pokud ve skupině nejsou žádné konektory, zpráva upozornění určuje, že skupina bude potřebovat alespoň jeden konektor. Pokud nemáte žádné aktivní konektory, zobrazí se upozornění. Je běžné mít neaktivní konektory. 

   ![Výběr skupiny konektorů v Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Podrobnosti o každé z těchto možností najdete v odpovídající části níže. V pokynech se předpokládá, že začínáte ze stránky pro správu konektoru. Pokud se vám zobrazí chybová zpráva, můžete přejít na tuto stránku kliknutím na zprávu upozornění. Stránku můžete zobrazit také tak, že přejdete na **Azure Active Directory**, kliknete na **podnikové aplikace** a pak na **proxy aplikace.**

   ![Správa skupin konektorů v Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Stažení nového konektoru

Pokud chcete stáhnout nový konektor, použijte tlačítko Stáhnout konektor v horní části stránky.

Nainstalujte konektor na počítač s přímým dohledem na back-endové aplikaci. Konektor se obvykle nainstaluje na stejný server jako aplikace. Po stažení by se měl konektor zobrazit v této nabídce. klikněte na konektor a pomocí rozevíracího seznamu "skupina konektorů" se ujistěte, že patří do správné skupiny. Uložte změnu.

   ![Stažení konektoru z Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Přesunout aktivní konektor

Pokud máte aktivní konektor, který by měl patřit do skupiny a má řadu pohledů na cílovou back-end aplikaci, můžete konektor přesunout do přiřazené skupiny. Provedete to tak, že kliknete na konektor. V poli Skupina konektoru vyberte správnou skupinu pomocí rozevíracího seznamu a klikněte na Uložit.

## <a name="resolve-an-inactive-connector"></a>Řešení neaktivního konektoru

Pokud jsou jediné konektory ve skupině neaktivní, jsou pravděpodobně na počítači, který nemá všechny nezbytné porty odblokovány.

Podrobnosti o vyšetřování tohoto problému najdete v dokumentu věnovaném řešení potíží s porty.

## <a name="next-steps"></a>Další kroky
[Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)


