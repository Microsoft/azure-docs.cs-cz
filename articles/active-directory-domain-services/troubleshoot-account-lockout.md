---
title: Řešení potíží s uzamknutím účtu v Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné problémy, které způsobují, že uživatelské účty budou v Azure Active Directory Domain Services uzamčeny.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 3341f290a5a5bb169b6e70ea22459a2afafedbbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103198960"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Řešení problémů s uzamknutím účtu pomocí spravované domény Azure Active Directory Domain Services

Aby nedocházelo k opakovaným pokusům o přihlášení, účty spravované domény Azure Active Directory Domain Services (Azure služba AD DS) se po definované prahové hodnotě zamkne. K uzamčení účtu může dojít i v případě nehody bez incidentu útoku na přihlášení. Pokud třeba uživatel opakovaně zadá nesprávné heslo nebo se služba pokusí použít původní heslo, účet se zamkne.

V tomto článku najdete informace o tom, proč dochází k uzamčení účtu a jak můžete nakonfigurovat chování a jak zkontrolovat audity zabezpečení pro řešení potíží s událostmi uzamčení.

## <a name="what-is-an-account-lockout"></a>Co je uzamčení účtu?

Uživatelský účet ve spravované doméně Azure služba AD DS je uzamčený, když byla splněna definovaná prahová hodnota pro neúspěšné pokusy o přihlášení. Toto chování uzamčení účtu je navrženo tak, aby vás chránilo před opakovanými pokusy o přihlašování hrubou silou, které můžou znamenat automatizovaný digitální útok.

**Ve výchozím nastavení platí, že pokud se v 2 minutách vyskytne 5 špatný počet pokusů o zadání hesla, účet se zablokuje na 30 minut.**

Výchozí prahové hodnoty pro uzamknutí účtu se konfigurují pomocí jemně odstupňovaných zásad hesel. Pokud máte konkrétní sadu požadavků, můžete tyto výchozí prahové hodnoty uzamčení účtu přepsat. Nedoporučuje se ale zvyšovat prahové limity, abyste se pokusili snížit počet uzamčení účtů. Nejprve vyřešte potíže se zdrojem uzamčení účtu.

### <a name="fine-grained-password-policy"></a>Jemně odstupňované zásady hesel

Jemně odstupňované zásady hesel (FGPPs) umožňují použít specifická omezení pro zásady hesel a uzamčení účtů pro různé uživatele v doméně. Podrobné zásady má vliv jenom na uživatele ve spravované doméně. Uživatelé cloudu a uživatelé domény synchronizovaný do spravované domény ze služby Azure AD jsou ovlivněny pouze zásadami hesel v rámci spravované domény. Jejich účty ve službě Azure AD nebo v místním adresáři nejsou ovlivněny.

Zásady jsou distribuovány prostřednictvím přidružení skupiny ve spravované doméně a veškeré provedené změny se uplatní při přihlášení dalšího uživatele. Změna zásad neodemkne uživatelský účet, který je už uzamčený.

Další informace o podrobných zásadách pro hesla a rozdílech mezi uživateli vytvořenými přímo v Azure služba AD DS v porovnání se službou Azure AD najdete v tématu [Konfigurace zásad hesel a uzamčení účtů][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Běžné důvody uzamčení účtu

Nejčastější důvody, proč je účet uzamčený bez jakéhokoli škodlivého záměru nebo faktorů, zahrnují následující scénáře:

* **Uživatel byl uzamčen.**
    * Uživatel po změně hesla pokračoval v používání předchozího hesla? Výchozí zásady uzamčení účtů o pět neúspěšných pokusů za 2 minuty můžou být způsobeny tím, že uživatel nechtěně opakuje původní heslo.
* **Existuje aplikace nebo služba, které mají staré heslo.**
    * Pokud se účet používá v aplikacích nebo službách, můžou se tyto prostředky opakovaně pokoušet přihlásit pomocí starého hesla. Toto chování způsobí, že dojde k uzamknutí účtu.
    * Zkuste minimalizovat použití účtu napříč několika různými aplikacemi nebo službami a zaznamenejte si, kde se používají přihlašovací údaje. Pokud dojde ke změně hesla účtu, aktualizujte odpovídající aplikace nebo služby.
* **Heslo bylo změněno v jiném prostředí a nové heslo dosud nebylo synchronizováno.**
    * Pokud se heslo účtu změní mimo spravovanou doménu, třeba v prostředí Prem služba AD DS, může trvat několik minut, než se změna hesla synchronizuje přes Azure AD a do spravované domény.
    * Uživatel, který se pokusí přihlásit k prostředku ve spravované doméně před dokončením procesu synchronizace hesel způsobí, že dojde k uzamknutí účtu.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Řešení potíží s uzamčeními účtů pomocí auditů zabezpečení

Pokud chcete řešit potíže, když dojde k událostem uzamčení účtu a odkud přicházejí, [Povolte audity zabezpečení pro Azure služba AD DS][security-audit-events]. Události auditu se zaznamenávají jenom z doby, kdy tuto funkci povolíte. V ideálním případě byste měli povolit audity zabezpečení *předtím, než* dojde k problému s uzamknutím účtu. Pokud má uživatelský účet opakovaně problémy s uzamčením, můžete povolit audity zabezpečení, které jsou připravené k dalšímu výskytu situace.

Po povolení auditů zabezpečení se v následujících ukázkových dotazech ukáže, jak zkontrolovat *události uzamčení účtu*, kód *4740*.

Zobrazit všechny události uzamčení účtu za posledních sedm dní:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Zobrazit všechny události uzamčení účtu za posledních sedm dní pro účet s názvem *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Zobrazit všechny události uzamčení účtu v rozmezí 26. června 2020 v 9:00 a od 1. července 2020 o půlnoci seřazené vzestupně podle data a času:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**Poznámka**

Můžete najít podrobnosti o události 4776 a 4740 v části "zdrojová pracovní stanice:", která je prázdná. Důvodem je to, že při přihlášení k síti přes některá jiná zařízení došlo k chybnému heslu.
Příklad: Pokud máte server RADIUS, který může přeposlání ověřování do služby AAD DS. Pokud chcete potvrdit, že se má povolit back-end protokolu RDP na řadiči domény nakonfigurovat protokoly Netlogon

03/04 19:07:29 [přihlášení] [10752] contoso: SamLogon: bylo zadáno přenosné přihlášení ze sítě contoso\Nagappan.Veerappan z (Via LOB11-RADIUS). 

03/04 19:07:29 [přihlášení] [10752] contoso: SamLogon: přihlášení přesná síť contoso\Nagappan.Veerappan z (přes LOB11-RADIUS) vrátí 0xC000006A.

03/04 19:07:35 [přihlášení] [10753] contoso: SamLogon: bylo zadáno přenosné přihlášení ze sítě contoso\Nagappan.Veerappan z (Via LOB11-RADIUS). 

03/04 19:07:35 [přihlášení] [10753] contoso: SamLogon: přihlášení přesná síť contoso\Nagappan.Veerappan z (přes LOB11-RADIUS) vrátí 0xC000006A.

Povolte protokol RDP pro vaše řadiče domény v NSG do back-endu pro konfiguraci zachycení diagnostiky (tj. Netlogon) https://docs.microsoft.com/azure/active-directory-domain-services/alert-nsg#inbound-security-rules Pokud jste už změnili výchozí NSG, sledujte prosím PSlet způsob, jak povolit. https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#port-3389---management-using-remote-desktop

Povolení protokolu Netlogon na jakémkoli serveru https://docs.microsoft.com/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service

## <a name="next-steps"></a>Další kroky

Další informace o jemně odstupňovaných zásadách pro hesla pro úpravu prahových hodnot uzamčení účtů najdete v tématu [Konfigurace zásad hesel a uzamčení účtů][configure-fgpp].

Pokud stále máte problémy s připojením k VIRTUÁLNÍmu počítači ke spravované doméně, [Najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
