---
title: Volba Azure hybridní řešení identit | Microsoft Docs
description: Získejte základní povědomí dostupné hybridní řešení identity a doporučení pro vám umožní provádět rozhodnutí nejlepší identity zásad správného řízení pro vaši organizaci.
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
ms.openlocfilehash: 9f9099c0ebd65ba84e171314e6f04d858648a805
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
ms.locfileid: "29800733"
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft hybridních řešení identit
[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hybridních řešení identit umožňují synchronizovat místní adresáře objekty s Azure AD při nadále spravuje uživatele místní. První rozhodnutí při plánování vaší místní Windows Server Active Directory synchronizovat s Azure AD je, jestli chcete používat identity synchronizovaných nebo federovaných identit. Synchronizované identity a volitelně hodnot hash hesel, povolte uživatelům používat stejné heslo pro přístup k místní i cloudové prostředkům organizace. Pro pokročilejší scénáře požadavky, například jednotného přihlašování (SSO) nebo místní vícefaktorové ověřování musíte k nasazení služby Active Directory Federation Services (AD FS) pro vytvoření federace identit. 

Nejsou k dispozici pro konfiguraci hybridní identita několik možností. Tento článek obsahuje informace, které vám pomůžou zvolit tu nejvhodnější pro vaši organizaci podle snadné nasazení a je třeba konkrétní správu identit a přístupu. Jak byste zvážit, které modelu identity nejlepší vyhovuje potřebám vaší organizace, musíte také vezměte v úvahu dobu, stávající infrastruktury, složitost a náklady. Tyto faktory se liší pro každou organizaci a může časem změnit. Ale pokud vaše požadavky se změní, máte také možnost přepnout na jinou identitu modelu.

> [!TIP]
> Tato řešení jsou doručovány prostřednictvím [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Synchronizované identity 
Synchronizované identity je nejjednodušší způsob, jak synchronizovat místní adresáře objekty (uživatelé a skupiny) s Azure AD. 

![Synchronizované hybridní identita](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Synchronizované identity je nejjednodušší a nejrychlejší metodu, uživatelé stále potřebovat udržovat samostatné hesla pro cloudové prostředky. Chcete-li předejít, můžete také (volitelně) [synchronizovat hodnotu hash hesla uživatele](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) do vašeho adresáře Azure AD. Synchronizace hodnot hash hesel umožňuje uživatelům přihlášení do cloudu prostředkům organizace pomocí stejné uživatelské jméno a heslo, používají místně. Azure AD Connect pravidelně zjišťuje, zda vaše místní adresář pro změny a udržuje adresáře Azure AD synchronizovány. Atribut uživatele nebo heslo je změněné na místním Active Directory, je automaticky aktualizovat ve službě Azure AD. 

Pro většinu organizací, kteří potřebují jenom umožnit uživatelům přihlášení k Office 365, aplikace SaaS a jiné na základě AD prostředky Azure se doporučuje výchozí možnost synchronizace hesel. Pokud to nebude fungovat pro vás, musíte se rozhodnout mezi předávací ověřování a služby AD FS.

> [!TIP]
> Uživatelská hesla jsou uloženy v místní Windows Server Active Directory ve formě hodnotu hash, který představuje skutečného uživatelského hesla. Hodnota hash je výsledek jednosměrné matematické funkce (algoritmu hash). Neexistuje žádná metoda vrátit výsledek jednosměrné funkce na prostý text verzi heslo. Hodnoty hash hesla nelze použít pro přihlášení k síti na pracovišti. Při rozhodnete synchronizovat hesla, Azure AD Connect extrahuje hodnot hash hesel z místní služby Active Directory a další bezpečnostní zpracování pro hodnotu hash hesla, než se synchronizují do Azure AD. Synchronizace hesel lze také společně s zpětný zápis hesla povolit samoobslužné služby ve službě Azure AD pro vytvoření nového hesla. Kromě toho můžete povolit jednotné přihlašování (SSO) pro uživatele v doméně počítače, které jsou připojené k podnikové síti. Pomocí jednotného přihlašování povolení uživatelé muset zadat uživatelské jméno bezpečný přístup k prostředkům cloudu. 

## <a name="pass-through-authentication"></a>Předávací ověřování
[Azure AD předávací ověřování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) poskytuje jednoduché heslo řešení ověřování pro Azure služeb na základě AD pomocí služby Active Directory v místě. Pokud zásady zabezpečení a dodržování předpisů pro vaši organizaci nepovoluje odeslání hesla uživatelů i ve formuláři hash a potřebujete podporovat plochy jednotného přihlašování pro zařízení připojená k doméně, doporučujeme vyhodnotit pomocí předávací ověřování. Předávací ověřování nevyžaduje žádné nasazení v hraniční síti, což zjednodušuje nasazení infrastruktury ve srovnání se službou AD FS. Když se uživatelé přihlašují pomocí služby Azure AD, ověří tato metoda ověřování hesla uživatelů přímo pro vaše místní službu Active Directory.

![Předávací ověřování](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

S předávací ověřování není nutné pro komplexní síťovou infrastrukturu a není nutné ukládat místních hesel v cloudu. V kombinaci s jednotné přihlašování, předávací ověřování poskytuje skutečně integrované možnosti pro přihlášení k Azure AD nebo jiných cloudových služeb.

S Azure AD Connect, který používá jednoduchý místní agent, který naslouchá požadavkům ověření hesla je nakonfigurované předávací ověřování. Agenta můžete snadno nasadit do více počítačů k zajištění vysoké dostupnosti a vyrovnávání zatížení. Vzhledem k tomu, že veškerá komunikace jsou pouze odchozí, není nutné konektor nainstalovat v hraniční síti. Požadavky na počítač serveru pro konektor jsou následující:

- Windows Server 2012 R2 nebo vyšší
- Připojený k doméně v doménové struktuře, pomocí kterého se uživatelé ověřují

## <a name="federated-identity-ad-fs"></a>Federované identity (AD FS)
Pro větší kontrolu nad přístup uživatelů k Office 365 a jiných cloudových služeb, můžete nastavit synchronizace adresáře se jednotné přihlašování (SSO) pomocí [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). Přihlášení uživatele se službou AD FS pro federaci deleguje ověřování na místním serveru, která ověřuje přihlašovací údaje uživatele. V tomto modelu jsou pověření služby Active Directory v místě nikdy předán do služby Azure AD.

![Federované identity](./media/choose-hybrid-identity-solution/federated-identity.png)

Používá se také označení federování identity, tato metoda přihlašování zajistí všechny ověření uživatele je řízené místní a správcům umožňuje implementovat přísnějším úroveň řízení přístupu. Federaci identit se službou AD FS je možnost nejvíce složitý a vyžaduje nasazení dalších serverů v místním prostředí. Federaci identit můžete také potvrdí k zajištění podpory 24 / 7 pro infrastrukturu služby Active Directory a AD FS. Tato vysokou úroveň podpory je nutné, protože přistupují na váš místní Internet, řadič domény nebo servery služby AD FS nejsou k dispozici, nemůžete se přihlásit uživatele cloudových služeb.

> [!TIP]
> Pokud se rozhodnete použít federační službou Active Directory Federation Services (AD FS), můžete volitelně nastavíte synchronizace hesel jako záložní případ selhání infrastruktury služby AD FS.


## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení
Tady jsou některé běžné hybridní identit a přístupu správu scénáře s doporučeními, která hybridní identity možnost (nebo možnosti) může být vhodné pro každou.

|Potřebuji:|Server PWS a jednotné přihlašování<sup>1</sup>| PTA a jednotné přihlašování<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Nového uživatele, obraťte se na a skupinové účty, které jsou vytvořené v mé místní služby Active Directory do cloudu automaticky synchronizovat.|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Nastavení klienta pro Office 365 hybridní scénáře|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Povolit vlastní uživatelé přihlásit a získat přístup ke službám cloudu pomocí hesla pro místní|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementace jednotné přihlašování pomocí podnikové přihlašovací údaje|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)| ![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png) |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Ujistěte se, že žádné hodnoty hash hesla se ukládají v cloudu| |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Povolit místní služby Multi-Factor authentication řešení| | |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Podpora ověřování pomocí čipové karty pro svoje uživatele<sup>4</sup>| | |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|
|Zobrazovat oznámení o vypršení platnosti hesla na portálu Office a na ploše Windows 10| | |![Doporučené](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> synchronizace hesel pomocí jednotného přihlašování. 

> <sup>2</sup> předávací ověřování a jednotné přihlašování. 

> <sup>3</sup> federované jednotné přihlašování se službou AD FS.

> <sup>4</sup> služby AD FS se dá integrovat s vaší podnikové infrastruktury veřejných KLÍČŮ povolit přihlašování pomocí certifikátů. Tyto certifikáty jde konfigurace soft certifikáty nasazené přes důvěryhodné zřizování kanálů, jako je například certifikáty MDM nebo čipová karta nebo objektu zásad skupiny (včetně karet standardu PIV/CAC) nebo Hello pro firmy (cert vztahu důvěryhodnosti). Další informace o podpoře ověřování pomocí čipové karty, najdete v části [tomto blogu](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Další postup
[Další informace v prostředí Azure testování konceptu](https://aka.ms/aad-poc)

[Instalace služby Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Monitorování synchronizace hybridní identity](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

