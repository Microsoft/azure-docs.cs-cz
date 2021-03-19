---
title: Terminologie služby Azure Data Share
description: Přečtěte si o běžných pojmech, které se používají k popisu prostředků používaných v Azure Data Share (poskytovatel dat, příjemce dat, sdílení dat, sdílení předplatného, snímek, pozvánka, příjemce).
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "73468553"
---
# <a name="azure-data-share-concepts"></a>Koncepty Azure Data Share 

Azure Data Share zavádí určitou novou terminologii související se sdílením dat. Tento článek vysvětluje některé často používané výrazy, které se můžou zobrazit v rámci služby. 

## <a name="data-provider"></a>Poskytovatel dat

Poskytovatel dat je organizace, která sdílí data se svými spotřebiteli. Obvykle může být poskytovatel dat vlastníkem nebo kurátor dat. Poskytovatelé dat chtějí sdílet data různých typů. Některé příklady dat, která může zprostředkovatel dat sdílet, zahrnují nezpracovaná data, jako např. data z prodejních nebo časových řad. Poskytovatel dat může také chtít sdílet předem zpracovaná a pořízená data, která už obsahují analýzy a přehledy. 

## <a name="data-consumer"></a>Příjemce dat 

Příjemce dat je organizace, která přijímá data od poskytovatele dat. Příjemce dat může chtít připojit sdílená data s vlastními daty a odvodit přehledy. V některých případech může příjemce dat přijímat data, která již byla zpracována. 

## <a name="data-share"></a>Data Share

Sdílená datová složka je skupina datových sad, které se sdílejí jako jediná entita. Datové sady mohou být z celé řady zdrojů dat v Azure, které podporuje služba Azure Data Share. V současné době Azure Data Share podporuje Azure Blob Storage a Azure Data Lake Store. 

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

## <a name="invitation"></a>Uživateli

Poskytovatel dat může pozvat více příjemců ke sdílení dat. Můžou to udělat tak, že přidáte příjemce do sdílené složky dat. Pozvánky je také možné přidat po vytvoření sdílené složky dat. 

Poskytovatel dat může odstranit pozvánku poté, co byla odeslána, pokud nebyla přijata. Pokud poskytovatel dat odstraní pozvánku a ještě nebyla přijata, příjemce dat je nebude moci přijmout. 

Pozvánky je možné znovu odeslat až pětkrát denně. 

## <a name="recipient"></a>Příjemce

Příjemce je osoba, která obdrží pozvánku ke sdílení dat. Poskytovatel dat obvykle přidá příjemce do sdílené složky dat, kterou vytvoří. Jakmile příjemce pozvánky pozvánku přijme, stane se příjemcem dat.  

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
