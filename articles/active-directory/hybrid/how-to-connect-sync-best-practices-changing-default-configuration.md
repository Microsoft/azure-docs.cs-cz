---
title: 'Azure AD Connect synchronizace: Změna výchozí konfigurace | Microsoft Docs'
description: Poskytuje osvědčené postupy pro změnu výchozí konfigurace Azure AD Connect synchronizace.
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
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e91ff8fa3666a2dfc5aaad07be7927852b08bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357694"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect Sync: osvědčené postupy pro změnu výchozí konfigurace
Účelem tohoto tématu je popsat podporované a nepodporované změny Azure AD Connect synchronizace.

Konfigurace vytvořená nástrojem Azure AD Connect funguje tak, jak je, pro většinu prostředí, která synchronizují místní službu Active Directory se službou Azure AD. V některých případech je však nutné použít některé změny konfigurace, aby splňovaly určitou potřebu nebo požadavek.

## <a name="changes-to-the-service-account"></a>Změny účtu služby
Synchronizace Azure AD Connect běží pod účtem služby vytvořeným průvodcem instalací. Tento účet služby uchovává šifrovací klíče pro databázi, kterou používá synchronizace. Vytvoří se s délkou hesla 127 znaků a heslo je nastavené na nevypršení platnosti.

* Změna nebo resetování hesla účtu služby se **nepodporuje** . Tím dojde k zničení šifrovacích klíčů a služba není schopna získat přístup k databázi a nelze ji spustit.

## <a name="changes-to-the-scheduler"></a>Změny plánovače
Od verzí od Build 1,1 (únor 2016) můžete nakonfigurovat, aby [Plánovač](how-to-connect-sync-feature-scheduler.md) měl jiný cyklus synchronizace než výchozí 30 minut.

## <a name="changes-to-synchronization-rules"></a>Změny pravidel synchronizace
Průvodce instalací poskytuje konfiguraci, která by měla fungovat v nejběžnějších scénářích. V případě, že je třeba provést změny v konfiguraci, je nutné dodržovat tato pravidla, aby byla stále podporovaná konfigurace.

> [!WARNING]
> Pokud provedete změny výchozích pravidel synchronizace, budou tyto změny při příští aktualizaci Azure AD Connect přepsány, což vede k neočekávaným a pravděpodobným nežádoucím výsledkům synchronizace.

* [Toky atributů můžete změnit](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) , pokud výchozí přímé toky atributů nejsou vhodné pro vaši organizaci.
* Pokud nechcete [přesměrovat atribut](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) a odebrat všechny existující hodnoty atributu v Azure AD, musíte pro tento scénář vytvořit pravidlo.
* Místo odstranění neodstraňujte [pravidlo pro nechtěné synchronizace](#disable-an-unwanted-sync-rule) . Při upgradu se znovu vytvoří odstraněné pravidlo.
* Chcete-li [změnit pravidlo pro předběžné pole](#change-an-out-of-box-rule), měli byste vytvořit kopii původního pravidla a zakázat pravidlo pro předdefinované. Editor pravidel synchronizace vás vyzve a pomůže vám.
* Exportujte vlastní pravidla synchronizace pomocí editoru pravidel synchronizace. Editor poskytuje skript PowerShellu, který můžete použít ke snadnému opětovnému vytvoření ve scénáři zotavení po havárii.

> [!WARNING]
> Pravidla synchronizace, která jsou mimo box, mají kryptografický otisk. Pokud provedete změnu těchto pravidel, kryptografický otisk již nebude odpovídat. Pokud se pokusíte použít novou verzi Azure AD Connect, může dojít k problémům v budoucnu. Pouze změnit způsob, jak je popsáno v tomto článku.

### <a name="disable-an-unwanted-sync-rule"></a>Zakázat pravidlo pro nechtěné synchronizace
Neodstraňujte pravidlo pro synchronizaci předem připraveného okna. Při dalším upgradu se znovu vytvoří.

V některých případech Průvodce instalací vytvořil konfiguraci, která pro vaši topologii nefunguje. Pokud máte například topologii doménové struktury prostředků účtů, ale rozšířili jste schéma v doménové struktuře účtu se schématem Exchange, budou pro doménovou strukturu účtů a doménovou strukturu prostředků vytvořená pravidla pro Exchange. V takovém případě je nutné zakázat pravidlo synchronizace pro Exchange.

![Pravidlo zakázané synchronizace](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na obrázku výše v doménové struktuře účtu našel Průvodce instalací staré schéma Exchange 2003. Toto rozšíření schématu se přidalo ještě před tím, než se zavedla struktura prostředků v prostředí společnosti Fabrikam. Chcete-li zajistit, aby nebyly žádné atributy z původní implementace systému Exchange synchronizovány, pravidlo synchronizace by mělo být zakázáno, jak je uvedeno níže.

### <a name="change-an-out-of-box-rule"></a>Změna pravidla pro předběžné pole
V případě, že je třeba změnit pravidlo spojení, stačí, když změníte pravidlo pro spojení. Pokud potřebujete změnit tok atributů, pak byste měli vytvořit pravidlo synchronizace s vyšší prioritou než pravidla pro dodávání v krabicích. Jediné pravidlo, které prakticky potřebujete klonovat, je pravidlo **v rámci připojení ke službě AD – uživatel**. Všechna ostatní pravidla můžete přepsat pravidlem s vyšší prioritou.

Pokud potřebujete provést změny v pravidle předdefinovaných verzí, pak byste měli vytvořit kopii předem připraveného pravidla a zakázat původní pravidlo. Pak proveďte změny klonovaného pravidla. Editor pravidla synchronizace vám pomáhá s těmito kroky. Když otevřete předdefinované pravidlo, zobrazí se toto dialogové okno:  
![Pravidlo upozornění mimo box](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Pokud chcete vytvořit kopii pravidla, vyberte **Ano** . Pak se otevře klonované pravidlo.  
![Klonované pravidlo](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

U tohoto klonovaného pravidla proveďte všechny nezbytné změny rozsahu, spojování a transformací.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
