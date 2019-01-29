---
title: 'Synchronizace Azure AD Connect: Změna výchozí konfigurace | Dokumentace Microsoftu'
description: Poskytuje osvědčené postupy pro změnu výchozí konfigurace synchronizace služby Azure AD Connect.
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
ms.topic: article
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9f60775bd057f0ad114bd7d7d0d42514a83186c5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55186837"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synchronizace Azure AD Connect: Osvědčené postupy pro změnu výchozí konfigurace
Účelem tohoto tématu je k popisu změn podporované a nepodporované synchronizace Azure AD Connect.

Konfigurace služby Azure AD Connect vytvořil funguje "tak jak jsou" pro většinu prostředí, které se synchronizují v místní službě Active Directory s Azure AD. V některých případech je potřeba použít některé změny na konfiguraci, kterou chcete splňují konkrétní požadavky nebo požadavky.

## <a name="changes-to-the-service-account"></a>Změny účtu služby
Synchronizace Azure AD Connect je spuštěná pod účtem služby vytvořené pomocí Průvodce instalací. Tento účet služby obsahuje šifrovací klíče do databáze používá synchronizaci. Je vytvořen s maximálně 127 znaků dlouhé heslo a heslo je nastavená na nevyprší platnost.

* Je **nepodporované** změnit nebo resetování hesla účtu služby. To odstraní šifrovací klíče a službu není možné získat přístup k databázi a není možné spustit.

## <a name="changes-to-the-scheduler"></a>Změny v Plánovači
Od vydání z buildu 1.1 (únor 2016) můžete nakonfigurovat [Plánovač](how-to-connect-sync-feature-scheduler.md) mít cyklus synchronizace jiné než výchozí 30 minut.

## <a name="changes-to-synchronization-rules"></a>Změny synchronizační pravidla
Průvodce instalací nabízí konfiguraci, která by měla fungovat pro nejběžnější scénáře. V případě, že budete muset provést změny v konfiguraci, je třeba dodržovat tato pravidla i nadále mít podporovanou konfiguraci.

> [!WARNING]
> Pokud provedete změny výchozích pravidel synchronizace pak tyto změny budou přepsány při příštím spuštění služby Azure AD Connect se aktualizuje, výsledkem je neočekávaný a pravděpodobně synchronizace nežádoucí výsledky.

* Je možné [změnit toky atributů](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) Pokud výchozí toky s přímým přístupem atributů nejsou vhodné pro vaši organizaci.
* Pokud chcete [není toku atributu](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) a odeberte všechny existující atributy hodnoty ve službě Azure AD, pak je potřeba vytvořit pravidlo pro tento scénář.
* [Zakázat pravidlo synchronizace nežádoucí](#disable-an-unwanted-sync-rule) místo jeho odstranění. Během upgradu se znovu vytvoří pravidlo odstraněno.
* K [změnit pravidlo out-of-box](#change-an-out-of-box-rule), měli byste vytvořit kopii původní pravidlo a zakázat pravidlo out-of-box. Editor pravidel synchronizace výzvy a pomůže vám.
* Exportujte vaše vlastní synchronizační pravidla pomocí Editor pravidel synchronizace. Editor vám poskytne skript prostředí PowerShell, které vám umožní snadno je znovu vytvořit ve scénáři zotavení po havárii.

> [!WARNING]
> Out-of-box synchronizační pravidla mají kryptografického otisku. Pokud změníte tato pravidla, je už odpovídající kryptografický otisk. V budoucnu, když se pokusíte použít nová verze služby Azure AD Connect, mohou mít problémy. Proveďte změny jen tak, jak je popsáno v tomto článku.

### <a name="disable-an-unwanted-sync-rule"></a>Zakázání nežádoucí pravidla synchronizace
Neodstraňujte pravidlo synchronizace out-of-box. Se znovu vytvoří při další upgradu.

V některých případech se Průvodce instalací vytvořil konfiguraci, která nefunguje u vaší topologii. Například pokud máte doménovou strukturu prostředků účtu, ale jste rozšířili schéma v doménové struktuře účtu se schématem systému Exchange, potom pravidla pro Exchange jsou vytvořeny pro účet doménové struktury a doménové struktury prostředku. V takovém případě musíte postup při zakázání pravidla synchronizace pro server Exchange.

![Zakázané synchronizační pravidlo](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na obrázku výše Průvodce instalací našla staré schématu Exchange 2003 v doménové struktuře účtu. Toto rozšíření schématu byl přidán předtím, než doménové struktury prostředku byl zaveden ve společnosti Fabrikam prostředí. K zajištění, že žádné atributy od implementace rozhraní staré Exchange jsou synchronizovány, pravidlo synchronizace je třeba zakázat jak je znázorněno.

### <a name="change-an-out-of-box-rule"></a>Změňte pravidlo out-of-box
Měli byste změnit pravidlo out-of-box se pouze pokud potřebujete změnit pravidla spojení. Pokud potřebujete změnit tok atributů, by měl vytvořit synchronizační pravidlo s vyšší prioritou než pravidla out-of-box. Toto pravidlo je pouze pravidlo prakticky muset naklonovat **v ze služby AD - uživatel připojit**. Všechna pravidla s vyšší prioritou pravidlo můžete přepsat.

Pokud potřebujete provést změny pravidlo out-of-box, vytvořte kopii pravidlo out-of-box a původní pravidlo zakázat. Proveďte změny do naklonovaného pravidla. Editor pravidel synchronizace vám pomáhá s těmito kroky. Když otevřete pravidlo out-of-box, zobrazí se toto dialogové okno:  
![Upozornění z pravidla pole](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Vyberte **Ano** vytvořit kopii tohoto pravidla. Pak otevření naklonované pravidlo.  
![Naklonované pravidlo](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Na toto pravidlo naklonované proveďte potřebné změny do oboru, spojení a transformace.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
