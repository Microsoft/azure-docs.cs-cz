---
title: Dostupnost oblasti a zasídlí dat
titleSuffix: Azure AD B2C
description: Dostupnost v oblasti, data a sídlo a informace o klientech Azure Active Directory B2C ve verzi Preview.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 46d8fb33c59fc5f0b6d844831e5ee1c937654afb
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193798"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: dostupnost oblasti & data zasídlí

Dostupnost oblasti a data zajistěte ze dvou velmi odlišných konceptů, které se liší Azure AD B2C ze zbytku Azure. Tento článek vysvětluje rozdíly mezi těmito dvěma koncepty a porovnává, jak se vztahují k Azure oproti Azure AD B2C.

Azure AD B2C je **všeobecně dostupná po celém světě** s možností pro **data** zaregistrovaná v **USA, Evropě nebo Asie a Tichomoří**.

[Dostupnost oblasti](#region-availability) odkazuje na to, kde je služba k dispozici pro použití.

[Data](#data-residency) , která jsou uložená v datech, se označují jako data uživatelů.

## <a name="region-availability"></a>Dostupnost v oblastech

Azure AD B2C je k dispozici po celém světě prostřednictvím veřejného cloudu Azure.

To se liší od modelu následovaného většinou ostatních služeb Azure, které obvykle mají k *dispozici více dostupnosti* s *datovým držitelem*. Příklady najdete na stránce [dostupné v produktech](https://azure.microsoft.com/regions/services/) Azure v oblasti a v [cenové kalkulačkě Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezidence dat

Azure AD B2C ukládá data uživatelů buď v USA, Evropě, nebo v oblasti Asie a Tichomoří.

Data zasídlí závisí na zemi nebo oblasti, kterou vyberete při [vytváření tenanta Azure AD B2C](tutorial-create-tenant.md):

![Snímek obrazovky s klientem verze Preview](./media/data-residency/data-residency-b2c-tenant.png)

Data se nachází v **USA** v následujících zemích nebo oblastech:

> USA, Kanada, Kostarika, Dominikánská republika, Salvador, Guatemala, Mexiko, Panama, Portoriko a Trinidad & Tobago

Data se nachází v **Evropě** v následujících zemích nebo oblastech:

> Alžírsko, Rakousko, Ázerbájdžán, Bahrajn, Bělorusko, Belgie, Bulharsko, Chorvatsko, Kypr, Česká republika, Dánsko, Egypt, Estonsko, Finsko, Francie, Německo, Řecko, Maďarsko, Irsko, Itálie, Island Kazachstán, Keňa, Kuvajt, Lotyšsko, Libanon, Rumunsko, Lotyšsko, Lucembursko, Severní Makedonie, Malta, Černá Hora, Maroko, Nizozemsko, Indie, Norsko, Rumunsko, Srbsko, Portugalsko, Portugalsko, Indie, Slovinsko, Španělsko, Švédsko, Švýcarsko, Egypt, Spojené arabské emiráty a Spojené království.

Data se nachází v **Asie a Tichomoří** v následujících zemích nebo oblastech:

> Afghánistán, Hongkong – zvláštní správní oblast, Indie, Indonésie, Japonsko, Indie, Malajsie, Singapur, Srí Lanka, Tchaj-wan a Thajsko.

Do seznamu se přidávají následující země nebo oblasti. Prozatím můžete dál používat Azure AD B2C, a to tak, že vybíráte některou z výše uvedených zemí nebo oblastí.

> Argentina, Austrálie, Brazílie, Chile, Kolumbie, Ekvádor, Irák, Nový Zéland, Paraguay, Peru, Uruguay a Venezuela.

## <a name="preview-tenant"></a>Klient verze Preview

Pokud jste vytvořili tenanta B2C během období Preview Azure AD B2C's, je pravděpodobný, že váš **typ tenanta** uvádí **tenanta verze Preview**.

V takovém případě je nutné použít svého tenanta pouze pro účely vývoje a testování. Nepoužívejte klienta Preview pro produkční aplikace.

Neexistuje **žádná cesta migrace** z klienta ve verzi Preview B2C do tenanta B2C v produkčním měřítku. Pro produkční aplikace musíte vytvořit nového tenanta B2C.

Při odstraňování tenanta B2C ve verzi Preview a vytvoření tenanta B2C ve výrobním měřítku se stejným názvem domény se vyskytly známé problémy. Je *nutné vytvořit klienta B2C ve výrobním měřítku s jiným názvem domény*.

![Snímek obrazovky s klientem verze Preview](./media/data-residency/preview-b2c-tenant.png)