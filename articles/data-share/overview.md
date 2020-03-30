---
title: Co je Azure Data Share?
description: Získejte informace o jednoduchém a bezpečném sdílení dat s více zákazníky a partnery pomocí služby Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 363feda1409d2bb54e60d1b2168cba38f2a8a41c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621860"
---
# <a name="what-is-azure-data-share"></a>Co je Azure Data Share?

V dnešním světě jsou data považována za klíčové strategické aktivum, které mnoho organizací potřebuje jednoduše a bezpečně sdílet se svými zákazníky a partnery. Existuje mnoho způsobů, jak to zákazníci dnes, včetně prostřednictvím FTP, e-mail, API abychom jmenovali několik. Organizace mohou snadno ztratit přehled o tom, s kým svá data sdílely. Sdílení dat prostřednictvím FTP nebo prostřednictvím vlastní infrastruktury rozhraní API je často nákladné zřídit a spravovat. Je režie správy spojené s použitím těchto metod sdílení ve velkém měřítku. 

Mnoho organizací musí být odpovědné za data, která sdílejí. Kromě odpovědnosti by mnoho organizací chtělo mít možnost jednoduše řídit, spravovat a monitorovat veškeré sdílení dat. V dnešním světě, kde se očekává, že data budou i nadále růst exponenciálním tempem, potřebují organizace jednoduchý způsob sdílení velkých objemů dat. Zákazníci požadují nejaktuálnější data, aby bylo zajištěno, že budou schopni odvodit včasné přehledy.

Azure Data Share umožňuje organizacím jednoduše a bezpečně sdílet data s více zákazníky a partnery. Pouhými několika kliknutími můžete zřídit nový účet sdílení dat, přidat datové sady a pozvat své zákazníky a partnery do sdílené složky dat. Poskytovatelé dat mají vždy kontrolu nad daty, která sdíleli. Azure Data Share usnadňuje správu a sledování, jaká data byla sdílena, kdy a kým. 

Poskytovatel dat může mít kontrolu nad tím, jak jsou jejich data nakládána, a to zadáním podmínek použití pro jejich sdílení dat. Příjemce dat musí tyto podmínky přijmout, aby mohl data přijmout. Poskytovatelé dat můžou určit frekvenci, s jakou jejich příjemci dat obdrží aktualizace. Poskytovatel dat může přístup k novým aktualizacím kdykoli odvolat. 

Azure Data Share pomáhá vylepšit přehledy tím, že usnadňuje kombinování dat od třetích stran za účelem obohacení scénářů analýz a ai. Snadno využijte výkon analytických nástrojů Azure k přípravě, zpracování a analýze dat sdílených pomocí Služby Azure Data Share. 

Zprostředkovatel dat i příjemce dat musí mít předplatné Azure pro sdílení a přijímání dat. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scénáře pro Azure Data Share

Azure Data Share se dá používat v řadě různých odvětví. Maloobchodník může například chtít sdílet údaje o posledním prodejním bodu se svými dodavateli. Pomocí služby Azure Data Share může prodejce nastavit sdílenou složku dat obsahující data o prodejních bodech pro všechny své dodavatele a sdílet prodej každou hodinu nebo denně. 

Azure Data Share se taky dá použít k vytvoření trhu dat pro konkrétní obor. Například vláda nebo výzkumná instituce, která pravidelně sdílí anonymizované údaje o populačním růstu s třetími stranami. 

Dalším případem použití služby Azure Data Share je vytvoření konsorcia dat. Například řada různých výzkumných institucí může sdílet data s jedním důvěryhodným orgánem. Data se analyzují, agregují nebo zpracovávají pomocí analytických nástrojů Azure a pak je sdílejí se zúčastněnými stranami. 

## <a name="how-it-works"></a>Jak to funguje

Azure Data Share aktuálně nabízí sdílení na základě snímků a sdílení na místě. 

Při sdílení založeném na snímcích se data přesunou z předplatného Azure poskytovatele dat a přijdou v předplatném Azure spotřebitele dat. Jako poskytovatel dat zřídíte sdílení dat a pozvete příjemce do sdílené složky dat. Uživatelé dat obdrží pozvánku ke sdílené složce dat prostřednictvím e-mailu. Jakmile příjemce dat přijme pozvánku, může aktivovat úplný snímek dat, která s nimi sdílí. Tato data jsou přijata do účtu úložiště pro spotřebitele dat. Spotřebitelé dat mohou přijímat pravidelné přírůstkové aktualizace dat, která s nimi budou sdílena, aby měli vždy nejnovější verzi dat. 

Zprostředkovatelé dat mohou svým spotřebitelům dat nabízet přírůstkové aktualizace dat, která s nimi sdílela prostřednictvím plánu snímků. Snímky plány jsou nabízeny na hodinové nebo denní bázi. Když příjemce dat přijme a nakonfiguruje jejich sdílení dat, může se přihlásit k odběru plánu snímek. To je výhodné ve scénářích, kde jsou sdílená data pravidelně aktualizována a příjemce dat potřebuje nejaktuálnější data. 

![tok sdílení dat](media/data-share-flow.png)

Když příjemce dat přijme sdílenou složku dat, může je přijímat v úložišti dat podle svého výběru. Například pokud poskytovatel dat sdílí data pomocí Azure Blob Storage, příjemce dat můžete přijímat tato data v Azure Data Lake Store. Podobně pokud poskytovatel dat sdílí data z Azure SQL Data Warehouse, příjemce dat si může vybrat, jestli chce přijímat data do Azure Data Lake Store, Azure SQL Database nebo Azure SQL Data Warehouse. V případě sdílení ze zdrojů založených na SQL může příjemce dat také zvolit, zda obdrží data v parketách nebo csv. 

Díky sdílení na místě mohou poskytovatelé dat sdílet data tam, kde se nacházejí, bez kopírování dat. Po vytvoření vztahu sdílení prostřednictvím toku pozvání je vytvořen symbolický odkaz mezi úložištěm zdrojových dat poskytovatele dat poskytovatele dat a cílovým úložištěm dat příjemce dat. Příjemce dat může číst a dotazovat data v reálném čase pomocí vlastního úložiště dat. Změny v úložišti zdrojových dat jsou okamžitě k dispozici spotřebiteli dat. Domácí sdílení je aktuálně ve verzi preview pro Azure Data Explorer.

## <a name="key-capabilities"></a>Klíčové funkce

Azure Data Share umožňuje poskytovatelům dat:

* Sdílení dat ze seznamu [podporovaných úložišť dat](supported-data-stores.md) se zákazníky a partnery mimo vaši organizaci

* Mějte přehled o tom, s kým jste svá data sdíleli

* Volba snímku nebo sdílení na místě

* Jak často vaši data spotřebitelé dostávají aktualizace vašich dat

* Umožněte zákazníkům podle potřeby stáhnout nejnovější verzi dat nebo jim umožnit automaticky přijímat přírůstkové změny vašich dat v intervalu, který jste definovali.

Azure Data Share umožňuje zákazníkům dat: 

* Zobrazení popisu typu sdílených dat

* Zobrazit podmínky použití pro data

* Přijetí nebo odmítnutí pozvánky ke sdílené službě Azure Data Share

* Přijímejte data sdílená s vámi do [podporovaného úložiště dat](supported-data-stores.md).

* Aktivace úplného nebo přírůstkového snímku sdílené složky dat, kterou s vámi organizace sdílela

* Přihlášení k odběru sdílené složky dat pro příjem nejnovější kopie dat prostřednictvím přírůstkového snímku

Všechny klíčové funkce uvedené výše jsou podporované prostřednictvím portálu Azure nebo prostřednictvím rest API. Další podrobnosti o používání azure sdílení dat prostřednictvím rest API, podívejte se na naši referenční dokumentaci. 

## <a name="security"></a>Zabezpečení

Azure Data Share využívá základní zabezpečení, které Azure nabízí k ochraně dat v klidovém stavu a při přenosu. Data jsou šifrována v klidovém stavu, kde je podporováno podkladovým úložištěm dat. Data jsou také šifrována při přenosu. Metadata o sdílené složce dat jsou také šifrována v klidovém stavu a při přenosu. 

Ovládací prvky přístupu lze nastavit na úrovni prostředků Azure Data Share, aby bylo zajištěno, že k nim mají přístup ti, kteří jsou autorizováni. 

Azure Data Share využívá spravované identity pro prostředky Azure (dříve označované jako MSI) pro automatickou správu identit ve službě Azure Active Directory. Spravované identity pro prostředky Azure se využívají pro přístup k úložištím dat, která se používají pro sdílení dat. Neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. Další informace najdete na [stránce Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Podporované oblasti

Seznam oblastí Azure, které zpřístupní Azure Data Share, najdete na stránce [produktů dostupných podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) a vyhledejte Azure Data Share. 

Azure Data Share neukládá kopii samotných dat. Data jsou uložena v podkladovém úložišti dat, které je sdíleno. Například pokud výrobce dat ukládá svá data v účtu Azure Data Lake Store umístěné v západní USA, to je místo, kde jsou uložena data. Pokud sdílejí data s účtem Azure Storage umístěným v západní Evropě prostřednictvím snímku, obvykle se data přenášejí přímo do účtu Azure Storage umístěného v západní Evropě.

Služba Azure Data Share nemusí být k dispozici ve vaší oblasti, aby mohla službu využívat. Například pokud máte data uložená v účtu Azure Storage umístěné v oblasti, kde Azure Data Share ještě není k dispozici, můžete stále využít službu ke sdílení dat. 

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak začít sdílet data, [pokračujte ve sdílení dat](share-your-data.md) kurzu.
