---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 630ecb5f6efff9f08bbf5d2f1dec1c35af30b673
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166004"
---
<!--author=SharS last changed: 9/17/15-->

### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Aktualizace SharePoint 2010 pro službu SharePoint 2013 a potom nainstalujte adaptér StorSomple pro SharePoint
> [!IMPORTANT]
> Všechny soubory, které byly dříve přesunuty do externího úložiště prostřednictvím RBS nebude k dispozici, až do dokončení upgradu a funkci RBS opět povolena. Pokud chcete omezit dopad na uživatele, provedení jakékoli upgradu nebo přeinstalování během naplánovaného časového období údržby.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>K upgradu služby SharePoint 2010 na SharePoint 2013 a potom nainstalujte adaptér
1. Ve farmě služby SharePoint 2010 Poznámka: cesta k úložišti objektů BLOB externalized objekty BLOB a u kterých je povolená RBS databázích obsahu. 
2. Nainstalujte a nakonfigurujte nové farmy serverů Sharepointu 2013. 
3. Přesun databází, aplikací a kolekce webů z farmy služby SharePoint 2010 do nové farmy serverů Sharepointu 2013. Pokyny najdete v části [přehled procesu upgradu na server SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Nainstalujte adaptér StorSimple pro SharePoint v nové farmě. Přejděte na [nainstalovat adaptér StorSimple pro SharePoint](#install-the-storsimple-adapter-for-sharepoint) postupy.
5. Pomocí informací, které jste si poznamenali v kroku 1, povolte RBS pro stejnou sadu databází obsahu a zadat stejnou cestu úložiště objektů BLOB, který byl použit v instalaci Sharepointu 2010. Přejděte na [konfigurace RBS](#configure-rbs) postupy. Po dokončení tohoto kroku dříve externalized soubory by měly být přístupné z nové farmy. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Aktualizace StorSimple Adapter pro SharePoint
> [!IMPORTANT]
> Měli byste naplánovat tento upgrade během plánované údržby dojít z následujících důvodů:
> 
> * Dříve externalized obsah nebude k dispozici, až bude znovu nainstalován adaptér.
> * Žádný obsah nahrány na web po odinstalování předchozí verze StorSimple Adapter pro SharePoint, ale před instalací nové verze, se uloží v databázi obsahu. Je potřeba přesunout obsah do zařízení StorSimple, po instalaci nového adaptéru. Můžete použít Microsoft` RBS Migrate()` rutiny Powershellu, které jsou součástí služby SharePoint k přenosu obsahu. Další informace najdete v tématu [migrovat do nebo z něj RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Aktualizace StorSimple Adapter pro SharePoint
1. Odinstalujte předchozí verzi StorSimple Adapter pro SharePoint.
   
   > [!NOTE]
   > Tato akce automaticky zakáže RBS v databázích obsahu. Existujících objektů BLOB však zůstanou v zařízení StorSimple. Protože je zakázán RBS a objekty BLOB nemigrovaly zpět do databáze obsahu, všechny požadavky pro tyto objekty BLOB se nezdaří. 
   > 
   > 
2. Nainstalujte nový adaptér StorSimple pro SharePoint. Nový adaptér databáze obsahu, které byly dříve zapnutá nebo vypnutá RBS automaticky rozpozná a použije předchozí nastavení.

