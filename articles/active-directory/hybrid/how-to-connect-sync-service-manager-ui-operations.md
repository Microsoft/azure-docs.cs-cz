---
title: Azure AD Connect Synchronization Service Manager Operations | Dokumentace Microsoftu
description: Vysvětlení kartě provoz Synchronization Service Manager pro Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195290"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Na kartě Synchronizace Service Manager Operations

![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Operace karta zobrazuje výsledky z nejnovější operace. Na této kartě je klíčem k pochopení a řešení potíží.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Seznamte se s informacemi, které jsou viditelné v kartě operace
V horní polovině zobrazuje všechna spuštění v chronologickém pořadí. Ve výchozím nastavení, operace přihlášení udržuje informace o posledních sedmi dnů, ale toto nastavení lze změnit pomocí [Plánovač](how-to-connect-sync-feature-scheduler.md). Chcete hledat jakékoli spuštění, který není uveden stav úspěšného dokončení. Můžete změnit kliknutím na záhlaví při řazení.

**Stav** sloupec je nejdůležitější informace a v něm nejzávažnějších problémů pro spuštění. Tady je stručný přehled nejběžnějších stavů v pořadí podle priority k prozkoumání (kde * označení několik možných chybové řetězce).

| Status | Poznámka |
| --- | --- |
| stopped-\* |Spuštění se nepovedlo dokončit. Například, pokud vzdálený systém je vypnutý a nelze kontaktovat. |
| stopped-error-limit |Existuje více než 5 000 chyby. Spustit automaticky zastavila z důvodu velkého počtu chyb. |
| dokončené -\*– chyby |Dokončení běhu, ale nejsou chyby (méně než 5 000), které by mělo být vypátráno. |
| dokončené -\*– upozornění |Běh dokončen, ale některá data není v očekávaném stavu. Pokud už máte chyby, pak tato zpráva je obvykle pouze příznakem. Až vyřešíte chyby by neměl prozkoumat upozornění. |
| úspěch |Žádné problémy. |

Při výběru řádku dolní části aktualizuje zobrazíte podrobnosti o spuštění. Úplně vlevo dole, můžete mít přehled o tom, že **krok #**. Tento seznam se zobrazí, jenom Pokud máte více domén v doménové struktuře kde každou doménu představuje krok. Název domény najdete v části **oddílu**. V části **statistické údaje o synchronizaci**, najdete další informace o počtu změn, které byly zpracovány. Můžete kliknout na odkazy na získání seznamu změněné objekty. Pokud máte objektů s chybami, tyto chyby se zobrazí v části **chyby synchronizace**.

Další informace najdete v tématu [potíží s objektem, který se nesynchronizuje](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
