---
title: Řešení potíží s chybami poskytovatele prostředků souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje příčiny, řešení a řešení pro běžné chyby poskytovatele prostředků NetApp soubory Azure.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769433"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Řešení potíží s chybami poskytovatele prostředků Azure NetApp Files
Tento článek popisuje běžné poskytovatele prostředků souborů NetApp Azure chyby, jejich příčiny, řešení a alternativní řešení. 

<a name="error_01"></a>***Azure Key Vault není nakonfigurováno.***   
Služba Azure Key Vault uchovává požadované přihlašovací údaje pro přístup k základní rozhraní API. Tato chyba označuje, že Azure Key Vault nepřijala kompletní přihlašovací údaje pro přístup k základní rozhraní API.

* Příčina  
Služba Azure Key Vault nepřijala správné přihlašovací údaje nebo přihlašovací údaje nejsou úplné.  

* Řešení   
Služba soubory Azure NetApp používat Azure Key Vault. Služba Azure Key Vault se ověřuje pomocí tokenu z Azure Active Directory. Vlastník aplikace proto musí zaregistrovat aplikaci v Azure Active Directory.

* Alternativní řešení   
Žádné.  Služba Azure Key Vault musí být zařídit správné nastavení pro použití služby soubory Azure NetApp.  

<a name="error_02"></a>***Vytvoření tokenu se nedá změnit.***   
Tato chyba nastane při změně tokenu vytvoření po vytvoření svazku.
Vytvoření tokenu musí být nastavena, když svazek je vytvořen a není možné později změnit.

* Příčina   
Se pokoušíte změnit token pro vytvoření svazku se vytvořil, což není podporovaná operace.

* Řešení   
Po vytvoření svazku, zvažte odebrání parametru z požadavku na Zavřít chybovou zprávu.

* Alternativní řešení   
Pokud potřebujete změnit vytvoření tokenu, můžete vytvořte nový svazek s nový token pro vytvoření a potom migrovat data do nového svazku.


<a name="error_03"></a>***Vytvoření tokenu musí být aspoň 16 znaků.***   
Této chybě dochází při vytváření tokenu nesplňuje požadavek na délku. Délka vytvoření tokenu musí být aspoň 16 znaků.

* Příčina   
Vytvoření tokenu nesplňuje požadavek na délku.  Při vytvoření svazku s použitím rozhraní API, se vyžaduje vytvoření tokenu. Pokud používáte portál, můžete automaticky generovat token.

* Řešení   
Zvyšte délku vytvoření tokenu. Například můžete přidat jiného slova na začátku nebo konci vytvoření tokenu.

* Alternativní řešení   
Minimální požadovaná délka vytvoření tokenu se nedají obejít.  Prefix nebo sufix můžete zvýšit vytvořením tokenu.


<a name="error_04"></a>***Chyba při odstraňování svazku, který nebyl nalezen v NetApp soubory Azure.***   
K této chybě došlo, protože vnitřní registru prostředků není synchronizovaný.

* Příčina   
Svazek může zůstat zobrazená na portálu nějakou dobu, po jeho odstranění. Když odstraníte svazek s použitím rozhraní API, je možné, že svazek nebyl správně zadán. Chyba může být způsobeno mezipaměti zastaralého prohlížeče.

* Řešení   
Pokud používáte portál vymazat prohlížeče mezipaměti. Je také interní mezipaměť, která se aktualizují každých 10 minut.  Můžete zkusit vymazat mezipaměť znovu.  Pokud se problém nevyřeší po 10 minutách, můžete vytvořit lístek podpory.

* Alternativní řešení   
Prozatím použijte jiný svazek a ignorovat existující.


<a name="error_05"></a>***Chyba při vkládání nový svazek na NetApp soubory Azure.***   
K této chybě dochází, protože vnitřní registru prostředků není synchronizovaný.

* Příčina   
Svazek může být zůstane zobrazené na portálu nechystáte nějakou dobu, po jeho odstranění. Když odstraníte svazek s použitím rozhraní API, je možné, že svazek nebyl správně zadán.

* Řešení   
Pokud používáte portál, svazek již byla vytvořena.  Svazek by se zobrazit automaticky. Pokud se problém nevyřeší, můžete vytvořit lístek podpory.

* Alternativní řešení   
Vytvořit svazek s jiným názvem a vytváření jiný token.


<a name="error_06"></a>***Název cesty souboru může obsahovat písmena, číslice a pomlčky (""-"") pouze.***   
Tato chyba nastane, pokud cesta k souboru obsahuje nepodporované znaky, například, období ("."), čárky (","), podtržítka ("\_"), nebo znak dolaru ("$").

* Příčina   
Cesta k souboru obsahuje nepodporované znaky, například, období ("."), čárky (","), podtržítka ("\_"), nebo znak dolaru ("$").

* Řešení   
Odebere znaky, které nejsou písmena abecedy, číslice nebo pomlčky ("-") ze zadané cesty k souboru.

* Alternativní řešení   
Můžete nahradit podtržítkem pomlčkou nebo použít malá a velká písmena namísto prostorů k označení začátku nového slova (například "NewVolume" místo "nový svazek").


<a name="error_07"></a>***ID svazku nejde změnit.***   
K této chybě dochází při pokusu o změnit ID svazku.  Změna ID svazku není podporovaná operace.

* Příčina   
ID systému souborů je nastaven při vytvoření svazku. ID svazku nelze později změnit.

* Řešení   
Žádné.

* Alternativní řešení   
Žádné.  ID svazku se vygeneruje, když svazek je vytvořen a následně se nedá změnit.


<a name="error_08"></a>***Neplatná hodnota "{0}' byl přijat pro {1}.***   
Tato zpráva znamená chybu v polích pro RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 a názvů Nfsv4.

* Příčina   
Žádost o ověření vstupu selhala. pro nejméně jednu z těchto polí: RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 a názvů Nfsv4.

* Řešení   
Ujistěte se, že můžete nastavit všechny povinné a nekonfliktní parametry příkazového řádku. Například nelze nastavit parametry UnixReadOnly i UnixReadWrite ve stejnou dobu.

* Alternativní řešení   
V části řešení.  


<a name="error_09"></a>***Chybí hodnota pro '{0}".***   
Tato chyba označuje, že požadovaný atribut chybí požadavek pro nejméně jednu z následujících parametrů: RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 a názvů Nfsv4.

* Příčina   
Žádost o ověření vstupu selhala. pro nejméně jednu z těchto polí: RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 a názvů Nfsv4.

* Řešení   
Ujistěte se, že můžete nastavit všechny povinné a nekonfliktní parametry příkazového řádku. Například nelze nastavit parametry UnixReadOnly i UnixReadWrite ve stejnou dobu

* Alternativní řešení   
V části řešení.  


<a name="error_10"></a> ***{0} již používán.***   
Tato chyba označuje, že již byl použit název prostředku.

* Příčina   
Pokoušíte se vytvořit svazek s názvem, který je stejný jako existující svazek.

* Řešení   
Při vytváření svazku, použijte jedinečný název.

* Alternativní řešení   
V případě potřeby můžete změnit název existující svazek tak, aby nový svazek, můžete použít odpovídající název.


<a name="error_11"></a> ***{0} příliš krátký.***   
Tato chyba označuje, že název svazku nesplňuje požadavek na minimální délku.

* Příčina   
Název svazku je příliš krátký.

* Řešení   
Zvyšte délku název svazku.  

* Alternativní řešení   
Název svazku, můžete přidat běžné prefix nebo sufix.


<a name="error_12"></a>***Azure API souborů v NetApp nedostupný.***   
Rozhraní API služby Azure využívá rozhraní API služby soubory Azure NetApp ke správě svazky.  Tato chyba znamená problém s připojením k rozhraní API.

* Příčina   
Základní rozhraní API neodpovídá, výsledkem vnitřní chyby. Tato chyba je pravděpodobně být dočasné.

* Řešení   
Problém je pravděpodobně být dočasné.  Požadavek uspěli po nějaké době.

* Alternativní řešení   
Žádné. Základní rozhraní API je nezbytné pro správu svazků.  


<a name="error_13"></a>***Pro předplatné se nenašly žádné přihlašovací údaje "{0}".***   
Tato chyba označuje, že zadané přihlašovací údaje jsou neplatné nebo nebyly nastaveny správně v rámci předplatného.

* Příčina   
Přihlašovací údaje, které jsou neplatné nebo nesprávně nastavenu zabránit přístupu ke službě pro správu svazků.

* Řešení   
Ujistěte se, že přihlašovací údaje nastavit a na příkazovém řádku zadán správně.

* Alternativní řešení   
Žádné.  Nastavení přihlašovacích údajů správně je nezbytné pro použití služby soubory Azure NetApp.  


<a name="error_14"></a>***Nenašlo žádné id výsledku operace pro "{0}".***   
Tato chyba označuje, že dokončení operace brání vnitřní chybě.

* Příčina   
Vnitřní chyba došlo k chybě a zabránila dokončení operace.

* Řešení   
Tato chyba je pravděpodobně být dočasné.  Počkejte pár minut a zkuste to znovu. Pokud se problém nevyřeší, vytvořte lístek a máte technické podpory prošetření problému.

* Alternativní řešení   
Počkejte pár minut a zkontrolujte, zda problém přetrvává.


<a name="error_15"></a>***Operace "{0}' není podporován.***   
Tato chyba označuje, že příkaz není k dispozici pro aktivní předplatné nebo prostředek.

* Příčina   
Operace není k dispozici pro předplatné nebo prostředek.

* Řešení   
Ujistěte se, že příkaz je zadán správně a dostupných prostředků a předplatným, které používáte.

* Alternativní řešení   
V části řešení.  


<a name="error_16"></a>***Operace opravy se nepodporuje pro tento typ prostředku.***   
K této chybě dochází při pokusu o změnit cíl připojení nebo snímku.

* Příčina   
Cíl připojení se definuje při vytvoření, a nedá se změnit později.

* Řešení   
Žádné.  Cíl připojení nelze změnit po vytvoření svazku.

* Alternativní řešení   
Žádné.


<a name="error_17"></a>***Přijala se hodnota pro vlastnost jen pro čtení "{0}".***   
K této chybě dochází, když definujete hodnotu pro vlastnost, která se nedá změnit. Například nelze změnit ID svazku.

* Příčina   
Jste se pokusili upravit parametr (například Identifikátor svazku), který se nedá změnit.

* Řešení   
Žádné. Parametr ID svazku se nedá upravit.

* Alternativní řešení   
ID svazku by neměla vyžadovat úpravy.  Alternativní řešení proto není nutné.

<a name="error_18"></a>***Požadovaná {0} nebyl nalezen.***   
K této chybě dochází při pokusu o odkazují na neexistující zdroj, například svazku nebo snímku. Prostředek byl pravděpodobně odstraněn nebo pojmenovaný špatně napsaný prostředků.

* Příčina   
Pokoušíte se odkazují na neexistující prostředku (například svazku nebo snímek), který je už Odstraněná nebo má název nesprávně hláskovaným prostředků.

* Řešení   
Kontrola žádosti o pravopisné chyby, abyste měli jistotu, že je správně odkazováno.

* Alternativní řešení   
V části řešení.

<a name="error_19"></a>***Nepovedlo se získat přihlašovací údaje pro předplatné "{0}".***   
Tato chyba označuje, že zadané přihlašovací údaje jsou neplatné nebo nesprávné nastavení v rámci předplatného.

* Příčina   
Přihlašovací údaje, které jsou neplatné nebo nesprávně sady v rámci předplatného zabránit přístupu ke službě pro správu svazků.

* Řešení   
Ujistěte se, že přihlašovací údaje nastavit a na příkazovém řádku zadán správně.

* Alternativní řešení   
Žádné.  Správně nastavit přihlašovací údaje jsou nezbytné pro použití služby soubory Azure NetApp.

<a name="error_20"></a>***Neznámý Azure NetApp souborů došlo k chybě.***   
Rozhraní API služby Azure využívá rozhraní API služby soubory Azure NetApp ke správě svazky. Chyba určuje problému v komunikaci se rozhraní API.

* Příčina   
Základní rozhraní API přihlašuje k neznámé chybě.  Tato chyba je pravděpodobně být dočasné.

* Řešení   
Tento problém je pravděpodobně dočasný a požadavek uspěli po nějaké době. Pokud potíže potrvají, vytvořte lístek podpory a mít problém prozkoumat.

* Alternativní řešení   
Žádné.  Základní rozhraní API je nezbytné pro správu svazků.

<a name="error_21"></a>***Hodnota pro na neznámou vlastnost '{0}".***   
Tato chyba nastane, pokud jsou k dispozici neexistující vlastnosti pro určitý prostředek, například svazků, snímku nebo cíl připojení.

* Příčina   
Požadavek obsahuje sadu vlastností, které lze použít s každého prostředku.  V požadavku nesmí obsahovat žádné neexistující vlastnosti.

* Řešení   
Ujistěte se, že všechny názvy vlastností jsou zadány správně a vlastnosti jsou k dispozici pro dané předplatné a prostředků.

* Alternativní řešení   
Snižte počet vlastností definovaných v požadavku na odstranění vlastnost, která je příčinou chyby.


<a name="error_22"></a>***Operace aktualizace není podporována pro tento typ prostředku.***   
Je možné aktualizovat jenom svazky. K této chybě dochází při pokusu o provedení nepodporované aktualizace operace, například aktualizace snímku.

* Příčina   
Prostředek, který se pokoušíte aktualizovat nepodporuje operaci update.  Pouze svazky může mít své vlastnosti změnit.

* Řešení   
Žádné.  Prostředek, který se pokoušíte aktualizovat nepodporuje operaci update. Proto jej nelze změnit.

* Alternativní řešení   
Pro svazek vytvořte nový prostředek se aktualizace na místě a znovu migrovat data.


<a name="error_23"></a>***Počet položek: {0} pro objekt: {1} je mimo rozsah maximálních.***   
K této chybě dochází, pokud pravidla zásad export nesplňuje požadavek na minimální nebo maximální rozsah.  Můžete definovat zásady exportu, musí mít a jeden export pravidlo zásad minimálně pět export pravidla zásad na maximum.

* Příčina   
Export zásady, které jste definovali nesplňuje požadovaný rozsah.  

* Řešení   
Ujistěte se, že se již nepoužívá index, který je v rozsahu od 1 do 5.

* Alternativní řešení   
Není to povinné použití export zásad na svazcích. Proto můžete vynechat export zásad úplně, pokud není potřeba mít pravidla zásad exportu.


<a name="error_24"></a>***Duplicitní hodnota chyby pro objekt {0}.***   
K této chybě dochází, když není definovaný export zásad s jedinečnými indexy.  Při definování zásad export všechna pravidla zásad export musí mít jedinečný index od 1 do 5.

* Příčina   
Zásady definované export nesplňuje požadavek na export pravidla zásad. Musí mít jeden export zásad pravidlo minimálně a pět export pravidla zásad na maximum.  

* Řešení   
Ujistěte se, že se již nepoužívá index a že je v rozsahu od 1 do 5.

* Alternativní řešení   
Pro pravidlo, které se pokoušíte nastavit, použijte jiný index.


