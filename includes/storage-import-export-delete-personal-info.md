---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175220"
---
## <a name="deleting-personal-information"></a>Odstranění osobních údajů

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Osobní údaje jsou relevantní pro službu importu/exportu (prostřednictvím portálu a rozhraní API) během importu a exportu. Údaje použité během těchto procesů zahrnují:

- Jméno kontaktu
- Telefonní číslo
- E-mail
- Ulice a číslo
- Město
- PSČ
- Stav
- Provincie/kraj/oblast
- ID disku
- Číslo účtu dopravce
- Sledovací číslo zásilky

Při vytvoření úlohy importu nebo exportu uživatelé poskytují kontaktní informace a dodací adresu. Osobní údaje jsou uloženy až na dvou různých místech: v úloze a volitelně v nastavení portálu. Osobní údaje jsou uloženy v nastavení portálu pouze v případě, že zaškrtnete políčko označené, **Uložit dopravce a zpáteční adresu jako výchozí** během části Informace o *vrácení* zásilky v procesu exportu.

Osobní kontaktní údaje mohou být vymazány následujícími způsoby:

- Data uložená s úlohou jsou s úlohou odstraněna. Uživatelé mohou odstranit úlohy ručně a dokončené úlohy jsou automaticky odstraněny po 90 dnech. Úlohy můžete ručně odstranit prostřednictvím rozhraní REST API nebo portálu Azure. Pokud chcete úlohu odstranit na portálu Azure, přejděte na úlohu importu a exportu a z panelu příkazů klikněte na *Odstranit.* Podrobnosti o odstranění úlohy importu nebo exportu prostřednictvím rozhraní REST API naleznete v části [Odstranění úlohy importu nebo exportu](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Kontaktní informace uložené v nastavení portálu mohou být odstraněny odstraněním nastavení portálu. Nastavení portálu můžete odstranit takto:
  - Přihlaste se k [portálu Azure](https://portal.azure.com).
  - Klikněte *Settings* na ![ikonu Nastavení nastavení nastavení ikony](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klikněte na *Exportovat všechna nastavení* `.json` (chcete-li uložit aktuální nastavení do souboru).
  - Kliknutím na *Odstranit všechna nastavení a soukromé řídicí panely* odstraňte všechna nastavení včetně uložených kontaktních informací.

Další informace naleznete v zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení.](https://www.microsoft.com/trustcenter)