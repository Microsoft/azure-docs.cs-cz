---
title: Zvolte Azure hybridní řešení identit | Dokumentace Microsoftu
description: Získejte základní informace k dispozici hybridních řešení identit a doporučení pro vás nejlepší rozhodnout zásad správného řízení identit pro vaši organizaci.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 03/02/2018
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 15a28cd5937be103aac888a5fc5485e39854a1b4
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319944"
---
# <a name="microsoft-hybrid-identity-solutions"></a>Hybridních řešení identit Microsoft

[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hybridních řešení identit umožňují synchronizaci místních objektů adresáře s Azure AD stále kontrolou uživatele na místní. První rozhodnutí při plánování synchronizace váš místní Windows Server Active Directory s Azure AD je, zda chcete použít identity synchronizovaných nebo federovaných identit. Synchronizované identity a volitelně hodnot hash hesel, umožní vašim uživatelům používat stejné heslo pro přístup k místní i cloudové prostředkům organizace. Pro pokročilejší požadavků scénáře, jako je například jednotného přihlašování (SSO) nebo v místním MFA budete muset nasadit služby Active Directory Federation Services (AD FS) pro vytvoření federace identit. 

Nejsou k dispozici pro konfiguraci hybridní identita několik možností. Tento článek obsahuje informace, které vám pomůžou zvolit ten nejvhodnější pro vaši organizaci podle snadné nasazení a je třeba konkrétní správu identit a přístupů. Jak byste zvážit, který nejlepší model identity nejlépe vyhovuje potřebám vaší organizace, musíte také uvažovat o čas, stávající infrastruktury, složitost a náklady. Tyto faktory se liší pro každou organizaci a může v průběhu času měnit. Nicméně pokud vaše požadavky se změní, máte také možnost přepnout na jinou identitou modelu.

> [!TIP]
> Tato řešení jsou doručovány prostřednictvím [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="synchronized-identity"></a>Synchronizované identity 

Synchronizované identity je nejjednodušší způsob, jak synchronizovat místní adresáře objekty (uživatelé a skupiny) s Azure AD. 

![Synchronizované hybridní identita](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Synchronizované identity je nejjednodušší a nejrychlejší metodu, uživatelé stále potřebují udržovat samostatnou hesla pro cloudové prostředky. Abyste tomu předešli, můžete také (volitelně) [synchronizaci hodnot hash hesel uživatelů](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) do adresáře služby Azure AD. Synchronizace hodnot hash hesel umožňuje uživatelům přihlášení do cloudových prostředkům organizace pomocí stejné uživatelské jméno a heslo, které používají místní. Azure AD Connect pravidelně kontroluje vašeho místního adresáře pro změny a udržuje synchronizované adresáře služby Azure AD. Atribut uživatele nebo heslo je změněné v místní službě Active Directory, se automaticky aktualizuje ve službě Azure AD. 

Pro většinu organizací, kteří potřebují jenom umožnit uživatelům přihlášení k Office 365, aplikacím SaaS a dalších založené na AD prostředků Azure se doporučuje výchozí možnost synchronizace hesel. Pokud to nepomůže, pro vás, bude nutné se rozhodnout mezi předávací ověřování a AD FS.

> [!TIP]
> Uživatelská hesla se ukládají do místní Windows Server Active Directory ve formě hodnotu hash, která představuje skutečného uživatelského hesla. Hodnota hash je výsledek jednosměrné matematické funkce (algoritmus hash). Neexistuje žádná metoda vrátit výsledek jednosměrné funkce, která se verze hesla v prostém textu. Nelze použít hodnotu hash hesla pro přihlášení k místní síti. Pokud se rozhodnete k synchronizaci hesel, Azure AD Connect extrahuje hodnoty hash hesel z místní služby Active Directory a platí další bezpečnostní zpracování pro hodnotu hash hesla předtím, než se synchronizují do služby Azure AD. Synchronizaci hesel můžete také použít společně se zpětným zápisem hesla Pokud chcete povolit samoobslužné resetování hesla ve službě Azure AD. Kromě toho můžete povolit jednotné přihlašování (SSO) pro uživatele v doméně počítače, které jsou připojené k podnikové síti. Pomocí jednotného přihlašování povolení uživatelé muset zadat uživatelské jméno pro zabezpečený přístup ke cloudovým prostředkům. 
>

## <a name="pass-through-authentication"></a>Předávací ověřování

[Předávací ověřování služby Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) poskytuje jednoduché heslo ověřování řešení služby Azure na základě AD pomocí služby Active Directory vaše místní. Pokud zásady zabezpečení a dodržování předpisů ve vaší organizaci nepovolují odeslání hesla uživatelů, dokonce i ve formě hodnoty hash a vám stačí pro podporu jednotného přihlašování pro zařízení připojených k doméně, doporučujeme vyhodnotit pomocí předávacího ověřování. Předávací ověřování nevyžaduje žádné nasazení v hraniční síti, což zjednodušuje nasazení infrastruktury ve srovnání se službou AD FS. Při přihlášení uživatele pomocí služby Azure AD, ověří tato metoda ověřování hesla uživatelů přímo proti místní Active Directory.

![Předávací ověřování](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Předávací ověřování není nutné pro komplexní síťovou infrastrukturu a není nutné pro ukládání místních hesel v cloudu. V kombinaci s jednotného přihlašování, předávací ověřování umožňuje skutečně integrované prostředí při přihlašování k Azure AD nebo jiných cloudových službách.

Předávací ověřování je nakonfigurovaný službou Azure AD Connect, která používá jednoduché místní agenta, který poslouchá žádosti o ověření hesla. Agenta můžete snadno nasadit do více počítačů pro zajištění vysoké dostupnosti a vyrovnávání zatížení. Protože veškerá komunikace se pouze odchozí, neexistuje žádný požadavek pro konektor nainstalovat v hraniční síti. Požadavky na počítač serveru pro konektor jsou následující:

- Windows Server 2012 R2 nebo vyšší
- Připojené k doméně v doménové struktuře, pomocí kterého se uživatelé ověřují

## <a name="federated-identity-ad-fs"></a>Federované identity služby AD FS)

Větší kontrolu nad tím, jak uživatelé přistupovat k Office 365 a dalším cloudovým službám, nastavením synchronizace adresářů pomocí jednotného přihlašování (SSO) [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). Přihlášení uživatele se službou AD FS pro federaci deleguje ověřování na místním serveru, která ověřuje přihlašovací údaje uživatele. V tomto modelu jsou místní přihlašovací údaje služby Active Directory nebyl nikdy předán do služby Azure AD.

![Federované identity](./media/choose-hybrid-identity-solution/federated-identity.png)

Zkratka federace identit, tato metoda přihlášení zajistí, že všechny ověřování uživatelů řízené místní a správcům umožňuje implementovat přísnější úrovně řízení přístupu. Federace identit se službou AD FS je nejkomplikovanější možnost a vyžaduje nasazení dalších serverů ve vašem místním prostředí. Federace identit můžete také potvrzení k poskytování podpory 24 x 7 pro vaši infrastrukturu služby Active Directory a AD FS. Tento vysoký stupeň podpory je nezbytné, protože pokud přístup k vaší místní Internet, řadič domény nebo servery služby AD FS nejsou k dispozici, uživatelům se přihlásit ke cloudovým službám.

> [!TIP]
> Pokud se rozhodnete federace pomocí služby Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizaci hesel jako záložní v případě selhání infrastruktury služby AD FS.
>

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení

Tady jsou některé běžné hybridních identit a přístupu scénáře správy s doporučeními, která hybridní identity možnost (nebo možnosti) může být vhodné pro každý.

|Je potřeba:|PWS a jednotné přihlašování<sup>1</sup>| PTA a jednotné přihlašování<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synchronizace nového uživatele, kontaktu a skupinových účtů automaticky vytvoří ve své místní služby Active Directory do cloudu.|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Nastavení mému tenantovi pro Office 365 hybridních scénářů|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Povolení uživatelé přihlásit a přístup ke cloudovým službám pomocí jejich místní heslo|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementaci jednotného přihlašování pomocí firemních přihlašovacích údajů|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Ujistěte se, že žádné hodnoty hash hesel se ukládají v cloudu| |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Povolit místní řešení vícefaktorového ověřování| | |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Podpora ověřování pomocí čipové karty pro moje uživatele<sup>4</sup>| | |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Zobrazení oznámení o vypršení platnosti hesel portálu Office a na Windows 10 desktop| | |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> synchronizace hesel pomocí jednotného přihlašování.
>
> <sup>2</sup> předávací ověřování a jednotné přihlašování. 
>
> <sup>3</sup> federované jednotné přihlašování se službou AD FS.
>
> <sup>4</sup> služby AD FS je možné integrovat s vaší podnikové infrastruktury veřejných KLÍČŮ umožňující přihlášení pomocí certifikátů. Tyto certifikáty se dají použít konfigurace soft certifikáty nasazené přes důvěryhodný zřizovací kanálů, jako je například certifikáty MDM nebo čipová karta nebo objektu zásad skupiny (včetně PIV/CAC karet) nebo Hello pro firmy (certifikátu vztahu důvěryhodnosti). Další informace o podpoře ověřování čipovou kartu, najdete v části [tento blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="next-steps"></a>Další postup

[Další informace najdete v prostředí Azure testování konceptu](https://aka.ms/aad-poc)
[instalace služby Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)
[monitorování synchronizace hybridní identity](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
