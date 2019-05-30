---
title: Základní zásady blok starší verze ověřování (preview) – Azure Active Directory
description: Zásady podmíněného přístupu na blok starší verze ověřovacích protokolů
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7eebc68ae8a55d636f3bc85e179bd7d6813be8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235556"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Základní zásady: Blok starší verze ověřování (preview)

Azure Active Directory (Azure AD) a poskytovat uživatelům snadný přístup k vašim cloudovým aplikacím, podporuje množství různých ověřovacích protokolů včetně starší verze ověřování. Starší verze ověřování je termín, který odkazuje na požadavek na ověření od:

* Starší klienti Office, které nepoužívají moderní ověřování (například klienta Office 2010)
* Libovolného klienta, který používá starší verzi poštovních protokolů, jako je například IMAP, SMTP/POP3

V současné době většinou všechny tím Ohrozíte pokusů o přihlášení pocházejí ze starší verze ověřování. Starší verze ověřování nepodporuje vícefaktorové ověřování (MFA). I v případě, že máte zásadu vícefaktorového ověřování v adresáři povoleno, chybný actor můžete ověřit pomocí starší verze protokolu a obejít vícefaktorové ověřování.

Nejlepší způsob, jak chránit váš účet ze škodlivých ověřování požadavků od starších verzí protokolů je blokování tyto pokusy všechno dohromady. Aby bylo snazší pro vás k blokování všech žádostí o přihlášení provedených starší protokoly, jsme vytvořili směrný plán zásad, který právě tohle dělá.

![Blok starší verze ověřování s podmíněným přístupem](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**Blok starší verze ověřování** je [základní zásady](concept-baseline-protection.md) , která blokuje všechny žádosti o ověření z starších protokolů. Moderní ověřování, musí využívat pro úspěšné přihlášení pro všechny uživatele. Používá ve spojení se zásadami jiné standardní hodnoty, všechny požadavky přicházející ze starších verzí protokolů se zablokuje a všichni uživatelé budou muset vícefaktorové ověřování pokaždé, když se vyžaduje. Tato zásada nedochází k blokování protokolu Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identifikovat pomocí starší verze ověřování

Předtím, než ve svém adresáři můžete zablokovat starší verze ověřování, musíte nejprve porozumět, pokud mají vaši uživatelé aplikace, které používají starší verze ověřování a o jejím dopadu na vaše celkové adresáře. Azure AD přihlášení protokoly je možné pochopit, pokud používáte starší verzi ověřování.

1. Přejděte **webu Azure portal** > **Azure Active Directory** > **přihlášení**.
1. Přidat sloupec klientskou aplikaci, pokud se zobrazí po kliknutí na **sloupce** > **klientskou aplikaci**.
1. Filtrovat podle **klientskou aplikaci** > **ostatní klienti** a klikněte na tlačítko **použít**.

Filtrování se pouze pokusí zobrazit přihlášení, které byly provedeny ve starší verzi ověřovacích protokolů. Kliknutím na jednotlivé jednotlivé pokusy o přihlášení se zobrazí další podrobnosti. **Klientskou aplikaci** pole **základní informace o** kartu označí, který starší verze ověřování protokol byl použit.

Tyto protokoly budou indikovat, kteří uživatelé jsou stále v závislosti na tom starší verze ověřování a aplikace, které používají starší protokoly k podání žádostí o ověření. Pro uživatele, kteří se nezobrazují v těchto protokolech a potvrzena nelze pomocí starší verze ověřování, implementovat zásady podmíněného přístupu nebo povolit **základní zásady: starší verze ověřování bloku** pouze tito uživatelé.

## <a name="moving-away-from-legacy-authentication"></a>Přesun směrem od starších verzí ověřování

Jakmile budete mít lepší představy, který používá starší verze ověřování ve vašem adresáři a aplikace, které jsou na ní závislé, dalším krokem upgraduje vaše uživatele, aby nepoužívala moderní ověřování. Moderní ověřování je metoda správy identit, která nabízí lepší zabezpečení ověřování a autorizaci uživatelů. Pokud máte zásadu vícefaktorového ověřování ve vašem adresáři, moderní ověřování zajišťuje, že bude uživatel vyzván pro vícefaktorové ověřování v případě potřeby. Je bezpečnější alternativu ke starší verzi ověřovacích protokolů.

Tato část obsahuje podrobný přehled o tom, jak aktualizovat vaše prostředí na moderní ověřování. Přečtěte si následující kroky před povolením starší verze ověřování blokují zásady ve vaší organizaci.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Krok 1: Povolit moderní ověřování ve vašem adresáři

Prvním krokem při povolení moderního ověřování, je zajistit, že adresář podporuje moderní ověřování. Moderní ověřování je povoleno standardně pro adresáře vytvořené 1. srpna 2017 nebo později. Pokud váš adresář byl vytvořen před tímto datem, bude nutné ručně povolit moderní ověřování pro svůj adresář pomocí následujících kroků:

1. Zkontrolujte, jestli váš adresář již podporuje moderní ověřování spuštěním `Get-CsOAuthConfiguration` z [Skype pro firmy Online PowerShell modul](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Pokud váš příkaz vrátí prázdnou `OAuthServers` je zakázána vlastnost a potom moderní ověřování. Aktualizovat nastavení k povolení moderního ověřování pomocí `Set-CsOAuthConfiguration`. Pokud vaše `OAuthServers` vlastností obsahuje položku, můžete začít.

Ujistěte se, že dokončení tohoto kroku před v budoucnu. Je velmi důležité, že vaše konfigurace adresáře jsou nejprve změnit, protože se vyžadují protokol, který se použije všichni klienti Office. I v případě, že používáte klienti Office, které podporují moderní ověřování, kterých se jako výchozí pro použití starších verzí protokolů, pokud moderní ověřování je zakázáno. v adresáři.

### <a name="step-2-office-applications"></a>Krok 2: Aplikace Office

Po povolení moderního ověřování ve svém adresáři můžete spustit aktualizace aplikací tím, že moderní ověřování pro klienty Office. Office 2016 nebo novější klienti podporují moderní ověřování ve výchozím nastavení. Žádné další kroky jsou povinné.

Pokud používáte klientům Office 2013 Windows nebo starší, doporučujeme provést upgrade na verzi Office 2016 nebo novější. I po dokončení předchozího kroku povolení moderního ověřování v adresáři, budou dál používat starší ověřovací protokoly starší aplikace Office. Pokud používáte klientech Office 2013 a budou moci ihned upgradujete na verzi Office 2016 nebo novější, postupujte podle kroků v následujícím článku [povolit moderní ověřování Office 2013 na zařízeních s Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Pomáhá chránit váš účet při použití starší verze ověřování, doporučujeme používat silná hesla ve vašem adresáři. Podívejte se na [ochrana hesel Azure AD](../authentication/concept-password-ban-bad.md) zakázat Slabá hesla ve vašem adresáři.

Office 2010 nepodporuje moderní ověřování. Je potřeba upgradovat všechny uživatele se systémem Office 2010 na novější verzi Office. Doporučujeme vám upgradovat Office 2016 nebo novější, protože ve výchozím nastavení blokuje starší verze ověřování.

Pokud používáte MacOS, doporučujeme upgradovat na Office pro Mac 2016 nebo novější. Pokud používáte nativní e-mailového klienta, musíte mít MacOS verze 10.14 nebo později na všech zařízeních.

### <a name="step-3-exchange-and-sharepoint"></a>Krok 3: Exchange a SharePoint

Exchange Online klienty založené na Windows Outlook, aby nepoužívala moderní ověřování, musí být také povolené moderní ověřování. Pokud moderní ověřování je vypnuté pro Exchange Online, založené na Windows Outlookové klienty podporující moderní ověřování (Outlook 2013 nebo novější) bude používat základní ověřování pro připojení k poštovním schránkám Exchange Online.

SharePoint Online je povolený pro moderní ověřování výchozí. Pro adresáře vytvořená po 1. srpna 2017 je ve výchozím nastavení v Exchangi Online povolené moderní ověřování. Nicméně pokud dříve zakázal moderní ověřování nebo používáte adresář vytvořené před tímto datem, postupujte podle kroků v následujícím článku [povolení moderního ověřování v systému Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Krok 4: Skype pro firmy

Pokud chcete zabránit starší verze ověřování žádosti Skype pro firmy, je potřeba povolit moderní ověřování pro Skype for Business Online. Pro adresáře vytvořená po 1. srpna 2017 je ve výchozím nastavení povolené moderní ověřování pro Skype pro firmy.

Pokud chcete povolit moderní ověřování ve Skypu pro firmy, doporučujeme přechod do Microsoft Teams, které podporují moderní ověřování ve výchozím nastavení. Ale pokud jste v tuto chvíli nejde tr, je potřeba povolit moderní ověřování pro Skype for Business Online tak, aby Skype pro firmy klienti spustí používající moderní ověřování. Postupujte podle těchto kroků v článku [Skype pro firmy topologie podporované s moderním ověřováním](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), kde najdete kroky pro povolení moderního ověřování pro Skype pro firmy.

Kromě povolení moderního ověřování pro Skype for Business Online, doporučujeme moderní ověřování povolit pro Exchange Online při povolení moderního ověřování pro Skype pro firmy. Tento proces vám pomůže synchronizaci stavu moderního ověřování v Exchangi Online a Skype pro firmy online a zabrání více výzvám k přihlášení pro Skype pro firmy klienty.

### <a name="step-5-using-mobile-devices"></a>Krok 5: Pomocí mobilních zařízení

Aplikace na mobilním zařízení je třeba zablokovat i starší verze ověřování. Doporučujeme používat aplikaci Outlook pro mobilní zařízení. Outlook Mobile ve výchozím nastavení podporuje moderní ověřování a bude vyhovovat jiných zásad MFA směrného plánu ochrany.

Chcete-li použít e-mailového klienta nativní aplikace pro iOS, musíte mít spuštěný systém iOS verze 11.0 nebo novější k zajištění, že byl aktualizován e-mailového klienta blokovat ověřování starší verze.

### <a name="step-6-on-premises-clients"></a>Krok 6: Klienty na místě

Pokud jste zákazník hybrid pomocí místního serveru Exchange a Skypu pro firmy v místním, obě služby bude nutné aktualizovat tak, aby povolit moderní ověřování. Při použití moderního ověřování v hybridním prostředí, budete pořád ověřování uživatelů v místním. Scénáře ověřování svůj přístup k prostředkům (soubory nebo e-mailů) změny.

Před zahájením povolení moderního ověřování v místním, ujistěte se, máte splněné theIf splňujete, teď jste připraveni k povolení moderního ověřování místní.

Postup pro povolení moderního ověřování najdete v následujících článcích:

* [Postup konfigurace Exchange serveru místní použití hybridním moderním ověřováním](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Jak používat moderní ověřování (ADAL) pomocí Skypu pro firmy](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Povolit zásady směrný plán

Zásady **směrný plán zásad: Blok starší verze ověřování (preview)** vybavená předem nakonfigurovaným a se zobrazí v horní části, když přejdete do okna podmíněného přístupu na webu Azure portal.

Tuto zásadu povolit a chránit vaši organizaci:

1. Přihlaste se k **webu Azure portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte do **Azure Active Directory** > **podmíněného přístupu**.
1. V seznamu zásad, vyberte **směrný plán zásad: Blok starší verze ověřování (preview)** .
1. Nastavte **povolit zásady** k **použít zásady okamžitě**.
1. Kliknutím na Přidat všechny uživatele vyloučení **uživatelé** > **vybrat vyloučené uživatele** a výběru uživatelů, které je třeba vyloučit. Klikněte na tlačítko **vyberte** pak **provádí**.
1. Klikněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

* [Zásady podmíněného přístupu směrný plán ochrany](concept-baseline-protection.md)
* [Zabezpečení vaší infrastruktury identit v pěti krocích](../../security/azure-ad-secure-steps.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
