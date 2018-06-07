---
title: Operace Správce synchronizace služby Azure AD Connect | Microsoft Docs
description: Porozumět kartu operace ve Správci služby synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d6a8e10c5b78317ffda0963a13907c4552892e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593465"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Pomocí karty operace synchronizace Service Manager

![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Na kartě operations zobrazuje výsledky z nejnovější operace. Na této kartě je klíčem k pochopení a řešení problémů.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Pochopení informace zobrazené na kartě operace
V horní polovině ukazuje všechny běží v chronologickém pořadí. Ve výchozím nastavení, operace protokolu udržuje informace o posledních sedmi dnů, ale toto nastavení lze změnit pomocí [Plánovač](active-directory-aadconnectsync-feature-scheduler.md). Chcete-li vyhledat všechny spuštění, které nejsou zobrazeny stav úspěšného dokončení. Řazení kliknutím na záhlaví, můžete změnit.

**Stav** je nejdůležitější informace a ukazuje nejzávažnějšího problém pro spuštění. Zde je stručný nejběžnější stavů v pořadí podle priority prozkoumat (kde * znamenat několik řetězců možná chyba).

| Status | Poznámka |
| --- | --- |
| ukončeno-\* |Spuštění se nepodařilo dokončit. Například pokud vzdálený systém je vypnutý a nelze kontaktovat. |
| Zastavit limit chyb |Existuje více než 5 000 chyby. Spustit automaticky zastavila z důvodu velkého počtu chyb. |
| dokončené -\*– chyby |Spustit dokončilo, ale nejsou chyby (méně než 5 000), které by se měly prozkoumat. |
| dokončené -\*– upozornění |Spustit dokončena, ale některá data, není v očekávaném stavu. Pokud máte chyby, pak tato zpráva je obvykle jenom příznakem. Dokud nebude mít řešit chyby, by neměl prozkoumat upozornění. |
| úspěch |Žádné problémy. |

Když vyberete řádek, aktualizuje dolní zobrazit podrobnosti, které používají. Na levém okraji dolní, můžete mít tom, že seznam **krok #**. Tento seznam se zobrazí, jenom Pokud máte více domén v doménové struktuře každou doménu, kde je reprezentována krok. Název domény naleznete v části **oddílu**. V části **Statistika synchronizace**, můžete najít další informace o počtu změn, které byly zpracovány. Můžete kliknutím na odkazy získat seznam změněných objektů. Pokud máte objektů s chybami, tyto chyby, zobrazí na **chyby synchronizace**.

Další informace najdete v tématu [řešení potíží s objekt, který není synchronizace](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
