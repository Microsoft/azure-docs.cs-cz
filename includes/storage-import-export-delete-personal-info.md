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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114978"
---
## <a name="deleting-personal-information"></a>Odstranění osobní údaje

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Osobní údaje vztahující se k importu/exportu služby (přes portál a rozhraní API) během importu a exportu. Data používaná během těchto procesů patří:

- Jméno kontaktu
- Telefonní číslo
- Email
- Ulice a číslo
- Město
- PSČ
- Stav
- Provincie/kraj/oblast
- ID disku
- Číslo účtu dopravce
- Sledovací číslo zásilky

Když se vytvoří úlohu importu/exportu, uživatelé zadat kontaktní informace a dodací adresu. Osobní informace jsou uloženy v až na dvou různých místech: v úloze a volitelně v nastavení portálu. Osobní údaje jsou uloženy pouze v nastavení portálu, pokud zaškrtnete políčko s popiskem, **uložte dopravce a vrátit adresu jako výchozí** během *vrátí informace o expedici* část procesu exportu.

Osobní informace mohou být odstraněny následujícími způsoby:

- Data uložená pomocí úloh, se odstraní pomocí úlohy. Uživatele můžete ručně odstraňovat úlohy a dokončené úlohy jsou automaticky odstraněny po 90 dnech. Můžete ručně odstranit úlohy prostřednictvím rozhraní REST API nebo na webu Azure portal. Odstraňování úlohy na webu Azure Portal, přejděte na úlohu importu/exportu a klikněte na tlačítko *odstranit* na panelu příkazů. Podrobnosti o tom, jak odstranit úlohu importu/exportu přes rozhraní REST API najdete v [odstranit úlohu importu/exportu](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Kontaktní informace uložené v nastavení portálu může odebrat odstraněním nastavení portálu. Nastavení portálu můžete odstranit pomocí následujících kroků:
  - Přihlaste se k webu [Azure Portal](https://portal.azure.com).
  - Klikněte na *nastavení* ikonu ![ikona nastavení Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klikněte na tlačítko *exportovat všechna nastavení* (uložit stávající nastavení `.json` souboru).
  - Klikněte na tlačítko *odstranit všechna nastavení a privátní řídicí panely* odstranit všechna nastavení, včetně uložené kontaktní informace.

Další informace najdete v tématu Zásady Microsoft Privacy na [Centrum zabezpečení](https://www.microsoft.com/trustcenter)