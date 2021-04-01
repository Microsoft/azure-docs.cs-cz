---
title: Operace Synchronization Service Manager Azure AD Connect | Microsoft Docs
description: Pochopte kartu operace v Synchronization Service Manager pro Azure AD Connect.
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
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357099"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Použití karty synchronizace Service Manager operací

![Synchronizovat Service Manager](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Na kartě operace se zobrazují výsledky z posledních operací. Tato karta je klíčem pro pochopení a řešení problémů.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Pochopení informací zobrazených na kartě operace
Horní polovina zobrazuje všechna spuštění v chronologickém pořadí. Ve výchozím nastavení protokol operací uchovává informace o posledních sedmi dnech, ale toto nastavení lze změnit pomocí [plánovače](how-to-connect-sync-feature-scheduler.md). Chcete vyhledat jakékoli spuštění, které nezobrazuje stav úspěch. Řazení můžete změnit kliknutím na záhlaví.

Sloupec **Status (stav** ) obsahuje nejdůležitější informace a zobrazuje nejzávažnější problém pro běh. Tady je stručný přehled nejběžnějších stavů v pořadí podle priority k prozkoumání (kde * značí několik možných chybových řetězců).

| Status | Komentář |
| --- | --- |
| ukončen\* |Spuštění se nepovedlo dokončit. Například pokud je vzdálený systém mimo provoz a nelze ho kontaktovat. |
| zastaveno – limit chyby |Došlo k více než 5 000 chybám. Běh se automaticky zastavil kvůli velkému počtu chyb. |
| dokončeno – \* chyby |Běh se dokončil, ale vyskytly se chyby (méně než 5 000), které by se měly prozkoumat. |
| dokončeno – \* Upozornění |Běh se dokončil, ale některá data nejsou v očekávaném stavu. Pokud máte chyby, pak je tato zpráva obvykle jenom příznakem. Dokud nebudete řešit chyby, neměli byste prozkoumat upozornění. |
| úspěch |Žádné problémy. |

Když vyberete řádek, v dolní části aktualizace zobrazíte podrobnosti o daném spuštění. Vlevo od dolního okraje může být seznam, který říká **Krok #**. Tento seznam se zobrazí pouze v případě, že máte více domén v doménové struktuře, kde je každá doména reprezentována krokem. Název domény najdete pod **oddílem** nadpisu. V části **Statistika synchronizace** můžete najít další informace o počtu zpracovaných změn. Můžete kliknout na odkazy a získat tak seznam změněných objektů. Pokud máte objekty s chybami, zobrazí se tyto chyby v části **chyby synchronizace**.

Další informace najdete v tématu [řešení potíží s objektem, který se nesynchronizuje](tshoot-connect-object-not-syncing.md) .

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
