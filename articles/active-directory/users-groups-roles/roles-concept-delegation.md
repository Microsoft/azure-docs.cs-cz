---
title: Delegování rolí správce ve službě Azure Active Directory | Dokumentace Microsoftu
description: Modely delegování, příklady a role zabezpečení v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 10/15/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: cf26cd53340d00e057492b8625b1ff5d6dcde884
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195014"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegování správy v Azure Active Directory

S nárůstem organizační dodává složitost. Běžné jedna odpověď je Zredukovat některé úlohy správy přístupu s rolí správce Azure Active Directory (AD). Nejmenší oprávnění můžete přiřadit uživatelům přístup k jejich aplikace a provádět své úkoly. I v případě, že každý majitel není přiřadit roli globálního správce, umísťování odpovědnosti správu aplikací na existující globální správci. Existuje mnoho důvodů, proč organizaci přesun směrem k více decentralizovanou správu. Tento článek vám pomohou naplánovat pro delegování ve vaší organizaci.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centralizované oproti delegovaná oprávnění

S růstem organizace, může být obtížné sledovat, kteří uživatelé mají konkrétní role. Pokud zaměstnanec má oprávnění správce, které by se neměly, vaše organizace může být náchylnější k porušení zabezpečení. Obecně platí, kolik správci podporujete a jak podrobné jsou oprávnění k nim závisí na velikosti a složitosti vašeho nasazení.

* V malých nebo testování konceptu nasazení jedné nebo několika správci provádět vše; neexistuje žádná delegování. V takovém případě vytvořte každý správce s rolí globálního správce.
* U větších nasazení s víc počítačů, aplikací a stolní počítače je nutné provést další delegování. Konkrétnější funkční odpovědnosti (role) může mít několik správci. Například některé můžou být správci privilegovaných identit a jiná můžou být správci aplikace. Kromě toho může správce spravovat pouze určité skupiny objektů, jako jsou například zařízení.
* Ještě větší nasazení může vyžadovat ještě podrobnější oprávnění, a pravděpodobně správcům neobvyklé nebo hybridní role.

Na portálu Azure AD, můžete [zobrazit všechny členy jakékoli role](directory-manage-roles-portal.md), které vám umožňují rychle zkontrolovat, nasazení a delegovat oprávnění.

Pokud vás zajímá delegování přístupu k prostředkům Azure místo přístup pro správu ve službě Azure AD, přečtěte si téma [přiřadit roli řízení přístupu na základě Role](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Plánování delegování

To je práce vyvinout model delegování, který nejlépe vyhovuje vašim potřebám. Vývoj modelu delegování je proces iterativní návrhu a doporučujeme, abyste že postupujte podle těchto kroků:

* Definování rolí, které potřebujete
* Delegovat správu aplikace
* Udělení oprávnění registrovat aplikace
* Delegovat vlastnictví aplikace
* Vývoj plánu zabezpečení
* Vytvoření účtů pro nouzový
* Vaše role správce zabezpečení
* Ujistěte se, zvýšení oprávnění privilegované dočasné

## <a name="define-roles"></a>Definování rolí

Určení úloh služby Active Directory, které se provádějí správci a jak jsou mapovány na role. Je možné [zobrazení popisů podrobné role](directory-manage-roles-portal.md) na webu Azure Portal.

Každý úkol by se mělo vyhodnotit pro frekvenci, důležitost a potíže. Tato kritéria jsou důležité aspekty definice úlohy, protože se řídí, zda by měl být delegovaná oprávnění:

* Úlohy dělat pravidelně, mají omezené riziko a jsou jednoduché kompletní jsou vhodnými kandidáty pro delegování.
* Úlohy provést jen zřídka, ale mají velký vliv celé organizaci a vyžadují vysokou úrovní je velmi pečlivě zvážit před delegování. Místo toho můžete [dočasně zvýšit oprávnění účtu k požadované roli](../active-directory-privileged-identity-management-configure.md) nebo změna přiřazení úkolu.

## <a name="delegate-app-administration"></a>Delegovat správu aplikace

Růst počtu aplikací ve vaší organizaci mohou zatěžovat modelu delegování. Pokud se zátěž pro správu přístupu k aplikacím na globálního správce, je pravděpodobné, že tento model se zvyšuje jeho režijní náklady na čas přejde. Pokud jste udělili uživatelé v roli globálního správce pro takové věci, jako jsou konfigurace podnikové aplikace, můžete je teď přesměrovat následující méně privilegovaným rolím. To pomáhá zlepšit tak stav zabezpečení a snižuje riziko unfortunate chyby. Většina oprávnění aplikačních rolí správce je:

* **Správce aplikace** roli, která uděluje možnost spravovat všechny aplikace v adresáři, včetně registrace, nastavení jednotného přihlašování, uživatele a přiřazení skupin a licencování, aplikaci nastavení proxy serveru a vyjádření souhlasu. To není udělit schopnost spravovat podmíněný přístup.
* **Správce cloudové aplikace** roli, která uděluje všechny schopnosti nástroje Správce aplikací s výjimkou ho nelze udělit přístup k nastavení Proxy aplikací (protože nemá žádné místní oprávnění).

## <a name="delegate-app-registration"></a>Registrace aplikace delegáta

Ve výchozím nastavení všichni uživatelé můžou vytvářet registrace aplikací. Pokud chcete selektivně udělit schopnost vytvářet registrace aplikací:

* Nastavte **uživatelé můžou registrovat aplikace** ne v **uživatelská nastavení**
* Přiřadit uživatele k roli pro vývojáře aplikací

Pokud chcete selektivně udělit schopnost souhlasit s povolením aplikace pro přístup k datům:

* Nastavte **uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem** číslo v **uživatelská nastavení**
* Přiřadit uživatele k roli pro vývojáře aplikací

Když vývojář aplikace se vytvoří nová registrace aplikace, jsou automaticky přidány jako první vlastník.

## <a name="delegate-app-ownership"></a>Delegovat vlastnictví aplikace

Pro delegování přístupu i citlivější aplikace můžete přiřadit vlastnictví jednotlivé podnikové aplikace. To doplňuje stávající podporu pro přiřazení vlastníků registrace aplikací. Přiřazení vlastníka na základě-podnikové aplikace v okně podnikové aplikace. Výhodou je, že vlastníci můžou spravovat jenom podnikové aplikace, které vlastní. Například můžete přiřadit vlastníka aplikace Salesforce a tento vlastník můžete spravovat přístup k a konfigurace pro Salesforce a další aplikace. Podniková aplikace může mít mnoho vlastníků a uživatel může být vlastníka pro mnoho podnikových aplikací. Existují dvě role vlastníka aplikace:

* **Vlastníka aplikace Enterprise** role uděluje možnost spravovat "podnikové aplikace, které uživatel vlastní, včetně nastavení jednotného přihlašování, uživatele a skupiny přiřazení a přidání dalších vlastníků. To není udělit schopnost spravovat nastavení Proxy aplikací nebo podmíněného přístupu.
* **Vlastník registrace aplikace** role uděluje možnost spravovat registrace aplikací pro aplikaci, která uživatel vlastní, včetně manifest aplikace a přidání dalších vlastníků.

## <a name="develop-a-security-plan"></a>Vývoj plánu zabezpečení

Azure AD poskytuje rozsáhlou pokyny k plánování a provádění plánu zabezpečení pro vaše role správce Azure AD [k zabezpečení privilegovaného přístupu, hybridní a cloudové nasazení](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Vytvoření účtů pro nouzový

Pokud chcete zachovat přístup k úložišti identity management, pokud vznikne problém, přípravu účtů pro nouzový přístup podle [vytvořit účty pro správu přístupu nouzovou](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Vaše role správce zabezpečení

Útočníci, kteří mají kontrolu nad privilegovaným účtům můžete dělat obrovské poškození, tak chránit tyto účty nejprve pomocí [základní zásady přístupu](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) , který je k dispozici ve výchozím nastavení pro všechny tenanty Azure AD (ve verzi public preview). Zásada vynucuje ověřování službou Multi-Factor Authentication na privilegované účty Azure AD. Následující role Azure AD jsou zahrnuté do základní zásady služby Azure AD:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení

## <a name="elevate-privilege-temporarily"></a>Dočasně zvýšení úrovně oprávnění

Pro většinu každodenních aktivit ne všichni uživatelé potřebovat oprávnění globálního správce, a ne všechny z nich by měla být trvale přiřazena role globálního správce. Pokud uživatelé potřebují oprávnění globálního správce, se musí aktivovat přiřazení role ve službě Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) na svůj vlastní účet nebo alternativního účtu správce.

## <a name="next-steps"></a>Další postup

Odkaz na popisy rolí Azure AD, najdete v části [přiřazení rolí správce ve službě Azure AD](directory-assign-admin-roles.md)
