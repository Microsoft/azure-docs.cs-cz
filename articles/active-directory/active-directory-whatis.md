---
title: Co je Azure Active Directory (Azure AD)? | Dokumenty Microsoft
description: Pomocí Azure Active Directory rozšířit vaše stávající místních identit do cloudu, nebo vyvíjet aplikace, integraci služby Azure AD.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 003ce2edda3e2069eb7e05f58ecc2e208c818946
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="what-is-azure-active-directory"></a>Představení služby Azure Active Directory
Azure Active Directory (Azure AD) je více klientů, cloudového adresáře společnosti Microsoft a služba identity management, který kombinuje základní adresářová služba, Správa přístupu aplikací a ochrany identit v jediném řešení. Azure AD také nabízí bohatou a založených na standardech platformu, která umožňuje vývojářům k poskytování řízení přístupu k jejich aplikacím na základě centralizovaných zásad a pravidla.

![Sada komponent Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Pro správce IT.** Azure AD poskytuje bezpečnější řešení pro vaši organizaci prostřednictvím silnější správu identit a jednotné přihlašování (SSO) přístup k tisícům [cloudové aplikace SaaS](active-directory-saas-tutorial-list.md) a místní aplikace. Pomocí těchto aplikací získáte také zabezpečení cloudové aplikace, bezproblémový přístup, lepší spolupráci a automatizace životního cyklu identity pro vaši zaměstnanci, která pomáhá zvýšit zabezpečení a dodržování předpisů.

    Kromě toho se právě [čtyři kliknutí](./connect/active-directory-aadconnect-get-started-express.md), Azure AD může integrovat existující Windows Server Active Directory, umožňuje vaší organizaci použít existující místní přístup investice identity můžete spravovat cloudové aplikaci SaaS .

- **Pro vývojáře aplikace.** Azure AD vám umožní soustředit na budování aplikace umožňují integrovat řešení pro správu identit, který se používá v milionech organizací po celém světě.

- **Pro zákazníky, Office 365, Azure nebo Dynamics CRM Online.** Už používáte Azure AD. Každý klient Office 365, Azure a Dynamics CRM Online je ve skutečnosti klient služby Azure AD, takže můžete okamžitě začít spravovat váš přístup zaměstnanců k integrované cloudové aplikace.

## <a name="how-reliable-is-azure-ad"></a>Jak spolehlivé je Azure AD?
Návrh víceklientské, zeměpisné polohy, vysokou dostupnost služby Azure AD znamená, že můžete spoléhat na pro vaše nejdůležitější obchodní potřeby. Systém mimo 28 datových centrech po celém světě s automatické převzetí služeb při selhání, budete mít pohodlí zároveň budete vědět, že Azure AD je vysoce spolehlivé a to i v případě, že datové centrum ocitne mimo provoz kopie adresáře data jsou v za provozu alespoň dva další regionální rozptýlená v datových centrech a k dispozici pro okamžitý přístup.

Další informace o smlouvě o úrovni služeb najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Zvolte edice
Všechny firemní služby Microsoft Online spoléhají na Azure Active Directory (Azure AD) pro přihlášení a je třeba další identity. Pokud se přihlásíte k některému z firemní služby Microsoft Online (například Office 365 nebo Microsoft Azure), bude docházet k Azure AD s přístupem ke všem funkcím volné. S edice Azure Active Directory Free můžete spravovat uživatele a skupiny, proveďte synchronizaci s místních adresářů, získat jednotné přihlašování v Azure, Office 365 a tisícům oblíbených aplikací SaaS jako Salesforce, Workday, Concur, DocuSign, Google Apps, pole, ServiceNow, Dropbox a další. 

K vylepšení služby Azure Active Directory, můžete přidat placené možnosti pomocí edice Azure Active Directory Basic, Premium P1 a Premium P2. Azure Active Directory placené edice jsou postavená na adresáře existující volné poskytování podnikové třídy pokrývání uzlů samoobslužné služby, rozšířené monitorování, vytváření sestav zabezpečení, služba Multi-Factor Authentication (MFA) a zabezpečený přístup pro mobilních pracovníků.

> [!NOTE]
> Cenová možnosti tyto edice najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 a Azure Active Directory Basic v Číně aktuálně nepodporují. Další informace o cenách služby Azure AD si můžete ověřit fóru Azure Active Directory.
>

* **Azure Active Directory Basic** -navržený pro úkolové pracovníky s potřebami cloudu první, tato edice poskytuje zaměřené na cloudové aplikace přístup a samoobsluhy identitu řešení pro správu. Azure Active Directory ve verzi Basic vám poskytne funkce pro zvýšení produktivity a snížení nákladů, jako je správa přístupu na základě skupin, samoobslužné obnovení hesel pro cloudové aplikace a aplikační proxy Azure Active Directory (aby se pomocí Azure Active Directory daly publikovat místní webové aplikace), a to všechno se smlouvou SLA na podnikové úrovni s 99,9procentní dostupností.
* **Azure Active Directory Premium P1** – navržené pro organizace s náročnější umožnit správu identit a přístupu musí, Azure Active Directory Premium edition přidá funkce pro správu identit podnikové úrovni bohaté funkce a umožňuje uživatelům hybridní bezproblémově přistupovat k místnímu a cloudové funkce. Tato edice zahrnuje vše, co potřebujete k pracovník s informacemi a správci identity v hybridních prostředích napříč přístup k aplikaci, identity samoobslužné služby a správu přístupu (IAM) ochrany identit a zabezpečení v cloudu. Podporuje rozšířené správy a delegování prostředky jako dynamických skupin a samoobslužnou správu skupin. Zahrnuje Microsoft Identity Manager (místní přístupu a identit a správy sady) a poskytuje cloudu zpětný zápis funkce povolení řešení, jako jsou samoobslužné resetování hesla pro místní uživatele.
* **Azure Active Directory Premium P2** -navržený s rozšířenou ochranu pro všechny uživatele a správce, tato nová nabídka zahrnuje všechny funkce v Azure AD Premium P1 a také ochrany identit a Privileged Identity Management. Azure Active Directory Identity Protection využívá až miliardy signály, které poskytují podmíněného přístupu na základě riziko k vašim aplikacím a důležitá firemní data. Nám také pomáhají spravovat a chránit privilegovaných účtů s Azure Active Directory Privileged Identity Management, můžete zjistit, omezit a sledovat správci a jejich přístup k prostředkům a poskytují přístup za běhu v případě potřeby.  

> [!NOTE]
> Počet možností Azure Active Directory jsou k dispozici prostřednictvím "platím průběžně" edice:
>
> * Active Directory B2C je řešení správy identit a přístupu pro aplikace určených. Další informace najdete v tématu [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication umožňuje použít pro uživatele, nebo v zprostředkovatele ověřování. Další informace najdete v tématu [co je Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Jak můžu začít?

**Pokud jste správce IT:**

* [Vyzkoušejte si to!](https://azure.microsoft.com/trial/get-started-active-directory/) -můžete zaregistrovat k bezplatné 30denní zkušební verzi a nasadit řešení první cloudu pomocí tohoto odkazu v části pět minut.

* Čtení [Začínáme s Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) pro klienta spuštěná rychlé tipy a triky na získávání Azure AD

**Pokud jste vývojář:**
 
* Podívejte se [Příručka pro vývojáře](active-directory-developers-guide.md) do Azure Active Directory

* [Spuštění zkušební verze](https://azure.microsoft.com/trial/get-started-active-directory/) – zaregistrovat k bezplatné 30denní zkušební verzi a spustit integrace aplikací s Azure AD

## <a name="next-steps"></a>Další postup
[Další informace o základní informace o Azure správu identit a přístupu](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
