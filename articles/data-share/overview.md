---
title: Co je Azure dat sdílené složky ve verzi Preview
description: Přehled sdílení dat Azure ve verzi Preview
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7d4e51ec9564bfb123cf73d9fe89d040f42fe650
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807547"
---
# <a name="what-is-azure-data-share-preview"></a>Co je Azure Data Share Preview?

V dnešním světě data zobrazit jako se strategickým aktivem klíče, který mnoho organizace potřebují jednoduše a bezpečně sdílet se svými zákazníky a partnery. Existuje mnoho způsobů, že zákazníci provést ještě dnes, včetně pomocí služby FTP, e-mailu, rozhraní API pro další. Organizace můžou snadno ztrátě informací o kdo sdílí svá data pomocí. Sdílení dat přes FTP nebo vlastních rozhraní API infrastruktury je často drahé ke zřízení a správě. Je režie spojená s využitím těchto metod sdílení ve velkém měřítku. 

Řada organizací musí být zodpovídají za data, která sdílejí. Kromě zodpovědnost řada organizací chcete mít možnost řídit, spravovat a monitorovat všechna svá data v jednoduchý způsob sdílení. Organizace potřebují jednoduchý způsob, jak sdílet velké objemy dat, informací o dnešku světě, kde je očekávána data nadále rozšiřovat exponenciální rychlostí. Zákazníci vyžadují nejnovější data k zajištění, aby je bylo možné včasné vyvoďte z nich.

Azure Data sdílet ve verzi Preview umožňuje organizacím jednoduše a bezpečně sdílet data s více partnery a zákazníky. Pomocí několika kliknutí můžete zřídit nový účet data sdílené složky, přidat datové sady a pozvat zákazníci a partneři dat sdílené složky. Zprostředkovatelé dat se vždycky nacházejí v kontrolu nad daty, která sdílejí. Sdílené složky Azure dat usnadňuje spravovat a sledovat, jaká data se sdílí, kdy a kým. 

Zprostředkovatel dat můžete zachovejte si kontrolu nad zpracování svá data zadáním podmínky použití pro svá data sdílet. Spotřebitel dat musí přijmout tyto podmínky teprve pak ji bude moci přijímat data. Zprostředkovatelé dat můžete zadat frekvenci, s jakou jejich spotřebitelé dat přijímat aktualizace. Kdykoli můžete poskytovatelem dat. odvolat přístup k nové aktualizace. 

Sdílené složky Azure dat pomáhá vylepšit insights usnadňuje kombinace dat od jiných výrobců rozšířit analýzy a AI scénáře. Snadno použijte nástroje Azure analytics ot k přípravě, zpracovávat a analyzovat data sdílená pomocí sdílené složky služby Azure Data. 

## <a name="scenarios-for-azure-data-share"></a>Scénáře pro sdílenou složku Azure dat

Sdílené složky Azure dat je možné v celou řadou různých odvětví. Prodejce může třeba nedávný bod prodejních dat sdílet s jejich dodavatele. Pomocí Azure Data sdílet, můžete prodejce nastavte sdílení dat obsahující bod prodejní data pro všechny jejich dodavatelů a sdílejte prodeje na hodinové nebo denní bázi. 

Sdílené složky Azure dat lze také vytvořit datové tržiště pro konkrétní odvětví. Například státní instituce nebo výzkumu instituce, která pravidelně sdílí anonymizovali data o růst s třetími stranami. 

Další případ použití pro sdílené složky služby Azure Data navazuje data consortium. Například celou řadou různých výzkumnými ústavy můžou sdílet data s jednoho důvěryhodného textu. Data se analyzují, agregují nebo zpracovány pomocí nástroje Azure analytics a pak je sdílet se zúčastněnými stranami. 

## <a name="how-it-works"></a>Jak to funguje

Sdílené složky Azure Data používá založených na snímcích sdílení přístup, odkud se data přesouvá z předplatného Azure zprostředkovatele dat a jsou v rámci předplatného Azure příjemce dat. Jako poskytovatel dat zřídíte sdílení dat a pozvat příjemcům sdílet data. Spotřebitelé dat přijímat pozvánky dat sdílené složky prostřednictvím e-mailu. Jakmile příjemce dat přijme pozvánku, mohou aktivovat plnou snímků sdílených dat je sdílíte. Přijetí tato data do účtu úložiště dat uživatele. Spotřebitelé dat dostávat pravidelné, přírůstkové aktualizace dat s nimi sdíleli, aby vždy měli nejnovější data. 

Zprostředkovatelé dat můžou nabízet svoje data příjemci přírůstkové aktualizace dat s nimi sdílí prostřednictvím plánu snímku. Plány snímku se nabízejí na hodinu nebo každý den. Když příjemce dat přijme a nakonfiguruje jejich sdílení dat, přihlášení k odběru plánu snímku. To je užitečné v situacích, kdy sdílených dat je aktualizována v pravidelných intervalech a příjemce dat vyžaduje nejnovější data. 

![tok dat sdílené složky](media/data-share-flow.png)

Když příjemce dat přijme sdílení dat, se může přijímat data v účtu úložiště podle vlastního uvážení. Například pokud poskytovatel dat sdílí data pomocí služby Azure Blob Storage, příjemce dat, dostanou tato data v Azure Data Lake Store. 

## <a name="key-capabilities"></a>Klíčové funkce

Sdílené složky Azure dat umožňuje poskytovatelům dat:

* Sdílení dat ze služby Azure Storage a Azure Data Lake Store se zákazníky a partnery mimo vaši organizaci

* Sledovat, s kým je sdílíte svá data pomocí

* Jak často jsou data uživatele příjem aktualizací k vašim datům

* Povolit zákazníkům podle potřeby si přetáhněte nejnovější data, nebo zajistí, aby automaticky obdrží přírůstkové změny ke svým datům v intervalu definované uživatelem

Sdílené složky Azure Data umožňuje uživatelům dat: 

* Zobrazit popis typu sdílení dat

* Zobrazit podmínky použití pro data

* Následně přijímal nebo odmítal pozvánku dat sdílené složky Azure

* Spustit úplné nebo přírůstkové snímek sdílení dat, který s vámi sdílí organizace

* Přihlaste se k sdílení dat pro příjem nejnovější kopii dat prostřednictvím přírůstkový snímek kopie odběru

* Přijmout data sdílená s vámi do účtu Azure Blob Storage nebo Azure Data Lake Gen2

Všechny klíčové funkce uvedené výše jsou podporovány prostřednictvím Azure nebo přes rozhraní REST API. Další podrobnosti o použití sdílení dat Azure pomocí rozhraní REST API projděte si naši referenční dokumentaci. 

## <a name="security"></a>Zabezpečení

Sdílené složky Azure dat využívá základního zabezpečení, které Azure nabízí pro ochranu dat v klidovém stavu a během přenosu. Data se šifrují při nečinnosti, kde je základní mechanismus úložiště podporuje. Data se šifrují taky při přenosu. Metadata o sdílené složce data se šifrují taky v klidovém stavu a během přenosu. 

Řízení přístupu je možné nastavit na úrovni zdroje dat sdílené složky Azure k zajištění, že se k nim přistupovat pomocí ty, které mají oprávnění. 

Sdílené složky Azure dat využívá identit spravovaných pro prostředky Azure (dříve označovanou jako souborů MSI) pro automatickou správu identity ve službě Azure Active Directory. Pro přístup k účtům úložiště, které se používají pro sdílení dat se využívají spravovaných identit pro prostředky Azure. Neexistuje žádné výměnu přihlašovacích údajů mezi zprostředkovatele dat a dat uživatelů. Další informace najdete [identit spravovaných prostředků Azure stránky](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 

## <a name="supported-regions"></a>Podporované oblasti

Seznam oblastí Azure, které usnadňují sdílení dat Azure k dispozici, najdete [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) stránky a vyhledejte Data sdílené složky Azure. 

Sdílené složky Azure dat neukládá vlastní data. Data se ukládají v základní účty úložiště, které jsou sdíleny. Například pokud poskytovatel dat ukládají svá data v účtu služby Azure Data Lake Store, který je umístěný v oblasti západní USA, to je, kde jsou data uložená. Pokud se účet služby Azure Storage, který je umístěný v oblasti západní Evropa, jsou sdílení dat, data se přenesou přímo do účtu Azure Storage, který je umístěný v oblasti západní Evropa. 

Služba sdílení dat Azure nemá k dispozici ve vaší oblasti využívat službu. Například pokud máte data uložená v účtu služby Azure Storage, který je umístěný v oblasti, kde Azure sdílet Data ještě nejsou k dispozici, můžete využít službu sdílet vaše data. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak začít, sdílení dat, nadále [sdílet vaše data](share-your-data.md) kurzu.
