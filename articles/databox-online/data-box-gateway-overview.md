---
title: Microsoft Azure Data Box Gateway– přehled | Microsoft Docs
description: Tento článek popisuje Azure Data Box Gateway, což je řešení úložiště virtuálního zařízení, které umožňuje přenášet data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b8c6b4085f56bc12b67bf87177ba33b82c6a6db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "69900584"
---
# <a name="what-is-azure-data-box-gateway"></a>Co je brána datové schránky Azure?

Azure Data Box Gateway je řešení úložiště, které vám umožňuje hladce odesílat data do Azure. Tento článek poskytuje přehled řešení Azure Data Box Gateway, jeho výhody, klíčové funkce a scénáře, ve kterých můžete toto zařízení nasadit.

Data Box Gateway je virtuální zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru. Toto virtuální zařízení se nachází ve vašich prostorách a data na něj zapisujete pomocí protokolů NFS a SMB. Zařízení potom přenese data do objektu blob bloku nebo objektu blob stránky Azure, případně do souborů Azure.

## <a name="use-cases"></a>Případy použití

Data Box Gateway lze využít pro přenos dat do cloudu, například pro účely archivace, zotavení po havárii nebo v případě potřeby zpracovat data v cloudovém měřítku. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Gateway.

- **Archivace v cloudu** – umožňuje zkopírovat stovky terabajtů dat do úložiště Azure pomocí zařízení Data Box Gateway bezpečným a efektivním způsobem. Ve scénářích archivace lze data přijímat jednorázově nebo průběžně.

- **Nepřetržité ingestování dat** – průběžně ingestuje data do zařízení ke kopírování do cloudu, bez ohledu na velikost dat. Když se data zapisují do zařízení brány, zařízení nahraje data do Azure Storage.  

- **Počáteční hromadný přenos následovaný přírůstkovým přenosem** – použijte datovou schránku pro hromadný přenos v režimu offline (počáteční osiva) a bránu datové schránky pro přírůstkové přenosy (průběžné přenosy) po síti.

Další informace najdete v [případech použití služby Azure Data Box Gateway](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Výhody

Data Box Gateway má následující výhody:

- **Snadný přenos dat** – přenos dat do a z úložiště Azure je stejně snadný jako práce s místní sdílenou síťovou složkou.  
- **Vysoký výkon** – vysoce výkonné přenosy dat do a z Azure eliminují nepříjemnosti spojené s přenosem dat přes síť.
- **Rychlý přístup a vysoké míry přijímání dat během pracovní doby** – brána datové schránky má místní mezipaměť, kterou definujete jako velikost místní kapacity při zřízení virtuálního zařízení. Velikost datového disku by měla být určena podle [minimálních požadavků virtuálního zařízení](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). Místní mezipaměť poskytuje následující výhody:
    - Místní mezipaměť umožňuje vysokovysokou rychlost přijím dat. Když se během pracovní doby ve špičce požívá velké množství dat, může mezipaměť data uchovat a nahrát do cloudu.
    - Místní mezipaměť umožňuje rychlý přístup pro čtení až do určité prahové hodnoty. Dokud není zařízení 50-60% plné, všechny čtení ze zařízení jsou přístupné z mezipaměti, což je rychlejší. Jakmile využité místo na zařízení překročí tuto prahovou hodnotu, zařízení začne odebírat místní soubory.
 
- **Využití omezené šířky pásma** – data lze zapisovat do Azure, i když je síť omezena, aby se snížilo využití během frekventované pracovní doby.  

## <a name="key-capabilities"></a>Klíčové funkce

Data Box Gateway nabízí následující funkce:

|Schopnost |Popis  |
|---------|---------|
|Rychlost     | Plně automatizovaný a vysoce optimalizovaný přenos dat a šířka pásma.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [systémových požadavcích pro Data Box Gateway](data-box-gateway-system-requirements.md).|
|Přístup k datům     | Jakmile jsou data odeslaná zařízením v cloudu, lze je dále upravit přímým přístupem k rozhraním API cloudu.|
|Rychlý přístup     | Místní mezipaměť na zařízení pro rychlý přístup k naposledy použitým souborům.|
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Odolnost     | Integrovaná odolnost sítě.        |


## <a name="specifications"></a>Specifikace

Virtuální zařízení Data Box Gateway má následující specifikace:

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Virtuální procesory (jádra)   | Minimálně 4 |
| Memory (Paměť)  |Minimálně 8 GB|
| Dostupnost|Jeden uzel|
| Disky|Disk operačního systému: 250 GB <br> Datový disk: minimálně 2 TB, dynamicky zřizovaný, musí být zálohován pomocí disků SSD|
| Síťová rozhraní |1 nebo více rozhraní virtuální sítě|
| Nativní protokoly pro sdílení souborů|SMB a NFS  |
| Zabezpečení|Ověřování pro odemknutí přístupu k zařízení a datům <br> Přenášená data šifrovaný pomocí 256bitového šifrování AES|
| Správa|Místní webové uživatelské rozhraní – počáteční instalace, diagnostika a řízení spotřeby zařízení <br> Azure Portal – každodenní správa zařízení Data Box Gateway       |

## <a name="components"></a>Komponenty

Řešení Data Box Gateway sestává z prostředku Data Box Gateway, virtuálního zařízení Data Box Gateway a místního webového uživatelského rozhraní.

- **Virtuální zařízení Data Box Gateway** – zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru, které umožňuje odesílat data do Azure.
    
- **Prostředek Data Box Gateway** – prostředek na webu Azure Portal, který vám umožní spravovat zařízení Data Box Gateway z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Pomocí prostředku brány datové schránky můžete zobrazit a spravovat zařízení, sdílené složky, uživatele a výstrahy. Další informace najdete v tématu [Správa pomocí portálu Azure](data-box-gateway-manage-shares.md).

- **Místní webové uživatelské rozhraní datové schránky** – Pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a restartovat zařízení, vygenerovat balíček podpory nebo kontaktovat podporu společnosti Microsoft a podat žádost o službu. Další informace naleznete v tématu [Správa pomocí místního webového uživatelského uživatelského prostředí](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box Gateway fyzické zařízení, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být všechny ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, kde je prostředek Data Box Edge k dispozici, přejděte na [produkty Azure dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox). Brána datových schronů se dá nasadit taky v Azure Government Cloud. Další informace najdete v tématu [Co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure.

    Oblasti, do kterých účty úložiště ukládají data služby Data Box, by se měly nacházet v blízkosti umístění zařízení, aby byl zajištěn optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.


## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.

