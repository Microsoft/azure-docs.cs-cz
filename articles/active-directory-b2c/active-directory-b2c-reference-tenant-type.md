---
title: Rezidence dat a dostupnost oblastí v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma o typech tenantů Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c0348dad507b118af542c1bbc47f6ceb88ae7be
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443048"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Rezidence dat a dostupnost oblastí
Dostupnost v oblastech a rezidence dat jsou dvě velmi odlišné koncepty, které se vztahují na Azure AD B2C odlišně od zbytku Azure. Tento článek vysvětluje rozdíly mezi tyto dva pojmy a porovnat, jak se vztahují na Azure a Azure AD B2C.

## <a name="summary"></a>Souhrn
Azure AD B2C je **obecně dostupný po celém světě** s možností pro **rezidenci dat v USA nebo Evropa**.

## <a name="concepts"></a>Koncepty
* **Dostupnost v oblastech** odkazuje na kde je k dispozici pro použití služby.
* **Rezidence dat** odkazuje na ukládat data uživatele.

## <a name="region-availability"></a>Dostupnost v oblastech
Azure AD B2C je dostupná po celém světě prostřednictvím veřejného cloudu Azure. 

Tím se liší od modelu postupujte většina jiných služeb Azure, které spárovat s rezidencí dat dostupnosti. Příklady tohoto v obou Azure se můžete podívat [produkty k dispozici v oblasti](https://azure.microsoft.com/regions/services/) stránky a [Active Directory B2C pomocí cenové kalkulačky](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezidence dat
Azure AD B2C ukládá uživatelská data v USA nebo Evropa.

Rezidence dat závisí na které země/oblast je vybrána při [vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md).

![Snímek obrazovky okna tenant ve verzi preview](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Data se nachází ve Spojených státech amerických pro následujících zemích nebo oblastech:

> USA, Kanadě, Kostarika, Dominikánská republika, El Salvador, Guatemala, Mexiko, Panama, Portoriko a Trinidad a Tobago

Data se nachází v Evropě pro následujících zemích nebo oblastech:

> Alžírsko, Rakousko, Ázerbájdžán, Bahrajn, Bělorusko, Belgie, Bulharsko, Chorvatsko, Kypr, Česká republika, Dánsko, Egypt, Estonsko, Finsko, Francie, Německo, Řecko, Maďarsko, Island, Irsko, Izrael, Itálie, Jordánsko, Kazachstán, Keňa, Kuvajt, Lotyšska, Libanon Lichtenštejnsko, Lituania, Lucembursko, Makedonie – bývalá republika Jugoslávie, Malta, Černá Hora, Maroko, Nizozemsko, Nigérie, Norsko, Omán, Pákistán, Polsko, Portugalsko, Katar, Rumunsko, Rusko, Saúdská Arábie, Srbsko, Slovensko, Slovinsko, Jižní Afrika, Španělsko, Švédsko, Švýcarsko, Tunisko, Turecko, Ukrajina, Spojené arabské emiráty a Spojeného království.

Zbývajících zemích nebo oblastech se v současnosti se přidávají do seznamu.  Prozatím se můžete stále použít Azure AD B2C výběrem některé z výše uvedených zemích nebo oblastech.

> Afghánistán, Argentina, Austrálie, Brazílie, Chile, Kolumbie, Ekvádor, Hongkong – zvláštní administrativní oblast, Indie, Indonésie, Irák, Japonsko, Koreji, Malajsie, Nový Zéland, Paraguay, Peru, Filipíny, Singapur, Srí Lanka, Tchaj-wan, Thajsko, Uruguay a Venezuela.

## <a name="preview-tenant"></a>Tenant ve verzi Preview
Pokud jste vytvořili tenanta B2C během období preview Azure AD B2C, je pravděpodobné, který vaše **Tenanta typ** říká **tenant ve verzi Preview**. Pokud je to tento případ, musíte použít vašeho tenanta jenom pro účely vývoje a testování a nikoli pro produkční aplikace.

> [!IMPORTANT]
> Neexistuje žádná cesta migrace z tenanta B2C ve verzi preview na tenanta B2C produkčním měřítku. Všimněte si, že jsou známy problémy odstranit tenanta B2C ve verzi preview a znovu vytvoříte tenanta B2C produkčním měřítku se stejným názvem domény. Je nutné vytvořit tenanta B2C produkčním měřítku s jiným názvem domény.


![Snímek obrazovky okna tenant ve verzi preview](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
