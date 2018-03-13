---
title: "Azure Active Directory Connect: Časté otázky – | Microsoft Docs"
description: "Tato stránka obsahuje časté otázky k Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: billmath
ms.openlocfilehash: 07b0209ef94f91c00b98b8801323a58cd9d14494
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Nejčastější dotazy pro Azure Active Directory Connect

## <a name="general-installation"></a>Obecné instalace
**Otázka: bude fungovat instalace Azure AD globálního správce má 2FA povolené?**  
S sestavení z února 2016 to je podporováno.

**Otázka: je způsob, jak nainstalovat Azure AD Connect bezobslužné?**  
Je podporován pouze pro instalaci Azure AD Connect s použitím Průvodce instalací. Bezobslužné a bezobslužné instalace není podporována.

**Otázka: je nutné do doménové struktury, kde nelze kontaktovat jednu doménu. Instalace Azure AD Connect**  
S sestavení z února 2016 to je podporováno.

**Otázka: agenta stavu služby AD DS funguje na jádro serveru?**  
Ano. Po instalaci agenta, můžete dokončit proces registrace pomocí následující rutiny prostředí PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Otázka: AADConnect podporuje synchronizace ze dvou domén na Azure AD?**</br>
Ano, tato možnost je podporována. Odkazovat na [více domén](active-directory-aadconnect-multiple-domains.md)
 
**Otázka: jsme podporují s více konektorů pro stejné domény služby Active Directory ve službě Azure AD connect?**</br> Ne, to není podporováno 

## <a name="network"></a>Síť
**Otázka: je nutné bránu firewall, síťové zařízení, nebo něco jiného, který omezuje maximální dobu, po připojení můžete zůstat otevřené v mé síti. Jak dlouho mají Moje prahová hodnota časového limitu straně klienta se při použití Azure AD Connect?**  
Všechny sítě softwaru, fyzických zařízení nebo cokoliv jiného, který omezuje maximální dobu, po připojení může zůstat otevřené by měl používat prahovou hodnotu alespoň 5 minut (300 sekund) pro připojení mezi serverem, kde je nainstalován klient Azure AD Connect a Azure Active Directory. To platí také pro všechny dřív vydaných synchronizace nástroje Microsoft Identity.

**Otázka: jsou podporovány domény SLD (jedné domény štítek)?**  
Ne, Azure AD Connect místními doménovými strukturami nebo doménami pomocí domény SLD nepodporuje.

**Otázka: jsou doménových strukturách s nesouvislým oborem doménami AD podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami obsahující nesouvislé obory názvů.

**Otázka: je "s tečkami" s názvem pro rozhraní NetBios podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami nebo doménami, kde název NetBios obsahuje tečku "." v názvu.

**Otázka: je čistě IPv6 prostředí podporovaná?**  
Ne, Azure AD Connect nepodporuje čistý prostředí IPv6.

## <a name="federation"></a>metadata
**Otázka: Co mám dělat, když se zobrazí e-mailu, zpráva se žádostí o obnovení certifikátu Moje Office 365**  
Použijte pokyny, které je uvedené v [obnovení certifikátů](active-directory-aadconnect-o365-certs.md) téma o tom, jak obnovit certifikát.

**Otázka: je nutné "Automaticky aktualizovat předávající strany" sadu O365 předávající strany. Je nutné provádět žádnou akci, když můj automaticky podpisový certifikát tokenu navyšování?**  
Použijte pokyny, které je popsané v článku [obnovení certifikátů](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Prostředí
**Otázka: je podporována přejmenování serveru po instalaci Azure AD Connect?**  
Ne. Změna názvu serveru způsobí, že synchronizační modul nebude moct připojit k databázi SQL a služba nebude možné spustit.

## <a name="identity-data"></a>Data identit
**Otázka: vybraný atribut UPN (userPrincipalName) ve službě Azure AD neodpovídá názvu UPN místní - proč?**  
Najdete v těchto článcích:

* [Uživatelská jména v Office 365, Azure nebo Intune se neshodují místní UPN nebo alternativním přihlašovacím ID](https://support.microsoft.com/en-us/kb/2523192)
* [Změny nejsou synchronizovány pomocí nástroje Azure Active Directory Sync po změně UPN uživatelský účet používat jiné federované domény](https://support.microsoft.com/en-us/kb/2669550)

Můžete také konfigurovat Azure AD umožňuje synchronizační modul aktualizovat userPrincipalName, jak je popsáno v [funkce služby Azure AD Connect sync](active-directory-aadconnectsyncservice-features.md).

**Otázka: je to, že nepodporuje logicky shodu místní AD skupiny nebo kontaktu objekty s existující objekty Azure AD skupiny nebo kontaktu?**  
Ano, to budou založeny na proxyAddress.  Logicky odpovídající není podporována pro skupiny, které nejsou povoleným e-mailem.

**Otázka: je to, že nepodporuje ručně nastavit atribut ImmutableId u stávajících objektů Azure AD skupiny nebo kontaktu pevného odpovídat jej do místní AD skupiny nebo kontaktu objekty?**  
Ne, to není aktuálně podporováno.

## <a name="custom-configuration"></a>Vlastní konfigurace
**Otázka: kde popsané rutiny prostředí PowerShell pro Azure AD Connect?**  
S výjimkou rutiny popsané v této lokalitě nejsou podporované jinými rutinami prostředí PowerShell nalezen ve službě Azure AD Connect pro použití zákazníka.

**Otázka: je možné použít "Serveru serveru pro export/import" nalezena v *Synchronization Service Manager* přesunutí konfigurací mezi servery?**  
Ne. Tato možnost nebude načíst všechna nastavení konfigurace a by se neměla používat. Místo toho používejte Průvodce a vytvořte základní konfigurace na druhý server, použijte editor pravidla synchronizace ke generování skriptů prostředí PowerShell přesunout všechny vlastní pravidlo mezi servery. V tématu [kyvu migrace](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Otázka: je možné hesla do mezipaměti Azure přihlašovací stránku a můžete to možné, protože obsahuje element input pro heslo pomocí automatického dokončování = "false" atribut?**</br>
Aktuálně nepodporujeme změna atributů HTML vstupní pole pro heslo, včetně automatického dokončování značky. Právě pracujeme na funkce, která vám umožní pro vlastní Javascript, která vám umožní přidat všechny atributy, do pole heslo. To by měl být k dispozici novější součástí 2017.

**Otázka: na Azure přihlašovací stránce se zobrazí uživatelská jména pro uživatele, kteří mají dříve úspěšně přihlášení.  Lze toto chování vypnout?**</br>
Aktuálně nepodporujeme změna atributů HTML přihlašovací stránky. Právě pracujeme na funkce, která vám umožní pro vlastní Javascript, která vám umožní přidat všechny atributy, do pole heslo. To by měl být k dispozici novější součástí 2017.

**Otázka: je způsob, jak zabránit souběžných relací?**</br>
Ne.

## <a name="troubleshooting"></a>Řešení potíží
**Otázka: jak můžete získat pomoc s Azure AD Connect?**

[Znalostní bázi Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Vyhledejte Microsoft Knowledge Base (KB) pro technické řešení pro běžné problémy opravu o podpoře pro Azure AD Connect.

[Fóra Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Můžete vyhledat a procházet technical otázky a odpovědi od komunity nebo položte svou vlastní otázku kliknutím [zde](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Jak získat podporu pro Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Použijte tento odkaz k získání podpory prostřednictvím portálu Azure.

