---
title: 'Azure AD Connect: Pokud už máte Azure AD | Microsoft Docs'
description: Toto téma popisuje, jak používat připojení, pokud máte existujícího klienta Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
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
ms.openlocfilehash: 726d8998d24a630808186eea417f236fdbfb565e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725203"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Pokud máte existující klienta
Většinu témat, jak používat Azure AD Connect předpokládá začínat nové Azure AD klienta a že nejsou žádní uživatelé nebo existuje jiné objekty. Ale pokud jste spustili s klient Azure AD, naplní uživatelů a dalších objektů a teď chcete použít připojení, pak toto téma je pro vás.

## <a name="the-basics"></a>Základy
Řídí objektu ve službě Azure AD se buď hlavním v cloudu (Azure AD) nebo místní. Pro jeden jednoho objektu se nedají spravovat některé atributy místní a některé další atributy ve službě Azure AD. Každý objekt má příznak označující, kde je spravovaný objekt.

Některé uživatele na místě a dalších můžete spravovat v cloudu. Běžný scénář pro tuto konfiguraci je organizace se smíšenými účetní pracovníků a prodeje pracovních procesů. Monitorování účtů pracovníci mít místní účet AD, ale prodeje pracovníci nepodporují, budou mít účet ve službě Azure AD. By spravovat některé uživatele místní a některé ve službě Azure AD.

Pokud jste začali ke správě uživatelů ve službě Azure AD, které jsou k dispozici také v místní službě AD a později chcete použít připojení, pak jsou některé další otázky, které je třeba vzít v úvahu.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizovat s stávajících uživatelů ve službě Azure AD
Při instalaci Azure AD Connect a spustíte synchronizaci, služby Azure AD sync (ve službě Azure AD) neobsahuje kontrolu pro každý nový objekt a akci k vyhledání existujícího objektu tak, aby odpovídaly. Pro tento proces se používají tři atributy: **userPrincipalName**, **proxyAddresses**, a **sourceAnchor**/**immutableID** . Shoda s **userPrincipalName** a **proxyAddresses** se označuje jako **logicky shodu**. Shoda s **sourceAnchor** se označuje jako **pevný shodu**. Pro **proxyAddresses** atribut pouze hodnotu s **SMTP:**, která je na primární e-mailovou adresu, slouží k vyhodnocení.

Shody je Vyhodnocená jenom pro všechny nové objekty pocházejících z připojení. Pokud změníte existující objekt, je odpovídající, žádný z těchto atributů, pak se zobrazí chybová místo.

Pokud Azure AD nalezne objekt, kde jsou hodnoty atributu stejné pro objekt pocházejících z připojení a zda se již nachází ve službě Azure AD, je objekt ve službě Azure AD byly převzaty připojit. Objekt dříve spravovanými přes cloud je označení místního spravované. Všechny atributy ve službě Azure AD s hodnotou v místním budou přepsána AD s místními hodnotou. Výjimkou je, když má atribut **NULL** hodnotu místně. V takovém případě je hodnota v Azure AD zůstane, ale stále ji změnit jedině tak místní na něco jiného.

> [!WARNING]
> Vzhledem k tomu, že všechny atributy ve službě Azure AD se bude možné přepsat hodnotou místně, zkontrolujte, zda že máte funkční data místně. Například pokud pouze jste nahráli e-mailovou adresu v Office 365 a není zachovány aktualizovat v místní službě AD DS a ztratit všechny hodnoty v Azure AD nebo Office 365 nejsou k dispozici ve službě AD DS.

> [!IMPORTANT]
> Pokud používat synchronizaci hesla, která je vždy používá Expresní nastavení, pak heslo ve službě Azure AD je přepsat heslo v místní AD. Pokud uživatelé používají ke správě různá hesla, budete muset informujte je, místní heslo se musí použít, pokud jste nainstalovali připojit.

Předchozí části a upozornění je třeba zvážit při plánování. Pokud jste provedli mnoho změn ve službě Azure AD není promítnuta místní služby AD DS, pak je potřeba naplánovat postup naplnění služby AD DS s aktualizovanými hodnotami před synchronizaci objektů vašeho službou Azure AD Connect.

-Li vyplněno objektů s konfigurace soft shodu, pak se **sourceAnchor** je přidána do objektu ve službě Azure AD, pevné shoda lze později.

### <a name="hard-match-vs-soft-match"></a>Pevný match vs konfigurace Soft-match
Pro novou instalaci připojení není žádný praktické rozdíl mezi soft - a pevné nalezena shoda. Rozdíl je v situaci, obnovení po havárii. Pokud jste ztratili serveru s Azure AD Connect, bez ztráty dat, můžete přeinstalovat novou instanci. Objekt s sourceAnchor je odeslána připojit během počáteční instalace. Pak může být vyhodnocena shoda klientem (Azure AD Connect), což je mnohem rychlejší než to stejné ve službě Azure AD. Pevné shoda bude vyhodnocena Connect a službou Azure AD. Logicky shoda pouze vyhodnotí se službou Azure AD.

### <a name="other-objects-than-users"></a>Jiné objekty než uživatelé
Poštovní skupiny, a kontakty vám může konfigurace soft-match na základě proxyAddresses. Vzhledem k tomu, že lze aktualizovat pouze sourceAnchor/immutableID (pomocí prostředí PowerShell) na uživatele pouze, pevné match není použitelný. Pro skupiny, které nejsou poštovní není aktuálně nepodporuje konfigurace soft-match nebo pevné shodu.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Vytvoření nové místní Active Directory z dat ve službě Azure AD
Někteří zákazníci začínat čistě cloudové řešení s Azure AD a nemají místní AD. Později chtějí využívat místních prostředků a chcete vytvořit místní AD podle data služby Azure AD. Azure AD Connect nemůže pomoct pro tento scénář. Nedojde k vytvoření uživatele na místě a nemá žádné možnost nastavit heslo na místě na stejný jako v Azure AD.

Pokud jenom důvod, proč plánujete přidat místní AD má zajistit podporu pro objekty LOBs (-obchodní aplikace), pak možná byste měli zvážit použití [služby Azure AD domain services](../../active-directory-domain-services/index.yml) místo.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
