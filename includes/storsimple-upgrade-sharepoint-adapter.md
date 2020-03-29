---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175050"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Upgrade SharePointu 2010 na SharePoint 2013 a instalace adaptéru StorSomple pro SharePoint
> [!IMPORTANT]
> Všechny soubory, které byly dříve přesunuty do externího úložiště prostřednictvím služby RBS, nebudou k dispozici, dokud nebude upgrade dokončen a funkce RBS znovu povolena. Chcete-li omezit dopad na uživatele, proveďte upgrade nebo přeinstalaci během plánovaného časového období údržby.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Upgrade SharePointu 2010 na SharePoint 2013 a instalace adaptéru
1. Ve farmě SharePointu 2010 si poznamenejte cestu úložiště objektů BLOB pro externalizované objekty BLOB a databáze obsahu, pro které je povolena funkce RBS. 
2. Nainstalujte a nakonfigurujte novou farmu SharePointu 2013. 
3. Přesuňte databáze, aplikace a kolekce webů z farmy SharePointu 2010 do nové farmy SharePointu 2013. Pokyny najdete v [článku Přehled procesu upgradu na SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Nainstalujte adaptér StorSimple pro SharePoint do nové farmy. Přejděte k [instalaci procedur StorSimple Adapter pro SharePoint.](#install-the-storsimple-adapter-for-sharepoint)
5. Pomocí informací, které jste si poznamenali v kroku 1, povolte RBS pro stejnou sadu databází obsahu a zadejte stejnou cestu úložiště objektů BLOB, která byla použita v instalaci SharePointu 2010. Postup [najdete v části Konfigurace služby RBS.](#configure-rbs) Po dokončení tohoto kroku by dříve externalizované soubory měly být přístupné z nové farmy. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Upgrade adaptéru StorSimple pro SharePoint
> [!IMPORTANT]
> Měli byste naplánovat tento upgrade dojít během plánované údržby okna z následujících důvodů:
> 
> * Dříve exterizovaný obsah nebude k dispozici, dokud nebude adaptér přeinstalován.
> * Veškerý obsah nahraný na web po odinstalaci předchozí verze adaptéru StorSimple pro službu SharePoint, ale před instalací nové verze, bude uložen v databázi obsahu. Po instalaci nového adaptéru bude nutné tento obsah přesunout do zařízení StorSimple. K migraci `RBS Migrate()` obsahu můžete použít rutinu prostředí Microsoft PowerShell, která je součástí SharePointu. Další informace naleznete v tématu [Migrace obsahu do nebo z rbs](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Upgrade adaptéru StorSimple pro sharepoint
1. Odinstalujte předchozí verzi adaptéru StorSimple pro SharePoint.
   
   > [!NOTE]
   > Tím se automaticky zakáže RBS v databázích obsahu. Existující objekty BLOB však zůstanou na zařízení StorSimple. Vzhledem k tomu, že rbs je zakázána a objekty BLOB nebyly přeneseny zpět do databází obsahu, všechny požadavky na tyto objekty BLOB se nezdaří. 
   > 
   > 
2. Nainstalujte nový adaptér StorSimple pro SharePoint. Nový adaptér automaticky rozpozná databáze obsahu, které byly pro službu RBS dříve povoleny nebo zakázány, a použije předchozí nastavení.

