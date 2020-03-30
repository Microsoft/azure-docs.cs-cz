---
title: Blokování starších ověřovacích protokolů ve službě Azure AD
description: Zjistěte, jak a proč by organizace měly blokovat starší ověřovací protokoly
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63271567e70955f6dfb0b10a5c882b6dce9545ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932486"
---
# <a name="blocking-legacy-authentication"></a>Blokování staršíverze ověřování
 
Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, azure active directory (Azure AD) podporuje širokou škálu ověřovacích protokolů včetně starších verzí ověřování. Starší verze ověřování je termín, který odkazuje na požadavek na ověření ze strany:

- Starší klienti Office, kteří nepoužívají moderní ověřování (například klient Office 2010)
- Každý klient, který používá starší poštovní protokoly, například IMAP/SMTP/POP3

Dnes většina všech kompromitujících pokusů o přihlášení pochází ze staršíverze ověřování. Starší verze ověřování nepodporuje vícefaktorové ověřování (MFA). I v případě, že máte v adresáři povolenou zásadu mfa, může chybný objekt actor ověřit pomocí staršího protokolu a obejít vícefaktorové ověřování. Nejlepší způsob, jak chránit váš účet před škodlivými požadavky na ověření ze strany starších protokolů, je tyto pokusy zcela zablokovat.

## <a name="identify-legacy-authentication-use"></a>Identifikace staršího použití ověřování

Než budete moci blokovat starší verze ověřování ve vašem adresáři, musíte nejprve pochopit, zda vaši uživatelé mají aplikace, které používají starší verze ověřování a jak ovlivňuje celkový adresář. Protokoly přihlášení Azure AD lze použít k pochopení, pokud používáte starší verze ověřování.

1. Přejděte na portál Azure > přihlášení > služby Azure Active Directory.
1. Pokud se sloupec Klientská aplikace nezobrazuje, přidejte kliknutím na sloupce > klientskou aplikaci.
1. Filtrovat podle klientské aplikace > zkontrolujte všechny zobrazené ostatní možnosti klienta a klepněte na tlačítko Použít.
1. Filtrovat podle stavu > úspěch a klikněte na Použít. 
1. V případě potřeby rozbalte rozsah dat pomocí filtru Datum.

Filtrování zobrazí pouze úspěšné pokusy o přihlášení, které byly provedeny vybranými staršími ověřovacími protokoly. Kliknutím na každý jednotlivý pokus o přihlášení se zobrazí další podrobnosti. Sloupec Klientská aplikace nebo pole Klientská aplikace na kartě Základní informace po výběru jednotlivého řádku dat bude označovat, který starší ověřovací protokol byl použit. Tyto protokoly budou označovat, kteří uživatelé jsou stále závislé na starší verze ověřování a které aplikace používají starší protokoly k požadavkům na ověření. Pro uživatele, kteří se nezobrazují v těchto protokolech a je potvrzeno, že nepoužívají starší verze ověřování, implementujte zásady podmíněného přístupu nebo povolte zásadu směrného plánu: blokovat starší verze ověřování pouze pro tyto uživatele.

## <a name="moving-away-from-legacy-authentication"></a>Odklon od staršíverze ověřování 

Jakmile budete mít lepší představu o tom, kdo používá starší verze ověřování ve vašem adresáři a které aplikace na něm závisí, dalším krokem je upgrade uživatelů na moderní ověřování. Moderní ověřování je metoda správy identit, která nabízí bezpečnější ověřování a autorizaci uživatelů. Pokud máte v adresáři zavedenou zásadu mfa, moderní ověřování zajistí, že uživatel bude v případě potřeby vyzván k vícefaktorovému ověřování. Jedná se o bezpečnější alternativu ke starším ověřovacím protokolům.

Tato část poskytuje podrobný přehled o tom, jak aktualizovat prostředí na moderní ověřování. Než povolíte starší zásady blokování ověřování ve vaší organizaci, přečtěte si níže uvedené kroky.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Krok 1: Povolení moderního ověřování v adresáři

Prvním krokem při povolení moderního ověřování je zajištění, že adresář podporuje moderní ověřování. Moderní ověřování je ve výchozím nastavení povoleno pro adresáře vytvořené 1. Pokud byl adresář vytvořen před tímto datem, budete muset ručně povolit moderní ověřování adresáře pomocí následujících kroků:

1. Zkontrolujte, jestli váš adresář už `Get-CsOAuthConfiguration` podporuje moderní ověřování, spuštěním z [modulu PowerShellu Online Skypu pro firmy](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Pokud příkaz vrátí `OAuthServers` prázdnou vlastnost, je moderní ověřování zakázáno. Aktualizujte nastavení a povolte moderní ověřování pomocí `Set-CsOAuthConfiguration`aplikace . Pokud `OAuthServers` váš majetek obsahuje položku, můžete jít.

Než se pohnete kupředu, ujistěte se, že tento krok dokončíte. Je důležité, aby konfigurace adresářů byly nejprve změněny, protože určují, který protokol budou používat všichni klienti sady Office. I v případě, že používáte klienty Office, které podporují moderní ověřování, budou ve výchozím nastavení používat starší protokoly, pokud je v adresáři zakázáno moderní ověřování.

### <a name="step-2-office-applications"></a>Krok 2: Aplikace Office

Jakmile povolíte moderní ověřování v adresáři, můžete začít aktualizovat aplikace povolením moderního ověřování pro klienty sady Office. Klienti Office 2016 nebo novější ve výchozím nastavení podporují moderní ověřování. Nejsou vyžadovány žádné další kroky.

Pokud používáte klienty Office 2013 windows nebo starší, doporučujeme upgradovat na Office 2016 nebo novější. I po dokončení předchozího kroku povolení moderního ověřování v adresáři budou starší aplikace sady Office nadále používat starší ověřovací protokoly. Pokud používáte klienty Office 2013 a nemůžete okamžitě upgradovat na Office 2016 nebo novější, postupujte podle následujících kroků k [povolení moderního ověřování pro Office 2013 na zařízeních s Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Chcete-li chránit svůj účet při používání starších verzí ověřování, doporučujeme používat silná hesla v celém adresáři. Podívejte se na  [ochranu heslem Azure AD](../authentication/concept-password-ban-bad.md)zakázat slabá hesla v celém adresáři.

Office 2010 nepodporuje moderní ověřování. Budete muset upgradovat všechny uživatele s Office 2010 na novější verzi Office. Doporučujeme upgradovat na Office 2016 nebo novější, protože ve výchozím nastavení blokuje starší verze ověřování.

Pokud používáte MacOS, doporučujeme upgradovat na Office for Mac 2016 nebo novější. Pokud používáte nativního poštovního klienta, budete muset mít macOS verze 10.14 nebo novější na všech zařízeních.

### <a name="step-3-exchange-and-sharepoint"></a>Krok 3: Exchange a SharePoint

Aby klienti outlooku se systémem Windows používali moderní ověřování, musí být exchange online také povoleno moderní ověřování. Pokud je pro Exchange Online zakázáno moderní ověřování, klienti Outlooku se systémem Windows, kteří podporují moderní ověřování (Outlook 2013 nebo novější), budou používat pro připojení k poštovním schránkám Exchange Online základní ověřování.

SharePoint Online je povolený pro moderní ověřování výchozí. Pro adresáře vytvořené po 1 srpnu 2017 je ve výchozím nastavení v Exchange Online povoleno moderní ověřování. Pokud jste však dříve zakázali moderní ověřování nebo používáte adresář vytvořený před tímto datem, postupujte podle pokynů v následujícím článku [a povolte moderní ověřování v exchange online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Krok 4: Skype pro firmy

Chcete-li zabránit starším požadavkům na ověření ze strany Skypu pro firmy, je nutné povolit moderní ověřování pro Online Skype pro firmy. Pro adresáře vytvořené po 1.

Doporučujeme přejít na Microsoft Teams, který ve výchozím nastavení podporuje moderní ověřování. Pokud se vám ale teď nedaří migrovat, budete muset povolit moderní ověřování pro Online Skype pro firmy, aby klienti Skypu pro firmy začali používat moderní ověřování. Chcete-li povolit moderní ověřování pro Skype pro firmy , postupujte podle pokynů v tomto článku [topologie Skypu pro firmy podporované moderním ověřováním](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

Kromě povolení moderního ověřování pro Online Skype pro firmy doporučujeme povolit moderní ověřování pro Exchange Online, když povolíte moderní ověřování pro Skype pro firmy. Tento proces pomůže synchronizovat stav moderního ověřování ve Exchange Online a Skypu pro firmy online a zabrání vícenásobným přihlašovacím výzvám pro klienty Skypu pro firmy.

### <a name="step-5-using-mobile-devices"></a>Krok 5: Používání mobilních zařízení

Aplikace na vašem mobilním zařízení musí také blokovat starší verze ověřování. Doporučujeme používat Outlook pro mobily. Outlook for Mobile ve výchozím nastavení podporuje moderní ověřování a bude splňovat ostatní zásady ochrany podle směrného plánu vícefaktorové ověřování.

Chcete-li používat nativní poštovní klient iOS, budete muset používat iOS verze 11.0 nebo novější, abyste zajistili, že poštovní klient byl aktualizován, aby zablokoval ověřování.

### <a name="step-6-on-premises-clients"></a>Krok 6: Místní klienti

Pokud jste hybridní zákazník, který používá místní Exchange Server a místní Skype pro firmy, bude nutné aktualizovat obě služby, aby bylo možné povolit moderní ověřování. Při použití moderního ověřování v hybridním prostředí stále ověřujete uživatele v místním prostředí. Příběh autorizace jejich přístupu k prostředkům (souborům nebo e-mailům) se změní.

Než začnete povolovat moderní ověřování místně, ujistěte se, že jste splnili požadavky. Nyní jste připraveni povolit moderní ověřování místně.

Kroky pro povolení moderního ověřování naleznete v následujících článcích:

* [Konfigurace místního serveru Exchange Server pro použití hybridního moderního ověřování](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Jak používat moderní ověřování (ADAL) se Skypem pro firmy](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Další kroky

- [Konfigurace místního serveru Exchange Server pro použití hybridního moderního ověřování](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Jak používat moderní ověřování (ADAL) se Skypem pro firmy](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Blokování starší verze ověřování](../conditional-access/block-legacy-authentication.md)
