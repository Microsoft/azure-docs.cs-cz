---
title: Načítání informací o stavu pro úlohu služby Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak získat informace o stavu pro úlohy služby Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: e40d8e7c05213e99fc2ef65f5dc05f17ba0d185e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890531"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Načítání informací o stavu pro úlohu importu/exportu
Můžete volat [Get Job](/rest/api/storageimportexport/jobs) operace k načtení informací o obou import a export úloh. Vrácené informace zahrnují:

-   Aktuální stav úlohy.

-   Přibližné procento dokončení každé úlohy.

-   Aktuální stav každé jednotky.

-   Identifikátory URI pro objekty BLOB obsahující protokoly chyb a podrobné informace o protokolování (je-li povoleno).

Následující části popisují informace vrácené `Get Job` operace.

## <a name="job-states"></a>Stavy úloh
Tabulky a diagramu stavu níže popisují stavy, které úloha přejde prostřednictvím během životního cyklu. Aktuální stav úlohy se dají určit pomocí volání `Get Job` operace.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

Následující tabulka popisuje každý stav, který úloha může procházet.

|Stav úlohy|Popis|
|---------------|-----------------|
|`Creating`|Po volání operace Put úlohy, se vytvoří úloha a její stav je nastavený na `Creating`. Během úlohy `Creating` stavu služby Import/Export předpokládá jednotky nebyly byl odeslán do datového centra. Úloha může zůstat v `Creating` stavu po dobu až dvou týdnů, po jejichž uplynutí je automaticky odstraní službu.<br /><br /> Pokud zavoláte operaci aktualizovat vlastnosti úlohy, pokud úloha v `Creating` stavu úlohy zůstává ve `Creating` stavu a časový limit se resetuje na dva týdny.|
|`Shipping`|Po dodání vašeho balíčku, byste měli volat operaci aktualizovat vlastnosti úlohy aktualizace stavu úlohy `Shipping`. Stav přesouvání lze nastavit pouze v případě `DeliveryPackage` (poštovní dopravce a sledování Číslo) a `ReturnAddress` byly nastaveny pro úlohu.<br /><br /> Úloha zůstane ve stavu dopravě po dobu až dvou týdnů. Pokud jste předali dva týdny, a neobdrželi jednotky, operátory služby Import/Export budete upozorněni.|
|`Received`|Po přijetí všech jednotkách v datovém centru se nastaví stav úlohy do stavu Received.|
|`Transferring`|Po zahájení zpracování alespoň jednu jednotku jednotky byly přijaty v datovém centru a stav úlohy bude nastavena na `Transferring` stavu. Zobrazit `Drive States` níže v části Podrobné informace.|
|`Packaging`|Po dokončení zpracování všech jednotkách, úlohy budou umístěny do `Packaging` stavu, dokud se dodávají jednotky zpátky k zákazníkovi.|
|`Completed`|Po všechny jednotky byly dodány zpět s konkrétním zákazníkem, pokud byla úloha dokončena bez chyb, potom úloha se nastaví `Completed` stavu. Úloha se automaticky odstraní po uplynutí 90 dnů v `Completed` stavu.|
|`Closed`|Po všechny jednotky byly dodány zpět s konkrétním zákazníkem, pokud byly nějaké chyby při zpracování úlohy, potom úloha se nastaví `Closed` stavu. Úloha se automaticky odstraní po uplynutí 90 dnů v `Closed` stavu.|

Zrušit úlohu jenom na určité stavy. Zrušené úlohy přeskočí krok kopírování dat, ale jinak postupuje stejné přechodů jako úlohu, která nebyla zrušena.

Následující tabulka popisuje chyby, které se mohou vyskytnout pro každý stav úlohy a také vliv na úlohy, když dojde k chybě.

|Stav úlohy|Událost|Řešení / další kroky|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Byly přijaty jeden nebo více disků pro úlohu, ale není v projektu `Shipping` stav nebo neexistuje žádný záznam úlohy ve službě.|Provozní tým služby Import/Export se vás pokusí kontaktovat zákazník vytvořit nebo aktualizovat úlohu s informace potřebné k úloze posunout vpřed.<br /><br /> Pokud provozní tým není schopen kontaktovat během dvou týdnů, provozní tým se pokusí vrátit jednotky.<br /><br /> V případě, že jednotky nejde vrátit a zákazník je nedostupné, jednotky bezpečně zničeny v 90 dnů.<br /><br /> Všimněte si, že úlohy nelze zpracovat, dokud jeho stav je aktualizován, aby `Shipping`.|
|`Shipping`|Balíček pro úlohu nebyla dodána pro více než dva týdny.|Provozní tým bude informovat zákazníka chybí balíček. Závislosti na odpovědi zákazníka, provozní tým se rozšířit interval čekání balíčku můžete přejít, nebo zrušit úlohy.<br /><br /> V případě, že zákazníka nelze kontaktovat nebo neobjeví odpověď během 30 dnů, provozní tým opraví, zahájí se akce, které chcete přesunout úlohy z `Shipping` přímo do stavu `Closed` stavu.|
|`Completed/Closed`|Jednotky nikdy dosaženo zpáteční adresu nebo byly v dodávky (platí jenom pro úlohu exportu).|Pokud není dosažením zpáteční adresu, by měl zákazník nejprve volání operace získání úlohy nebo kontrole stavu úlohy na portálu a ujistěte se, že byl odeslán jednotky. Pokud jednotky byly dodány, by měl zákazník kontaktujte poskytovatele dopravě a zkuste najít jednotky.<br /><br /> Pokud jednotky jsou poškozené během dodávky, Zákazník může chtít požádat o další úloha exportu nebo chybějící objekty BLOB můžete stáhnout.|
|`Transferring/Packaging`|Úloha má nesprávnou nebo chybějící návratovou hodnotu.|Provozní tým vás bude kontaktovat kontaktní osobě pro úlohu pro získání správné adresy.<br /><br /> V případě, že zákazník je nedostupné, jednotky bezpečně zničeny v 90 dnů.|
|`Creating / Shipping/ Transferring`|Přesouvání balíčku je součástí jednotce, která nejsou uvedena v seznamu jednotek, které se mají naimportovat.|Další jednotky nebude možné zpracovat a bude vrácen zákazníkovi, pokud je úloha dokončena.|

## <a name="drive-states"></a>Stavy disku
Tabulky a diagramu níže popisují životní cyklus jednotlivé jednotky jako přechází prostřednictvím úlohu importu nebo exportu. Aktuální stav jednotky můžete načíst pomocí volání `Get Job` operace a zkontrolujete `State` elementu `DriveList` vlastnost.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

Následující tabulka popisuje každý stav, který může procházet jednotku.

|Stav disku|Popis|
|-----------------|-----------------|
|`Specified`|Pro úlohu importu při vytvoření úlohy se operace Put úlohy počáteční stav pro jednotku je `Specified` stavu. Pro úlohu exportu protože není zadána žádná jednotka při vytvoření úlohy, stav počáteční jednotku je `Received` stavu.|
|`Received`|Na jednotce přejde do `Received` stavu, kdy operátor služby Import/Export zpracovala jednotky, které byly přijaty z přepravní společnost pro úlohu importu. Pro úlohu exportu stav počáteční jednotku je `Received` stavu.|
|`NeverReceived`|Na jednotce se přesunou na `NeverReceived` stav, když dorazí balíček pro úlohu, ale balíček neobsahuje jednotce. Jednotky můžete také přesunout do tohoto stavu, pokud to bylo dva týdny, protože služba přijala dodací informace, ale balíček ještě nedorazily v datovém centru.|
|`Transferring`|Jednotky se přesunou na `Transferring` stavu, kdy začne služba přenosu dat z jednotky do služby Windows Azure Storage.|
|`Completed`|Jednotky se přesunou na `Completed` stavu, kdy služba byla úspěšně převedena všechna data bez chyb.|
|`CompletedMoreInfo`|Jednotky se přesunou na `CompletedMoreInfo` stavu, když službu došlo k některým problémům při kopírování dat z nebo na jednotku. Informace může patřit chyby, upozornění nebo informační zprávy o přepsání objektů BLOB.|
|`ShippedBack`|Na jednotce se přesunou na `ShippedBack` stav, když má byla odeslaná z zpět System center data na návratovou adresu.|

Následující tabulka popisuje stavy selhání jednotky a akce prováděné na jednotlivých stavech.

|Stav disku|Událost|Řešení / další krok|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Jednotku, která je označena jako `NeverReceived` (protože nebyla přijata jako součást úlohy dodávky) dorazí v jiném dodávky.|Provozní tým se přesune jednotku `Received` stavu.|
|`N/A`|Jednotce, která není součástí jakoukoliv úlohu dorazí na datovém centru jako součást jiná úloha.|Jednotka se označí jako dodatečné jednotky a vrátí se zákazníkům při dokončení úlohy spojené s původní balíček.|

## <a name="faulted-states"></a>Chybovém stavu
Selhání úlohy nebo jednotky obvykle projděte očekávané životního cyklu, úlohy nebo jednotky se přesunou do `Faulted` stavu. V tomto okamžiku provozní tým bude kontaktovat e-mailem nebo telefon. Po vyřešení problému chybnou úlohy nebo jednotky se provedou mimo `Faulted` stavu a přešli do příslušné stavu.

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
