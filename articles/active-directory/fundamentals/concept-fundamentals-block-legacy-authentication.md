---
title: Blokování protokolů pro ověřování starší verze v Azure AD
description: Zjistěte, jak a proč by měly organizace blokovat starší verze ověřovacích protokolů.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: b50c942d2e05d7f5234a17f1cf36137309c7ce97
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320949"
---
# <a name="blocking-legacy-authentication"></a>Blokování starších verzí ověřování
 
Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, Azure Active Directory (Azure AD) podporuje širokou škálu ověřovacích protokolů, včetně starších verzí ověřování. Starší verze ověřování je termín, který odkazuje na žádost o ověření, kterou provedla:

- Starší klienti Office, kteří nepoužívají moderní ověřování (například klient Office 2010)
- Každý klient, který používá starší e-mailové protokoly, jako je IMAP/SMTP/POP3

V současné době většina všech neúspěšných pokusů o přihlášení pocházela ze staršího ověřování. Starší verze ověřování nepodporuje službu Multi-Factor Authentication (MFA). I v případě, že máte ve svém adresáři povolené zásady vícefaktorového ověřování, může se špatný objekt actor ověřit pomocí starší verze protokolu a obejít MFA. Nejlepším způsobem, jak ochránit váš účet před nebezpečnými požadavky na ověření, které udělaly starší protokoly, je blokovat tyto pokusy úplně.

## <a name="identify-legacy-authentication-use"></a>Identifikovat použití starší verze ověřování

Než budete moct v adresáři zablokovat starší verze ověřování, musíte nejdřív pochopit, jestli mají vaši uživatelé aplikace, které používají starší verze ověřování a jak ovlivňují celkový adresář. Protokoly přihlášení k Azure AD se dají použít k pochopení, jestli používáte starší verze ověřování.

1. Přejděte do **Azure Portal**  >  **Azure Active Directory**  >  **přihlášení**.
1. Pokud není zobrazený, klikněte na **sloupce** **Client App**   >  **klientská aplikace**a přidejte sloupec aplikace klienta.
1. Filtrovat podle **klientské aplikace** > zajděte si uvedené možnosti pro **starší verze klientů ověřování** .
1. Filtrovat podle **stavu**  >  **úspěch**. 
1. V případě potřeby rozbalíte rozsah dat pomocí filtru **data** .

Filtrování zobrazí jenom úspěšné pokusy o přihlášení, které udělaly vybrané starší protokoly ověřování. Kliknutím na každý jednotlivý pokus o přihlášení zobrazíte další podrobnosti. Sloupec klientské aplikace nebo pole klientské aplikace na kartě Základní informace po výběru jednotlivého řádku dat určuje, který starší verze ověřovacího protokolu se použil. Tyto protokoly určují, kteří uživatelé pořád závisejí na starší verzi ověřování a které aplikace používají starší protokoly k provádění požadavků na ověření. Pro uživatele, kteří se v těchto protokolech nezobrazují a které se potvrzují, že nepoužívají starší verze ověřování, implementujte zásady podmíněného přístupu nebo zapněte zásady standardních hodnot: zablokuje starší ověřování jenom pro tyto uživatele.

## <a name="moving-away-from-legacy-authentication"></a>Přesun směrem od starší verze ověřování 

Jakmile budete mít lepší představu o tom, kdo používá starší verze ověřování v adresáři a které aplikace jsou na něm závislé, bude dalším krokem upgrade vašich uživatelů na používání moderního ověřování. Moderní ověřování je metoda správy identit, která nabízí bezpečnější ověřování uživatelů a autorizaci. Pokud máte v adresáři zásady MFA, moderní ověřování zajistí, že se uživateli zobrazí výzva k MFA v případě potřeby. Je to bezpečnější alternativa k starším ověřovacím protokolům.

Tato část obsahuje podrobný přehled o tom, jak aktualizovat prostředí na moderní ověřování. Před povolením zásad blokování ověřování starší verze v organizaci si Projděte následující kroky.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Krok 1: povolení moderního ověřování v adresáři

Prvním krokem při povolování moderního ověřování je zajištění, že váš adresář podporuje moderní ověřování. Moderní ověřování je ve výchozím nastavení povolené pro adresáře vytvořené od 1. srpna 2017. Pokud byl adresář vytvořen před tímto datem, budete muset ručně povolit moderní ověřování pro svůj adresář pomocí následujících kroků:

1. Zkontrolujte, jestli váš adresář už podporuje moderní ověřování, a to spuštěním  `Get-CsOAuthConfiguration`   z [online modulu PowerShellu pro Skype pro firmy](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Pokud váš příkaz vrátí prázdnou  `OAuthServers`   vlastnost, je moderní ověřování zakázané. Aktualizujte nastavení tak, aby umožňovalo moderní ověřování pomocí  `Set-CsOAuthConfiguration` . Pokud vaše  `OAuthServers`   vlastnost obsahuje položku, můžete začít.

Než budete pokračovat, nezapomeňte tento krok dokončit. Je důležité, abyste nejdřív změnili konfigurace adresáře, protože určují, který protokol budou používat všichni klienti Office. I když používáte klienty Office, kteří podporují moderní ověřování, budou ve výchozím nastavení používat starší protokoly, pokud je ve vašem adresáři zakázané moderní ověřování.

### <a name="step-2-office-applications"></a>Krok 2: aplikace Office

Jakmile povolíte moderní ověřování v adresáři, můžete začít aktualizovat aplikace tím, že povolíte moderní ověřování pro klienty Office. Klienti Office 2016 nebo novější podporují moderní ověřování ve výchozím nastavení. Nejsou nutné žádné další kroky.

Pokud používáte klienty se systémem Office 2013 nebo starší verze, doporučujeme upgradovat na sadu Office 2016 nebo novější. I po dokončení předchozího kroku povolení moderního ověřování ve vašem adresáři budou starší aplikace Office dál používat starší protokoly ověřování. Pokud používáte klienty Office 2013 a nemůžete hned upgradovat na Office 2016 nebo novější, postupujte podle kroků v následujícím článku a [Povolte moderní ověřování pro Office 2013 na zařízeních s Windows](/office365/admin/security-and-compliance/enable-modern-authentication). Aby bylo možné chránit váš účet i v případě, že používáte starší verzi ověřování, doporučujeme v adresáři používat silná hesla. Podívejte se na [ochranu heslem Azure AD](../authentication/concept-password-ban-bad.md)   , abyste v adresáři zakazují slabá hesla.

Office 2010 nepodporuje moderní ověřování. Budete muset upgradovat všechny uživatele pomocí Office 2010 na novější verzi Office. Doporučujeme upgradovat na Office 2016 nebo novější, protože ve výchozím nastavení blokuje starší verze ověřování.

Pokud používáte macOS, doporučujeme upgradovat na Office pro Mac 2016 nebo novější. Pokud používáte nativního poštovního klienta, budete muset mít na všech zařízeních verzi macOS 10,14 nebo novější.

### <a name="step-3-exchange-and-sharepoint"></a>Krok 3: Exchange a SharePoint

Aby klienti Outlooku založené na Windows používali moderní ověřování, musí mít Exchange Online povolené i moderní ověřování. Pokud je pro Exchange Online zakázané moderní ověřování, klienti Outlooku s Windows, kteří podporují moderní ověřování (Outlook 2013 nebo novější), použijí základní ověřování pro připojení k poštovním schránkám Exchange Online.

SharePoint Online je povolen pro výchozí moderní ověřování. Pro adresáře vytvořené od 1. srpna 2017 je moderní ověřování ve výchozím nastavení povolené v Exchange Online. Pokud jste však dříve vypnuli moderní ověřování nebo jste používali adresář vytvořený před tímto datem, použijte postup v následujícím článku, který umožňuje [moderní ověřování v systému Exchange Online](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Krok 4: Skype pro firmy

Aby se předešlo tomu, že se starší požadavky na ověřování provedly přes Skype pro firmy, je potřeba povolit moderní ověřování pro Online Skype pro firmy. Pro adresáře vytvořené od 1. srpna 2017 je moderní ověřování pro Skype pro firmy ve výchozím nastavení povolené.

Doporučujeme, abyste přešli na Microsoft teams, který ve výchozím nastavení podporuje moderní ověřování. Pokud ale v tuto chvíli nemůžete migrovat, budete muset povolit moderní ověřování pro Skype for Business Online, aby klienti Skypu pro firmy začali používat moderní ověřování. Pokud chcete povolit moderní ověřování pro Skype pro firmy, postupujte podle kroků v tomto článku [topologie Skypu pro firmy podporované moderním ověřováním](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

Kromě povolení moderního ověřování pro Online Skype pro firmy doporučujeme povolit moderní ověřování pro Exchange Online, když povolíte moderní ověřování pro Skype pro firmy. Tento proces pomůže synchronizovat stav moderního ověřování v Exchange Online a Online Skypu pro firmy a zabrání pro klienty Skypu pro firmy víc přihlašovacích výzev.

### <a name="step-5-using-mobile-devices"></a>Krok 5: použití mobilních zařízení

Aplikace na vašem mobilním zařízení musí také zablokovat starší verze ověřování. Doporučujeme používat Outlook pro mobilní zařízení. Outlook pro mobilní zařízení podporuje ve výchozím nastavení moderní ověřování a bude vyhovovat ostatním zásadám základní ochrany MFA.

Aby bylo možné používat nativního e-mailového klienta pro iOS, bude nutné, abyste spustili iOS verze 11,0 nebo novější, abyste zajistili, že se klient pro poštu aktualizoval tak, aby blokoval starší ověřování.

### <a name="step-6-on-premises-clients"></a>Krok 6: místní klienti

Pokud jste hybridní zákazník používající místní Exchange Server a Skype pro firmy v místním prostředí, bude nutné aktualizovat obě služby, aby bylo možné povolit moderní ověřování. Při použití moderního ověřování v hybridním prostředí stále ověřujete uživatele místně. Příběh, který autorizuje přístup k prostředkům (soubory nebo e-maily), se změní.

Než budete moct začít s povolením moderního ověřování v místním prostředí, ujistěte se, že jste splnili požadavky. Nyní jste připraveni povolit moderní ověřování v místním prostředí.

Postup povolení moderního ověřování najdete v následujících článcích:

* [Jak nakonfigurovat místní Exchange Server tak, aby používal hybridní moderní ověřování](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Jak používat moderní ověřování (ADAL) se Skypem pro firmy](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat místní Exchange Server tak, aby používal hybridní moderní ověřování](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Jak používat moderní ověřování (ADAL) se Skypem pro firmy](/skypeforbusiness/manage/authentication/use-adal)
- [Blokování starší verze ověřování](../conditional-access/block-legacy-authentication.md)