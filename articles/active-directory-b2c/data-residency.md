---
title: Dostupnost regionu a rezidence dat
titleSuffix: Azure AD B2C
description: Dostupnost oblasti, rezidence dat a informace o klientech Azure Active Directory B2C ve verzi Preview.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188844"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Dostupnost oblasti & rezidence dat

Dostupnost oblasti a rezidence dat jsou dva velmi odlišné koncepty, které platí odlišně pro Azure AD B2C od zbytku Azure. Tento článek vysvětluje rozdíly mezi těmito dvěma koncepty a porovnává, jak se vztahují na Azure versus Azure AD B2C.

Azure AD B2C je **obecně k dispozici po celém světě** s možností **rezidence dat** ve Spojených **státech, Evropě nebo Asii a Tichomoří**.

[Dostupnost oblasti](#region-availability) označuje, kde je služba k dispozici pro použití.

[Rezidence dat](#data-residency) označuje, kde jsou uložena uživatelská data.

## <a name="region-availability"></a>Dostupnost v oblastech

Azure AD B2C je dostupné po celém světě prostřednictvím veřejného cloudu Azure.

To se liší od modelu následuje většina ostatních služeb Azure, které obvykle pár *dostupnost* s *rezidencí dat*. Příklady můžete zobrazit na stránce [Azure Products Available by Region](https://azure.microsoft.com/regions/services/) a v cenové [kalkulačce služby Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezidence dat

Azure AD B2C ukládá uživatelská data ve Spojených státech, Evropě nebo v oblasti Asie a Tichomoří.

Rezidence dat je určena zemí nebo oblastí, kterou vyberete při [vytváření klienta Azure AD B2C](tutorial-create-tenant.md):

![Snímek obrazovky s tenantem náhledu](./media/data-residency/data-residency-b2c-tenant.png)

Data jsou uložena **ve Spojených státech pro** následující země nebo oblasti:

> Spojené státy, Kanada, Kostarika, Dominikánská republika, Salvador, Guatemala, Mexiko, Panama, Portoriko a Trinidad & Tobago

Údaje se nacházejí v **Evropě** pro tyto země nebo oblasti:

> Alžírsko, Rakousko, Ázerbájdžán, Bahrajn, Bělorusko, Belgie, Bulharsko, Chorvatsko, Kypr, Česká republika, Dánsko, Egypt, Estonsko, Finsko, Francie, Německo, Řecko, Maďarsko, Island, Irsko, Izrael, Itálie, Jordánsko, Kazachstán, Keňa, Kuvajt, Lotyšsko, Libanon, Lichtenštejnsko, Litva, Lucembursko, Severní Makedonie, Malta, Černá Hora, Maroko, Nizozemí, Nigérie, Norsko, Omán, Pákistán, Polsko, Portugalsko, Katar, Rumunsko, Rusko, Saúdská Arábie, Srbsko, Slovensko, Slovinsko, Jihoafrická republika, Španělsko, Švédsko, Švýcarsko, Tunisko, Turecko, Ukrajina, Spojené arabské emiráty a Spojené království.

Data jsou umístěna v **Asii a Tichomoří** pro následující země nebo oblasti:

> Afghánistán, Hongkong – zvláštní správní oblast, Indie, Indonésie, Japonsko, Korea, Malajsie, Filipíny, Singapur, Srí Lanka, Tchaj-wan a Thajsko.

Následující země nebo oblasti jsou právě přidávány do seznamu. Pro tuto chvíli můžete stále používat Azure AD B2C výběrem některé z výše uvedených zemí nebo oblastí.

> Argentina, Austrálie, Brazílie, Chile, Kolumbie, Ekvádor, Irák, Nový Zéland, Paraguay, Peru, Uruguay a Venezuela.

## <a name="preview-tenant"></a>Náhled klienta

Pokud jste vytvořili klienta B2C během období náhledu Azure AD B2C, je pravděpodobné, že **váš typ klienta** říká, že **tenant preview tenanta**.

Pokud se jedná o tento případ, musíte použít svého tenanta POUZE pro účely vývoje a testování. Nepoužívejte předpremiéra pro produkční aplikace.

**Neexistuje žádná cesta migrace** z klienta B2C náhledu na klienta B2C v měřítku výroby. Je nutné vytvořit nového klienta B2C pro vaše produkční aplikace.

Existují známé problémy při odstranění klienta B2C náhledu a vytvoření klienta B2C v měřítku výroby se stejným názvem domény. *Je nutné vytvořit klienta B2C v měřítku výroby s jiným názvem domény*.

![Snímek obrazovky s tenantem náhledu](./media/data-residency/preview-b2c-tenant.png)