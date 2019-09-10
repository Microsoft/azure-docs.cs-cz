---
title: Terminologie ve službě Azure Data Share Preview
description: Terminologie ve službě Azure Data Share Preview
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: b78a1b250f46c2ddd2b36e19590c4a94ca3ab3fb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844702"
---
# <a name="azure-data-share-preview-concepts"></a>Koncepty verze Preview služby Azure Data Share 

Azure Data Share Preview zavádí určitou novou terminologii související se sdílením dat. Tento článek vysvětluje některé často používané výrazy, které se můžou zobrazit v rámci služby. 

## <a name="data-provider"></a>Poskytovatel dat

Poskytovatel dat je organizace, která sdílí data se svými spotřebiteli. Obvykle může být poskytovatel dat vlastníkem nebo kurátor dat. Poskytovatelé dat chtějí sdílet data různých typů. Některé příklady dat, která může zprostředkovatel dat sdílet, zahrnují nezpracovaná data, jako např. data z prodejních nebo časových řad. Poskytovatel dat může také chtít sdílet předem zpracovaná a pořízená data, která už obsahují analýzy a přehledy. 

## <a name="data-consumer"></a>Příjemce dat 

Příjemce dat je organizace, která přijímá data od poskytovatele dat. Příjemce dat může chtít připojit sdílená data s vlastními daty a odvodit přehledy. V některých případech může příjemce dat přijímat data, která již byla zpracována. 

## <a name="data-share"></a>Sdílená data

Sdílená složka je skupina datových sad, které jsou sdíleny jako jediná entita. Datové sady mohou být z řady zdrojů dat Azure, které podporuje sdílená složka Azure. V současné době Azure Data Share podporuje Azure Blob Storage a Azure Data Lake Store. 

## <a name="share-subscription"></a>Sdílet předplatné 

Předplatné sdílení se vytvoří, když příjemce dat přijme pozvánku ke sdílení dat od poskytovatele dat. Poskytovatelé dat můžou zobrazit aktivní sdílení předplatných tak, že přejdete na **odeslané sdílené složky** v účtu Azure Data Share a vyberete **sdílet odběry**.

Příjemce dat může zjistit, jestli mají aktivní předplatné sdílení, a to tak, že přejde na **přijaté sdílené složky** a zobrazí stav svých přijatých sdílených složek. 

## <a name="snapshot"></a>Snímek

Snímek může vytvořit příjemce dat, když přijme pozvánku ke sdílení dat. Po přijetí pozvánky můžou aktivovat úplný snímek dat, která s nimi sdílíte. Snímek je kopie dat v okamžiku, kdy příjemce dat vygeneroval snímek. 

Existují dva typy snímků – úplné a přírůstkové. Úplný snímek obsahuje všechna data v rámci sdílené složky dat. Přírůstkový snímek obsahuje všechna data, která byla aktualizována nebo přidána od spuštění posledního snímku. 

## <a name="snapshot-settings-in-azure-data-share"></a>Nastavení snímků v Azure Data Share
 
Poskytovatel dat může povolit nastavení snímku pro sdílenou složku dat. Toto nastavení umožňuje příjemcům dat přijímat přírůstkové aktualizace, když k nim dojde. Toto nastavení by mělo být povoleno v případě, že zprostředkovatel dat bude chtít, aby příjemci dat dostávali aktualizace dat, která byla sdílena. 

Pokud zprostředkovatel dat povolí toto nastavení, je možné vybrat interval opakování. Interval opakování může být hodinu nebo každý den. 

Příjemce dat má možnost se přihlásit k tomuto plánu snímků a získat tak přírůstkové aktualizace, což zahrnuje všechna data, která se změnila od prvního vygenerování nového snímku. 

## <a name="invitation"></a>Pozvánka

Poskytovatel dat může pozvat více příjemců ke sdílení dat. Můžou to udělat tak, že přidáte příjemce do sdílené složky dat. Pozvánky je také možné přidat po vytvoření sdílené složky dat. 

Poskytovatel dat může odstranit pozvánku poté, co byla odeslána, pokud nebyla přijata. Pokud poskytovatel dat odstraní pozvánku a ještě nebyla přijata, příjemce dat je nebude moci přijmout. 

Pozvánky je možné znovu odeslat až pětkrát denně. 

## <a name="recipient"></a>Příjemce

Příjemce je osoba, která obdrží pozvánku ke sdílení dat. Poskytovatel dat obvykle přidá příjemce do sdílené složky dat, kterou vytvoří. Jakmile příjemce pozvánky pozvánku přijme, stane se příjemcem dat.  

## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .

