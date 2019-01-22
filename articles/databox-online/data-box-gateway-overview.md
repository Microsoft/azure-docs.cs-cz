---
title: Microsoft Azure Data Box Gateway– přehled | Microsoft Docs
description: Tento článek popisuje Azure Data Box Gateway, což je řešení úložiště virtuálního zařízení, které umožňuje přenášet data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 01/18/2019
ms.author: alkohli
ms.openlocfilehash: 9670d67fa1eb79e9e5e8c81726c10cc78767fb74
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435457"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Co je Azure Data Box Gateway (Preview)? 

Azure Data Box Gateway je řešení úložiště, které vám umožňuje hladce odesílat data do Azure. Tento článek poskytuje přehled řešení Azure Data Box Gateway, jeho výhody, klíčové funkce a scénáře, ve kterých můžete toto zařízení nasadit. 

Data Box Gateway je virtuální zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru. Toto virtuální zařízení se nachází ve vašich prostorách a data na něj zapisujete pomocí protokolů NFS a SMB. Zařízení potom přenese data do objektu blob bloku nebo objektu blob stránky Azure, případně do souborů Azure. 

> [!IMPORTANT]
> Data Box Gateway je ve verzi Preview. Před nasazením tohoto řešení si prosím přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Případy použití

Data Box Gateway lze využít pro přenos dat do cloudu, například pro účely archivace, zotavení po havárii nebo v případě potřeby zpracovat data v cloudovém měřítku. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Gateway.

- **Archivace v cloudu** – umožňuje zkopírovat stovky terabajtů dat do úložiště Azure pomocí zařízení Data Box Gateway bezpečným a efektivním způsobem. Ve scénářích archivace lze data přijímat jednorázově nebo průběžně.

- **Agregace dat** – umožňuje agregovat data z více zdrojů v jediném umístění ve službě Azure Storage za účelem zpracování a analýzy dat.  

- **Integrace s místními sadami funkcí** – umožňuje integraci s místními sadami funkcí, jako je zálohování a obnovení, které používají cloudové úložiště a které potřebují místní přístup k běžně používaným souborům. 

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
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu.|
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
| Memory (Paměť)  | Minimálně 8 GB|
| Dostupnost|Jeden uzel|
| Disky| Disk s operačním systémem: 250 GB <br> Datový disk: Minimálně 2 TB, dynamického zajišťování zřízené a musí být založená na jednotkách SSD|
| Síťová rozhraní|1 nebo více rozhraní virtuální sítě|
| Nativní protokoly pro sdílení souborů|SMB a NFS  |
| Zabezpečení| Ověřování pro odemknutí přístupu k zařízení a datům <br> Přenášená data šifrovaný pomocí 256bitového šifrování AES|
| Správa| Místní webové uživatelské rozhraní – počáteční instalace, diagnostika a řízení spotřeby zařízení <br> Azure Portal – každodenní správa zařízení Data Box Gateway       |


## <a name="components"></a>Komponenty

Řešení Data Box Gateway sestává z prostředku Data Box Gateway, virtuálního zařízení Data Box Gateway a místního webového uživatelského rozhraní.

* **Virtuální zařízení Data Box Gateway** – zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru, které umožňuje odesílat data do Azure. 
    
* **Prostředek Data Box Gateway** – prostředek na webu Azure Portal, který vám umožní spravovat zařízení Data Box Gateway z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Prostředek Data Box Gateway slouží k vytvoření a správě prostředků, zobrazení, správě zařízení a výstrah a ke správě sdílených složek.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Místní webové uživatelské rozhraní Data Box** – můžete ho použít ke spuštění diagnostiky, vypnutí nebo restartování zařízení Data Box Gateway, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Data Box Edge, prostředek Azure a účet cílového úložiště, do kterého přenášíte data, se nemusí nacházet ve stejné oblasti.

- **Dostupnost prostředku** – u této vydané verze je prostředek Data Box Edge dostupný v následujících oblastech:
    - **Spojené státy americké** – Západní USA 2 a Východní USA
    - **Evropská unie** – Západní Evropa
    - **Asie a Tichomoří** – Jihovýchodní Asie

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. 

    Oblasti, do kterých účty úložiště ukládají data služby Data Box, by se měly nacházet v blízkosti umístění zařízení, aby byl zajištěn optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon. 


## <a name="next-steps"></a>Další postup

- Přečtěte si [systémové požadavky služby Data Box Gateway](data-box-gateway-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Gateway](data-box-gateway-limits.md).
- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.




