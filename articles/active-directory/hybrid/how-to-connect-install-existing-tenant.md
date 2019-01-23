---
title: 'Azure AD Connect: Pokud už máte Azure AD | Dokumentace Microsoftu'
description: Toto téma popisuje, jak použít připojit, pokud máte existujícího tenanta Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6f0c16a86d4e3dbe582f1471d2b94e1ec85b70b5
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475915"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Pokud máte existující tenanta
Většina z témat o tom, jak používat Azure AD Connect se předpokládá začínat novou Azure AD tenant a, že neexistují žádní uživatelé, nebo existuje jiné objekty. Pokud jste začali s tenantem Azure AD, ale naplněný uživatelů a dalších objektů a teď se chcete připojit, použijte pak toto téma je pro vás.

## <a name="the-basics"></a>Základy
Objekt ve službě Azure AD je buď spravovaná v cloudu (Azure AD) nebo místně. Pro jeden jednoho objektu nelze spravovat některé atributy místně a některé atributy ve službě Azure AD. Každý objekt má příznak, který udává, ve kterém je spravovaný objekt.

Některé uživatele v místním a další můžete spravovat v cloudu. Běžným scénářem pro tuto konfiguraci je organizace s využitím kombinace monitorování účtů pracovních procesů a prodejní pracovních procesů. Monitorování účtů pracovních procesů mít místní účet AD, ale prodejní pracovních procesů nepodporují, že máte účet ve službě Azure AD. Bude spravovat některé uživatele místně a některé ve službě Azure AD.

Pokud jste nespustili ke správě uživatelů ve službě Azure AD, které jsou k dispozici také v místní AD a později chtít připojit a používat, existují některé další otázky, které je potřeba zvážit.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizovat s existující uživatele ve službě Azure AD
Při instalaci služby Azure AD Connect a zahájit synchronizaci, služba synchronizace Azure AD (ve službě Azure AD) provede kontrolu na každý nový objekt a pokuste se najít existující objekt tak, aby odpovídaly. Existují tři atributy použité pro tento proces: **userPrincipalName**, **proxyAddresses**, a **sourceAnchor**/**immutableID** . Shoda s **userPrincipalName** a **proxyAddresses** se označuje jako **obnovitelně shoda**. Shoda s **sourceAnchor** se označuje jako **pevné shoda**. Pro **proxyAddresses** pouze hodnotu pomocí atributu **SMTP:**, která je primární e-mailovou adresu, se používá pro hodnocení.

Porovnání je vyhodnocen pouze pro nové objekty z připojení. Pokud změníte existující objekt, je porovnávání, tyto atributy, pak se zobrazí chyba místo.

Pokud Azure AD vyhledá objekt, kde hodnoty atributů jsou stejné pro objekt z připojit a, který je již k dispozici ve službě Azure AD, je objekt ve službě Azure AD byly převzaty připojit. Dříve cloudově spravovaného objektu je označený jako místní spravované. Všechny atributy ve službě Azure AD s hodnotou v místní AD přepsány s hodnotou v místním prostředí. Výjimkou je, když má atribut **NULL** místní hodnoty. V tomto případě hodnoty v Azure AD zůstane, ale přesto ji změnit jedině v místním na něco jiného.

> [!WARNING]
> Vzhledem k tomu, že všechny atributy ve službě Azure AD bude možné přepsat hodnotou v místním, ujistěte se, že máte dobré data dostupná místně. Například pokud e-mailovou adresu v Office 365 jenom mají spravovat a udržovat není aktualizován v místní službě AD DS a ztratit všechny hodnoty v Azure AD nebo Office 365 není k dispozici ve službě AD DS.

> [!IMPORTANT]
> Pokud budete používat synchronizaci hesla, který vždy používá expresního nastavení, pak hesla ve službě Azure AD je přepsána heslo v místní AD. Pokud vaši uživatelé se používají ke správě různých hesel, budete muset informovat, že pokud jste nainstalovali Connect musí používat místní heslo.

Předchozí části a upozornění musí být zvážen při plánování. Pokud provedete mnoho změn ve službě Azure AD není projeví v místní službě AD DS, je nutné naplánovat způsob naplnění služby AD DS s aktualizovanými hodnotami před synchronizací objektů s Azure AD Connect.

Pokud odpovídající objekty se obnovitelného shoda, pak bude **sourceAnchor** je přidána do objektu ve službě Azure AD, pevný shodu se dá použít později.

>[!IMPORTANT]
> Společnost Microsoft důrazně doporučuje proti synchronizaci místních účtů s již existující účty pro správu v Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Pevné match vs konfigurace Soft-match
Pro novou instalaci připojit není žádný praktické rozdíl mezi konfigurace soft - a pevné match. Rozdíl je v situaci, zotavení po havárii. Pokud jste ztratili serveru s Azure AD Connect, je znovu nainstalovat novou instanci bez ztráty dat. Objekt se sourceAnchor posílá připojit během počáteční instalace. Pak může být vyhodnocena shoda klientem (Azure AD Connect), což je mnohem rychlejší než udělejte to stejné ve službě Azure AD. Pevné shoda bude vyhodnocena Connect a službou Azure AD. Obnovitelné shoda je vyhodnocen pouze ve službě Azure AD.

### <a name="other-objects-than-users"></a>Jiné objekty, než uživatelé
Skupin s povoleným e-mailu a kontakty vám může konfigurace soft-match na základě proxyAddresses. Pevné shoda se nedá použít, protože lze aktualizovat pouze sourceAnchor/immutableID (pomocí Powershellu) pro uživatele pouze. Pro skupiny, které nejsou povolenou poštu aktuálně není dostupná podpora pro konfigurace soft-match nebo pevné match.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Vytvoření nové v místní službě Active Directory z dat ve službě Azure AD
Zákazníci, kteří začínají čistě cloudové řešení s Azure AD a že nemají místní AD. Později, do které chtějí využívat místních prostředků a chcete vytvořit místní AD podle dat služby Azure AD. Azure AD Connect nemůže nápovědu pro tento scénář. Nevytváří žádné uživatele v místním a nemá žádné možnost nastavit heslo v místní stejně jako v Azure AD.

Pokud jediným důvodem, proč máte v plánu přidat místní AD je k podpoře objekty LOBs (-obchodní aplikace), možná byste měli zvážit použití [služby Azure AD domain services](../../active-directory-domain-services/index.yml) místo.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
