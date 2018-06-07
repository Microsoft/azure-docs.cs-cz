---
title: 'Synchronizace Azure AD Connect: Změna výchozí konfigurace | Microsoft Docs'
description: Obsahuje doporučené postupy pro změnu výchozí konfigurace synchronizace služby Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2c2fc3bcba4b685fba36683f89c0b6ad877dbb1d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595134"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synchronizace Azure AD Connect: osvědčené postupy pro změnu výchozí konfigurace
Účelem tohoto tématu je k popisu podporované a nepodporované změny synchronizace Azure AD Connect.

Konfigurace vytvořené Azure AD Connect funguje tak, jak je"pro většinu prostředí, které se synchronizují místní služby Active Directory s Azure AD. V některých případech je potřeba použít některé změny v konfiguraci, kterou chcete splňují konkrétní požadavky nebo požadavky.

## <a name="changes-to-the-service-account"></a>Změny účtu služby
Synchronizace Azure AD Connect je spuštěna pod účtem služby, který je vytvořený pomocí Průvodce instalací. Tento účet služby obsahuje šifrovací klíče do databáze používá synchronizaci. Bude vytvořen pomocí 127 znaků dlouhé heslo a heslo je nastaveno na nevyprší platnost.

* Je **nepodporované** změnit nebo resetování hesla účtu služby. Díky tomu zničí šifrovacích klíčů a služba nemůže získat přístup k databázi a není možné spustit.

## <a name="changes-to-the-scheduler"></a>Změny plánovače
Od verze ze sestavení 1.1 (leden 2016) můžete nakonfigurovat [Plánovač](active-directory-aadconnectsync-feature-scheduler.md) tak, aby měl cyklus synchronizace jiný než výchozí 30 minut.

## <a name="changes-to-synchronization-rules"></a>Změny synchronizační pravidla
Průvodce instalací nabízí konfiguraci, která by měla fungovat pro nejběžnějších scénářů. V případě, že potřebujete provést změny konfigurace, je třeba provést tyto pravidla, která ještě podporovanou konfiguraci.

* Můžete [změnit toky atributů](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) Pokud toky přímé atributů výchozí nejsou vhodné pro vaši organizaci.
* Pokud chcete [není toku atribut](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) a odeberte všechny existující atribut hodnoty ve službě Azure AD, je třeba vytvořit pravidlo pro tento scénář.
* [Zakázat pravidlo synchronizace nežádoucí](#disable-an-unwanted-sync-rule) místo odstranění. Během upgradu se znovu vytvoří pravidlo odstraněné.
* K [změnit pravidlo out-of-box](#change-an-out-of-box-rule), měli byste vytvořit kopii původní pravidlo a zakázat pravidlo out-of-box. Editor pravidla synchronizace vyzve a vám pomůže.
* Exportujte vaše vlastní synchronizační pravidla pomocí editoru pravidla synchronizace. Editor vám poskytne Powershellový skript, který vám pomůže snadno je znovu vytvořit ve scénáři zotavení po havárii.

> [!WARNING]
> Out-of-box synchronizační pravidla mají kryptografický otisk. Pokud změníte tato pravidla, je už odpovídající kryptografický otisk. Můžete mít problémy s v budoucnu, pokud se pokusíte použít novou verzi služby Azure AD Connect. Pouze změnit způsob, jakým je popsaný v tomto článku.

### <a name="disable-an-unwanted-sync-rule"></a>Zakázat pravidlo nežádoucí synchronizace
Neodstraňujte pravidlo synchronizace out-of-box. Pak se znovu vytvoří při další upgradu.

V některých případech se vytváří Průvodce instalací konfigurace, která nepracuje v topologii. Například pokud máte doménovou strukturu prostředků účtu, ale jste rozšířili schéma v doménové struktuře účtu se schématem systému Exchange, pravidla pro Exchange vytvoří se pro účet doménové struktury a doménové struktury prostředku. V takovém případě je nutné zakázat pravidlo synchronizace pro Exchange.

![Zakázané synchronizační pravidlo](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na obrázku výše Průvodce instalací zjistil zadáno staré schéma Exchange 2003 v doménové struktuře účtu. Toto rozšíření schématu bylo přidáno před doménové struktury prostředku byla zavedená v prostředí společnosti Fabrikam. K zajištění, že jsou synchronizovány žádné atributy od původního implementace systému Exchange, pravidlo synchronizace je třeba zakázat jak je vidět.

### <a name="change-an-out-of-box-rule"></a>Změnit pravidlo out-of-box
Měli byste změnit pravidlo out-of-box je pouze když potřebujete změnit pravidlo spojení. Pokud potřebujete změnit tok atributů, měli vytvořit synchronizační pravidlo s vyšší prioritou než pravidla out-of-box. Pouze pravidla potřebujete prakticky klonování je pravidlo **v ze služby Active Directory - připojení uživatele k**. Můžete přepsat všechna pravidla s pravidlem vyšší prioritu.

Pokud potřebujete provést změny pravidlo out-of-box, vytvořit kopii pravidlo out-of-box a zakázat původní pravidlo. Proveďte změny klonovaného pravidle. Editor pravidla synchronizace vám pomáhá s těchto kroků. Když otevřete pravidlo out-of-box, se zobrazí toto dialogové okno:  
![Upozornění z pravidla pole](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Vyberte **Ano** vytvoření kopie pravidla. Pak je otevřené klonovaného pravidle.  
![Klonovaný pravidlo](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Na tomto klonovaného pravidle proveďte potřebné změny oboru, spojení a transformace.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
