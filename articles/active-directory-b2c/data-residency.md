---
title: Rezidence dat a dostupnost oblastí
titleSuffix: Azure AD B2C
description: Dostupnost v oblasti, data a sídlo a informace o klientech Azure Active Directory B2C ve verzi Preview.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309685"
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

![Snímek obrazovky s formulářem pro vytvoření tenanta s volbou země nebo oblast](./media/data-residency/data-residency-b2c-tenant.png)

Data se nachází v **USA** v následujících zemích nebo oblastech:

> USA (US), Kanada (CA), Kostarika (CR), Dominikánská republika (DO), Salvador (SV), Guatemala (GT), Mexiko (MX), Panama (PA), Portoriko (PR) a Trinidad & Tobago (TT)

Data se nachází v **Evropě** v následujících zemích nebo oblastech:

> Alžírsko (DZ), Ázerbájdžán (AZ), Bahrajn (BH), Bělorusko (BY), Belgie (), Bulharsko (BG), Chorvatsko (HR), Kypr (CY), Česká republika (a Dánsko), Egypt (např.), Estonsko (EE), Finsko (FT), Francie (FR), Čeština (FR), Německo (DE), Řecko (GR), Maďarsko (HU), Island (IS), Irsko (IE), Izrael (IT), Jordánsko (JO), Kazachstán (KZ), Keňa (), Kuvajt (KW), Lotyšsko (LV), Libanon (9,1), Lotyšsko (a), Litva (LT) , Lucembursko (LU), Severní Makedonie (ML), Malta (MT), Černá Hora (já), Maroko (MA), Nizozemsko (NL), Nigérie (NG), Norsko (ne), Omán (OM), Pákistán (PK), Polsko (PT), Portugalsko (QA), Rumunsko (RO), Rumunsko (RO); Rusko (RU), Saúdská Arábie (SA), Srbsko (RS), Slovensko (SK), Slovinsko (Svatý), Jižní Afrika (ZA), Španělsko (ES), Švýcarsko (SE), Švýcarsko (CH), Tunisko (TR), Turecko (TR), Ukrajina (UA), Spojené arabské emiráty (AE) a Spojené království (GB)

Data se nachází v **Asie a Tichomoří** v následujících zemích nebo oblastech:

> Afghánistán (AF), Hongkong – zvláštní správní oblast (HK), Indie (IN), Indonésie (ID), Japonsko (JP), Korea (KR), Malajsie (MY), Filipíny (PH), Singapur (SG), Srí Lanka (LK), Tchaj-wan (TW) a Thajsko (TH).

Do seznamu se přidávají následující země nebo oblasti. Prozatím můžete dál používat Azure AD B2C, a to tak, že vybíráte některou z výše uvedených zemí nebo oblastí.

> Argentina, Austrálie, Brazílie, Chile, Kolumbie, Ekvádor, Irák, Nový Zéland, Paraguay, Peru, Uruguay a Venezuela.

## <a name="remote-profile-solution"></a>Řešení vzdáleného profilu

Díky Azure AD B2C [vlastní zásady](custom-policy-overview.md)můžete integrovat se [službami rozhraní API RESTful](custom-policy-rest-api-intro.md), které umožňují ukládat a číst profily uživatelů ze vzdálené databáze (jako je marketingová databáze, systém CRM nebo libovolná obchodní aplikace).  
- V průběhu registrace a úprav profilu Azure AD B2C zavolá vlastní REST API, aby zachoval profil uživatele do vzdáleného zdroje dat. Přihlašovací údaje uživatele jsou uloženy v adresáři Azure AD B2C. 
- Po přihlášení po ověření přihlašovacích údajů s místním nebo sociálním účtem Azure AD B2C vyvolá REST API, které pošle jedinečný identifikátor uživatele jako primární klíč uživatele (e-mailová adresa nebo identifikátor objectId uživatele). REST API čte data ze vzdálené databáze a vrací profil uživatele.  

Po registraci, úpravách profilu nebo přihlášení se Azure AD B2C zahrne profil uživatele do přístupového tokenu, který se vrátí do aplikace. Další informace najdete v [ukázkovém řešení vzdáleného profilu Azure AD B2C](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) v GitHubu.

## <a name="preview-tenant"></a>Klient verze Preview

Pokud jste vytvořili tenanta B2C během období Preview Azure AD B2C's, je pravděpodobný, že váš **typ tenanta** uvádí **tenanta verze Preview**.

V takovém případě je nutné použít svého tenanta pouze pro účely vývoje a testování. Nepoužívejte klienta Preview pro produkční aplikace.

Neexistuje **žádná cesta migrace** z klienta ve verzi Preview B2C do tenanta B2C v produkčním měřítku. Pro produkční aplikace musíte vytvořit nového tenanta B2C.

Při odstraňování tenanta B2C ve verzi Preview a vytvoření tenanta B2C ve výrobním měřítku se stejným názvem domény se vyskytly známé problémy. Je *nutné vytvořit klienta B2C ve výrobním měřítku s jiným názvem domény*.

![Snímek obrazovky typu tenanta jako tenant verze Preview](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>Další kroky

- [Vytvořte tenanta Azure AD B2C](tutorial-create-tenant.md).
