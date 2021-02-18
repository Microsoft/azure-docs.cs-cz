---
title: Pochopení delegování role správce – Azure Active Directory | Microsoft Docs
description: Modely delegování, příklady a zabezpečení rolí v Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9074fdbf31cdd8516b8d42f7cc13b4bc16f6634a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095740"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegovat správu v Azure Active Directory

Růst organizačního růstu přináší složitost. Jednou z běžných reakcí je omezit některé úlohy správy přístupu pomocí rolí správce služby Azure Active Directory (AD). Uživatelům můžete přiřadit nejnižší možná oprávnění, abyste mohli přistupovat ke svým aplikacím a mohli provádět jejich úkoly. I když nepřiřadíte roli globálního správce ke každému vlastníkovi aplikace, zadáváte odpovědnost za správu aplikací u stávajících globálních správců. Existuje mnoho důvodů, proč se organizace může přesunout k více decentralizované správě. Tento článek vám může pomáhat při plánování delegování ve vaší organizaci.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centralizované versus delegovaná oprávnění

V organizaci roste, může být obtížné sledovat, kteří uživatelé mají konkrétní role správce. Pokud má zaměstnanec práva správce, může být vaše organizace náchylná k narušení zabezpečení. Obecně platí, kolik správců podporujete a jak jsou podrobná jejich oprávnění závislá na velikosti a složitosti vašeho nasazení.

* V případě nasazení v malém nebo zkušebním prostředí má jeden nebo několik správců vše. neexistuje žádné delegování. V takovém případě vytvořte každého správce s rolí globálního správce.
* V rozsáhlejších nasazeních s více počítači, aplikacemi a stolními počítači je potřeba víc delegování. Několik správců může mít konkrétnější funkční zodpovědnosti (role). Někteří můžou být například správci privilegovaných identit a jiní můžou být správci aplikací. Kromě toho může správce spravovat pouze určité skupiny objektů, jako jsou například zařízení.
* I větší nasazení mohou vyžadovat ještě více přesnější oprávnění, a případně i správce s nekonvenčními nebo hybridními rolemi.

Na portálu Azure AD můžete [Zobrazit všechny členy jakékoli role](manage-roles-portal.md), což vám umožní rychle kontrolovat oprávnění k nasazení a delegování.

Pokud vás zajímá delegování přístupu k prostředkům Azure místo oprávnění správce ve službě Azure AD, přečtěte si téma [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Plánování delegování

Pracujete na vývoji modelu delegování, který vyhovuje vašim potřebám. Vývoj modelu delegování je iterativní proces návrhu a doporučujeme vám postupovat podle těchto kroků:

* Definice potřebných rolí
* Delegování správy aplikace
* Udělení možnosti registrovat aplikace
* Delegování vlastnictví aplikace
* Vývoj plánu zabezpečení
* Navázání mimořádných účtů
* Zabezpečení rolí správce
* Dočasné zvýšení oprávnění zvýšit

## <a name="define-roles"></a>Definování rolí

Určete úlohy služby Active Directory, které jsou prováděny správci a jak se mapují na role. [Podrobné popisy rolí můžete zobrazit](manage-roles-portal.md) v Azure Portal.

Každý úkol by měl být vyhodnocen pro četnost, důležitost a obtížnost. Tato kritéria jsou zásadními aspekty definice úlohy, protože určují, jestli by měla být delegovaná oprávnění:

* Úlohy, které provedete v rutinách, mají omezené riziko a jsou triviální k dokončení, jsou vynikajícími kandidáty na delegování.
* Úlohy, které provedete zřídka, ale mají velký dopad na celou organizaci a vyžadují vysokou úroveň dovedností, se před delegováním považují za velmi pečlivě. Místo toho můžete [dočasně zvýšit účet na požadovanou roli](../privileged-identity-management/pim-configure.md) nebo znovu přiřadit úlohu.

## <a name="delegate-app-administration"></a>Delegování správy aplikace

Navýšení aplikací v rámci vaší organizace může mít za svůj model delegování. Pokud dojde k zatížení pro správu přístupu k aplikacím u globálního správce, je pravděpodobnější, že model zvyšuje režii podle času. Pokud jste uživatelům udělili roli globálního správce pro věci, jako je třeba konfigurace podnikových aplikací, můžete je nyní převzít na následující méně privilegované role. Díky tomu je možné zlepšit stav zabezpečení a omezit potenciál unfortunate chyb. Role Správce aplikací s největším oprávněním jsou:

* Role **Správce aplikací** , která uděluje možnost spravovat všechny aplikace v adresáři, včetně registrací, nastavení jednotného přihlašování, přiřazení uživatelů a skupin a licencí, nastavení proxy aplikací a souhlasu. Neuděluje možnost spravovat podmíněný přístup.
* Role **správce cloudové aplikace** , která uděluje všem schopnostem správce aplikace, s výjimkou toho, že neuděluje přístup k nastavení proxy aplikace (protože nemá žádné místní oprávnění).

## <a name="delegate-app-registration"></a>Registrace aplikace delegování

Ve výchozím nastavení mohou registraci aplikací vytvářet všichni uživatelé. Chcete-li selektivně udělit možnost vytvářet registrace aplikací:

* Nastavení **uživatelů může registrovat aplikace bez použití** **uživatelského nastavení**
* Přiřazení uživatele k roli vývojáře aplikace

Chcete-li selektivně udělit možnost souhlasu s povolením aplikace získat přístup k datům:

* Nastavení **uživatelů můžou udělit souhlas s aplikacemi, které přistupují k firemním datům** , a to bez jakýchkoli **uživatelských nastavení** .
* Přiřazení uživatele k roli vývojáře aplikace

Když vývojář aplikace vytvoří novou registraci aplikace, automaticky se přidá jako první vlastník.

## <a name="delegate-app-ownership"></a>Delegování vlastnictví aplikace

U souběžně podrobnějšího přístupu k aplikacím můžete přiřadit vlastnictví individuálním podnikovým aplikacím. Tím se doplňují stávající podpora pro přiřazování vlastníků registrace aplikací. Vlastnictví je přiřazeno na bázi jednotlivých podnikových aplikací v okně podnikové aplikace. Výhodou je, že vlastníci můžou spravovat jenom podnikové aplikace, které vlastní. Můžete například přiřadit vlastníka aplikace Salesforce a tento vlastník může spravovat přístup ke službě Salesforce a jejich konfiguraci a žádné jiné aplikace. Podniková aplikace může mít mnoho vlastníků a uživatel může být vlastníkem pro mnoho podnikových aplikací. K dispozici jsou dvě role vlastníka aplikace:

* Role **vlastníka podnikové aplikace** uděluje možnost spravovat podnikové aplikace, které uživatel vlastní, včetně nastavení jednotného přihlašování, přiřazení uživatelů a skupin a přidávání dalších vlastníků. Neuděluje možnost spravovat nastavení proxy aplikací ani podmíněný přístup.
* Role **vlastníka registrace aplikace** uděluje možnost spravovat registrace aplikací pro aplikaci, které uživatel vlastní, včetně manifestu aplikace a přidání dalších vlastníků.

## <a name="develop-a-security-plan"></a>Vývoj plánu zabezpečení

Azure AD poskytuje rozsáhlý průvodce plánováním a prováděním plánu zabezpečení v rolích správce Azure AD a [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení](security-planning.md).

## <a name="establish-emergency-accounts"></a>Navázání mimořádných účtů

Chcete-li zachovat přístup k úložišti správy identit, když vzniká problém, připravte účty pro nouzový přístup v souladu s [vytvářením účtů pro správu pro nouzový přístup](security-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Zabezpečení rolí správce

Útočníci, kteří získají kontrolu nad privilegovanými účty, můžou dělat obrovský škodu, takže tyto účty nejdřív ochráníte pomocí [zásad přístupu podle směrného plánu](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) , které jsou ve výchozím nastavení dostupné pro všechny organizace Azure AD (ve verzi Public Preview). Tato zásada vynutila vícefaktorové ověřování u privilegovaných účtů Azure AD. Následující role Azure AD jsou pokryté zásadami standardních hodnot Azure AD:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení

## <a name="elevate-privilege-temporarily"></a>Dočasné zvýšení oprávnění

Pro většinu každodenních činností není nutné, aby všichni uživatelé měli oprávnění globálního správce, a ne všechny z nich museli být trvale přiřazeni k roli globálního správce. Když uživatelé potřebují oprávnění globálního správce, měli by aktivovat přiřazení role v Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md) na svém vlastním účtu nebo alternativním účtu správce.

## <a name="next-steps"></a>Další kroky

Odkaz na popis role Azure AD najdete v tématu [přiřazení rolí správce ve službě Azure AD](permissions-reference.md) .