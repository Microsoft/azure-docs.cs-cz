---
title: Azure AD Connect Synchronizační služba Správce operací | Dokumenty společnosti Microsoft
description: Seznamte se s kartou Operace ve Správci synchronizačních služeb pro Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381388"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Použití karty Synchronizační správce provozu Operations

![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Karta Operace zobrazuje výsledky nejnovějších operací. Tato karta je klíčem k pochopení a řešení problémů.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Vysvětlení informací viditelných na kartě Operace
Horní polovina zobrazuje všechny běží v chronologickém pořadí. Ve výchozím nastavení protokol operací uchovává informace o posledních sedmi dnech, ale toto nastavení lze změnit pomocí [plánovače](how-to-connect-sync-feature-scheduler.md). Chcete vyhledat jakýkoli běh, který nezobrazuje stav úspěchu. Řazení můžete změnit kliknutím na záhlaví.

Sloupec **Stav** je nejdůležitější informace a zobrazuje nejzávažnější problém pro spuštění. Zde je stručný přehled nejběžnějších stavů v pořadí podle priority prozkoumat (kde * uveďte několik možných chybových řetězců).

| Status | Poznámka |
| --- | --- |
| zastaveno-\* |Spuštění nelze dokončit. Například pokud je vzdálený systém vypnutý a nelze jej kontaktovat. |
| limit zastavené chyby |Existuje více než 5 000 chyb. Spuštění bylo automaticky zastaveno z důvodu velkého počtu chyb. |
| dokončeno-\*-chyby |Spuštění bylo dokončeno, ale existují chyby (méně než 5 000), které by měly být prošetřeny. |
| dokončeno-\*-varování |Spuštění bylo dokončeno, ale některá data nejsou v očekávaném stavu. Pokud máte chyby, pak tato zpráva je obvykle pouze příznakem. Dokud nevyřešíte chyby, neměli byste prošetřovávat upozornění. |
| úspěch |Žádné problémy. |

Když vyberete řádek, dolní aktualizace zobrazí podrobnosti o tomto spuštění. Úplně vlevo od dolní části, můžete mít seznam říká **Krok #**. Tento seznam se zobrazí pouze v případě, že máte v doménové struktuře více domén, kde je každá doména reprezentována krokem. Název domény naleznete pod nadpisem **Oddíl**. V části **Statistika synchronizace**najdete další informace o počtu zpracovaných změn. Kliknutím na odkazy získáte seznam změněných objektů. Pokud máte objekty s chybami, tyto chyby se zobrazí v části **Chyby synchronizace**.

Další informace naleznete [v tématu poradce při potížích s objektem, který není synchronizován.](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
