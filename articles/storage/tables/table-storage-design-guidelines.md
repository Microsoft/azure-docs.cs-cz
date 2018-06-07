---
title: Pokyny pro úložiště Azure table návrhu | Microsoft Docs
description: Návrh služby tabulky Azure pro podporu operací čtení efektivně.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 5329d33aee1bb1a55e9982b1ba9e3e8329246980
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660754"
---
# <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulky

Návrh tabulky pro použití s služby úložiště Azure table se příliš neliší od aspektech návrhu pro relační databázi. Tento článek popisuje pokyny k návrhu řešení pro služby tabulky efektivní číst a zapisovat efektivní.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Navrhněte řešení služby Table účinný pro čtení

* ***Návrh pro dotazování v aplikacích číst náročné.*** Při navrhování vaší tabulky, vezměte v úvahu dotazy (zejména latence citlivé ty), které se provedou předtím, než si myslíte o tom, jak bude aktualizovat váš entity. To obvykle vede efektivní a původce řešení.  
* ***Zadejte klíč oddílu a RowKey v dotazech.*** *Bod dotazy* , jako jsou ty se nejúčinnější dotazů služby table.  
* ***Zvažte uložení duplicitní kopie entit.*** Table storage je levných, zvažte uložení stejné entity vícekrát (s různými klíči) umožňující efektivnější dotazy.  
* ***Vezměte v úvahu denormalizing vaše data.*** Table storage je levných, vezměte v úvahu denormalizing vaše data. Například uložte souhrn entit, tak, aby dotazy pro shromáždění dat stačí pro přístup k jedné entity.  
* ***Použijte hodnoty složeného klíče.*** Jsou pouze klíče máte **PartitionKey** a **RowKey**. Například použijte složené klíče hodnoty povolit cesty alternativní s klíčem přístupu na entity.  
* ***Použijte projekce dotazu.*** Můžete snížit množství dat, který přenos přes síť pomocí dotazů, které vyberte pole, které potřebujete.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Navrhněte řešení služby Table účinný zápisu  

* ***Nevytvářejte aktivní oddíly.*** Zvolte klíče, které vám umožní rozloženy více oddílů v libovolném bodě čas své žádosti.  
* ***Vyhněte se špičky v provozu.*** Funkce Smooth provoz přiměřené období a vyhnout se špičky v provozu.
* ***Nevytvářejte nutně do samostatné tabulky pro každý typ entity.*** Pokud požadujete jednotlivé transakce mezi typy entit, můžete uložit těchto více typů entit ve stejném oddílu ve stejné tabulce.
* ***Vezměte v úvahu maximální propustnost, které musí dosáhnout.*** Musíte mít na paměti cíle škálovatelnosti pro službu tabulky a ujistěte se, že váš návrh nezpůsobí je delší, než je.  

Při čtení této příručky, zobrazí se příklady, které všechny tyto zásady uvedené do praxe. 

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat v tabulce](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)