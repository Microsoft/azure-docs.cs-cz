---
title: Poradce při potížích se uzamčením účtu ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit běžné problémy, které způsobují uzamčení uživatelských účtů ve službě Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 2e274aa353f6c3e485ae10a6a67ee2940eb88b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246312"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Poradce při potížích se uzamčením účtu se spravovanou doménou služby Azure AD Domain Services

Chcete-li zabránit opakovaným pokusům o přihlášení se škodlivým způsobem, služba Azure AD DS uzamkne účty po definované prahové hodnotě. K tomuto uzamčení účtu může dojít také náhodou bez incidentu s přihlašovacím útokem. Pokud například uživatel opakovaně zadá nesprávné heslo nebo se služba pokusí použít staré heslo, účet se uzamkne.

Tento článek o řešení potíží popisuje, proč dochází k uzamčení účtů a jak můžete nakonfigurovat chování a jak zkontrolovat audity zabezpečení k řešení událostí uzamčení.

## <a name="what-is-an-account-lockout"></a>Co je uzamčení účtu?

Uživatelský účet ve službě Azure AD DS je uzamčen, když byla splněna definovaná prahová hodnota pro neúspěšné pokusy o přihlášení. Toto chování uzamčení účtu je navržentak, aby vás chránil před opakovanými pokusy o přihlášení hrubou silou, které mohou naznačovat automatický digitální útok.

**Ve výchozím nastavení, pokud je za 2 minuty 5 pokusů o chybné heslo, je účet uzamčen po dobu 30 minut.**

Výchozí prahové hodnoty uzamčení účtu jsou konfigurovány pomocí zásad jemných hesel. Pokud máte určitou sadu požadavků, můžete přepsat tyto výchozí prahové hodnoty uzamčení účtu. Nedoporučuje se však zvýšit limity prahových hodnot a pokusit se snížit uzamčení účtu. Nejprve vyřešte zdroj chování uzamčení účtu.

### <a name="fine-grained-password-policy"></a>Zásady jemně odstupňovaných hesel

Jemně odstupňované zásady hesel (FGPP) umožňují použít konkrétní omezení pro zásady uzamčení hesla a účtu pro různé uživatele v doméně. FGPP ovlivňuje jenom uživatele vytvořené v Azure AD DS. Zásady hesel nemají vliv na uživatele cloudu a uživatele domény synchronizované do spravované domény Azure AD DS ze služby Azure AD.

Zásady jsou distribuovány prostřednictvím přidružení skupiny ve spravované doméně Azure AD DS a všechny změny, které provedete, se použijí při příštím přihlášení uživatele. Změnou zásadse neodemkne uživatelský účet, který je již uzamčen.

Další informace o jemně odstupňovaných zásadách hesel naleznete v [tématu Konfigurace zásad uzamčení hesel a účtů][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Běžné důvody uzamčení účtu

Mezi nejčastější důvody uzamčení účtu bez škodlivých úmyslů nebo faktorů patří následující scénáře:

* **Uživatel se zamkl.**
    * Po nedávné změně hesla, má uživatel i nadále používat předchozí heslo? Výchozí zásady uzamčení účtu 5 neúspěšných pokusů za 2 minuty může být způsobena uživatelem neúmyslně opakování staré heslo.
* **Existuje aplikace nebo služba, která má staré heslo.**
    * Pokud je účet používán aplikacemi nebo službami, mohou se tyto prostředky opakovaně pokoušet přihlásit pomocí starého hesla. Toto chování způsobí, že účet uzamčen.
    * Pokuste se minimalizovat používání účtu v různých aplikacích nebo službách a zaznamenejte, kde se používají pověření. Pokud dojde ke změně hesla účtu, aktualizujte odpovídajícím způsobem přidružené aplikace nebo služby.
* **Heslo bylo změněno v jiném prostředí a nové heslo ještě nebylo synchronizováno.**
    * Pokud se změní heslo účtu mimo Azure AD DS, například v prostředí služby AD DS prem, může trvat několik minut, než se změna hesla synchronizuje prostřednictvím služby Azure AD a do služby Azure AD DS.
    * Uživatel, který se pokusí přihlásit k prostředku prostřednictvím Služby Azure AD DS před dokončením tohoto procesu synchronizace hesel způsobí, že jejich účet bude uzamčen.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Poradce při potížích s uzamčením účtu pomocí auditů zabezpečení

Chcete-li vyřešit řešení potíží s událostmi uzamčení účtu a odkud pocházejí, [povolte audity zabezpečení pro službu Azure AD DS][security-audit-events]. Události auditu jsou zachyceny pouze od okamžiku, kdy povolíte funkci. V ideálním případě byste měli povolit audity zabezpečení *dříve, než* dojde k problému uzamčení účtu k řešení potíží. Pokud má uživatelský účet opakovaně problémy se uzamčením, můžete povolit audity zabezpečení připravené pro další situaci.

Po povolení auditů zabezpečení následující ukázkové dotazy ukazují, jak zkontrolovat *události uzamčení účtu*, kód *4740*.

Zobrazení všech událostí uzamčení účtu za posledních sedm dní:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Zobrazení všech událostí uzamčení účtu za posledních sedm dní pro účet s názvem *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Zobrazit všechny události uzamčení účtu mezi 26. a 1. července 2019 o půlnoci, seřazeno vzestupně podle data a času:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Další kroky

Další informace o jemně odstupňovaných zásadách hesel pro úpravu prahových hodnot uzamčení účtu naleznete v [tématu Konfigurace zásad uzamčení hesla a účtu][configure-fgpp].

Pokud máte pořád problémy s připojením virtuálního počítače ke spravované doméně Azure AD DS, [najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
