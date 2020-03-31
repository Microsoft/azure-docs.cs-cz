---
title: Poradce při potížích s chybami zprostředkovatele prostředků souborů Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje příčiny, řešení a zástupná řešení pro běžné chyby zprostředkovatele prostředků Soubory Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597212"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Řešení potíží s chybami poskytovatele prostředků Azure NetApp Files 

Tento článek popisuje běžné chyby zprostředkovatele prostředků Azure NetApp Files, jejich příčiny, řešení a řešení (pokud jsou k dispozici).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Běžné chyby zprostředkovatele prostředků Azure NetApp Files

***BareMetalTenantId nelze změnit.***  

K této chybě dochází při pokusu o `BaremetalTenantId` aktualizaci nebo opravu svazku a vlastnost má změněnou hodnotu.

* Příčina:   
Pokoušíte se aktualizovat svazek `BaremetalTenantId` a vlastnost má jinou hodnotu než hodnota uložená v Azure.
* Řešení:   
Nezahrnejte `BaremetalTenantId` do žádosti o opravu a aktualizaci (put). Případně se ujistěte, že `BaremetalTenantId` je stejný v požadavku.

***Úroveň služby ServiceLevel nelze změnit.***  

K této chybě dochází při pokusu o aktualizaci nebo opravu fondu kapacity s jinou úrovní služby, pokud fond kapacity již obsahuje svazky v něm.

* Příčina:   
Pokoušíte se aktualizovat úroveň služby fondu kapacity, pokud fond obsahuje svazky.
* Řešení:   
Odstraňte všechny svazky z fondu kapacity a změňte úroveň služby.
* Alternativní řešení:   
Vytvořte další fond kapacit a pak znovu vytvořte svazky v novém fondu kapacit.

***PoolId nelze změnit***  

K této chybě dochází při pokusu o aktualizaci `PoolId` nebo opravu fondu kapacity se změněnou vlastností.

* Příčina:   
Pokoušíte se aktualizovat vlastnost `PoolId` fondu kapacity. Vlastnost `PoolId` je vlastnost jen pro čtení a nelze ji změnit.
* Řešení:   
Nezahrnejte `PoolId` do žádosti o opravu a aktualizaci (put).  Případně se ujistěte, že `PoolId` je stejný v požadavku.

***CreationToken nelze změnit.***

K této chybě dochází při pokusu`CreationToken`o změnu cesty k souboru ( ) po vytvoření svazku. Cesta k`CreationToken`souboru ( ) musí být nastavena při vytvoření svazku a nelze jej později změnit.

* Příčina:   
Pokoušíte se změnit cestu`CreationToken`k souboru ( ) po vytvoření svazku, což není podporovaná operace. 
* Řešení:   
Pokud změna cesty k souboru není potřeba, zvažte odebrání parametru z požadavku na zavření chybové zprávy.
* Alternativní řešení:   
Pokud potřebujete změnit cestu k`CreationToken`souboru ( ), můžete vytvořit nový svazek s novou cestou k souboru a potom migrovat data na nový svazek.

***CreationToken musí mít alespoň 16 znaků.***

K této chybě dochází,`CreationToken`pokud cesta k souboru ( ) nesplňuje požadavek na délku. Délka cesty k souboru musí být alespoň jeden znak na délku.

* Příčina:   
Cesta k souboru je prázdná.  Při vytváření svazku pomocí rozhraní API je vyžadován token vytvoření. Pokud používáte portál Azure, cesta k souboru se vygeneruje automaticky.
* Řešení:   
Jako cestu k souboru zadejte alespoň jeden znak (`CreationToken`).

***Název domény nelze změnit.***

K této chybě dochází při pokusu o změnu názvu domény ve službě Active Directory.

* Příčina:   
Pokoušíte se aktualizovat vlastnost názvu domény.
* Řešení:    
Žádné. Název domény nelze změnit.
* Alternativní řešení:   
Odstraňte všechny svazky pomocí konfigurace služby Active Directory. Potom odstraňte konfiguraci služby Active Directory a znovu vytvořte svazky.

***Duplicitní chyba hodnoty pro objekt ExportPolicy.Rules[RuleIndex].***

K této chybě dochází, pokud zásady exportu nejsou definovány s jedinečným indexem. Při definování zásad exportu musí mít všechna pravidla zásad exportu jedinečný index mezi 1 a 5.

* Příčina:   
Definovaná zásada vývozu nesplňuje požadavek na pravidla zásad vývozu. Musíte mít jedno pravidlo zásad exportu na minimum a maximálně pět pravidel zásad exportu.
* Řešení:   
Ujistěte se, že index již není použit a že je v rozsahu od 1 do 5.
* Alternativní řešení:   
Pro pravidlo, které se pokoušíte nastavit, použijte jiný index.

***Chyba {akce} {resourceTypeName}***

Tato chyba se zobrazí, pokud se při provádění akce s prostředkem nepodařilo zpracovat jiné zpracování chyb.   Obsahuje text 'Chyba'. Může `{action}` být libovolný`getting`( `creating` `updating`, `deleting`, , nebo ).  The `{resourceTypeName}` je `resourceTypeName` (například `netAppAccount` `capacityPool`, `volume`, , a tak dále).

* Příčina:   
Tato chyba je neošetřená výjimka, kde není známa příčina.
* Řešení:   
Chcete-li v protokolech nahlásit podrobný důvod, obraťte se na Centrum odborné pomoci Azure.
* Alternativní řešení:   
Žádné.

***Název cesty souboru může obsahovat pouze písmena, čísla a pomlčky ("-""").***

K této chybě dochází, pokud cesta k souboru obsahuje nepodporované znaky, například tečka ("."), čárka (","), podtržítko ("_"), nebo znak dolaru ("$").

* Příčina:   
Cesta k souboru obsahuje nepodporované znaky, například tečku ("."), čárku (","), podtržítko ("_"), nebo znak dolaru ("$").
* Řešení:   
Z cesty k souboru, kterou jste zadali, odeberte znaky, které nejsou abecedními písmeny, čísly nebo spojovníky ("-").
* Alternativní řešení:   
Podtržítko můžete nahradit pomlčkou nebo použít velká písmena namísto mezer k označení začátku nových slov.  Například použijte "NewVolume" místo "nový svazek".

***Soubor FileSystemId nelze změnit.***

K této chybě dochází při `FileSystemId`pokusu o změnu .  Změna `FileSystemdId` není podporovaná operace. 

* Příčina:   
ID systému souborů je nastaveno při vytvoření svazku. `FileSystemId`nelze následně změnit.
* Řešení:   
Nezahrnejte `FileSystemId` do žádosti o opravu a aktualizaci (put).  Případně se ujistěte, že `FileSystemId` je stejný v požadavku.

***Služba ActiveDirectory s id: {string} neexistuje.***

Část `{string}` je hodnota, kterou `ActiveDirectoryId` jste zadali do vlastnosti připojení služby Active Directory.

* Příčina:   
Při vytváření účtu v konfiguraci služby Active Directory `ActiveDirectoryId` jste zadali hodnotu, která má být prázdná.
* Řešení:   
Nezahrnovat `ActiveDirectoryId` do požadavku vytvořit (put).

***Neplatná verze rozhraní API.***

Verze rozhraní API buď není odeslána, nebo obsahuje neplatnou hodnotu.

* Příčina:   
Hodnota v parametru `api-version` dotazu obsahuje neplatnou hodnotu.
* Řešení:   
Použijte správnou hodnotu verze rozhraní API.  Poskytovatel prostředků podporuje mnoho verzí rozhraní API. Hodnota je ve formátu yyyy-mm-dd.

***Byla přijata neplatná hodnota {value}. {1}***

Tato zpráva označuje chybu v `RuleIndex` `AllowedClients`polích `UnixReadWrite` `Nfsv3`pro `Nfsv4`, , `UnixReadOnly`, , a .

* Příčina:   
Požadavek na ověření vstupu se nezdařil alespoň `RuleIndex`pro `AllowedClients` `UnixReadOnly`jedno `UnixReadWrite` `Nfsv`z následujících polí: , , , , 3 a `Nfsv4`.
* Řešení:   
Ujistěte se, že na příkazovém řádku nastavíte všechny požadované a nekonfliktní parametry. Například nelze nastavit i `UnixReadOnly` `UnixReadWrite` parametry současně.
* Alternativní řešení:   
Viz výše uvedené řešení.

***Rozsah {0} IP {1} adres {2} pro vlan je již používán***

K této chybě dochází, protože interní záznamy použitých rozsahů IP adres jsou v konfliktu s nově přiřazenou adresou IP.

* Příčina:   
Adresa IP přiřazená pro vytvoření svazku je již zaregistrována.
Důvodem může být dřívější neúspěšné vytvoření svazku.
* Řešení:   
Kontaktujte Centrum podpory Azure.

***Chybí hodnota pro {property}.***

Tato chyba označuje, že v požadavku chybí požadovaná vlastnost. Řetězec {property} obsahuje název chybějící vlastnosti.

* Příčina:   
Požadavek na ověření vstupu se nezdařil alespoň pro jednu z vlastností.
* Řešení:   
Ujistěte se, že nastavit všechny požadované a nekonfliktní vlastnosti v požadavku, speciálně vlastnost z chybové zprávy.

***Cíle mountlze nelze změnit.***

K této chybě dochází, když se uživatel pokouší aktualizovat nebo opravit vlastnost MountTargets svazku.

* Příčina:   
Pokoušíte se aktualizovat `MountTargets` vlastnost svazku. Změna této vlastnosti není podporována.
* Řešení:   
Nezahrnejte `MountTargets` do žádosti o opravu a aktualizaci (put).  Případně se ujistěte, že `MountTargets` je stejný v požadavku.

***Jméno, které je již používáno.***

Tato chyba označuje, že název prostředku je již používán.

* Příčina:   
Pokoušíte se vytvořit prostředek s názvem, který se používá pro existující prostředek.
* Řešení:   
Při vytváření prostředku použijte jedinečný název.

***Cesta k souboru, která je již používána.***

Tato chyba označuje, že cesta k souboru pro svazek je již používána.

* Příčina:   
Pokoušíte se vytvořit svazek s cestou k souboru, který je stejný jako existující svazek.
* Řešení:   
Při vytváření svazku použijte jedinečnou cestu k souboru.

***Jméno příliš dlouho.***

Tato chyba označuje, že název prostředku nesplňuje požadavek maximální délky.

* Příčina:   
Název prostředku je příliš dlouhý.
* Řešení:   
Použijte kratší název pro prostředek.

***Cesta k souboru je příliš dlouhá.***

Tato chyba označuje, že cesta k souboru pro svazek nesplňuje požadavek maximální délky.

* Příčina:   
Cesta k souboru svazku je příliš dlouhá.
* Řešení:   
Použijte kratší cestu k souboru.

***Jméno je příliš krátké.***

Tato chyba označuje, že název prostředku nesplňuje požadavek na minimální délku.

* Příčina:   
Název prostředku je příliš krátký.
* Řešení:   
Použijte delší název pro prostředek.

***Cesta k souboru je příliš krátká.***

Tato chyba označuje, že cesta k souboru svazku nesplňuje požadavek na minimální délku.

* Příčina:   
Cesta k souboru svazku je příliš krátká.
* Řešení:   
Zvětšete délku cesty k souboru svazku.

***Azure NetApp Soubory API nedostupné.***

Rozhraní Azure API spoléhá na rozhraní API Azure NetApp Files API pro správu svazků. Tato chyba označuje problém s připojením rozhraní API.

* Příčina:   
Základní rozhraní API neodpovídá, výsledkem vnitřní chybu. Tato chyba je pravděpodobně dočasné.
* Řešení:   
Tato otázka bude pravděpodobně dočasná. Požadavek by měl být po určité době úspěšný.
* Alternativní řešení:   
Žádné. Základní rozhraní API je nezbytné pro správu svazků.

***Pro "{0}".***

Tato chyba označuje, že dokončení operace brání vnitřní chybě.

* Příčina:   
Došlo k vnitřní chybě a znemožňovalo dokončení operace.
* Řešení:   
Tato chyba je pravděpodobně dočasné. Počkejte několik minut a zkuste akci zopakovat. Pokud problém přetrvává, vytvořte lístek, aby technická podpora prošetřila problém.
* Alternativní řešení:   
Počkejte několik minut a zkontrolujte, zda problém přetrvává.

***Není povoleno kombinovat typy protokolů CIFS a NFS***

K této chybě dochází při pokusu o vytvoření svazku a existují typy protokolů CIFS (SMB) a NFS ve vlastnostech svazku.

* Příčina:   
Ve vlastnostech svazku se používají typy protokolů CIFS (SMB) i NFS.
* Řešení:   
Odeberte jeden z typů protokolu.
* Alternativní řešení:   
Ponechte vlastnost typu protokolu prázdnou nebo nulovou.

***Počet položek: {value} pro objekt: ExportPolicy.Rules[RuleIndex] je mimo maximální rozsah min-max.***

K této chybě dochází, pokud pravidla zásad exportu nesplňují požadavek na minimální nebo maximální rozsah. Pokud definujete zásady exportu, musí mít na minimu jedno pravidlo zásad exportu a maximálně pět pravidel zásad vývozu.

* Příčina:   
Definované zásady exportu nesplňují požadovaný rozsah.
* Řešení:   
Ujistěte se, že index již není použit a že je v rozsahu od 1 do 5.
* Alternativní řešení:   
Není povinné používat zásady vývozu na svazcích. Pokud nepotřebujete použít pravidla zásad exportu, můžete exportní zásady zcela vynechat.

***Povolen je pouze jeden active directory.***

K této chybě dochází při pokusu o vytvoření konfigurace služby Active Directory a jeden již existuje pro odběr v oblasti. K chybě může dojít také při pokusu o vytvoření více než jedné konfigurace služby Active Directory.

* Příčina:   
Pokoušíte se vytvořit (neaktualizovat) aktivní adresář, ale jeden již existuje.
* Řešení:   
Pokud se konfigurace služby Active Directory nepoužívá, můžete nejprve odstranit existující konfiguraci a potom operaci vytvoření zopakovat.
* Alternativní řešení:   
Žádné. Je povolena pouze jedna služba Active Directory.

***Operace {operation} není podporována.***

Tato chyba označuje, že operace není k dispozici pro aktivní předplatné nebo prostředek.

* Příčina:   
Operace není k dispozici pro odběr nebo prostředek.
* Řešení:   
Ujistěte se, že operace je zadána správně a že je k dispozici pro prostředek a odběr, který používáte.

***OwnerId nelze změnit***

K této chybě dochází při pokusu o změnu OwnerId vlastnost svazku. Změna OwnerId není podporovaná operace. 

* Příčina:   
Vlastnost `OwnerId` je nastavena při vytvoření svazku. Vlastnost nelze následně změnit.
* Řešení:   
Nezahrnejte `OwnerId` do žádosti o opravu a aktualizaci (put). Případně se ujistěte, že `OwnerId` je stejný v požadavku.

***Nadřazený fond nebyl nalezen.***

K této chybě dochází při pokusu o vytvoření svazku a fond kapacity, ve kterém vytváříte svazek nebyl nalezen.

* Příčina:   
Fond kapacity, kde je svazek vytvářen, nebyl nalezen.
* Řešení:   
S největší pravděpodobností fond nebyl plně vytvořen nebo již byl odstraněn v době vytvoření svazku.

***Operace opravy není pro tento typ prostředku podporována.***

K této chybě dochází při pokusu o změnu cíle připojení nebo snímek.

* Příčina:   
Cíl připojení je definován při jeho vytvoření a nelze jej následně změnit.
Snímky neobsahují žádné vlastnosti, které lze změnit.
* Řešení:   
Žádné. Tyto prostředky nemají žádné vlastnosti, které lze změnit.

***Velikost fondu je pro celkovou velikost svazku příliš malá.***

K této chybě dochází při aktualizaci velikosti fondu kapacity `usedBytes` a velikost je menší než celková hodnota všech svazků v tomto fondu kapacity.  K této chybě může dojít také při vytváření nového svazku nebo změně velikosti existujícího svazku a nová velikost svazku přesahuje volné místo ve fondu kapacity.

* Příčina:   
Pokoušíte se aktualizovat fond kapacity na menší velikost než použité bajty ve všech svazcích ve fondu kapacity.  Nebo se pokoušíte vytvořit svazek, který je větší než volné místo ve fondu kapacity.  Případně se pokoušíte změnit velikost svazku a nová velikost přesahuje volné místo ve fondu kapacity.
* Řešení:   
Nastavte velikost fondu kapacity na větší hodnotu nebo vytvořte menší svazek pro svazek.
* Alternativní řešení:   
Odeberte dostatek svazků, aby bylo možné aktualizovat velikost fondu kapacity na tuto velikost.

***Vlastnost: Umístění pro snímek musí být stejné jako svazek***

K této chybě dochází při vytváření snímku s umístěním než svazku, který vlastní snímek.

* Příčina:   
Neplatná hodnota ve vlastnosti Location pro snímek.
* Řešení:   
Nastavte platný řetězec ve vlastnosti Location.

***Název {resourceType} musí být stejný jako název identifikátoru prostředku.***

K této chybě dochází při vytváření prostředku a vyplníte vlastnost name jinou hodnotou než vlastnost název . `resourceId`

* Příčina:   
Neplatná hodnota ve vlastnosti name při vytváření prostředku.
* Řešení:   
Ponechte vlastnost name prázdnou nebo jí povolte použít stejnou hodnotu jako vlastnost name (mezi posledním `resourceId`zpětným lomítkem "/" a otazníkem "?") v .

***Typ protokolu {value} není znám.***

K této chybě dochází při vytváření svazku s neznámým typem protokolu.  Platné hodnoty jsou "NFSv3", "NFSv4" a "CIFS".

* Příčina:   
Pokoušíte se nastavit neplatnou `protocolType` hodnotu ve vlastnosti svazku.
* Řešení:   
Nastavte platný řetězec `protocolType`v .
* Alternativní řešení:   
Nastaveno `protocolType` jako null.

***Typy protokolů nelze změnit.***

K této chybě dochází při pokusu o aktualizaci nebo opravu `ProtocolType` svazku.  Změna typu ProtocolType není podporovanou operací.

* Příčina:   
Vlastnost `ProtocolType` je nastavena při vytvoření svazku.  Nelze jej aktualizovat.
* Řešení:   
Žádné.
* Alternativní řešení:   
Vytvořte jiný svazek s novými typy protokolů.

***Vytvoření zdroje typu {resourceType} by překročilo kvótu prostředků {quota} typu {resourceType} na {parentResourceType}. Aktuální počet prostředků je {currentCount}, prosím, odstraňte některé prostředky tohoto typu před vytvořením nového.***

K této chybě dochází při pokusu`NetAppAccount`o `CapacityPool` `Volume`vytvoření `Snapshot`prostředku ( , , , nebo ), ale kvóta dosáhla svého limitu.

* Příčina:   
Pokoušíte se vytvořit prostředek, ale je dosaženo limitu `NetAppAccounts` kvóty `CapacityPools` `NetAppAccount`(například: na předplatné nebo na ).
* Řešení:   
Zvyšte limit kvóty.
* Alternativní řešení:   
Odstraňte nepoužívané prostředky stejného typu a vytvořte je znovu.

***Byla přijata hodnota vlastnosti jen pro čtení {propertyName}.***

K této chybě dochází, když definujete hodnotu pro vlastnost, kterou nelze změnit. Například nelze změnit ID svazku.

* Příčina:   
Pokoušíte se upravit parametr (například ID svazku), který nelze změnit.
* Řešení:   
Neupravujte hodnotu vlastnosti.

***Požadovaný {resource} nebyl nalezen.***

K této chybě dochází při pokusu o odkaz na neexistující prostředek, například svazek nebo snímek. Prostředek byl pravděpodobně odstraněn nebo má název zdroje misspelt.

* Příčina:   
Pokoušíte se odkazovat na neexistující prostředek (například svazek nebo snímek), který již byl odstraněn nebo má chybně napsaný název prostředku.
* Řešení:   
Zkontrolujte, zda je požadavek na pravopisné chyby správně odkazován.
* Alternativní řešení:   
Viz výše uvedená část Řešení.

***Úroveň služby {volumeServiceLevel}je vyšší než nadřazená úroveň {poolServiceLevel}.***

K této chybě dochází při vytváření nebo aktualizaci svazku a nastavili jste úroveň služby na vyšší úroveň než fond kapacity, který jej obsahuje.

* Příčina:   
Pokoušíte se vytvořit nebo aktualizovat svazek s vyšší úrovní služeb než nadřazený fond kapacit.
* Řešení:   
Nastavte úroveň služby na stejné nebo nižší pořadí než nadřazený fond kapacity.
* Alternativní řešení:   
Vytvořte svazek v jiném fondu kapacity se správnou úrovní služeb. Případně odstraňte všechny svazky z fondu kapacity a nastavte úroveň služby pro fond kapacity na vyšší pořadí.

***Název serveru SMB nesmí být delší než 10 znaků.***

K této chybě dochází při vytváření nebo aktualizaci konfigurace služby Active Directory pro účet.

* Příčina:   
Délka názvu serveru SMB přesahuje 10 znaků.
* Řešení:   
Použijte kratší název serveru. Maximální délka je 10 znaků.
* Alternativní řešení:   
Žádné.  Viz výše uvedené řešení. 

***Podsouborní křežou nelze změnit.***

K této chybě dochází při `subnetId` pokusu o změnu po vytvoření svazku.  `SubnetId`musí být nastavena při vytvoření svazku a nelze jej později změnit.

* Příčina:   
Pokoušíte se změnit `subnetId` po vytvoření svazku, což není podporovaná operace. 
* Řešení:   
Pokud změna `subnetId` není potřeba, zvažte odebrání parametru z požadavku na zavření chybové zprávy.
* Alternativní řešení:   
Pokud potřebujete změnit `subnetId`, můžete vytvořit nový svazek `subnetId`s novým a potom migrovat data na nový svazek.

***Podznak Id je v neplatném formátu.***

K této chybě dochází při pokusu o `subnetId` vytvoření `resourceId` nového svazku, ale není pro podsíť.

* Příčina:   
K této chybě dochází při pokusu o `subnetId` vytvoření nového svazku, ale není `resourceId` pro podsíť. 
* Řešení:   
Zkontrolujte hodnotu `subnetId` pro ujistěte `resourceId` se, že obsahuje pro podsíť použité.
* Alternativní řešení:   
Žádné. Viz výše uvedené řešení. 

***Podsíť musí mít delegování Microsoft.NetApp/volumes.***

K této chybě dochází při vytváření svazku a vybraná `Microsoft.NetApp/volumes`podsíť není delegována na program .

* Příčina:   
Pokusili jste se vytvořit svazek a vybrali jste `Microsoft.NetApp/volumes`podsíť, která není delegována na program .
* Řešení:   
Vyberte jinou podsíť, `Microsoft.NetApp/volumes`která je delegována na program .
* Alternativní řešení:   
Přidejte správné delegování do podsítě.

***Zadaný typ prostředku není znám/není použitelný.***

K této chybě dochází, pokud byla požadována kontrola názvu u nepříslušného typu prostředku nebo u neznámého typu prostředku.

* Příčina:   
Kontrola názvů byla požadována pro neznámý nebo nepodporovaný typ prostředku.
* Řešení:   
Zkontrolujte, zda je prostředek, pro který požadavek provádíte, podporován nebo zda neobsahuje žádné pravopisné chyby.
* Alternativní řešení:   
Viz výše uvedené řešení.

***Neznámá chyba souborů Azure NetApp.***

Rozhraní Azure API spoléhá na rozhraní API Azure NetApp Files API pro správu svazků. Chyba označuje problém v komunikaci s rozhraním API.

* Příčina:   
Základní rozhraní API odesílá neznámou chybu. Tato chyba je pravděpodobně dočasné.
* Řešení:   
Problém je pravděpodobně dočasný a požadavek by měl být po určité době úspěšný. Pokud problém přetrvává, vytvořte lístek podpory, aby byl problém prošetřen.
* Alternativní řešení:   
Žádné. Základní rozhraní API je nezbytné pro správu svazků.

***Hodnota přijatá pro neznámou vlastnost {propertyName}.***

K této chybě dochází, pokud jsou k dispozici neexistující vlastnosti pro prostředek, jako je například svazek, snímek nebo cíl připojení.

* Příčina:   
Požadavek má sadu vlastností, které lze použít s každým prostředkem. Do požadavku nelze zahrnout žádné neexistující vlastnosti.
* Řešení:   
Ujistěte se, že všechny názvy vlastností jsou napsány správně a že vlastnosti jsou k dispozici pro předplatné a prostředek.
* Alternativní řešení:   
Snižte počet vlastností definovaných v požadavku na odstranění vlastnosti, která je příčinou chyby.

***Operace aktualizace není pro tento typ prostředku podporována.***

Aktualizovat lze pouze svazky. K této chybě dochází při pokusu o provedení nepodporované aktualizace operace, například aktualizace snímku.

* Příčina:   
Prostředek, který se pokoušíte aktualizovat, nepodporuje operaci aktualizace. Pouze svazky mohou mít své vlastnosti změněny.
* Řešení:   
Žádné. Prostředek, který se pokoušíte aktualizovat nepodporuje operaci aktualizace. Proto jej nelze změnit.
* Alternativní řešení:   
Pro svazek vytvořte nový prostředek s aktualizací na místě a migrujte data.

***Svazek nelze vytvořit ve fondu, který není ve stavu úspěšný.***

K této chybě dochází při pokusu o vytvoření svazku ve fondu, který není ve stavu úspěšné. S největší pravděpodobností operace vytvoření pro fond kapacity se nezdařilo z nějakého důvodu.

* Příčina:   
Fond kapacity obsahující nový svazek je ve stavu selhání.
* Řešení:   
Zkontrolujte, zda je fond kapacity úspěšně vytvořen a zda není ve stavu selhání.
* Alternativní řešení:   
Vytvořte nový fond kapacit a vytvořte svazek v novém fondu.

***Svazek je vytvářen a v tuto chvíli jej nelze odstranit.***

K této chybě dochází při pokusu o odstranění svazku, který je stále vytvářen.

* Příčina:   
Svazek se stále vytváří při pokusu o odstranění svazku.
* Řešení:   
Počkejte, až bude dokončeno vytváření svazku, a potom opakujte odstranění.
* Alternativní řešení:   
Viz výše uvedené řešení.

***Svazek je odstraňován a v tuto chvíli jej nelze odstranit.***

K této chybě dochází při pokusu o odstranění svazku, když je již odstraněn.

* Příčina:   
Svazek je již odstraněn při pokusu o odstranění svazku.
* Řešení:   
Počkejte, dokud nebude dokončena aktuální operace odstranění.
* Alternativní řešení:   
Viz výše uvedené řešení.

***Svazek je aktualizován a v tuto chvíli ji nelze odstranit.***

K této chybě dochází při pokusu o odstranění svazku, který je aktualizován.

* Příčina:   
Svazek je aktualizován při pokusu o odstranění svazku.
* Řešení:   
Počkejte, až bude dokončena operace aktualizace, a potom opakujte odstranění.
* Alternativní řešení:   
Viz výše uvedené řešení.

***Svazek nebyl nalezen nebo nebyl úspěšně vytvořen.***

K této chybě dochází, pokud se vytvoření svazku nezdařilo a pokoušíte se změnit svazek nebo vytvořit snímek svazku.

* Příčina:   
Svazek neexistuje nebo se nezdařilo.
* Řešení:   
Zkontrolujte, zda měníte správný svazek a zda bylo vytvoření svazku úspěšné. Nebo zkontrolujte, zda existuje svazek, pro který vytváříte snímek.
* Alternativní řešení:   
Žádné.  Viz výše uvedené řešení. 

***Zadaný token vytvoření již existuje.***

K této chybě dochází při pokusu o vytvoření svazku a zadáte token vytvoření (cestu exportu), pro který svazek již existuje.

* Příčina:   
Vytvoření tokenu (cesty exportu), který jste zadali při vytváření svazku, je již přidružen k jinému svazku. 
* Řešení:   
Zvolte jiný token vytvoření.  Případně odstraňte druhý svazek.

***Zadaný token vytvoření je rezervován.***

K této chybě dochází při pokusu o vytvoření svazku a zadáte "výchozí" nebo "žádný" jako cestu k souboru (vytvoření tokenu).

* Příčina:    
Pokoušíte se vytvořit svazek a jako cestu k souboru (token vytvoření) zadáte "výchozí" nebo "žádný".
* Řešení:   
Zvolte jinou cestu k souboru (vytvoření tokenu).
 
***Pověření služby Active Directory jsou používána***

K této chybě dochází při pokusu o odstranění konfigurace služby Active Directory z účtu, ve kterém stále existuje alespoň jeden svazek SMB.  Svazek SMB byl vytvořen pomocí konfigurace služby Active Directory, kterou se pokoušíte odstranit.

* Příčina:   
Pokoušíte se odstranit konfiguraci služby Active Directory z účtu, ale stále existuje alespoň jeden svazek SMB, který byl původně vytvořen pomocí konfigurace služby Active Directory. 
* Řešení:   
Nejprve odstraňte všechny svazky SMB, které byly vytvořeny pomocí konfigurace služby Active Directory.  Potom opakujte odstranění konfigurace.

***Přiřazení organizační jednotky nelze změnit, pokud jsou pověření používána.***

K této chybě dochází při pokusu o změnu organizační jednotky konfigurace služby Active Directory, ale stále existuje alespoň jeden svazek SMB.  Svazek SMB byl vytvořen pomocí této konfigurace služby Active Directory, kterou se pokoušíte odstranit.

* Příčina:   
Pokoušíte se změnit organizační jednotku konfigurace služby Active Directory.  Stále však existuje alespoň jeden svazek SMB, který byl původně vytvořen pomocí konfigurace služby Active Directory.
* Řešení:   
 Nejprve odstraňte všechny svazky SMB, které byly vytvořeny pomocí konfigurace služby Active Directory.  Potom opakujte odstranění konfigurace. 

***Aktualizace služby Active Directory již probíhá***

K této chybě dochází při pokusu o úpravu konfigurace služby Active Directory, pro kterou již probíhá operace úprav.

* Příčina:   
Pokoušíte se upravit konfiguraci služby Active Directory, ale již probíhá jiná operace úprav.
* Řešení:   
Počkejte, dokud nebude dokončena aktuálně spuštěná operace úprav.

***Nejprve odstranit všechny svazky pomocí vybraných pověření***

K této chybě dochází při pokusu o odstranění konfigurace služby Active Directory, ale stále existuje alespoň jeden svazek SMB.  Svazek SMB byl vytvořen pomocí konfigurace služby Active Directory, kterou se pokoušíte odstranit.

* Příčina:   
Pokoušíte se odstranit konfiguraci služby Active Directory, ale stále existuje alespoň jeden svazek SMB, který byl původně vytvořen pomocí konfigurace služby Active Directory.
* Řešení:   
Nejprve odstraňte všechny svazky SMB, které byly vytvořeny pomocí konfigurace služby Active Directory.  Potom opakujte odstranění konfigurace. 

***V oblasti nebyla nalezena žádná pověření služby Active Directory.***

K této chybě dochází při pokusu o vytvoření svazku SMB, ale do účtu pro oblast nebyla přidána žádná konfigurace služby Active Directory.

* Příčina:   
Pokoušíte se vytvořit svazek SMB, ale k účtu nebyla přidána žádná konfigurace služby Active Directory. 
* Řešení:   
Před vytvořením svazku SMB přidejte ke účtu konfiguraci služby Active Directory.

***Nelze dotazovat server DNS. Ověřte, zda je konfigurace sítě správná a zda jsou k dispozici servery DNS.***

K této chybě dochází při pokusu o vytvoření svazku SMB, ale server DNS (určený v konfiguraci služby Active Directory) je nedostupný. 

* Příčina:   
Pokoušíte se vytvořit svazek SMB, ale server DNS (určený v konfiguraci služby Active Directory) je nedostupný.
* Řešení:   
Zkontrolujte konfiguraci služby Active Directory a zkontrolujte, zda jsou adresy IP serveru DNS správné a dosažitelné.
Pokud nejsou žádné problémy s IP adresami serveru DNS, ověřte, zda přístup neblokují žádné brány firewall.

***Příliš mnoho souběžných úloh***

K této chybě dochází při pokusu o vytvoření snímku, když tři další operace vytváření snímek jsou již v průběhu pro odběr.

* Příčina:   
Pokoušíte se vytvořit snímek, když tři další operace vytváření snímek jsou již v průběhu pro odběr. 
* Řešení:   
Vytváření snímků úlohy trvat několik sekund maximálně dokončit.  Počkejte několik sekund a opakujte operaci vytvoření snímku.

***Nelze vytvořit další úlohy. Počkejte na dokončení probíhajících úloh a akci opakujte.***

K této chybě může dojít při pokusu o vytvoření nebo odstranění svazku za určitých okolností.

* Příčina:   
Pokoušíte se vytvořit nebo odstranit svazek za určitých okolností.
* Řešení:   
Počkejte minutu nebo tak a opakujte operaci.

***Svazek již přechází mezi stavy.***

K této chybě může dojít při pokusu o odstranění svazku, který je aktuálně v přechodovém stavu (to znamená, že aktuálně ve stavu vytváření, aktualizace nebo odstraňování).

* Příčina:   
Pokoušíte se odstranit svazek, který je aktuálně v přechodovém stavu.
* Řešení:   
Počkejte, dokud nebude dokončena aktuálně spuštěná operace (přechod stavu), a potom operaci opakujte.

***Rozdělení nového svazku ze snímku zdrojového svazku se nezdařilo.***

 K této chybě může dojít při pokusu o vytvoření svazku ze snímku.  

* Příčina:   
Pokusíte se vytvořit svazek ze snímku a svazek končí v chybovém stavu.
* Řešení:   
Odstraňte svazek a opakujte operaci vytvoření svazku ze snímku.

 
## <a name="next-steps"></a>Další kroky

* [Vývoj pro soubory Azure NetApp s rozhraním REST API](azure-netapp-files-develop-with-rest-api.md)
