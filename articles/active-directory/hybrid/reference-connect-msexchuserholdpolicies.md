---
title: 'Azure AD Connect: msExchUserHoldPolicies a cloudMsExchUserHoldPolicies | Dokumenty společnosti Microsoft'
description: Toto téma popisuje chování atributů atributů atributů msExchUserHoldPolicies a cloudMsExchUserHoldPolicies.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213073"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect – msExchUserHoldPolicies a cloudMsExchUserHoldPolicies
Následující referenční dokument popisuje tyto atributy používané serverem Exchange a správný způsob úpravy výchozích pravidel synchronizace.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Co jsou msExchUserHoldPolicies a cloudMsExchUserHoldPolicies?
Pro Exchange Server jsou k dispozici dva typy [blokování:](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) blokování z důvodu soudních sporů a blokování na místě. Je-li povoleno blokování z důvodu soudních sporů, jsou všechny poštovní schránky pozastaveny.  Místní blokování se používá k zachování pouze těch položek, které splňují kritéria vyhledávacího dotazu, který jste definovali pomocí nástroje Místní eDiscovery.

Atributy MsExchUserHoldPolcies a cloudMsExchUserHoldPolicies umožňují místnímu službě AD a Azure AD určit, kteří uživatelé jsou pod blokováním v závislosti na tom, zda používají místní exchange nebo exchange on-line.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>tok synchronizace msExchUserHoldPolicies
Ve výchozím nastavení msExchUserHoldPolcies je synchronizován aslužbou Azure AD Connect přímo do atributu msExchUserHoldPolicies v metaverse a potom na atribut msExchUserHoldPolices v Azure AD

Následující tabulky popisují tok:

Příchozí z místní služby Active Directory:

|Atribut Active Directory|Název atributu|Typ toku|Metaverse, atribut|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Místní služby Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|V od AD - Výměna uživatelů|

Odchozí do Azure AD:

|Metaverse, atribut|Název atributu|Typ toku|Atribut Azure AD|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Ven na AAD - UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies tok synchronizace
Ve výchozím nastavení cloudMsExchUserHoldPolicies je synchronizován aSlužby Azure AD Connect přímo do cloudMsExchUserHoldPolicies atribut v metaverse. Potom pokud msExchUserHoldPolices není null v metaverse, atribut v teče do služby Active Directory.

Následující tabulky popisují tok:

Příchozí z Azure AD:

|Atribut Active Directory|Název atributu|Typ toku|Metaverse, atribut|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Místní služby Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|V od AAD - Výměna uživatelů|

Odchozí do místní služby Active Directory:

|Metaverse, atribut|Název atributu|Typ toku|Atribut Azure AD|Pravidlo synchronizace|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|POKUD(NE NULL)|msExchUserHoldPolicies|Ven do AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informace o chování atributu
MsExchangeUserHoldPolicies je atribut jednoho autority.  Atribut jednoho autority lze nastavit u objektu (v tomto případě objektu uživatele) v místním adresáři nebo v cloudovém adresáři.  Pravidla Spuštění autority diktují, že pokud je atribut synchronizován z místního, pak Azure AD nebude povoleno aktualizovat tento atribut.

Chcete-li uživatelům povolit nastavení zásady blokování objektu uživatele v cloudu, použije se atribut cloudMSExchangeUserHoldPolicies. Tento atribut se používá, protože Azure AD nelze nastavit msExchangeUserHoldPolicies přímo na základě pravidel vysvětlených výše.  Tento atribut se pak synchronizuje zpět do místního adresáře, pokud msExchangeUserHoldPolicies není null a nahradí aktuální hodnotu msExchangeUserHoldPolicies.

Za určitých okolností, například pokud byly změněny místní a v Azure ve stejnou dobu, může to způsobit některé problémy.  

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
