---
title: Přehled spravovaných aplikací
description: V této části najdete popis konceptů Azure Managed Applications, které poskytují cloudová řešení, která zákazníkům umožňují nasazení a provozovat.
author: tfitzmac
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 65b8c21251f092311150a50480f5abecb1ad7c17
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434562"
---
# <a name="azure-managed-applications-overview"></a>Přehled spravovaných aplikací Azure

Spravované aplikace Azure umožňují nabízet cloudová řešení, která můžou spotřebitelé snadno nasadit a spravovat. Vy implementujete infrastrukturu a poskytujete průběžnou podporu. Pokud chcete zpřístupnit spravovanou aplikaci všem zákazníkům, publikujte ji v Azure Marketplace. Pokud ji chcete zpřístupnit jenom uživatelům ve vaší organizaci, publikujte ji do interního katalogu. 

Spravovaná aplikace se podobá šabloně řešení v Marketplace, ale s jedním zásadním rozdílem. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení.

> [!NOTE]
> Dříve byla dokumentace pro vlastní zprostředkovatele Azure zahrnutá v dokumentaci pro spravované aplikace. Tato dokumentace byla přesunuta. Nyní si přečtěte téma [Custom Providers pro Azure](../custom-providers/overview.md).

## <a name="advantages-of-managed-applications"></a>Výhody spravovaných aplikací

Spravované aplikace usnadňují zákazníkům používání vašich řešení. Zákazníci díky tomu nepotřebují mít k používání vašeho řešení zkušenosti s cloudovou infrastrukturou. Zákazníci mají omezený přístup k zásadně důležitým prostředkům a nemusejí se obávat, že při jejich správě udělají chybu. 

Spravované aplikace vám umožňují vytvořit si se zákazníky dlouhodobý vztah. Vy určujete podmínky správy aplikace a všechny platby se zpracovávají prostřednictvím fakturace v Azure.

I když zákazníci nasadí tyto spravované aplikace do svého předplatného, nemusejí je spravovat, aktualizovat ani udržovat. Vy se staráte o to, aby všichni zákazníci používali schválené verze. Zákazníci si nepotřebují osvojovat znalosti jednotlivých aplikací, aby je mohli spravovat. Zákazníci automaticky získávají aktualizace aplikací, aniž by si museli dělat starosti s řešením potíží a diagnostikou problémů s aplikacemi. 

Týmům IT umožňují spravované aplikace nabízet uživatelům v rámci organizace předem schválená řešení. Máte jistotu, že jsou tato řešení v souladu se standardy organizace.

Spravované aplikace podporují [spravované identity prostředků Azure](./publish-managed-identity.md).

## <a name="types-of-managed-applications"></a>Typy spravovaných aplikací

Spravovanou aplikaci můžete publikovat externě nebo interně.

![Interní nebo externí publikování](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Katalog služeb

Katalog služeb je interní katalog schválených řešení určených pro uživatele v rámci organizace. Katalog slouží k dodržování určitých standardů organizace v nabídce řešení pro organizaci. Zaměstnanci v katalogu snadno najdou aplikace doporučené a schválené oddělením IT. Zobrazují se jim spravované aplikace, které s nimi sdílejí jiní lidé z jejich organizace.

Informace o publikování spravované aplikace v katalogu služeb najdete v článku [Vytvoření aplikace katalogu služeb](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Dodavatelé, kteří si chtějí za své služby účtovat poplatky, můžou zpřístupnit spravovanou aplikaci prostřednictvím Azure Marketplace. Jakmile dodavatel publikuje aplikaci, bude dostupná i uživatelům mimo organizaci. Díky tomuto přístupu můžou poskytovatelé spravovaných služeb, nezávislí dodavatelé softwaru a integrátoři systémů nabízet svá řešení všem zákazníkům Azure.

Informace o publikování spravovaných aplikací do Marketplace najdete v tématu [Vytvoření aplikace Marketplace](../../marketplace/create-new-azure-apps-offer.md).

## <a name="resource-groups-for-managed-applications"></a>Skupiny prostředků pro spravované aplikace

Prostředky pro spravovanou aplikaci jsou obvykle ve dvou skupinách prostředků. Jednu skupinu prostředků spravuje zákazník a druhou vydavatel. Při definování spravované aplikace určí vydavatel úrovně přístupu. Vydavatel může požádat o trvalé přiřazení role nebo [přístup za běhu](request-just-in-time-access.md) pro přiřazení, které je omezené na časové období.

Omezení přístupu pro [operace dat](../../role-based-access-control/role-definitions.md) se momentálně nepodporuje pro všechny poskytovatele dat v Azure.

Následující obrázek znázorňuje situaci, kdy vydavatel požaduje pro spravovanou skupinu prostředků roli vlastníka. Pro zákazníka nastavil vydavatel u této skupiny prostředků zámek jen pro čtení. Zámek se nevztahuje na identity vydavatelů s uděleným přístupem ke spravované skupině prostředků.

![Přístup ke skupině prostředků](./media/overview/access.png)

### <a name="application-resource-group"></a>Skupina prostředků aplikace

Tato skupina prostředků obsahuje instanci spravované aplikace. Tato skupina prostředků smí obsahovat jenom jeden prostředek. Typ prostředku spravované aplikace je **Microsoft.Solutions/applications**.

Zákazník má k této skupině prostředků plný přístup a používá ji ke správě životního cyklu spravované aplikace.

### <a name="managed-resource-group"></a>Spravovaná skupina prostředků

Tato skupina prostředků obsahuje všechny prostředky, které spravovaná aplikace potřebuje. Tato skupina prostředků třeba obsahuje virtuální počítače, účty úložiště a virtuální sítě pro dané řešení. Zákazník má k této skupině prostředků omezený přístup, protože nespravuje jednotlivé prostředky spravované aplikace. Přístup vydavatele k této skupině prostředků odpovídá roli stanovené v definici spravované aplikace. Vydavatel si může pro tuto skupinu prostředků vyžádat třeba roli vlastníka nebo přispěvatele. Přístup je buď trvalý, nebo omezený na určitou dobu.

Při publikování [spravované aplikace na webu Marketplace](../../marketplace/create-new-azure-apps-offer.md)může vydavatel zákazníkům udělit možnost provádět určité akce s prostředky ve spravované skupině prostředků. Vydavatel může například určit, že uživatelé můžou restartovat virtuální počítače. Všechny ostatní akce nad akcí čtení jsou stále odmítnuty. Změny prostředků ve spravované skupině prostředků příjemcem s udělenými akcemi podléhají [Azure Policy](../../governance/policy/overview.md) přiřazení v rámci oboru tenanta klienta, aby zahrnovali spravovanou skupinu prostředků.

Když zákazník spravovanou aplikaci odstraní, dojde i k odstranění spravované skupiny prostředků.

## <a name="azure-policy"></a>Azure Policy

Můžete použít [Azure Policy](../../governance/policy/overview.md) k auditování spravované aplikace. Můžete použít definice zásad a zajistit, aby nasazené instance spravované aplikace splňovaly požadavky na data a zabezpečení. Pokud aplikace pracuje s citlivými daty, nezapomeňte vyhodnotit, jak by se měla tato data chránit. Například pokud vaše aplikace komunikuje s daty z Microsoft 365, použijte definici zásady, abyste se ujistili, že je povolené šifrování dat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o výhodách používání spravovaných aplikací. Přejděte na další článek, který se zabývá vytvořením definice spravované aplikace.

> [!div class="nextstepaction"]
> [Rychlý start: Publikování definice aplikace spravované Azure](publish-service-catalog-app.md)