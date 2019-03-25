---
title: Microsoft Azure Data Box Gateway– přehled | Microsoft Docs
description: Tento článek popisuje Azure Data Box Gateway, což je řešení úložiště virtuálního zařízení, které umožňuje přenášet data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e7a65a6b49544783ed3f40194e2338540819536b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400961"
---
# <a name="what-is-azure-data-box-gateway"></a>Co je Azure Data Box brány?

Azure Data Box Gateway je řešení úložiště, které vám umožňuje hladce odesílat data do Azure. Tento článek poskytuje přehled řešení Azure Data Box Gateway, jeho výhody, klíčové funkce a scénáře, ve kterých můžete toto zařízení nasadit.

Data Box Gateway je virtuální zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru. Toto virtuální zařízení se nachází ve vašich prostorách a data na něj zapisujete pomocí protokolů NFS a SMB. Zařízení potom přenese data do objektu blob bloku nebo objektu blob stránky Azure, případně do souborů Azure.

## <a name="use-cases"></a>Případy použití

Data Box Gateway lze využít pro přenos dat do cloudu, například pro účely archivace, zotavení po havárii nebo v případě potřeby zpracovat data v cloudovém měřítku. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Gateway.

- **Archivace v cloudu** – umožňuje zkopírovat stovky terabajtů dat do úložiště Azure pomocí zařízení Data Box Gateway bezpečným a efektivním způsobem. Ve scénářích archivace lze data přijímat jednorázově nebo průběžně.

- **Průběžné ingestování** -průběžně ingestovat data do zařízení ke zkopírování do cloudu, bez ohledu na velikost data. Jak data se zapisují do zařízení brány, zařízení nahrává data do služby Azure Storage.  

- **Počáteční přenos hromadných za nímž následuje přírůstkového přenosu** – použijte Data Box pro hromadného přenosu do offline režimu (počátečních) a brána pole dat pro přírůstkové přenosy (probíhající informačního kanálu) přes síť.

## <a name="benefits"></a>Výhody

Data Box Gateway má následující výhody:

- **Snadný přenos dat** – přenos dat do a z úložiště Azure je stejně snadný jako práce s místní sdílenou síťovou složkou.  
- **Vysoký výkon** – vysoce výkonné přenosy dat do a z Azure eliminují nepříjemnosti spojené s přenosem dat přes síť.
- **Rychlý přístup a sazby za příjem vysokou dat během pracovní doby** – brána dat pole má místní mezipaměti, který definujete jako místní kapacita velikost při zřizování virtuálního zařízení. Velikost datového disku by měl uvést jak je uvedeno [minimální požadavky na virtuální zařízení](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). Místní mezipaměť poskytuje následující výhody:
    - Místní mezipaměť umožňuje příjem dat vysokou rychlostí. Při vysoké množství data ingestují během pracovní doby ve špičce, můžete mezipaměti uchování dat a nahrajte ho do cloudu.
    - Místní mezipaměť umožňuje rychlý přístup ke čtení až do určité prahové hodnoty. Než se zařízení 50 až 60 % plný, všechny operace čtení ze zařízení jsou přístupné z mezipaměti, díky kterým jsou rychlejší. Jakmile se využité místo na zařízení překročí tuto prahovou hodnotu, zařízení spustí odstranit místní soubory.
 
- **Využití omezené šířky pásma** – data lze zapisovat do Azure, i když je síť omezena, aby se snížilo využití během frekventované pracovní doby.  

## <a name="key-capabilities"></a>Klíčové funkce

Data Box Gateway nabízí následující funkce:

|Schopnost |Popis  |
|---------|---------|
|Rychlost     | Plně automatizovaný a vysoce optimalizovaný přenos dat a šířka pásma.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [systémových požadavcích pro Data Box Gateway](data-box-gateway-system-requirements.md).|
|Přístup k datům     | Jakmile jsou data zaslaná zařízení v cloudu, může být dále upraveno přímým přístupem cloudových rozhraní API.|
|Rychlý přístup     | Místní mezipaměť na zařízení pro rychlý přístup k naposledy použitým souborům.|
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.       |
|Odolnost     | Integrovaná odolnost sítě.        |


## <a name="specifications"></a>Specifikace

Virtuální zařízení Data Box Gateway má následující specifikace:

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Virtuální procesory (jádra)   | Minimálně 4 |
| Memory (Paměť)  |Minimálně 8 GB|
| Dostupnost|Jeden uzel|
| Disky|Disk s operačním systémem: 250 GB <br> Datový disk: Minimálně 2 TB, dynamického zajišťování zřízené a musí být založená na jednotkách SSD|
| Síťová rozhraní |1 nebo více rozhraní virtuální sítě|
| Nativní protokoly pro sdílení souborů|SMB a NFS  |
| Zabezpečení|Ověřování pro odemknutí přístupu k zařízení a datům <br> Přenášená data šifrovaný pomocí 256bitového šifrování AES|
| Správa|Místní webové uživatelské rozhraní – počáteční instalace, diagnostika a řízení spotřeby zařízení <br> Azure Portal – každodenní správa zařízení Data Box Gateway       |

## <a name="components"></a>Komponenty

Řešení Data Box Gateway sestává z prostředku Data Box Gateway, virtuálního zařízení Data Box Gateway a místního webového uživatelského rozhraní.

- **Virtuální zařízení Data Box Gateway** – zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru, které umožňuje odesílat data do Azure.
    
- **Prostředek Data Box Gateway** – prostředek na webu Azure Portal, který vám umožní spravovat zařízení Data Box Gateway z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Použijte prostředek brány Data pole k zobrazení a správě sdílených složek, výstrahy, uživatelé a zařízení. Další informace najdete v tématu Jak [spravovat pomocí webu Azure portal](data-box-gateway-manage-shares.md).

- **Data Box místního webového uživatelského rozhraní** – pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a restartovat zařízení, vygenerujte balíček pro podporu, nebo kontaktujte Microsoft Support do souboru žádosti o službu. Další informace najdete v tématu Jak [spravovat pomocí místního webového uživatelského rozhraní](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení data Box brány, prostředků Azure a cílový účet úložiště, ke kterému přenosu dat všechny musí být ve stejné oblasti.

- **Dostupnost prostředků** – pro tuto verzi prostředku brány Data Box je k dispozici v těchto oblastech, které podporují veřejného cloudu:
    - **Spojené státy** – USA – východ
    - **Evropská unie** – Západní Evropa
    - **Asie a Tichomoří** -jihovýchodní Asie

    Brána dat pole můžete také nasadit v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure.

    Oblasti, do kterých účty úložiště ukládají data služby Data Box, by se měly nacházet v blízkosti umístění zařízení, aby byl zajištěn optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.


## <a name="next-steps"></a>Další postup

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.

