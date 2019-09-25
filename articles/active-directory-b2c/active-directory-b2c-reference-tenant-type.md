---
title: Dostupnost & dat v oblasti v Azure Active Directory B2C
description: Téma na typech klientů Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bb7a7ef2aabd7f93531d9afa662d7e582e05550
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273569"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Rezidence dat a dostupnost oblastí

Dostupnost oblasti a data zajistěte ze dvou velmi odlišných konceptů, které se liší Azure AD B2C ze zbytku Azure. Tento článek vysvětluje rozdíly mezi těmito dvěma koncepty a porovnává, jak se vztahují k Azure oproti Azure AD B2C.

Azure AD B2C je **všeobecně dostupná po celém světě** s možností pro **data** zaregistrovaná v **USA, Evropě nebo Asie a Tichomoří**.

[Dostupnost oblasti](#region-availability) odkazuje na to, kde je služba k dispozici pro použití.

[Data](#data-residency) , která jsou uložená v datech, se označují jako data uživatelů.

## <a name="region-availability"></a>Dostupnost v oblastech

Azure AD B2C je k dispozici po celém světě prostřednictvím veřejného cloudu Azure.

To se liší od modelu následovaného většinou ostatních služeb Azure, které obvykle mají k *dispozici více dostupnosti* s *datovým držitelem*. Příklady najdete na stránce [dostupné v produktech](https://azure.microsoft.com/regions/services/) Azure v oblasti a v [cenové kalkulačkě Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezidence dat

Azure AD B2C ukládá data uživatelů buď v USA, Evropě, nebo v oblasti Asie a Tichomoří.

Data zasídlí závisí na zemi nebo oblasti, kterou vyberete při [vytváření tenanta Azure AD B2C](active-directory-b2c-get-started.md):

![Snímek obrazovky s klientem verze Preview](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Data se nachází v **USA** v následujících zemích nebo oblastech:

> USA, Kanada, Kostarika, Dominikánská republika, Salvador, Guatemala, Mexiko, Panama, Portoriko a Trinidad & Tobago

Data se nachází v **Evropě** v následujících zemích nebo oblastech:

> Alžírsko, Rakousko, Ázerbájdžán, Bahrajn, Bělorusko, Belgie, Bulharsko, Chorvatsko, Kypr, Česká republika, Dánsko, Egypt, Estonsko, Finsko, Francie, Německo, Řecko, Maďarsko, Irsko, Island, Itálie, Libanon, Lotyšsko Lichtenštejnsko, Lotyšsko, Lucembursko, Severní Makedonie, Malta, Černá Hora, Maroko, Nizozemsko, Indie, Norsko, Rumunsko, Portugalsko, Polsko, Portugalsko, Indie, Rumunsko, Saúdská Arábie, Srbsko, Švédsko, Slovinsko, Jižní Afrika, Španělsko, Švédsko, Slovensko Švýcarsko, Tunisko, Turecko, Ukrajina, Spojené arabské emiráty a Spojené království.

Data se nachází v **Asie a Tichomoří** v následujících zemích nebo oblastech:

> Afghánistán, Hongkong – zvláštní správní oblast, Indie, Indonésie, Japonsko, Indie, Malajsie, Singapur, Srí Lanka, Tchaj-wan a Thajsko.

Do seznamu se přidávají následující země nebo oblasti. Prozatím můžete dál používat Azure AD B2C, a to tak, že vybíráte některou z výše uvedených zemí nebo oblastí.

> Argentina, Austrálie, Brazílie, Chile, Kolumbie, Ekvádor, Irák, Nový Zéland, Paraguay, Peru, Uruguay a Venezuela.

## <a name="preview-tenant"></a>Tenant ve verzi Preview

Pokud jste vytvořili tenanta B2C během období Preview Azure AD B2C's, je pravděpodobný, že váš **typ tenanta** uvádí **tenanta verze Preview**.

V takovém případě je nutné použít svého tenanta pouze pro účely vývoje a testování. Nepoužívejte klienta Preview pro produkční aplikace.

Neexistuje **žádná cesta migrace** z klienta ve verzi Preview B2C do tenanta B2C v produkčním měřítku. Pro produkční aplikace musíte vytvořit nového tenanta B2C.

Při odstraňování tenanta B2C ve verzi Preview a vytvoření tenanta B2C ve výrobním měřítku se stejným názvem domény se vyskytly známé problémy. Je *nutné vytvořit klienta B2C ve výrobním měřítku s jiným názvem domény*.

![Snímek obrazovky s klientem verze Preview](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)