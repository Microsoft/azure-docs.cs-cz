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
ms.openlocfilehash: 137aca7c6c857ee6e833c359b710e1c1848d15ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555665"
---
## <a name="deleting-personal-information"></a>Odstraňují se osobní údaje.

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Osobní údaje jsou relevantní pro službu import/export (prostřednictvím portálu a rozhraní API) během operací importu a exportu. K datům použitým během těchto procesů patří:

- Jméno kontaktu
- Telefonní číslo
- E-mail
- Ulice a číslo
- City (Město)
- PSČ
- Stav
- Provincie/kraj/oblast
- ID disku
- Číslo účtu dopravce
- Sledovací číslo zásilky

Když se vytvoří úloha importu/exportu, poskytnou uživatelé kontaktní údaje a dodací adresu. Osobní údaje jsou uloženy v až dvou různých umístěních: v úloze a volitelně v nastavení portálu. Osobní údaje jsou uloženy pouze v nastavení portálu, pokud zaškrtnete zaškrtávací políčko s označením, **uložíte dopravce a zpáteční adresu jako výchozí** v části *informace o vrácení odeslání* procesu exportu.

Osobní kontaktní údaje mohou být odstraněny následujícími způsoby:

- Data uložená pomocí úlohy se odstraní s úlohou. Uživatelé můžou úlohy odstraňovat ručně a dokončené úlohy se automaticky odstraní po 90 dnech. Úlohy můžete ručně odstranit pomocí REST API nebo Azure Portal. Pokud chcete úlohu odstranit v Azure Portal, přejděte do úlohy import/export a na panelu příkazů klikněte na *Odstranit* . Podrobnosti o tom, jak odstranit úlohu importu/exportu prostřednictvím REST API, najdete v tématu [odstranění úlohy importu/exportu](/previous-versions/azure/storage/common/storage-import-export-cancelling-and-deleting-jobs).

- Kontaktní informace uložené v nastavení portálu je možné odebrat odstraněním nastavení portálu. Nastavení portálu můžete odstranit pomocí následujících kroků:
  - Přihlaste se na [Azure Portal](https://portal.azure.com).
  - Klikněte na ikonu *Nastavení* ![ ikona nastavení Azure.](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klikněte na *exportovat všechna nastavení* (a uložte aktuální nastavení do `.json` souboru).
  - Kliknutím na *Odstranit všechna nastavení a privátní řídicí panely* odstraňte všechna nastavení včetně uložených kontaktních údajů.

Další informace najdete v části zásady ochrany osobních údajů Microsoftu na webu [Trust Center](https://www.microsoft.com/trustcenter) .