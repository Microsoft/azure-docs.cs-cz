---
title: 'Synchronizace služby Azure AD Connect: Změna výchozí konfigurace | Dokumenty společnosti Microsoft'
description: Poskytuje doporučené postupy pro změnu výchozí konfigurace synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242115"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synchronizace azure ad připojení: Doporučené postupy pro změnu výchozí konfigurace
Účelem tohoto tématu je popsat podporované a nepodporované změny synchronizace Azure AD Connect.

Konfigurace vytvořená službou Azure AD Connect funguje "tak, jak je" pro většinu prostředí, která synchronizují místní službu Active Directory s Azure AD. V některých případech je však nutné použít některé změny konfigurace k uspokojení určité potřeby nebo požadavku.

## <a name="changes-to-the-service-account"></a>Změny účtu služby
Synchronizace Azure AD Connect běží pod účtem služby vytvořeným průvodcem instalací. Tento účet služby obsahuje šifrovací klíče k databázi používané synchronizací. Je vytvořen s 127 znaků dlouhé heslo a heslo je nastavena na nevyprší.

* Není **podporováno** změnit nebo obnovit heslo účtu služby. Tím zničíšte šifrovací klíče a služba nemá přístup k databázi a nemůže začít.

## <a name="changes-to-the-scheduler"></a>Změny plánovače
Počínaje verzemi z sestavení 1.1 (únor 2016) můžete nakonfigurovat [plánovač](how-to-connect-sync-feature-scheduler.md) tak, aby měl jiný cyklus synchronizace než výchozí 30 minut.

## <a name="changes-to-synchronization-rules"></a>Změny pravidel synchronizace
Průvodce instalací poskytuje konfiguraci, která má fungovat pro nejběžnější scénáře. V případě, že potřebujete provést změny v konfiguraci, musíte dodržovat tato pravidla, abyste měli stále podporovanou konfiguraci.

> [!WARNING]
> Pokud provedete změny výchozích pravidel synchronizace, budou tyto změny přepsány při příští aktualizaci služby Azure AD Connect, což vede k neočekávaným a pravděpodobně nežádoucím výsledkům synchronizace.

* [Toky atributů](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) můžete změnit, pokud výchozí toky přímých atributů nejsou vhodné pro vaši organizaci.
* Pokud nechcete [tok atributu](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) a odebrat všechny existující hodnoty atributů ve službě Azure AD, pak je třeba vytvořit pravidlo pro tento scénář.
* Místo odstranění [zakažte nežádoucí pravidlo synchronizace.](#disable-an-unwanted-sync-rule) Odstraněné pravidlo je znovu vytvořeno během upgradu.
* [Chcete-li změnit pravidlo po vybalení](#change-an-out-of-box-rule), měli byste vytvořit kopii původního pravidla a zakázat pravidlo out-of-box. Editor pravidel synchronizace zobrazí výzvy a pomůže vám.
* Exportujte vlastní pravidla synchronizace pomocí Editoru pravidel synchronizace. Editor poskytuje skript prostředí PowerShell, který můžete použít ke snadnému jejich opětovnému vytvoření ve scénáři zotavení po havárii.

> [!WARNING]
> Pravidla synchronizace předváděcí skříně mají kryptografický otisk. Pokud provedete změnu těchto pravidel, kryptografický otisk již nebude shodovat. Můžete mít problémy v budoucnu při pokusu o použití nové verze Azure AD Connect. Proveďte pouze změny tak, jak je popsáno v tomto článku.

### <a name="disable-an-unwanted-sync-rule"></a>Zakázání nežádoucího pravidla synchronizace
Neodstraňujte pravidlo synchronizace předváděcí rámečku. Je znovu vytvořen během dalšího upgradu.

V některých případech průvodce instalací vytvořil konfiguraci, která nefunguje pro topologii. Máte-li například topologii doménové struktury prostředků obchodního vztahu, ale rozšířili jste schéma v doménové struktuře účtu pomocí schématu serveru Exchange, budou vytvořena pravidla pro server Exchange pro doménovou strukturu účtu a doménovou strukturu prostředků. V takovém případě je třeba zakázat pravidlo synchronizace pro exchange.

![Zakázané pravidlo synchronizace](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na obrázku výše nalezl průvodce instalací v doménové struktuře účtu staré schéma serveru Exchange 2003. Toto rozšíření schématu byla přidána před doménové struktury prostředků byla zavedena v prostředí společnosti Fabrikam. Chcete-li zajistit, že nejsou synchronizovány žádné atributy ze staré implementace serveru Exchange, mělo by být pravidlo synchronizace zakázáno, jak je znázorněno.

### <a name="change-an-out-of-box-rule"></a>Změna pravidla po vybalení
Pravidlo out-of-box byste měli změnit pouze tehdy, když potřebujete změnit pravidlo spojení. Pokud potřebujete změnit tok atributů, měli byste vytvořit pravidlo synchronizace s vyšší prioritou než pravidla out-of-box. Jediné pravidlo, které prakticky potřebujete klonovat, je pravidlo **In z AD - User Join**. Všechna ostatní pravidla můžete přepsat pravidlem vyšší priority.

Pokud potřebujete provést změny pravidla out-of-box, měli byste vytvořit kopii pravidla out-of-box a zakázat původní pravidlo. Potom proveďte změny klonovaného pravidla. S těmito kroky vám pomáhá Editor pravidel synchronizace. Když otevřete pravidlo out-of-box, zobrazí se toto dialogové okno:  
![Upozornění na pravidlo po vybalení z krabice](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Chcete-li vytvořit kopii pravidla, vyberte **ano.** Klonované pravidlo se pak otevře.  
![Klonované pravidlo](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Na toto klonované pravidlo proveďte všechny nezbytné změny oboru, spojení a transformace.

## <a name="next-steps"></a>Další kroky
**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
