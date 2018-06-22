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
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313876"
---
## <a name="deleting-personal-information"></a>Odstranění osobní údaje

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Osobní informace vztahující se k importu a exportu služby (prostřednictvím portálu a rozhraní API) během importu a exportu. Data použitá během těchto procesů patří:

- Jméno kontaktu
- Telefonní číslo
- Email
- Adresa
- Město
- Poštovní směrovací číslo
- Stav
- Země nebo oblasti nebo oblast
- ID jednotky
- Poskytovatel číslo účtu
- Číslo sledování přesouvání

Když je vytvořen úlohu importu a exportu, uživatelé zadat kontaktní informace a adresu příjemce. Osobní informace jsou uloženy v až dvou různých místech: v úloze a volitelně v nastavení portálu. Osobní informace je uložena pouze v nastavení portálu, pokud zaškrtnete políčko s názvem bez přípony, **uložit jako výchozí poskytovatel a vrátit adresu** během *vrátí informace o přesouvání* část procesu exportu.

Osobní kontaktní údaje budou odstraněny následujícími způsoby:

- Data uložena do úlohy se odstraní pomocí úlohy. Uživatele můžete ručně odstranit úlohy a dokončené úlohy jsou automaticky odstraněny po 90 dnech. Můžete ručně odstranit úlohy prostřednictvím rozhraní API REST nebo portálu Azure. Chcete-li odstranit tuto úlohu na portálu Azure, přejděte na úlohu importu a exportu a klikněte na *odstranit* z řádku nabídek. Podrobnosti o tom, jak odstranit úlohu importu a exportu přes rozhraní REST API, [odstranit úlohu importu a exportu](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Kontaktní informace uložené v nastavení portálu může odebrat odstraněním nastavení portálu. Nastavení portálu můžete odstranit pomocí následujících kroků:
  - Přihlaste se k webu [Azure Portal](https://portal.azure.com).
  - Klikněte na *nastavení* ikonu ![ikonu nastavení Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klikněte na tlačítko *exportovat všechna nastavení* (uložit stávající nastavení `.json` souboru).
  - Klikněte na tlačítko *odstranit všechna nastavení a privátní řídicí panely* odstranit všechna nastavení, včetně uložené kontaktní informace.

Další informace najdete v tématu Zásady Microsoft Privacy v [Centrum zabezpečení](https://www.microsoft.com/trustcenter)