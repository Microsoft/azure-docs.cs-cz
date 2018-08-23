---
title: Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 385106065322cae9b8e29f66bfd6920d4ebb52f8
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42056585"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Oprava uživatelů označených příznakem rizika na portálu Azure Active Directory

Sestavy zabezpečení v Azure Active Directory (Azure AD) umožňují získat přehled o pravděpodobnosti ohrožení uživatelských účtů ve vašem prostředí. [Uživatel označený příznakem rizika](../identity-protection/overview.md#users-flagged-for-risk) je indikátorem uživatelského účtu, který mohl být ohrožený.

Prioritou společnosti Microsoft je přispívat k udržení zabezpečení vašich prostředí. V rámci této snahy Microsoft neustále monitoruje aktivity, které jsou neobvyklé nebo odpovídají známým vzorcům útoku. 


Pokud dojde ke zjištění neobvyklých aktivit, které můžou značit neoprávněný přístup k účtům některých vašich uživatelů, obdržíte oznámení, abyste mohli zakročit. Poskytování oznámení neznamená jakékoli ohrožení vlastních systémů Microsoftu.
 

## <a name="access-the-users-flagged-for-risk-report"></a>Zpřístupnění sestavy uživatelů označených příznakem rizika

Uživatele označené příznakem rizika si můžete prohlédnout v související [sestavě](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk) ve službě Azure Active Directory (AD). Pokud nemáte předplatné služby Azure AD, můžete si ji zdarma jednorázově předplatit na adrese [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). V této sestavě můžete provádět různé akce, například:

- Vygenerovat dočasné heslo
- Vyžádat, aby si uživatel při příštím přihlášení bezpečně resetoval heslo
- Zavřít riziko uživatele bez provedení nápravné akce

Další informace najdete v tématu věnovaném [sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Předplatné služby Azure AD pro zákazníky Office 365

Jakmile budete hotovi, můžete pro přístup k Centru pro správu Azure použít své přihlašovací údaje Office 365. Po aktivaci přístupu ke službě Azure AD budete přesměrováni na portál služby Azure AD. Na úrovni základního předplatného je v sestavách uvedené omezené množství podrobností. Pro předplatitele Azure na úrovni Premium jsou k dispozici další data a analýzy.


**Přístup k sestavám Uživatelé označení příznakem rizika v Centru pro správu Office 365:**

1.  V navigační nabídce na levé straně klikněte na **Centra pro správu**. 
2.  Klikněte na **Azure AD**.
3.  Přihlaste se do **Centra pro správu Azure Active Directory**.
4.  Pokud se v horní části stránky zobrazí banner se zprávou **Vyzkoušejte nový portál**, klikněte na odkaz.
4.  V navigační nabídce na levé straně klikněte na **Azure Active Directory**. 
5.  V navigačním podokně v části **Zabezpečení** klikněte na **Uživatelé označení příznakem rizika**.

Zkontrolujte informace, které se zde zobrazí. U všech uvedených účtů byste měli resetovat heslo. 

## <a name="remediation-actions"></a>Nápravné akce

Provedení následujících akcí vám pomůže napravit ovlivněné účty a zabezpečit vaše prostředí:

1.  [Ověřte](http://aka.ms/MFAValid) správnost informací pro vícefaktorové ověřování a samoobslužné resetování hesla. 
2.  [Povolte](http://aka.ms/MFAuth) vícefaktorové ověřování pro všechny uživatele. 
3.  Pomocí tohoto [nápravného skriptu](http://aka.ms/remediate) můžete pro všechny ovlivněné účty automaticky provádět následující kroky: 

    a. Resetováním hesla zabezpečit účet a ukončit aktivní relace.

    b. Odstranit delegáty poštovní schránky.

    c. Zakázat pravidla předávání e-mailů do externích domén.

    d. Odebrat z poštovní schránky globální vlastnost předávání e-mailů.

    e. Povolit MFA pro účet uživatele.

    f. Nastavit vysokou složitost hesla účtu.

    g. Povolit auditování poštovní schránky.

    h. Vygenerovat protokol auditu, který může správce zkontrolovat.

4. Prozkoumejte svého tenanta Office 365 a další IT infrastrukturu, včetně kontroly případných úprav všech nastavení tenanta, uživatelských účtů a nastavení konfigurace jednotlivých uživatelů. Zkontrolujte indikátory metod trvalosti a také indikátory, že útočník mohl k získání přihlašovacích údajů sítě VPN nebo přístupu k jiným prostředkům organizace využít počáteční základnu. 

5.  V rámci šetření zvažte, jestli není vhodné nebo nutné upozornit orgány veřejné správy, včetně orgánů činných v trestním řízení.

Kromě toho byste měli provést následující:

- Přečíst si a implementovat tyto [pokyny](http://aka.ms/fixaccount) pro řešení neobvyklých aktivit. 
- [Povolit kanál auditu](http://aka.ms/improvesecurity), který vám pomůže analyzovat aktivity ve vašem tenantovi. Jakmile budete hotovi, vaše úložiště auditu se začne naplňovat protokoly aktivit. Teď můžete využít [vyhledávání a šetření v centru zabezpečení a dodržování předpisů](http://aka.ms/sccsearch). 
- Pomocí tohoto [skriptu](http://aka.ms/mailboxaudit1) povolit auditování poštovní schránky pro všechny vaše účty. 
- Zkontrolovat oprávnění delegáta a pravidla předávání e-mailů pro všechny vaše poštovní schránky. Tuto úlohu můžete provést pomocí tohoto [skriptu PowerShellu](http://aka.ms/delegateforwardrules). 



## <a name="next-steps"></a>Další postup

- Další informace o Azure Active Directory Identity Protection najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

