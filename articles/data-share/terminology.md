---
title: Terminologie služby Azure Data Share
description: Další informace o běžných termínech používaných k popisu prostředků používaných ve službě Azure Data Share (poskytovatel dat, příjemce dat, sdílení dat, sdílení předplatného, snímek, pozvánka, příjemce.)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468553"
---
# <a name="azure-data-share-concepts"></a>Koncepty sdílení dat Azure 

Azure Data Share zavádí některé nové terminologie související se sdílením dat. Tento článek vysvětluje některé často používané termíny, které se mohou zobrazit v celé službě. 

## <a name="data-provider"></a>Poskytovatel dat

Zprostředkovatel dat je organizace, která sdílí data se svými spotřebiteli. Obvykle může být vlastníkem nebo správcem dat. Zprostředkovatelé dat chtějí sdílet data různých typů. Mezi data, která může zprostředkovatel dat chtít sdílet, patří nezpracovaná data, například data prodejního místa nebo časových řad. Poskytovatel dat může také chtít sdílet předem zpracovaná, kurátorská data, která již obsahují analýzy a přehledy. 

## <a name="data-consumer"></a>Příjemce dat 

Příjemce dat je organizace, která přijímá data od poskytovatele dat. Příjemce dat může chtít připojit sdílená data s vlastními daty k odvození přehledů. V některých případech může příjemce dat přijímat data, která již byla zpracována. 

## <a name="data-share"></a>Data Share

Sdílená datová skupina je skupina datových sad, které jsou sdíleny jako jedna entita. Datové sady mohou pomíjet z několika zdrojů dat Azure, které jsou podporované službou Azure Data Share. Azure Data Share v současné době podporuje Azure Blob Storage a Azure Data Lake Store. 

## <a name="share-subscription"></a>Sdílet předplatné 

Sdílené předplatné se vytvoří, když příjemce dat přijme pozvánku ke sdílení dat od poskytovatele dat. Poskytovatelé dat můžou zobrazit předplatná aktivních sdílení tak, že přejdete na **Odeslané sdílené složky** v účtu Azure Data Share a vyberete **předplatná sdílení**.

Příjemce dat může zkontrolovat, zda mají aktivní sdílení předplatného přechodem na **přijaté sdílené složky** a zobrazení stavu jejich přijaté sdílené složky. 

## <a name="snapshot"></a>Snímek

Snímek může vytvořit příjemce dat, když přijme pozvánku ke sdílené složce dat. Když přijmou pozvánku, mohou aktivovat úplný snímek dat, která s nimi budou sdílena. Snímek je kopie dat v okamžiku, kdy příjemce dat vygeneroval snímek. 

Existují dva typy snímků - úplné a přírůstkové. Úplný snímek obsahuje všechna data v rámci sdílené složky dat. Přírůstkový snímek obsahuje všechna data, která byla aktualizována nebo přidána od spuštění posledního snímku. 

## <a name="snapshot-settings-in-azure-data-share"></a>Nastavení snímků ve službě Azure Data Share
 
Zprostředkovatel dat může povolit nastavení snímku pro sdílenou složku dat. Toto nastavení umožňuje spotřebitelům dat přijímat přírůstkové aktualizace v takovém stavu. Toto nastavení by mělo být povoleno, pokud by poskytovatel dat chtěl, aby jejich spotřebitelé dat dostávali aktualizace sdílených dat. 

Pokud poskytovatel dat toto nastavení povolí, lze vybrat interval opakování. Interval opakování může být hodinový nebo denní. 

Příjemce dat má možnost přihlásit se k tomuto plánu snímek přijímat přírůstkové aktualizace, která zahrnuje všechna data, která se změnila od prvního generování nového snímku. 

## <a name="invitation"></a>Pozvání

Poskytovatel dat může pozvat více příjemců do sdílené složky dat. Mohou tak učinit přidáním příjemců do sdílené složky dat. Pozvánky lze také přidat po vytvoření sdílené složky dat. 

Poskytovatel dat může odstranit pozvánku po odeslání, pokud nebyla přijata. Pokud poskytovatel dat pozvánku odstraní a pozvánka ještě nebyla přijata, příjemce dat ji nebude moci přijmout. 

Pozvánky mohou být nesnášeny až pětkrát denně. 

## <a name="recipient"></a>Příjemce

Příjemce je uživatel, který obdrží pozvánku ke sdílené složce dat. Poskytovatel dat obvykle přidá příjemce do sdílené složky dat, kterou vytvoří. Jakmile příjemce pozvánky pozvánku přijme, stane se příjemcem dat.  

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak začít sdílet data, [pokračujte ve sdílení dat](share-your-data.md) kurzu.
