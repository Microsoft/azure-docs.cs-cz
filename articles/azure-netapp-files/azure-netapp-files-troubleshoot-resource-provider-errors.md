---
title: Řešení potíží s Azure NetApp Filesmi chybami poskytovatele prostředků | Microsoft Docs
description: Popisuje příčiny, řešení a alternativní řešení pro běžné chyby Azure NetApp Files zprostředkovatelů prostředků.
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
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: b-juche
ms.openlocfilehash: ac83e5a9366a12c5adce0e08f04f2bb28a7d788d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374877"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Řešení potíží s chybami poskytovatele prostředků Azure NetApp Files 

Tento článek popisuje běžné Azure NetApp Files chyby poskytovatele prostředků, jejich příčiny, řešení a alternativní řešení (pokud jsou k dispozici).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Běžné chyby poskytovatele prostředků Azure NetApp Files

***Vytvoření `netAppAccounts` bylo v této oblasti omezeno.***

K této situaci dochází, když je předplatné waitlisted pro Azure NetApp Files a uživatel se pokusí vytvořit účet NetApp.

* Příčina:   
Poskytovatel prostředků Azure pro Azure NetApp Files není úspěšně zaregistrovaný. 
 
* Řešení:   
Po waitlisted předplatného dokončete všechny kroky popsané v tématu [registrace poskytovatele prostředků Azure NetApp](azure-netapp-files-register.md#resource-provider) .

***BareMetalTenantId nelze změnit.***  

K této chybě dochází, když se pokusíte aktualizovat nebo opravit svazek a `BaremetalTenantId` vlastnost má změněnou hodnotu.

* Příčina:   
Pokoušíte se aktualizovat svazek a `BaremetalTenantId` vlastnost má jinou hodnotu než hodnota uložená v Azure.
* Řešení:   
Nezahrnovat `BaremetalTenantId` do žádosti o opravu a aktualizaci (Put). Případně se ujistěte, že `BaremetalTenantId` je to v žádosti stejné.

***ServiceLevel nelze změnit.***  

K této chybě dochází, když se pokusíte aktualizovat nebo opravit fond kapacit s jinou úrovní služeb, když fond kapacit již obsahuje svazky.

* Příčina:   
Pokoušíte se aktualizovat úroveň služby fondu kapacity, když fond obsahuje svazky.
* Řešení:   
Odstraňte všechny svazky z fondu kapacit a pak změňte úroveň služby.
* Alternativní řešení:   
Vytvořte další fond kapacit a pak znovu vytvořte svazky v novém fondu kapacity.

***PoolId se nedá změnit.***  

K této chybě dochází, když se pokusíte aktualizovat nebo opravit fond kapacit se změněnou `PoolId` vlastností.

* Příčina:   
Pokoušíte se aktualizovat vlastnost fondu kapacity `PoolId` . `PoolId`Vlastnost je vlastnost jen pro čtení a nelze ji změnit.
* Řešení:   
Nezahrnovat `PoolId` do žádosti o opravu a aktualizaci (Put).  Případně se ujistěte, že `PoolId` je to v žádosti stejné.

***CreationToken nelze změnit.***

K této chybě dojde, když se pokusíte změnit cestu k souboru ( `CreationToken` ) Po vytvoření svazku. Cesta k souboru ( `CreationToken` ) musí být nastavena při vytvoření svazku a nelze ji později změnit.

* Příčina:   
Pokoušíte se změnit cestu k souboru ( `CreationToken` ) Po vytvoření svazku, což není podporovaná operace. 
* Řešení:   
Pokud není potřeba měnit cestu k souboru, zvažte odebrání parametru z požadavku na zavření chybové zprávy.
* Alternativní řešení:   
Pokud potřebujete změnit cestu k souboru ( `CreationToken` ), můžete vytvořit nový svazek s novou cestou k souboru a potom migrovat data na nový svazek.

***CreationToken musí mít délku alespoň 16 znaků.***

K této chybě dochází, pokud cesta k souboru ( `CreationToken` ) nesplňuje požadavek na délku. Délka cesty k souboru musí být alespoň jeden znak.

* Příčina:   
Cesta k souboru je prázdná.  Když vytvoříte svazek pomocí rozhraní API, je vyžadován token pro vytváření. Pokud používáte Azure Portal, cesta k souboru se vygeneruje automaticky.
* Řešení:   
Zadejte alespoň jeden znak jako cestu k souboru ( `CreationToken` ).

***Název domény se nedá změnit.***

K této chybě dojde, když se pokusíte změnit název domény ve službě Active Directory.

* Příčina:   
Pokoušíte se aktualizovat vlastnost název domény.
* Řešení:    
Žádné Název domény se nedá změnit.
* Alternativní řešení:   
Pomocí konfigurace služby Active Directory odstraňte všechny svazky. Pak odstraňte konfiguraci služby Active Directory a znovu vytvořte svazky.

***Chyba duplicitní hodnoty pro Object ExportPolicy. rules [RuleIndex].***

K této chybě dochází, pokud není definována zásada exportu s jedinečným indexem. Při definování zásad exportu musí mít všechna pravidla zásad exportu jedinečný index mezi 1 a 5.

* Příčina:   
Definované zásady exportu nesplňují požadavek pro export pravidel zásad. Musíte mít jedno pravidlo pro export zásad, které platí pro pravidla zásad exportu minimálně a pět.
* Řešení:   
Ujistěte se, že se index ještě nepoužívá a že je v rozsahu od 1 do 5.
* Alternativní řešení:   
Pro pravidlo, které se pokoušíte nastavit, použijte jiný index.

***Chyba {Action} {ResourceType}***

Tato chyba se zobrazí, pokud jiné zpracování chyb nedokázalo zpracovat chybu při provádění akce u prostředku.   Obsahuje text Error (chyba). `{action}`Může to být libovolný z ( `getting` , `creating` , `updating` , nebo `deleting` ).  `{resourceTypeName}`Je (například,,, `resourceTypeName` `netAppAccount` `capacityPool` `volume` a tak dále).

* Příčina:   
Tato chyba je Neošetřená výjimka, kde není známa příčina.
* Řešení:   
Kontaktujte podporu Azure Support Center a ohlaste podrobný důvod v protokolech.
* Alternativní řešení:   
Žádné

***Název cesty k souboru může obsahovat jenom písmena, číslice a spojovníky ("" – ").***

K této chybě dochází, pokud cesta k souboru obsahuje nepodporované znaky, například tečka ("."), čárku (","), podtržítko ("_") nebo znak dolaru ("$").

* Příčina:   
Cesta k souboru obsahuje nepodporované znaky, například tečka ("."), čárku (","), podtržítko ("_"), nebo znak dolaru ("$").
* Řešení:   
Z zadané cesty k souboru odeberte znaky, které nejsou abecední písmena, číslice ani spojovníky ("-").
* Alternativní řešení:   
Podtržítko můžete nahradit pomlčkou nebo použít místo mezer k označení začátku nových slov.  Použijte například "NewVolume" místo "New Volume".

***FileSystemId nelze změnit.***

K této chybě dojde, když se pokusíte změnit `FileSystemId` .  Změna `FileSystemdId` není podporovaná operace. 

* Příčina:   
ID systému souborů je nastaveno při vytvoření svazku. `FileSystemId` nelze následně změnit.
* Řešení:   
Nezahrnovat `FileSystemId` do žádosti o opravu a aktualizaci (Put).  Případně se ujistěte, že `FileSystemId` je to v žádosti stejné.

***Active Directory s ID: {String} neexistuje.***

`{string}`Část je hodnota, kterou jste zadali ve `ActiveDirectoryId` vlastnosti pro připojení služby Active Directory.

* Příčina:   
Když jste vytvořili účet s konfigurací služby Active Directory, zadali jste hodnotu, která by měla `ActiveDirectoryId` být prázdná.
* Řešení:   
Nezahrnovat `ActiveDirectoryId` do žádosti o vytvoření (vložení).

***Neplatná verze API-Version.***

Verze rozhraní API se buď neodeslala, nebo obsahuje neplatnou hodnotu.

* Příčina:   
Hodnota v parametru dotazu `api-version` obsahuje neplatnou hodnotu.
* Řešení:   
Použijte správnou hodnotu verze rozhraní API.  Poskytovatel prostředků podporuje mnoho verzí rozhraní API. Hodnota je ve formátu rrrr-mm-dd.

***Byla přijata neplatná hodnota {Value} pro {1} .***

Tato zpráva označuje chybu v polích pro `RuleIndex` ,,, `AllowedClients` `UnixReadOnly` , a `UnixReadWrite` `Nfsv3` `Nfsv4` .

* Příčina:   
Vstupní žádost o ověření se nezdařila minimálně u jednoho z následujících polí: `RuleIndex` , `AllowedClients` , `UnixReadOnly` , `UnixReadWrite` , `Nfsv` 3 a `Nfsv4` .
* Řešení:   
Ujistěte se, že jste na příkazovém řádku nastavili všechny parametry povinného a nekonfliktu. Například nelze současně nastavit `UnixReadOnly` `UnixReadWrite` parametry i.
* Alternativní řešení:   
Podívejte se na výše uvedené řešení.

***Rozsah IP {0} adres {1} pro síť VLAN {2} se už používá.***

K této chybě dochází, protože interní záznamy použitých rozsahů IP adres jsou v konfliktu s nově přiřazenou IP adresou.

* Příčina:   
IP adresa přiřazená k vytvoření svazku je už zaregistrovaná.
Důvodem může být dřívější vytvoření svazku se selháním.
* Řešení:   
Kontaktujte centrum podpory Azure.

***Chybí hodnota pro {Property}.***

Tato chyba označuje, že v žádosti chybí požadovaná vlastnost. Řetězec {Property} obsahuje název chybějící vlastnosti.

* Příčina:   
Vstupní žádost o ověření se nezdařila nejméně u jedné z vlastností.
* Řešení:   
Ujistěte se, že jste v žádosti nastavili všechny vlastnosti povinné a nekonfliktu, což je speciálně vlastnost z chybové zprávy.

***MountTargets nelze změnit.***

K této chybě dojde, když se uživatel pokusí aktualizovat nebo opravit vlastnost Volume MountTargets.

* Příčina:   
Pokoušíte se aktualizovat `MountTargets` vlastnost Volume. Změna této vlastnosti není podporována.
* Řešení:   
Nezahrnovat `MountTargets` do žádosti o opravu a aktualizaci (Put).  Případně se ujistěte, že `MountTargets` je to v žádosti stejné.

***Název se už používá.***

Tato chyba označuje, že název prostředku je již používán.

* Příčina:   
Pokoušíte se vytvořit prostředek s názvem, který se používá pro existující prostředek.
* Řešení:   
Při vytváření prostředku použijte jedinečný název.

***Cesta k souboru se už používá.***

Tato chyba označuje, že se už používá cesta k souboru pro svazek.

* Příčina:   
Pokoušíte se vytvořit svazek s cestou k souboru, která je shodná s existujícím svazkem.
* Řešení:   
Při vytváření svazku použijte jedinečnou cestu k souboru.

***Název je příliš dlouhý.***

Tato chyba označuje, že název prostředku nesplňuje požadavek na maximální délku.

* Příčina:   
Název prostředku je příliš dlouhý.
* Řešení:   
Pro prostředek použijte kratší název.

***Cesta k souboru je příliš dlouhá.***

Tato chyba označuje, že cesta k souboru pro svazek nesplňuje požadavek na maximální délku.

* Příčina:   
Cesta k souboru svazku je příliš dlouhá.
* Řešení:   
Použijte kratší cestu k souboru.

***Název je moc krátký.***

Tato chyba označuje, že název prostředku nesplňuje požadavek na minimální délku.

* Příčina:   
Název prostředku je moc krátký.
* Řešení:   
Pro prostředek použijte delší název.

***Cesta k souboru je příliš krátká.***

Tato chyba označuje, že cesta k souboru svazku nesplňuje požadavek na minimální délku.

* Příčina:   
Cesta k souboru svazku je příliš krátká.
* Řešení:   
Zvětšete délku cesty k souboru svazku.

***Rozhraní API Azure NetApp Files nedostupné.***

Rozhraní API Azure využívá ke správě svazků rozhraní Azure NetApp Files API. Tato chyba označuje problém s připojením rozhraní API.

* Příčina:   
Základní rozhraní API neodpovídá, takže došlo k vnitřní chybě. Tato chyba je pravděpodobně dočasná.
* Řešení:   
Problém je pravděpodobně dočasný. Požadavek by měl být po nějaké době úspěšný.
* Alternativní řešení:   
Žádné Základní rozhraní API je nezbytné pro správu svazků.

***Pro ' ' se nenašlo žádné ID výsledku operace {0} .***

Tato chyba označuje, že vnitřní chyba brání v dokončení operace.

* Příčina:   
Došlo k vnitřní chybě a zabránilo se dokončení operace.
* Řešení:   
Tato chyba je pravděpodobně dočasná. Počkejte několik minut a zkuste akci zopakovat. Pokud potíže potrvají, vytvořte lístek, který bude mít technickou podporu k tomuto problému.
* Alternativní řešení:   
Počkejte pár minut a ověřte, jestli problém přetrvává.

***Nepovoluje se kombinovat typy protokolů CIFS a NFS.***

K této chybě dochází, když se pokoušíte vytvořit svazek a ve vlastnostech svazku jsou typy protokolu CIFS (SMB) a NFS.

* Příčina:   
Ve vlastnostech svazku se používají typy protokolu CIFS (SMB) i NFS.
* Řešení:   
Odeberte jeden z typů protokolů.
* Alternativní řešení:   
Vlastnost typu protokolu nechejte prázdná nebo má hodnotu null.

***Počet položek: {value} pro objekt: ExportPolicy. rules [RuleIndex] je mimo rozsah min-max.***

K této chybě dochází, pokud pravidla zásad exportu nesplňují požadavek na minimální nebo maximální rozsah. Pokud definujete zásadu exportu, musí mít jedno pravidlo pro export zásad na maximum a pět pravidel pro export zásad.

* Příčina:   
Vámi definovaná zásada exportu nesplňuje požadovaný rozsah.
* Řešení:   
Ujistěte se, že index ještě není použitý a je v rozsahu od 1 do 5.
* Alternativní řešení:   
Pro svazky není nutné používat zásady exportu. Zásadu exportu můžete vynechat úplně, pokud nepotřebujete exportovat pravidla zásad.

***Je povolená jenom jedna služba Active Directory.***

K této chybě dochází při pokusu o vytvoření konfigurace služby Active Directory a pro odběr v oblasti již existuje. K této chybě může dojít také při pokusu o vytvoření více než jedné konfigurace služby Active Directory.

* Příčina:   
Pokoušíte se vytvořit (ne aktualizovat) službu Active Directory, ale jedna z nich již existuje.
* Řešení:   
Pokud se konfigurace služby Active Directory nepoužívá, můžete nejdřív odstranit existující konfiguraci a potom operaci vytvoření zopakovat.
* Alternativní řešení:   
Žádné Je povolená jenom jedna služba Active Directory.

***Operace {Operation} není podporovaná.***

Tato chyba označuje, že operace není k dispozici pro aktivní předplatné nebo prostředek.

* Příčina:   
Tato operace není pro odběr nebo prostředek k dispozici.
* Řešení:   
Přesvědčte se, zda je operace zadána správně a zda je k dispozici pro prostředek a předplatné, které používáte.

***OwnerId se nedá změnit.***

K této chybě dojde, když se pokusíte změnit vlastnost OwnerId svazku. Změna OwnerId není podporovaná operace. 

* Příčina:   
`OwnerId`Vlastnost je nastavena při vytvoření svazku. Vlastnost nelze následně změnit.
* Řešení:   
Nezahrnovat `OwnerId` do žádosti o opravu a aktualizaci (Put). Případně se ujistěte, že `OwnerId` je to v žádosti stejné.

***Nenašel se nadřazený fond.***

K této chybě dochází, pokud se pokusíte vytvořit svazek a fond kapacit, ve kterém vytváříte svazek, nebyl nalezen.

* Příčina:   
Fond kapacit, ve kterém se svazek vytváří, se nenašel.
* Řešení:   
Pravděpodobně fond nebyl zcela vytvořen nebo byl v době vytváření svazku již odstraněn.

***Operace opravy není pro tento typ prostředku podporována.***

K této chybě dojde při pokusu o změnu cíle připojení nebo snímku.

* Příčina:   
Cíl připojení je definován při jeho vytvoření a následně jej nelze změnit.
Snímky neobsahují žádné vlastnosti, které by bylo možné změnit.
* Řešení:   
Žádné U těchto prostředků nejsou k dispozici žádné vlastnosti, které by bylo možné změnit.

***Velikost fondu je pro celkovou velikost svazku příliš malá.***

K této chybě dochází, když aktualizujete velikost fondu kapacity a velikost je menší než celková `usedBytes` hodnota všech svazků v tomto fondu kapacit.  K této chybě může dojít také při vytváření nového svazku nebo změně velikosti existujícího svazku a velikost nového svazku překračuje volné místo ve fondu kapacit.

* Příčina:   
Pokoušíte se aktualizovat fond kapacit na menší velikost než usedBytes ve všech svazcích fondu kapacity.  Nebo se pokoušíte vytvořit svazek, který je větší než volné místo ve fondu kapacit.  Případně se pokoušíte změnit velikost svazku a nová velikost přesahuje volné místo ve fondu kapacit.
* Řešení:   
Nastavte velikost fondu kapacity na větší hodnotu nebo pro svazek vytvořte menší svazek.
* Alternativní řešení:   
Odstraňte dostatek svazků, aby se velikost fondu kapacity mohla aktualizovat na tuto velikost.

***Vlastnost: umístění snímku musí být stejné jako svazek.***

K této chybě dochází při vytváření snímku s jiným umístěním, než je svazek, který je vlastníkem snímku.

* Příčina:   
Hodnota vlastnosti Location pro snímek je neplatná.
* Řešení:   
Ve vlastnosti umístění nastavte platný řetězec.

***Název {ResourceType} musí být stejný jako název identifikátoru prostředku.***

K této chybě dojde při vytváření prostředku a zadáním vlastnosti Name s jinou hodnotou než vlastnost name `resourceId` .

* Příčina:   
Při vytváření prostředku je neplatná hodnota vlastnosti Name.
* Řešení:   
Ponechte vlastnost Name prázdnou nebo ji umožněte, aby používala stejnou hodnotu jako vlastnost Name (mezi posledním zpětným lomítkem "/" a otazníkem "?") v `resourceId` .

***Typ protokolu {value} není známý.***

K této chybě dochází, když vytváříte svazek s neznámým typem protokolu.  Platné hodnoty jsou "NFSv3", "názvů NFSv4" a "CIFS".

* Příčina:   
Pokoušíte se nastavit ve vlastnosti Volume neplatnou hodnotu `protocolType` .
* Řešení:   
Nastavte platný řetězec v `protocolType` .
* Alternativní řešení:   
Nastavit `protocolType` jako null.

***Typy protokolů se nedají změnit.***

K této chybě dojde, když se pokusíte aktualizovat nebo opravit `ProtocolType` svazek.  Změna Typprotokolu není podporovaná operace.

* Příčina:   
`ProtocolType`Vlastnost je nastavena při vytvoření svazku.  Nedá se aktualizovat.
* Řešení:   
Žádné
* Alternativní řešení:   
Vytvořte další svazek s novými typy protokolů.

***Vytvoření prostředku typu {ResourceType} by překročilo kvótu prostředků {quot} typu {ResourceType} na {parentResourceType}. Aktuální počet prostředků je {currentCount}. před vytvořením nového prostředku prosím odstraňte některé prostředky tohoto typu.***

K této chybě dochází při pokusu o vytvoření prostředku ( `NetAppAccount` , `CapacityPool` , `Volume` nebo `Snapshot` ), ale kvóta dosáhla svého limitu.

* Příčina:   
Pokoušíte se vytvořit prostředek, ale dosáhlo se limitu kvóty (například: `NetAppAccounts` podle předplatného nebo `CapacityPools` podle `NetAppAccount` ).
* Řešení:   
Zvyšte limit kvóty.
* Alternativní řešení:   
Odstraňte nepoužívané prostředky stejného typu a znovu je vytvořte.

***Byla přijata hodnota pro vlastnost {propertyName}, která je jen pro čtení.***

K této chybě dochází, pokud definujete hodnotu vlastnosti, kterou nelze změnit. Nemůžete například změnit ID svazku.

* Příčina:   
Pokoušíte se upravit parametr (například ID svazku), který nelze změnit.
* Řešení:   
Neupravujte hodnotu vlastnosti.

***Požadovaný prostředek {Resource} se nenašel.***

K této chybě dochází při pokusu o odkazování na neexistující prostředek, například na svazku nebo snímku. Prostředek se možná odstranil nebo má název prostředku.

* Příčina:   
Pokoušíte se vytvořit odkaz na neexistující prostředek (například svazek nebo snímek), který již byl odstraněn nebo má chybný název prostředku.
* Řešení:   
Zkontrolujte, jestli se v žádosti o pravopisné chyby správně odkazuje.
* Alternativní řešení:   
Přečtěte si část řešení výše.

***Úroveň služby {volumeServiceLevel} je vyšší než nadřazená hodnota {poolServiceLevel}.***

K této chybě dochází při vytváření nebo aktualizaci svazku a úroveň služby je nastavena na vyšší úroveň než fond kapacit, který jej obsahuje.

* Příčina:   
Pokoušíte se vytvořit nebo aktualizovat svazek s vyšší úrovní úrovně služby, než je nadřazený fond kapacity.
* Řešení:   
Nastavte úroveň služby na stejný nebo nižší, než je nadřazený fond kapacit.
* Alternativní řešení:   
Vytvořte svazek v jiném fondu kapacity se správnou úrovní služeb. Případně odstraňte všechny svazky z fondu kapacit a nastavte úroveň služby pro fond kapacity na vyšší rozměr.

***Název serveru SMB nesmí být delší než 10 znaků.***

K této chybě dochází při vytváření nebo aktualizaci konfigurace služby Active Directory pro účet.

* Příčina:   
Délka názvu serveru SMB překračuje 10 znaků.
* Řešení:   
Použijte kratší název serveru. Maximální délka je 10 znaků.
* Alternativní řešení:   
Žádné  Podívejte se na výše uvedené řešení. 

***SubnetId nelze změnit.***

K této chybě dojde, když se pokusíte změnit `subnetId` po vytvoření svazku.  `SubnetId` musí být nastavené, když se svazek vytvoří a nedá se později změnit.

* Příčina:   
Pokoušíte se změnit `subnetId` po vytvoření svazku, což není podporovaná operace. 
* Řešení:   
Pokud se změna `subnetId` nevyžaduje, zvažte odebrání parametru z požadavku k zavření chybové zprávy.
* Alternativní řešení:   
Pokud potřebujete změnit `subnetId` , můžete vytvořit nový svazek s novým `subnetId` a potom migrovat data na nový svazek.

***SubnetId má neplatný formát.***

K této chybě dojde, když se pokusíte vytvořit nový svazek, ale `subnetId` není to `resourceId` pro podsíť.

* Příčina:   
K této chybě dojde, když se pokusíte vytvořit nový svazek, ale `subnetId` není to `resourceId` pro podsíť. 
* Řešení:   
Zkontrolujte hodnotu pro `subnetId` a ujistěte se, že obsahuje a `resourceId` pro použitou podsíť.
* Alternativní řešení:   
Žádné Podívejte se na výše uvedené řešení. 

***Podsíť musí mít delegování Microsoft. NetApp/svazky.***

K této chybě dochází, když vytváříte svazek a vybraná podsíť není delegovaná na `Microsoft.NetApp/volumes` .

* Příčina:   
Pokusili jste se vytvořit svazek a vybrali jste podsíť, která není delegována na `Microsoft.NetApp/volumes` .
* Řešení:   
Vyberte jinou podsíť, která je delegována na `Microsoft.NetApp/volumes` .
* Alternativní řešení:   
Přidejte do podsítě správné delegování.

***Zadaný typ prostředku je neznámý/nelze použít.***

K této chybě dojde, pokud se požaduje ověření názvu buď na nepoužitelném typu prostředku, nebo u neznámého typu prostředku.

* Příčina:   
Kontrole názvů bylo vyžádáno pro neznámý nebo nepodporovaný typ prostředku.
* Řešení:   
Zkontrolujte, zda prostředek, pro který provádíte požadavek, je podporován nebo neobsahuje žádné pravopisné chyby.
* Alternativní řešení:   
Podívejte se na výše uvedené řešení.

***Neznámá chyba Azure NetApp Files.***

Rozhraní API Azure využívá ke správě svazků rozhraní Azure NetApp Files API. Chyba indikuje problém v komunikaci s rozhraním API.

* Příčina:   
Základní rozhraní API odesílá neznámou chybu. Tato chyba je pravděpodobně dočasná.
* Řešení:   
Problém se pravděpodobně dočasná a žádost by se měla za chvíli zdařit. Pokud potíže potrvají, vytvořte lístek podpory, abyste mohli problém prozkoumat.
* Alternativní řešení:   
Žádné Základní rozhraní API je nezbytné pro správu svazků.

***Byla přijata hodnota pro neznámou vlastnost {propertyName}.***

K této chybě dochází, pokud jsou k dispozici neexistující vlastnosti pro prostředek, jako je například svazek, snímek nebo cíl připojení.

* Příčina:   
Požadavek obsahuje sadu vlastností, které lze použít s každým prostředkem. V požadavku nemůžete zahrnout žádné neexistující vlastnosti.
* Řešení:   
Přesvědčte se, zda jsou všechny názvy vlastností zadány správně a zda jsou k dispozici vlastnosti pro předplatné a prostředek.
* Alternativní řešení:   
Snižte počet vlastností definovaných v žádosti, aby se vyloučila vlastnost, která způsobuje chybu.

***Operace aktualizace není pro tento typ prostředku podporována.***

Aktualizovat lze pouze svazky. K této chybě dochází při pokusu o provedení nepodporované operace aktualizace, například aktualizace snímku.

* Příčina:   
Prostředek, který se pokoušíte aktualizovat, nepodporuje operaci aktualizace. Změny vlastností mohou mít pouze svazky.
* Řešení:   
Žádné Prostředek, který se pokoušíte aktualizovat, nepodporuje operaci aktualizace. Proto jej nelze změnit.
* Alternativní řešení:   
V případě svazku vytvořte nový prostředek s aktualizací na místě a migrujte data.

***Svazek nelze vytvořit ve fondu, který není ve stavu úspěšné.***

K této chybě dochází při pokusu o vytvoření svazku ve fondu, který není ve stavu úspěšné. Z nějakého důvodu se z nějakého důvodu nezdařila operace vytvoření pro fond kapacit.

* Příčina:   
Fond kapacit obsahující nový svazek je ve stavu selhání.
* Řešení:   
Ověřte, že se fond kapacit úspěšně vytvořil a že není ve stavu selhání.
* Alternativní řešení:   
Vytvořte nový fond kapacity a vytvořte svazek v novém fondu.

***Svazek se vytváří a momentálně se nedá odstranit.***

K této chybě dochází, když se pokusíte odstranit svazek, který se pořád vytváří.

* Příčina:   
Při pokusu o odstranění svazku se stále vytváří svazek.
* Řešení:   
Počkejte, než se dokončí vytváření svazku, a pak zkuste odstranění zopakovat.
* Alternativní řešení:   
Podívejte se na výše uvedené řešení.

***Svazek se odstraňuje a momentálně se nedá odstranit.***

K této chybě dojde, když se pokusíte odstranit svazek, když už je odstraněný.

* Příčina:   
Svazek se už odstraňuje při pokusu o odstranění svazku.
* Řešení:   
Počkejte na dokončení aktuální operace odstranění.
* Alternativní řešení:   
Podívejte se na výše uvedené řešení.

***Svazek se aktualizuje a momentálně se nedá odstranit.***

K této chybě dochází při pokusu o odstranění aktualizovaného svazku.

* Příčina:   
Svazek se aktualizuje při pokusu o odstranění svazku.
* Řešení:   
Počkejte, než se dokončí operace aktualizace, a pak zkuste odstranění zopakovat.
* Alternativní řešení:   
Podívejte se na výše uvedené řešení.

***Svazek se nenašel nebo se nepovedlo úspěšně vytvořit.***

K této chybě dojde, pokud se nepovedlo vytvořit svazek a pokoušíte se změnit svazek nebo vytvořit snímek pro svazek.

* Příčina:   
Svazek neexistuje nebo se nepovedlo vytvořit.
* Řešení:   
Ověřte, že měníte správný svazek a že vytvoření svazku bylo úspěšné. Nebo ověřte, že existuje svazek, pro který vytváříte snímek.
* Alternativní řešení:   
Žádné  Podívejte se na výše uvedené řešení. 

***Zadaný token pro vytváření již existuje.***

K této chybě dochází při pokusu o vytvoření svazku a zadání tokenu pro vytvoření (cesta k exportu), pro který svazek již existuje.

* Příčina:   
Token vytváření (cesta pro export), který jste zadali během vytváření svazku, je už přidružený k jinému svazku. 
* Řešení:   
Vyberte jiný token pro vytváření.  Případně odstraňte i jiný svazek.

***Zadaný token pro vytvoření je rezervovaný.***

K této chybě dojde, když se pokusíte vytvořit svazek a jako cestu k souboru (vytvořit token) zadáte "default" nebo "none".

* Příčina:    
Pokoušíte se vytvořit svazek a jako cestu k souboru (vytvořit token) zadáte "default" nebo "none".
* Řešení:   
Vyberte jinou cestu k souboru (token pro vytvoření).
 
***Přihlašovací údaje služby Active Directory se používají.***

K této chybě dochází, pokud se pokusíte odstranit konfiguraci služby Active Directory z účtu, ve kterém ještě existuje aspoň jeden svazek SMB.  Svazek SMB byl vytvořen pomocí konfigurace služby Active Directory, kterou se pokoušíte odstranit.

* Příčina:   
Pokoušíte se odstranit konfiguraci služby Active Directory z účtu, ale alespoň jeden svazek SMB stále existuje, který byl původně vytvořen pomocí konfigurace služby Active Directory. 
* Řešení:   
Nejprve odstraňte všechny svazky SMB, které byly vytvořeny pomocí konfigurace služby Active Directory.  Pak zkuste odstranit konfiguraci znovu.

***Přiřazení organizační jednotky nejde změnit, pokud se přihlašovací údaje používají.***

K této chybě dochází, když se pokusíte změnit organizační jednotku konfigurace služby Active Directory, ale alespoň jeden svazek SMB stále existuje.  Svazek SMB byl vytvořen pomocí této konfigurace služby Active Directory, kterou se pokoušíte odstranit.

* Příčina:   
Pokoušíte se změnit organizační jednotku konfigurace služby Active Directory.  Ale alespoň jeden svazek SMB stále existuje, který byl původně vytvořen pomocí konfigurace služby Active Directory.
* Řešení:   
 Nejprve odstraňte všechny svazky SMB, které byly vytvořeny pomocí konfigurace služby Active Directory.  Pak zkuste odstranit konfiguraci znovu. 

***Aktualizace služby Active Directory už probíhá.***

K této chybě dochází při pokusu o úpravu konfigurace služby Active Directory, pro kterou již operace Edit probíhá.

* Příčina:   
Pokoušíte se upravit konfiguraci služby Active Directory, ale už probíhá jiná operace úprav.
* Řešení:   
Počkejte, dokud nebude dokončena právě spuštěná operace úprav.

***Nejdřív odstraňte všechny svazky s vybranými přihlašovacími údaji.***

K této chybě dochází při pokusu o odstranění konfigurace služby Active Directory, ale alespoň jeden svazek SMB stále existuje.  Svazek SMB byl vytvořen pomocí konfigurace služby Active Directory, kterou se pokoušíte odstranit.

* Příčina:   
Pokoušíte se odstranit konfiguraci služby Active Directory, ale alespoň jeden svazek SMB stále existuje, který byl původně vytvořen pomocí konfigurace služby Active Directory.
* Řešení:   
Nejprve odstraňte všechny svazky SMB, které byly vytvořeny pomocí konfigurace služby Active Directory.  Pak zkuste odstranit konfiguraci znovu. 

***V oblasti se nenašla žádná pověření služby Active Directory.***

K této chybě dochází, když se pokusíte vytvořit svazek SMB, ale do účtu pro tuto oblast se nepřidala žádná konfigurace služby Active Directory.

* Příčina:   
Pokoušíte se vytvořit svazek SMB, ale do účtu se nepřidala žádná konfigurace služby Active Directory. 
* Řešení:   
Před vytvořením svazku SMB přidejte k účtu konfiguraci služby Active Directory.

***Nepovedlo se odeslat dotaz na server DNS. Ověřte, zda je konfigurace sítě správná a zda jsou k dispozici servery DNS.***

K této chybě dochází při pokusu o vytvoření svazku SMB, ale server DNS (zadaný v konfiguraci služby Active Directory) je nedosažitelný. 

* Příčina:   
Pokoušíte se vytvořit svazek SMB, ale server DNS (zadaný v konfiguraci služby Active Directory) je nedosažitelný.
* Řešení:   
Zkontrolujte konfiguraci služby Active Directory a ujistěte se, že IP adresy serveru DNS jsou správné a dosažitelné.
Pokud nejsou k dispozici žádné problémy s IP adresami serveru DNS, ověřte, že přístup neblokuje žádné brány firewall.

***Příliš mnoho souběžných úloh***

K této chybě dojde, když se pokusíte vytvořit snímek, pokud pro odběr již probíhá tři operace vytváření snímků.

* Příčina:   
Pokoušíte se vytvořit snímek, když už pro předplatné probíhá tři další operace vytváření snímků. 
* Řešení:   
Úlohy vytváření snímků zabírají maximálně několik sekund.  Počkejte několik sekund a zkuste operaci vytvoření snímku zopakovat.

***Nelze vytvořit další úlohy. Počkejte prosím, než se probíhající úlohy dokončí, a zkuste to znovu.***

K této chybě může dojít, když se pokusíte vytvořit nebo odstranit svazek za určitých okolností.

* Příčina:   
Pokoušíte se vytvořit nebo odstranit svazek za určitých okolností.
* Řešení:   
Počkejte minutu nebo to zkuste znovu.

***Svazek už mezi stavy Probíhá převod.***

K této chybě může dojít, když se pokusíte odstranit svazek, který je aktuálně ve stavu přechodu (tj. aktuálně ve stavu vytváření, aktualizace nebo odstranění).

* Příčina:   
Pokoušíte se odstranit svazek, který je aktuálně ve stavu přechodu.
* Řešení:   
Počkejte, než se dokončí operace (přechod stavu), a potom zkuste operaci zopakovat.

***Nepovedlo se rozdělit nový svazek ze snímku zdrojového svazku.***

 K této chybě může dojít při pokusu o vytvoření svazku ze snímku.  

* Příčina:   
Pokusíte se vytvořit svazek ze snímku a svazek skončí v chybovém stavu.
* Řešení:   
Odstraňte svazek a pak znovu spusťte operaci vytvoření svazku ze snímku.

 
## <a name="next-steps"></a>Další kroky

* [Vývoj pro službu Azure NetApp Files s využitím rozhraní REST API](azure-netapp-files-develop-with-rest-api.md)
