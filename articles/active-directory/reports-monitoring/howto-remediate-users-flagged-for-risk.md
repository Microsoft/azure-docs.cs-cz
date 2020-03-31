---
title: Uživatelé označeni za rizika na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30d02c5484ea4cce2953eac6b1b7b26a17c142bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68989701"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Oprava uživatelů označených příznakem rizika na portálu Azure Active Directory

Pomocí sestav zabezpečení ve službě Azure Active Directory (Azure AD) můžete odhadnout pravděpodobnost ohrožení uživatelských účtů ve vašem prostředí. Uživatel označený příznakem rizika je indikátorem uživatelského účtu, který mohl být ohrožený.

Prioritou společnosti Microsoft je přispívat k udržení zabezpečení vašich prostředí. V rámci této snahy Microsoft neustále monitoruje aktivity, které jsou neobvyklé nebo odpovídají známým vzorcům útoku. 

Pokud jsou zjištěny neobvyklé aktivity, které mohou znamenat neoprávněný přístup k některým účtům uživatelů, obdržíte oznámení, která vám umožní provést akci. To neznamená, že vlastní systémy společnosti Microsoft byly ohroženy.

## <a name="access-the-users-flagged-for-risk-report"></a>Zpřístupnění sestavy uživatelů označených příznakem rizika

Můžete zkontrolovat uživatele označené pro riziko prostřednictvím [sestavy ohrožených uživatelů](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) na webu Azure Portal. Pokud nemáte Azure AD, můžete se zaregistrovat [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD)zdarma na . 

U uživatelů označených pro sestavu rizik můžete pro každého uživatele provést následující akce:

- Vygenerovat dočasné heslo
- Vyžádat, aby si uživatel při příštím přihlášení bezpečně resetoval heslo
- Zavřít riziko uživatele bez provedení nápravné akce

Další informace naleznete [v tématu Uživatelé označeni příznakem pro zprávu o zabezpečení rizik](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Předplatné služby Azure AD pro zákazníky Office 365

Přihlašovací údaje Office 365 můžete taky použít k přístupu do **Centra pro správu Azure**. Po aktivaci přístupu ke službě Azure AD budete přesměrováni na portál služby Azure AD. Na úrovni základního předplatného je v sestavách uvedené omezené množství podrobností. Pro předplatitele Azure na úrovni Premium jsou k dispozici další data a analýzy.

Přístup k **uživatelům označeným příznakem pro** sestavy rizik v Centru pro správu Microsoftu 365:

1.  V navigační nabídce na levé straně vyberte **Centra pro správu**. 
2.  Vyberte **Azure AD**.
3.  Přihlaste se do **Centra pro správu Azure Active Directory**.
4.  Pokud se v horní části stránky zobrazí nápis **Přehled nového portálu**, vyberte odkaz.
4.  V navigační nabídce na levé straně vyberte **Azure Active Directory**. 
5.  V navigačním podokně včásti **Zabezpečení** vyberte **možnost Uživatelé označeni příznakem pro riziko.**

## <a name="remediation-actions"></a>Nápravné akce

Provedení následujících akcí vám pomůže napravit ovlivněné účty a zabezpečit vaše prostředí:

1.  [Ověřte správné informace](https://aka.ms/MFAValid) pro vícefaktorové ověřování a samoobslužné resetování hesla. 
2.  [Povolte vícefaktorové ověřování](https://aka.ms/MFAuth) pro všechny uživatele. 
3.  Tento [nápravný skript](https://aka.ms/remediate) použijte pro každý ovlivněný účet k automatickému provedení následujících kroků: 

    a. Resetujte heslo pro zabezpečení účtu a zakažte aktivní relace.

    b. Odstranit delegáty poštovní schránky.

    c. Zakázat pravidla předávání e-mailů do externích domén.

    d. Odebrat z poštovní schránky globální vlastnost předávání e-mailů.

    e. Povolit MFA pro účet uživatele.

    f. Nastavit vysokou složitost hesla účtu.

    g. Povolit auditování poštovní schránky.

    h. Vytvoření protokolu auditu, který má správce zkontrolovat.

4. Prozkoumejte svého tenanta Office 365 a další IT infrastrukturu, včetně kontroly případných úprav všech nastavení tenanta, uživatelských účtů a nastavení konfigurace jednotlivých uživatelů. Zkontrolujte indikátory metod trvalosti a také indikátory, že útočník mohl k získání přihlašovacích údajů sítě VPN nebo přístupu k jiným prostředkům organizace využít počáteční základnu. 

5.  V rámci vyšetřování zvažte, zda byste měli informovat vládní orgány, včetně orgánů činných v trestním řízení.

Kromě toho byste měli provést následující:

- Přečtěte si a implementujte tyto [pokyny pro řešení neobvyklých činností](https://aka.ms/fixaccount). 
- [Povolte kanál auditu,](https://aka.ms/improvesecurity) který vám pomůže analyzovat aktivitu ve vašem tenantovi. Po dokončení úložiště auditu začne naplnění protokoly aktivit. V tomto okamžiku můžete také využít [zdroje pro vyhledávání a šetření Centra pro zabezpečení a dodržování předpisů](https://aka.ms/sccsearch). 
- Tento skript slouží [k povolení auditování poštovních schránek](https://aka.ms/mailboxaudit1) pro všechny vaše účty. 
- Zkontrolovat oprávnění delegáta a pravidla předávání e-mailů pro všechny vaše poštovní schránky. Tuto úlohu můžete provést pomocí tohoto [skriptu PowerShellu](https://aka.ms/delegateforwardrules). 

## <a name="next-steps"></a>Další kroky

* [Ochrana identit služby Azure Active Directory](../active-directory-identityprotection.md)
* [Uživatelé označení příznakem rizika](concept-user-at-risk.md)
