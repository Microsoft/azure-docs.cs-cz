---
title: Přehled spravovaných aplikací Azure | Microsoft Docs
description: Popisuje koncepty spravovaných aplikací Azure.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: d87b27101b5bbb3fb589987865649e102d7835de
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639203"
---
# <a name="azure-managed-applications-overview"></a>Přehled spravovaných aplikací Azure

Spravované aplikace Azure umožňují nabízet cloudová řešení, která můžou spotřebitelé snadno nasadit a spravovat. Vy implementujete infrastrukturu a poskytujete průběžnou podporu. Pokud chcete zpřístupnit spravovanou aplikaci všem zákazníkům, publikujte ji v Azure Marketplace. Pokud ji chcete zpřístupnit jenom uživatelům ve vaší organizaci, publikujte ji do interního katalogu. 

Spravovaná aplikace se podobá šabloně řešení v Marketplace, ale s jedním zásadním rozdílem. Ve spravované aplikaci jsou prostředky zřízené do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení.

## <a name="advantages-of-managed-applications"></a>Výhody spravovaných aplikací

Spravované aplikace usnadňují zákazníkům používání vašich řešení. Zákazníci díky tomu nepotřebují mít k používání vašeho řešení zkušenosti s cloudovou infrastrukturou. Zákazníci mají omezený přístup ke kriticky důležitým prostředkům. Nemusejí se obávat, že při jejich správě udělají chybu. 

Spravované aplikace vám umožňují vytvořit si se zákazníky dlouhodobý vztah. Vy určujete podmínky správy aplikace a všechny platby se zpracovávají prostřednictvím fakturace v Azure.

I když zákazníci nasadí tyto spravované aplikace do svého předplatného, nemusejí je spravovat, aktualizovat ani udržovat. Vy se staráte o to, aby všichni zákazníci používali schválené verze. Zákazníci si nepotřebují osvojovat znalosti jednotlivých aplikací, aby je mohli spravovat. Zákazníci automaticky získávají aktualizace aplikací, aniž by si museli dělat starosti s řešením potíží a diagnostikou problémů s aplikacemi. 

Týmům IT umožňují spravované aplikace nabízet uživatelům v rámci organizace předem schválená řešení. Vy zajišťujete, aby byla tato řešení v souladu se standardy organizace.

## <a name="types-of-managed-applications"></a>Typy spravovaných aplikací

Spravovanou aplikaci můžete publikovat externě nebo interně.

![Interní nebo externí publikování](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Katalog služeb

Katalog služeb je interní katalog schválených řešení určených pro uživatele v rámci organizace. Katalog slouží k zajištění dodržování určitých standardů organizace při poskytování řešení pro organizaci. Zaměstnanci najdou v katalogu bohatou nabídku aplikací doporučených a schválených jejich oddělením IT. Zobrazují se jim spravované aplikace, které s nimi sdílejí jiní lidé z jejich organizace.

Informace o publikování spravované aplikace v katalogu služeb najdete v článku [Vytvoření aplikace katalogu služeb](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Dodavatelé, kteří si chtějí za své služby účtovat poplatky, můžou zpřístupnit spravovanou aplikaci prostřednictvím Azure Marketplace. Jakmile dodavatel publikuje aplikaci, bude dostupná i uživatelům mimo organizaci. Díky tomuto přístupu můžou poskytovatelé spravovaných služeb, nezávislí dodavatelé softwaru a integrátoři systémů nabízet svá řešení všem zákazníkům Azure.

Informace o publikování spravovaných aplikací do Marketplace najdete v tématu [Vytvoření aplikace Marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Skupiny prostředků pro spravované aplikace

Prostředky pro spravované aplikace se většinou nacházejí ve dvou skupinách prostředků. Jednu skupinu prostředků spravuje zákazník a druhou vydavatel. Při definování spravované aplikace určí vydavatel úrovně přístupu. Omezení přístupu pro [operace dat](../role-based-access-control/role-definitions.md) se momentálně nepodporuje pro všechny poskytovatele dat v Azure.

Následující obrázek znázorňuje situaci, kdy vydavatel požaduje pro spravovanou skupinu prostředků roli vlastníka. Pro zákazníka nastavil vydavatel u této skupiny prostředků zámek jen pro čtení. Zámek se nevztahuje na identity vydavatelů s uděleným přístupem ke spravované skupině prostředků.

![Přístup ke skupině prostředků](./media/overview/access.png)

### <a name="application-resource-group"></a>Skupina prostředků aplikace

Tato skupina prostředků obsahuje instanci spravované aplikace. Tato skupina prostředků smí obsahovat jenom jeden prostředek. Typ prostředku spravované aplikace je **Microsoft.Solutions/applications**.

Zákazník má k této skupině prostředků plný přístup a používá ji ke správě životního cyklu spravované aplikace.

### <a name="managed-resource-group"></a>Spravovaná skupina prostředků

Tato skupina prostředků obsahuje všechny prostředky, které spravovaná aplikace potřebuje. Tato skupina prostředků třeba obsahuje virtuální počítače, účty úložiště a virtuální sítě pro dané řešení. Zákazník má k této skupině prostředků omezený přístup, protože nespravuje jednotlivé prostředky spravované aplikace. Přístup vydavatele k této skupině prostředků odpovídá roli stanovené v definici spravované aplikace. Vydavatel si může pro tuto skupinu prostředků vyžádat třeba roli vlastníka nebo přispěvatele.

Když zákazník spravovanou aplikaci odstraní, dojde i k odstranění spravované skupiny prostředků.

## <a name="next-steps"></a>Další kroky

* Úvod do definování a nasazení spravované aplikace najdete v článku [Vytvoření a nasazení spravované aplikace Azure pomocí rozhraní příkazového řádku Azure](managed-apps-quickstart-cli.md).
* Informace o publikování interní aplikace najdete v článku [Vytvoření aplikace katalogu služeb](publish-service-catalog-app.md).
* Informace o publikování spravovaných aplikací do Marketplace najdete v tématu [Vytvoření aplikace Marketplace](publish-marketplace-app.md).
