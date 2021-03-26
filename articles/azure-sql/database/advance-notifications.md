---
title: Oznámení o plánované údržbě v předstihu (Preview)
description: Získejte oznámení před plánovanou údržbou pro Azure SQL Database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 895b9081ba7eb6d7e8b5d3304d37168e4064ed39
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560026"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Oznámení o plánované údržbě v předběžné verzi (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Oznámení s předstihem (Preview) jsou k dispozici pro databáze nakonfigurované pro časové [období údržby (Preview)](maintenance-window.md). Předběžná oznámení umožňují zákazníkům konfigurovat oznámení až na 24 hodin předem v každé plánované události.

Oznámení můžou být nakonfigurovaná, abyste mohli získat text, e-maily, nabízená oznámení Azure a hlasové zprávy, když se plánovaná údržba spustí za dalších 24 hodin. Další oznámení se odesílají při zahájení údržby a při skončení údržby.

> [!Note]
> I když je pro spravované instance Azure SQL k dispozici okno údržby, nejsou pro spravované instance Azure SQL aktuálně k dispozici žádná oznámení.

## <a name="create-an-advance-notification"></a>Vytvořit předběžné oznámení

Pro databáze SQL Azure s nakonfigurovaným časovým intervalem pro správu a údržbu jsou k dispozici předběžné oznámení. 

Pokud chcete povolit oznámení, proveďte následující kroky.  

1. Přejít na stránku [plánovaná údržba](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) , vyberte **Upozornění na stav** a pak **přidejte výstrahu o stavu služby**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="Vytvoří novou možnost nabídky upozornění na stav.":::

2. V části **Akce** vyberte **Přidat skupiny akcí**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="Přidání možnosti nabídky Skupina akcí":::

3. Vyplňte formulář **vytvořit skupinu akcí** a pak vyberte **Další: oznámení**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="formulář pro vytvoření skupiny akcí":::

1. Na kartě **oznámení** vyberte **Typ oznámení**. Možnost **e-mailu/zprávy SMS/nabízení/hlas** nabízí největší flexibilitu a je to doporučená možnost. Vyberte pero pro konfiguraci oznámení.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="Konfigurace oznámení":::



   1. Vyplňte formulář *Přidat nebo upravit oznámení* , který se otevře, a vyberte **OK**: 

   2. Akce a značky jsou volitelné. Tady můžete nakonfigurovat další akce, které se aktivují, nebo použít značky ke kategorizaci a uspořádání prostředků Azure. 

   4. Zkontrolujte podrobnosti na kartě **Revize + vytvořit** a vyberte **vytvořit**. 

7. Po výběru možnosti vytvořit se otevře obrazovka konfigurace pravidla výstrahy a vybere se skupina akcí. Zadejte nové pravidlo výstrahy, zvolte pro něj skupinu prostředků a vyberte **vytvořit pravidlo upozornění**. 

8. Znovu klikněte na položku nabídky **výstrahy stavu** a seznam výstrah nyní obsahuje novou výstrahu. 


Nyní je vše připraveno. Když příště dojde k plánované události údržby Azure SQL, obdržíte oznámení s předstihem.

## <a name="receiving-notifications"></a>Příjem oznámení

V následující tabulce jsou uvedena obecná oznámení týkající se informací, která se mohou zobrazit: 

|Status|Popis|
|:---|:---|
|**Plánované nasazení**| Bylo přijato 24 hodin před událostí údržby. Údržba se plánuje podle data mezi 17:00-8:00 (místní čas) pro DB xyz.|
|**Probíhá zpracování** | Probíhá údržba databáze *XYZ*   .| 
|**Dokončit** | Údržba databáze *XYZ* je dokončená. |

V následující tabulce jsou uvedena další oznámení, která je možné odeslat, když probíhá údržba: 

|Status|Popis|
|:---|:---|
|**Rozšířený** | Probíhá údržba, ale nedokončila se pro databázi *XYZ*. Údržba bude pokračovat v dalším časovém intervalu pro správu a údržbu.| 
|**Zrušeno**| Údržba databáze *XYZ* je zrušená a bude naplánována později. |
|**Blokované**|Při údržbě databáze *XYZ* došlo k problému. Až budeme pokračovat, pošleme vám zprávu.| 
|**Obnovení**|Problém byl vyřešen a údržba bude pokračovat v dalším časovém intervalu pro správu a údržbu.|


## <a name="next-steps"></a>Další kroky

- [Časové období údržby](maintenance-window.md)
- [Nejčastější dotazy k časovému období údržby](maintenance-window-faq.yml)
- [Přehled upozornění v Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [Poslat e-mail roli Azure Resource Manageru](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)