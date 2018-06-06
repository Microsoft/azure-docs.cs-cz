---
title: Oblast sídle dostupnosti & dat v Azure Active Directory B2C | Microsoft Docs
description: Téma na typy klientů, které Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/10/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: add7c49d8efaec0fddf057c454b4f01aec5df1ce
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709593"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Dostupnost & data sídle oblast
Dostupnost v oblastech a sídla dat jsou dvě velmi různých koncepty, které jinak týkají Azure AD B2C, od zbytku Azure. Tento článek se popisují rozdíly mezi těmito dvěma konceptů a porovnání, jak se vztahují na Azure oproti využívání Azure AD B2C.

## <a name="summary"></a>Souhrn
Azure AD B2C je **všeobecně dostupná po celém světě** s možností pro **sídle data ve Spojených státech amerických nebo Evropa**.

## <a name="concepts"></a>Koncepty
* **Dostupnost v oblastech** odkazuje na to, kde je k dispozici pro použití služby.
* **Data sídle** odkazuje na kterém jsou uložená data uživatele.

## <a name="region-availability"></a>Dostupnost v oblastech
Azure AD B2C je k dispozici po celém světě prostřednictvím veřejného cloudu Azure. 

To se liší od modelu většině ostatních služeb Azure postupujte podle kroků, které spojte dostupnosti s sídle data. Zobrazí se tato příklady v obou Azure [produkty dostupné podle oblasti](https://azure.microsoft.com/regions/services/) stránky a [cenové kalkulačky Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezidence dat
Azure AD B2C data uživatelů uloží ve Spojených státech amerických nebo Evropa.

Sídlo dat je určen podle zemi nebo region je vybraná při [vytvoření klienta Azure AD B2C](active-directory-b2c-get-started.md).

![Snímek obrazovky preview klienta](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Data se nachází ve Spojených státech amerických pro následující země nebo oblasti:

> Spojené státy, Kanada, Kostarika, Dominikánská republika, El Salvador, Kostarika, Mexico, Panama, Portoriko a Trinidad a Tobago

Data se nachází v Evropě pro následující země nebo oblasti:

> Alžírsko, Rakousko, Ázerbájdžán, Bahrajn, Běloruska, Belgie, Bulharsko, Chorvatsko, Kypru, Česká republika, Dánsko, Egypta, Estonska, Finsko, Francie, Německo, Řecko, Maďarska, Island, Irská, Izrael, Itálie, Jordánsko, Kazachstán, Keni, Kuvajt, Lotyšska, Libanon, Lichtenštejnsko, Lituania, Lucembursko, Makedonie FYRO, Malty, Černá Hora, Maroko, Nizozemsko, Nigérie, Norsko, Omán, Pákistánu, Polsko, Portugalsko, Katar, Rumunska, Ruska, Saúdská Arábie, Srbsko, Slovenska, Slovinsko, Jihoafrická republika, Španělsko, Švédsko, Švýcarska, Tunisko, Turecko, Ukrajina, Spojené arabské emiráty a Spojené království.

Zbývající zemích probíhá se přidává do seznamu.  Teď stále můžete Azure AD B2C výběrem některé z výše uvedených zemí/oblastí.

> Afghánistán, Argentiny, Austrálie, Brazílie, základě, Kolumbie, Ekvádor, Hongkong – zvláštní správní oblast ČLR, Indie, Indonésie, Irák, Japonsko, Korejská, Malajsie, Nový Zéland, Paraguay, Peru, Filipíny, Singapur, Srí Lanka, Tchaj-wan, Thajska, Uruguayského a Venezuela.

## <a name="preview-tenant"></a>Tenant ve verzi Preview
Pokud jste vytvořili klienta B2C během období preview Azure AD B2C, je pravděpodobné, který vaše **klienta typu** uvádí **Preview klienta**. Pokud je to tento případ, používejte pouze pro účely vývoje a testování a ne pro produkční aplikace vašeho klienta.

> [!IMPORTANT]
> Neexistuje žádné cesty migrace z náhled klienta B2C na produkční škálování klienta B2C. Všimněte si, že existují známé problémy při odstranit preview B2C klienta a znovu vytvořit klienta B2C produkční škálování se stejným názvem domény. Je nutné vytvořit klienta B2C produkční škálování s jiným názvem domény.


![Snímek obrazovky preview klienta](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
