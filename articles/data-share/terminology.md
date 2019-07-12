---
title: Terminologie ve verzi Preview sdílenou složku Azure dat
description: Terminologie ve verzi Preview sdílenou složku Azure dat
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 4e7db84666b9d3786c3fc25e3653d24d0b95f2e4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789225"
---
# <a name="azure-data-share-preview-concepts"></a>Koncepty Azure Data sdílené složky ve verzi Preview 

Azure dat Share Preview zavádí některé nový terminologií souvisejících se sdílením dat. Tento článek vysvětluje, že některé často používané termíny, které se mohou zobrazit používané v rámci služby. 

## <a name="data-provider"></a>Zprostředkovatel dat

Poskytovatel dat je organizace, která je sdílení dat s jejich příjemce. Zprostředkovatel dat může být obvykle vlastníka nebo Kurátor data. Zprostředkovatelé dat chcete sdílet data různých typů. Mezi příklady dat, který má zprostředkovatele dat může sdílet patří nezpracovaná data, jako je například bod sales nebo data časových řad. Zprostředkovatel dat také chtít sdílet předběžného zpracování, kurátorované data, která už obsahuje analýzy a přehledy. 

## <a name="data-consumer"></a>Data Consumer 

Spotřebitel dat je organizace, která přijímá data od poskytovatele dat. Příjemce dat může být chce připojit k sdíleným datům s jejich daty a vyvoďte z nich. V některých případech může spotřebitel dat přijímat data, která již byla zpracována. 

## <a name="data-share"></a>Sdílení dat

Sdílení dat je skupina datové sady, které jsou sdíleny jako jedna entita. Datové sady mohou být z mnoha zdrojů dat Azure, které podporuje sdílení dat Azure. Data sdílené složky Azure v současné době podporuje Azure Blob Storage a Azure Data Lake Store. 

## <a name="share-subscription"></a>Sdílet předplatné 

Předplatné sdílené složky se vytvoří při příjemce dat přijme pozvánku sdílené složky do dat od poskytovatele dat. Zprostředkovatelé dat můžete zobrazit předplatná aktivní sdílené složky tak, že přejdete do **odesílány složky** v Azure Data sdílet účet a vyberete **sdílet předplatná**.

Příjemce dat můžete zkontrolovat, že přejdete na předplatné aktivní sdílené složky mají **složky přijal** a zobrazit stav jejich přijatý sdílené složky. 

## <a name="snapshot"></a>Snímek

Spotřebitel dat mohou vytvořit snímek při přijetí pozvánky sdílenou složku data. Při přijetí pozvánky, se může aktivovat plnou snímek dat s nimi sdílí. Snímek je kopie dat v bodě v čase, aby se vyvolala příjemce dat snímku. 

Existují dva typy snímků – úplné a přírůstkové. Úplné snímku obsahuje veškerá data ve sdílené složce data. Přírůstkový snímek obsahuje všechna data, která se aktualizuje/přidala od poslední snímek se aktivoval. 

## <a name="snapshot-settings-in-azure-data-share"></a>Nastavení snímků ve sdílené složce dat Azure
 
Zprostředkovatel dat můžete povolit nastavení snímek pro sdílenou složku data. Toto nastavení umožňuje spotřebitelé dat a získat přírůstkové aktualizace, když k nim dojde. Toto nastavení musí být povolené, pokud poskytovatel dat jejich spotřebitelé dat pro příjem aktualizací dat, která sdílí. 

Pokud poskytovatel dat aktivuje toto nastavení, lze vybrat interval opakování. Interval opakování může být hodinových nebo denních. 

Spotřebitel dat má možnost vyjádřit výslovný souhlas pro tento plán snímku přijímat přírůstkové aktualizace, která zahrnuje všechna data, která změnila, nejprve vygeneruje nový snímek. 

## <a name="invitation"></a>Pozvání

Zprostředkovatel dat můžete pozvat více příjemcům sdílet svoje data. Jsou to tak, že přidání příjemců do sdílené složky data. Pozvánky můžete přidat i po vytvoření sdílené složky data. 

Zprostředkovatel dat můžete odstranit pozvánku po byl odeslán. Všimněte si, že pokud zprostředkovatele dat odstraní pozvánku poté, co byl přijat, může příjemce dat stále mít předplatné aktivní sdílené složky. Pokud poskytovatel dat odstraní pozvánku a nebyla dosud přijata, příjemce dat nebude možné ji přijmout. 

## <a name="recipient"></a>Příjemce

Příjemce je osoba, která bude přijímat pozvánky ke sdílené složce data. Zprostředkovatel dat se obvykle přidat příjemce do sdílení dat, který vytvoří. Po příjemce pozvánku přijímá e-mailové pozvánce, budou data uživatelů.  

## <a name="next-steps"></a>Další postup

Zjistěte, jak začít, sdílení dat, nadále [sdílet vaše data](share-your-data.md) kurzu.

