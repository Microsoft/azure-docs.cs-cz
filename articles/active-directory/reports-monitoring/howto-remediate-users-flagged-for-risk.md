---
title: Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db4c2fc0781225af6cc59df72467a69e0ba73e1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189548"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Oprava uživatelů označených příznakem rizika na portálu Azure Active Directory

Sestavy zabezpečení v Azure Active Directory (Azure AD) můžete odhadnout pravděpodobnosti ohrožení uživatelských účtů ve vašem prostředí. Uživatele označené příznakem rizika je indikátorem uživatelského účtu, který mohl být ohrožený.

Prioritou společnosti Microsoft je přispívat k udržení zabezpečení vašich prostředí. V rámci této snahy Microsoft neustále monitoruje aktivity, které jsou neobvyklé nebo odpovídají známým vzorcům útoku. 

Pokud se zjistí neobvyklé aktivity, které můžou značit neoprávněný přístup k některým z vaší uživatelské účty, obdržíte oznámení, abyste k akci. To neznamená, že byl ohrožení vlastních systémů Microsoftu.

## <a name="access-the-users-flagged-for-risk-report"></a>Zpřístupnění sestavy uživatelů označených příznakem rizika

Uživatelé označení příznakem rizika prostřednictvím můžete zkontrolovat [sestava ohrožených uživatelů](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) na webu Azure Portal. Pokud nemáte Azure AD, které můžete ZDARMA zaregistrovat v [ https://aka.ms/AccessAAD ](https://aka.ms/AccessAAD). 

Od uživatelů označených příznakem rizika sestavy můžete provést následující akce pro každého uživatele:

- Vygenerovat dočasné heslo
- Vyžádat, aby si uživatel při příštím přihlášení bezpečně resetoval heslo
- Zavřít riziko uživatele bez provedení nápravné akce

Další informace najdete v tématu [sestava zabezpečení ohrožení uživatelé označení příznakem](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Předplatné služby Azure AD pro zákazníky Office 365

Můžete také použít svoje přihlašovací údaje Office 365 pro přístup k **centra pro správu Azure**. Po aktivaci přístupu ke službě Azure AD budete přesměrováni na portál služby Azure AD. Na úrovni základního předplatného je v sestavách uvedené omezené množství podrobností. Pro předplatitele Azure na úrovni Premium jsou k dispozici další data a analýzy.

Pro přístup **uživatelé označení příznakem rizika** sestavy v Centru pro správu Office 365:

1.  V navigační nabídce na levé straně vyberte **centra pro správu**. 
2.  Vyberte **Azure AD**.
3.  Přihlaste se do **Centra pro správu Azure Active Directory**.
4.  Pokud se zobrazí banner v horní části stránky, která uvádí, že **podívejte se na novém portálu**, vyberte odkaz.
4.  V navigační nabídce na levé straně vyberte **Azure Active Directory**. 
5.  V navigačním podokně vyberte **uživatelé označení příznakem rizika** z **zabezpečení** oddílu.

## <a name="remediation-actions"></a>Nápravné akce

Provedení následujících akcí vám pomůže napravit ovlivněné účty a zabezpečit vaše prostředí:

1.  [Ověření správné informace](https://aka.ms/MFAValid) pro vícefaktorové ověřování a samoobslužné služby heslo resetovat. 
2.  [Povolení služby Multi-Factor authentication](https://aka.ms/MFAuth) pro všechny uživatele. 
3.  Použijte tento [skript pro nápravu](https://aka.ms/remediate) pro všechny ovlivněné účty automaticky provádět následující kroky: 

    a. Resetováním hesla zabezpečit účet a ukončit aktivní relace.

    b. Odstranit delegáty poštovní schránky.

    c. Zakázat pravidla předávání e-mailů do externích domén.

    d. Odebrat z poštovní schránky globální vlastnost předávání e-mailů.

    e. Povolit MFA pro účet uživatele.

    f. Nastavit vysokou složitost hesla účtu.

    g. Povolit auditování poštovní schránky.

    h. Vytvoření protokolu auditu pro správce ke kontrole.

4. Prozkoumejte svého tenanta Office 365 a další IT infrastrukturu, včetně kontroly případných úprav všech nastavení tenanta, uživatelských účtů a nastavení konfigurace jednotlivých uživatelů. Zkontrolujte indikátory metod trvalosti a také indikátory, že útočník mohl k získání přihlašovacích údajů sítě VPN nebo přístupu k jiným prostředkům organizace využít počáteční základnu. 

5.  V rámci šetření zvažte, zda by měl upozornit orgány veřejné správy, včetně vymáhání zákona.

Kromě toho byste měli provést následující:

- Přečtěte si a implementovat tyto [doprovodné materiály pro řešení neobvyklých aktivit](https://aka.ms/fixaccount). 
- [Povolit kanál auditu](https://aka.ms/improvesecurity) umožňují analyzovat aktivity ve vašem tenantovi. Jakmile budete hotovi, vaše úložiště auditu začne naplňovat protokoly aktivit. V tomto okamžiku můžete také využít [zabezpečení a dodržování předpisů Center vyhledávání a šetření prostředků](https://aka.ms/sccsearch). 
- Použijte tento [skriptu povolit auditování poštovní schránky](https://aka.ms/mailboxaudit1) pro všechny vaše účty. 
- Zkontrolovat oprávnění delegáta a pravidla předávání e-mailů pro všechny vaše poštovní schránky. Tuto úlohu můžete provést pomocí tohoto [skriptu PowerShellu](https://aka.ms/delegateforwardrules). 

## <a name="next-steps"></a>Další postup

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Uživatelé označení příznakem rizika](concept-user-at-risk.md)
