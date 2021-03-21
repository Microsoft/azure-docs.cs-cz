---
title: Microsoft Azure Data Box Gateway– přehled | Microsoft Docs
description: Tento článek popisuje Azure Data Box Gateway, což je řešení úložiště virtuálního zařízení, které umožňuje přenášet data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: 2adbefff2a0154268157c7d1735d83e70aa92ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563585"
---
# <a name="what-is-azure-data-box-gateway"></a>Co je Azure Data Box Gateway?

Azure Data Box Gateway je řešení úložiště, které vám umožňuje hladce odesílat data do Azure. Tento článek poskytuje přehled řešení Azure Data Box Gateway, jeho výhody, klíčové funkce a scénáře, ve kterých můžete toto zařízení nasadit.

Data Box Gateway je virtuální zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru. Toto virtuální zařízení se nachází ve vašich prostorách a data na něj zapisujete pomocí protokolů NFS a SMB. Zařízení potom přenese data do objektu blob bloku nebo objektu blob stránky Azure, případně do souborů Azure.

## <a name="use-cases"></a>Případy použití

Data Box Gateway lze využít pro přenos dat do cloudu, například pro účely archivace, zotavení po havárii nebo v případě potřeby zpracovat data v cloudovém měřítku. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Gateway.

- **Archivace v cloudu** – umožňuje zkopírovat stovky terabajtů dat do úložiště Azure pomocí zařízení Data Box Gateway bezpečným a efektivním způsobem. Ve scénářích archivace lze data přijímat jednorázově nebo průběžně.

- **Průběžné přijímání dat** – nepřetržitá příjem dat do zařízení pro kopírování do cloudu bez ohledu na velikost dat. Při zápisu dat do zařízení brány zařízení nahraje data do Azure Storage.  

- **Počáteční hromadný přenos následovaný přírůstkovým přenosem** – data box použít pro hromadný přenos v režimu offline (počáteční počáteční hodnoty) a data box Gateway přírůstkových přenosů (pokračujících kanálů) po síti.

Další informace najdete v [Azure Data box Gateway případech použití](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Výhody

Data Box Gateway má následující výhody:

- **Snadný přenos dat** – přenos dat do a z úložiště Azure je stejně snadný jako práce s místní sdílenou síťovou složkou.  
- **Vysoký výkon** – vysoce výkonné přenosy dat do a z Azure eliminují nepříjemnosti spojené s přenosem dat přes síť.
- **Rychlý přístup a rychlost přijímání dat za běhu v pracovní době** – data box Gateway má místní mezipaměť, kterou definujete jako velikost místní kapacity při zřizování virtuálního zařízení. Velikost datového disku by měla být zadaná podle [minimálních požadavků na virtuální zařízení](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). Místní mezipaměť nabízí následující výhody:
    - Místní mezipaměť umožňuje přijímání dat vysokou mírou. Pokud během špičky v pracovní době dojde k ingestování vysokého množství dat, mezipaměť může uchovávat data a nahrajte ji do cloudu.
    - Místní mezipaměť umožňuje rychlý přístup pro čtení až do určité prahové hodnoty. Až do chvíle, kdy je zařízení 50-60%, bude z mezipaměti k dispozici všechny čtení ze zařízení, které je bude rychlejší. Jakmile se využité místo na zařízení překročí k této prahové hodnotě, zařízení začne odebírat místní soubory.
 
- **Využití omezené šířky pásma** – data lze zapisovat do Azure, i když je síť omezena, aby se snížilo využití během frekventované pracovní doby.  

## <a name="key-capabilities"></a>Klíčové funkce

Data Box Gateway nabízí následující funkce:

|Schopnost |Description  |
|---------|---------|
|Rychlost     | Plně automatizovaný a vysoce optimalizovaný přenos dat a šířka pásma.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [systémových požadavcích pro Data Box Gateway](data-box-gateway-system-requirements.md).|
|Přístup k datům     | Jakmile se data odesílaná zařízením nacházejí v cloudu, můžete je dál upravovat přímým přístupem k rozhraním API cloudu.|
|Rychlý přístup     | Místní mezipaměť na zařízení pro rychlý přístup k naposledy použitým souborům.|
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Odolnost     | Integrovaná odolnost sítě.        |


## <a name="specifications"></a>Specifikace

Virtuální zařízení Data Box Gateway má následující specifikace:

| Specifikace                                          | Description              |
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
    
- **Prostředek Data Box Gateway** – prostředek na webu Azure Portal, který vám umožní spravovat zařízení Data Box Gateway z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Pomocí prostředku Data Box Gateway můžete zobrazit a spravovat zařízení, sdílené položky, uživatele a výstrahy. Další informace najdete v tématu [Správa pomocí Azure Portal](data-box-gateway-manage-shares.md).

- **Data box lokální webové uživatelské rozhraní** – pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a restartovat zařízení, vytvořit balíček pro podporu nebo kontaktovat podpora Microsoftu k zabalení žádosti o službu. Další informace najdete v tématu [Správa pomocí místního webového uživatelského rozhraní](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box Gateway zařízení, prostředku Azure a cílového účtu úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je prostředek Azure Data box Gateway k dispozici, najdete v části [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox). Data Box Gateway taky můžete nasadit v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](../azure-government/documentation-government-welcome.md).

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure.

    Oblasti, do kterých účty úložiště ukládají data služby Data Box, by se měly nacházet v blízkosti umístění zařízení, aby byl zajištěn optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.


## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.
