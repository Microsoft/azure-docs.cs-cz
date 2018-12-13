---
title: Obnovení ze zálohy – Azure App Service
description: Zjistěte, jak obnovit aplikaci ze snímku.
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
ms.custom: seodec18
ms.openlocfilehash: 174fbd8f3e4c652c25a78911185ef8a98a80bb50
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260034"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Obnovení aplikace v Azure ze snímku
V tomto článku se dozvíte, jak obnovit aplikaci v [služby Azure App Service](../app-service/app-service-web-overview.md) ze snímku. Aplikaci můžete obnovit do předchozího stavu, na základě jedné z vaší aplikace snímky. Není potřeba povolit snímky zálohování, platformu automaticky uloží snímek pro účely obnovení dat. všechny aplikace.

Snímky jsou přírůstkové stínové kopie a nabízejí několik výhod oproti standardní [zálohy](web-sites-backup.md):
- Žádné chyby kopírování souboru z důvodu uzamčení.
- Žádné omezení velikosti úložiště.
- Není nutná žádná konfigurace.

Obnovení ze snímků je k dispozici pro aplikace běžící **Premium** vrstvy nebo vyšší. Informace o vertikálním navýšení kapacity aplikace naleznete v tématu [vertikální navýšení kapacity aplikace v Azure](web-sites-scale.md).

## <a name="limitations"></a>Omezení

- Tato funkce je aktuálně ve verzi preview.
- Můžete obnovit jenom do stejné aplikace nebo do slotu patřící do této aplikace.
- App Service přestane cílové aplikace nebo cílovém slotu během operace obnovení.
- App Service zajišťuje tři měsíce za snímky pro účely obnovení dat. platformy.
- Snímky lze obnovit pouze za posledních 30 dní.
 

## <a name="restore-an-app-from-a-snapshot"></a>Obnovení aplikace ze snímku

1. Na **nastavení** stránky vaší aplikace v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **zálohy** zobrazíte **zálohy** stránky. Pak klikněte na tlačítko **obnovení** pod **Snapshot(Preview)** oddílu.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. V **obnovení** vyberte snímek pro obnovení.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Zadejte cílovou složku pro obnovení aplikace v **cíl obnovení**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Pokud se rozhodnete **přepsat**, všechny existující data v aktuálním systému souborů vaší aplikace je vymazána a přepsat. Před kliknutím na **OK**, ujistěte se, že je co chcete udělat.
   > 
   > 
      
   > [!Note]
   > Kvůli aktuálním technická omezení můžete obnovit jenom do aplikací ve stejné jednotce škálování. Toto omezení se odeberou v budoucí verzi.
   > 
   > 
   
    Můžete vybrat **existující aplikaci** obnovit do slotu. Před použitím této možnosti by jste již vytvořili slot ve vaší aplikaci.

4. Je možné obnovit konfiguraci webu.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klikněte na **OK**.
