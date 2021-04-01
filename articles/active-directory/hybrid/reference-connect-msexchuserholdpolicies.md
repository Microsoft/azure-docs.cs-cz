---
title: 'Azure AD Connect: msExchUserHoldPolicies a cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Toto téma popisuje chování atributů atributů msExchUserHoldPolicies a cloudMsExchUserHoldPolicies.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861761"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect – msExchUserHoldPolicies a cloudMsExchUserHoldPolicies
Následující referenční dokument popisuje tyto atributy používané systémem Exchange a správný způsob, jak upravit výchozí pravidla synchronizace.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Co jsou msExchUserHoldPolicies a cloudMsExchUserHoldPolicies?
Pro Exchange [Server jsou k dispozici dva](/Exchange/policy-and-compliance/holds/holds) typy blokovaných akcí: přijetí a In-Place blokování. Pokud je povoleno blokování sporů, jsou všechny položky poštovních schránek blokovány.  In-Place blokování se používá k zachování pouze těch položek, které splňují kritéria vyhledávacího dotazu, který jste definovali pomocí nástroje In-Place eDiscovery.

Atributy MsExchUserHoldPolcies a cloudMsExchUserHoldPolicies umožňují místní službě AD a službě Azure AD určit, kteří uživatelé se budou držet v závislosti na tom, jestli používají místní Exchange nebo Exchange on-line.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>průběh synchronizace msExchUserHoldPolicies
Ve výchozím nastavení se MsExchUserHoldPolcies synchronizuje Azure AD Connect přímo k atributu msExchUserHoldPolicies v úložišti metaverse a potom k atributu msExchUserHoldPolicies v Azure AD.

Tok je popsán v následujících tabulkách:

Příchozí z místní služby Active Directory:

|Atribut služby Active Directory|Název atributu|Typ toku|Atribut úložiště metaverse|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Místní služby Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|V nástroji ze služby AD-User Exchange|

Odchozí do Azure AD:

|Atribut úložiště metaverse|Název atributu|Typ toku|Atribut Azure AD|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Do AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>průběh synchronizace cloudMsExchUserHoldPolicies
Ve výchozím nastavení jsou cloudMsExchUserHoldPolicies synchronizovány pomocí Azure AD Connect přímo do atributu cloudMsExchUserHoldPolicies v úložišti Metaverse. Pokud msExchUserHoldPolicies v úložišti Metaverse není null, atribut v toku se přesměruje do služby Active Directory.

Tok je popsán v následujících tabulkách:

Příchozí z Azure AD:

|Atribut služby Active Directory|Název atributu|Typ toku|Atribut úložiště metaverse|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Místní služby Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|V nástroji AAD – uživatel Exchange|

Odchozí do místní služby Active Directory:

|Atribut úložiště metaverse|Název atributu|Typ toku|Atribut Azure AD|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Z AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informace o chování atributu
MsExchangeUserHoldPolicies jsou jedním z atributů Authority.  U objektu (v tomto případě uživatelského objektu) je možné nastavit atribut s jednou autoritou v místním adresáři nebo v adresáři cloudu.  Pravidla začátku úřadu určují, že pokud je atribut synchronizovaný z místního prostředí, Azure AD nebude moct tento atribut aktualizovat.

Pokud chcete uživatelům dovolit nastavit zásadu blokování u objektu uživatele v cloudu, použije se atribut cloudMSExchangeUserHoldPolicies. Tento atribut se používá, protože Azure AD nemůže nastavit msExchangeUserHoldPolicies přímo na základě pravidel, která jsou vysvětlená výše.  Tento atribut se pak synchronizuje zpátky do místního adresáře, pokud msExchangeUserHoldPolicies nemá hodnotu null a nahradí aktuální hodnotu msExchangeUserHoldPolicies.

Za určitých okolností, například pokud byly oba změněny místně i v Azure, může to způsobit určité problémy.  

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
