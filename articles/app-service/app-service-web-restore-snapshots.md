---
title: Obnovení aplikace v Azure
description: Zjistěte, jak obnovit aplikace ze snímku.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.openlocfilehash: e1ae8fcc30323c865aa96937f43054515f293394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Obnovení ze snímku aplikace v Azure
Tento článek ukazuje, jak obnovit aplikaci [Azure App Service](../app-service/app-service-web-overview.md) ze snímku. Aplikace můžete obnovit do předchozího stavu, na základě jedné z vaší aplikace snímky. Není nutné k povolení zálohování snímky, platformu automaticky uloží všechny aplikace pro účely obnovení dat snímku.

Snímky jsou přírůstkové stínové kopie, a nabízí několik výhod oproti regular [zálohování](web-sites-backup.md):
- Žádné chyby kopírování souboru z důvodu uzamčení souborů.
- Žádné omezení velikosti úložiště.
- Není nutná žádná konfigurace.

Obnovení ze snímků je k dispozici pro aplikace běžící **Premium** vrstvy nebo vyšší. Informace o vertikálním navýšení kapacity aplikace naleznete v tématu [škálování aplikace v Azure](web-sites-scale.md).

## <a name="limitations"></a>Omezení

- Tato funkce je aktuálně ve verzi preview.
- Lze obnovit pouze ke stejné aplikaci, nebo do přihrádky, které patří k této aplikaci.
- Aplikační služba zastaví cílové aplikace nebo cílový slot přitom obnovení.
- Služby App Service udržuje tři měsíce vhodné snímků pro účely obnovení dat platformy.
- Snímky lze obnovit pouze za posledních 30 dní.
 

## <a name="restore-an-app-from-a-snapshot"></a>Obnovení aplikace ze snímku

1. Na **nastavení** stránka aplikace v rámci [portál Azure](https://portal.azure.com), klikněte na tlačítko **zálohování** zobrazíte **zálohování** stránky. Pak klikněte na tlačítko **obnovení** pod **Snapshot(Preview)** části.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. V **obnovení** vyberte snímek pro obnovení.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Zadejte cíl pro obnovení aplikace v **cíl obnovení**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Pokud se rozhodnete **přepsat**, všechny je vymazat a přepsat existující data v systému souborů aktuální vaší aplikace. Před kliknutím na **OK**, ujistěte se, že je co chcete udělat.
   > 
   > 
      
   > [!Note]
   > Z důvodu stávajících technických možností lze obnovit pouze na aplikace ve stejné jednotce škálování. Toto omezení bude v budoucí verzi odebrána.
   > 
   > 
   
    Můžete vybrat **stávající aplikace** k obnovení slot. Než použijete tuto možnost, měli jste již vytvořili slot ve vaší aplikaci.

4. Můžete obnovit konfiguraci lokality.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klikněte na **OK**.
