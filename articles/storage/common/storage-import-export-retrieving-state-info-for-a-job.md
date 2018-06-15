---
title: Načítání informací o stavu pro úlohu Azure Import/Export | Microsoft Docs
description: Zjistěte, jak získat informace o stavu úloh služby Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874007"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Načítání informací o stavu pro úlohu importu/exportu
Můžete volat [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operace k načtení informací o obě import a export úloh. Vrátí následující informace:

-   Aktuální stav úlohy.

-   Přibližná procento, každá úloha se dokončila.

-   Aktuální stav každé jednotky.

-   Identifikátory URI pro objekty BLOB obsahující protokoly chyb a podrobné informace o protokolování (Pokud je povoleno).

Následující části popisují informace o vrácené `Get Job` operace.

## <a name="job-states"></a>Stavy úlohy
V tabulce a stavu obrázek popisují stavy, které úloha přejde prostřednictvím během jejich životního cyklu. Aktuální stav úlohy můžete určit voláním `Get Job` operaci.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

Následující tabulka popisuje všechny stavy, které může předávat úlohu.

|Stav úlohy|Popis|
|---------------|-----------------|
|`Creating`|Po volání operace Put úlohy, vytvoření úlohy a její stav je nastavený na `Creating`. Když úloha `Creating` stavu, službu Import/Export předpokládá jednotky nebyly byla odeslaná do datového centra. Úlohu může zůstat v `Creating` dobu až dvou týdnů, po které se automaticky odstraní službou stavu.<br /><br /> Pokud provádíte volání operace vlastnosti úlohy aktualizace během úlohy `Creating` stavu úlohy zůstane v `Creating` stavu a interval vypršení časového limitu je obnovena na dva týdny.|
|`Shipping`|Po dodáte vašeho balíčku, měli byste zavolat operaci aktualizace aktualizovat vlastnosti úlohy stav úlohy `Shipping`. Stav přesouvání lze nastavit pouze v případě `DeliveryPackage` (poštovní poskytovatel a sledování Číslo) a `ReturnAddress` byly nastaveny pro úlohu.<br /><br /> Úloha zůstane ve stavu přesouvání dobu až dvou týdnů. Pokud byly úspěšně dva týdny, a jednotky nebyly přijaty, operátory službu Import/Export budete upozorněni.|
|`Received`|Po všechny jednotky byly přijaty v datovém centru, stav úlohy bude nastavit stav přijaté.|
|`Transferring`|Poté, co byly přijaty jednotky v datovém centru a alespoň jednu jednotku zahájení zpracování, stav úlohy je možnost `Transferring` stavu. Najdete v článku `Drive States` části níže podrobné informace.|
|`Packaging`|Po dokončení zpracování všech jednotkách, úloha bude uložena v umístění `Packaging` stavu, dokud jednotky jsou sice zpět na zákazníka.|
|`Completed`|Po všechny jednotky byly dodány zpět na zákazníka, pokud úloha byla dokončena bez chyb, potom úlohu se nastaví `Completed` stavu. Úlohy budou automaticky odstraněny po 90 dnech v `Completed` stavu.|
|`Closed`|Po všechny jednotky byly dodány zpět na zákazníka, pokud zde nejsou žádné chyby během zpracování úlohy, potom úlohu se nastaví `Closed` stavu. Úlohy budou automaticky odstraněny po 90 dnech v `Closed` stavu.|

Můžete zrušit úlohu jenom na určité stavy. Zrušené úlohy přeskočí krok kopie dat, ale jinak postupuje stejné přechodů mezi stavy jako úlohu, která nebyla zrušena.

Následující tabulka popisuje chyby, které se můžou vyskytnout pro každý stav úlohy a také vliv na úlohu, když dojde k chybě.

|Stav úlohy|Událost|Řešení / další kroky|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Byly přijaty jedné nebo více jednotek pro úlohu, ale není v úlohy `Shipping` stavu nebo není žádný záznam úlohy ve službě.|Provozní tým služby importu a exportu se vás pokusí kontaktovat zákazník vytvořit nebo aktualizovat úlohu informace nezbytné k přejít úlohy.<br /><br /> Pokud je provozní tým nelze kontaktovat zákazník během dvou týdnů, provozní tým se pokusí vrátit jednotky.<br /><br /> V případě, že jednotky nelze vrátit, a zákazník nelze získat přístup, jednotky bude bezpečným způsobem zničena za 90 dní.<br /><br /> Všimněte si, že úlohy nelze zpracovat, dokud jeho stav se aktualizuje na `Shipping`.|
|`Shipping`|Balíček pro úlohu nebyl doručen více než dva týdny.|Provozní tým oznámí zákazník chybějící balíčku. Podle zákazníka odpovědi, provozní tým se rozšířit intervalu čekání balíčku, který má doručení, nebo úlohu zrušit.<br /><br /> V případě, že zákazník nelze kontaktovat nebo nereaguje do 30 dní, provozní tým zahájí akce přesouvat úlohy z `Shipping` přímo do stavu `Closed` stavu.|
|`Completed/Closed`|Jednotky nikdy dosaženo zpáteční adresu nebo jsou poškozené v dodávky (platí pouze pro úlohy exportu).|Pokud jednotky nedojde k dosažení zpáteční adresu, musí zákazník nejprve volání operace Get Job nebo zkontrolujte stav úlohy na portálu a ujistěte se, že bylo odesláno jednotky. Pokud bylo dodáno na discích, musí zákazník obraťte na poskytovatele přesouvání a zkuste najít jednotky.<br /><br /> Pokud během dodávky jsou poškozené jednotky, Zákazník může chtít požádat jiná úloha exportu nebo stáhnout chybějící objekty BLOB.|
|`Transferring/Packaging`|Úloha má nesprávnou nebo chybějící zpáteční adresu.|Provozní tým bude přístup ke kontaktní osoby pro úlohu správnou adresu získat.<br /><br /> V případě, že zákazník nedostupné, jednotky bude bezpečným způsobem zničena za 90 dní.|
|`Creating / Shipping/ Transferring`|Disk, který se nenachází v seznamu jednotek určených k importu je součástí balíčku přesouvání.|Další jednotky nebude zpracován a obnoví se zákazník při dokončení úlohy.|

## <a name="drive-states"></a>Stavy jednotky
V tabulce a na obrázku níže popisují životní cyklus jednotlivé jednotky jako přechází prostřednictvím úlohu import nebo export. Aktuální stav disku můžete načíst pomocí volání `Get Job` operace a zkontrolujete `State` element `DriveList` vlastnost.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

Následující tabulka popisuje všechny stavy, které může předávat na jednotku.

|Stav disku|Popis|
|-----------------|-----------------|
|`Specified`|Pro úlohu importu při vytvoření úlohy v operaci Put úlohy je počáteční stav pro jednotku `Specified` stavu. Pro úlohy exportu, protože není zadána žádná jednotka při vytvoření úlohy, stav počáteční jednotky je `Received` stavu.|
|`Received`|Jednotka přechází do `Received` stavu, při importu a exportu služby operátor zpracovala jednotky, které bylo přijato z společnosti přesouvání úlohy importu. Pro úlohy exportu, je stav počáteční jednotku `Received` stavu.|
|`NeverReceived`|Jednotka přesune do `NeverReceived` stav, když dorazí balíčku pro úlohy, ale balíček neobsahuje jednotku. Jednotku také můžete přesunout do tohoto stavu, pokud to bylo dva týdny, protože služba přijala přesouvání informace, ale balíček nebyl ještě přijaty v datovém centru.|
|`Transferring`|Bude přesouvat na jednotku `Transferring` stavu, kdy začne služba přenosu dat z jednotky do služby Windows Azure Storage.|
|`Completed`|Bude přesouvat na jednotku `Completed` stavu, když má služba úspěšně přenesla všechna data bez chyb.|
|`CompletedMoreInfo`|Bude přesouvat na jednotku `CompletedMoreInfo` stavu, kdy služba narazila některé problémy při kopírování dat z nebo na jednotku. Informace může obsahovat chyby, upozornění a informativní zprávy o přepsání objektů BLOB.|
|`ShippedBack`|Jednotka přesune do `ShippedBack` stav, když ho byl dodán z center zálohování dat zpáteční adresu.|

Následující tabulka popisuje stavy selhání jednotky a akcí provedených pro každý stav.

|Stav disku|Událost|Řešení / další krok|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Jednotku, která je označena jako `NeverReceived` (protože nebyla přijata jako součást úlohy dodávky) dorazí v jiné dodávky.|Provozní tým přesune jednotky a `Received` stavu.|
|`N/A`|Jednotku, která není součástí všechny úlohy dorazí v datovém centru jako součást jiná úloha.|Jednotka budou označeny jako další jednotky a obnoví se zákazník při dokončení úlohy spojené s původní balíčku.|

## <a name="faulted-states"></a>Chybný stav
Pokud úlohy nebo jednotka nezdaří normálně projděte očekávané celou dobu životního cyklu, úlohy nebo jednotka bude přesunuta do `Faulted` stavu. V tomto bodě provozní tým vás bude kontaktovat zákazník e-mailem nebo telefon. Jakmile je problém vyřešen, chybný úlohy nebo jednotka se provedou mimo `Faulted` stavu a přesouvat do příslušné stavu.

## <a name="next-steps"></a>Další kroky

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
