---
title: Blokovat starší verze ověřování – Služba Azure Active Directory
description: Zjistěte, jak zlepšit stav zabezpečení blokováním starších ověřování pomocí podmíněného přístupu Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76dd07a59a9fa7c0d6231a766ff4090c11f9f5bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331915"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Postup: Blokování staršího ověřování ve službě Azure AD pomocí podmíněného přístupu   

Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, azure active directory (Azure AD) podporuje širokou škálu ověřovacích protokolů včetně starších verzí ověřování. Starší protokoly však nepodporují vícefaktorové ověřování (MFA). Vícefaktorové finanční autority je v mnoha prostředích běžným požadavkem na řešení krádeže identity. 

Alex Weinert, ředitel zabezpečení identity společnosti Microsoft, ve svém [blogu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) z 12.

> Aby bylo vícefaktorové ověřování efektivní, musíte také blokovat starší verze ověřování. Důvodem je, že starší ověřovací protokoly jako POP, SMTP, IMAP a MAPI nemohou vynutit vícefaktorové ověřování, což z nich činí upřednostňované vstupní body pro protivníky útočící na vaši organizaci...
> 
>... Čísla na starší verze ověřování z analýzy provozu Azure Active Directory (Azure AD) jsou neúprosná:
> 
> - Více než 99 procent útoků sprejem heslem používá starší ověřovací protokoly
> - Více než 97 procent útoků na vycpávání přihlašovacích údajů používá starší verze ověřování
> - Účty Azure AD v organizacích, které zakázaly starší verze ověřování, jsou o 67 procent méně kompromisů než ty, kde je povoleno starší verze ověřování
>

Pokud je vaše prostředí připravené blokovat starší verze ověřování, aby se zlepšila ochrana vašeho tenanta, můžete tohoto cíle dosáhnout pomocí podmíněného přístupu. Tento článek vysvětluje, jak můžete nakonfigurovat zásady podmíněného přístupu, které blokují starší verze ověřování pro vašeho klienta.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni s: 

- [Základní koncepty](overview.md) podmíněného přístupu Azure AD 
- [Doporučené postupy](best-practices.md) pro konfiguraci zásad podmíněného přístupu na webu Azure Portal

## <a name="scenario-description"></a>Popis scénáře

Azure AD podporuje několik nejpoužívanějších ověřovacích a autorizačních protokolů, včetně staršíverze ověřování. Starší verze ověřování odkazuje na protokoly, které používají základní ověřování. Tyto protokoly obvykle nelze vynutit jakýkoli typ ověřování druhého faktoru. Příklady aplikací založených na starším ověřování jsou:

- Starší aplikace Microsoft Office
- Aplikace používající poštovní protokoly jako POP, IMAP a SMTP

Jednofaktorové ověřování (například uživatelské jméno a heslo) nestačí v těchto dnech. Hesla jsou špatné, protože jsou snadno uhodnout a my (lidé) jsou špatné při výběru dobrých hesel. Hesla jsou také zranitelná vůči různým útokům, jako je phishing a sprej s hesly. Jednou z nejjednodušších věcí, které můžete udělat pro ochranu před hrozbami hesla, je implementace vícefaktorového ověřování. S MFA, i v případě, že útočník získá v držení hesla uživatele, samotné heslo nestačí k úspěšnému ověření a přístup u dat.

Jak můžete zabránit aplikacím používajícím starší verze ověřování v přístupu k prostředkům vašeho tenanta? Doporučujeme je pouze zablokovat pomocí zásad podmíněného přístupu. V případě potřeby povolíte pouze určitým uživatelům a určitým síťovým umístěním používat aplikace založené na starším ověřování.

Zásady podmíněného přístupu jsou vynuceny po dokončení ověřování prvního faktoru. Podmíněný přístup proto není určen jako obrana první linie pro scénáře, jako je odmítnutí služby (DoS), ale může využít signály z těchto událostí (například úroveň rizika přihlášení, umístění požadavku a tak dále) k určení přístupu.

## <a name="implementation"></a>Implementace

Tato část vysvětluje, jak nakonfigurovat zásady podmíněného přístupu pro blokování staršíverze ověřování. 

### <a name="legacy-authentication-protocols"></a>Starší ověřovací protokoly

Následující možnosti jsou považovány za starší ověřovací protokoly

- Ověřený protokol SMTP – používá klient POP a IMAP k odesílání e-mailových zpráv.
- Automatická konfigurace – klienti Outlooku a EAS používají k vyhledání poštovních schránek exchange online a připojení k ní.
- Exchange Online PowerShell – používá se k připojení k Exchange Online pomocí vzdáleného Prostředí PowerShell. Pokud zablokujete základní ověřování pro Exchange Online PowerShell, budete muset použít Exchange Online PowerShell modul pro připojení. Pokyny najdete v [tématu Připojení k Exchange Online PowerShellpomocí vícefaktorového ověřování](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) – programovací rozhraní, které používá Outlook, Outlook pro Mac a aplikace třetích stran.
- IMAP4 - Používá e-mailové klienty IMAP.
- MAPI přes HTTP (MAPI/HTTP) – používá aplikace Outlook 2010 a novější.
- Offline adresář (OAB) – kopie kolekcí seznamů adres, které jsou staženy a používány aplikací Outlook.
- Outlook Anywhere (RPC přes HTTP) – používá outlook 2016 a starší.
- Outlook Service – používá aplikace Pošta a Kalendář pro Windows 10.
- POP3 - Používá se e-mailovými klienty POP.
- Reporting Web Services – používá se k načtení dat sestavy v Exchange Online.
- Ostatní klienti – jiné protokoly identifikované jako starší verze ověřování.

Další informace o těchto ověřovacích protokolech a službách najdete [v tématu Sestavy aktivit přihlášení na portálu Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identifikace staršího použití ověřování

Než budete moci blokovat starší verze ověřování ve vašem adresáři, musíte nejprve pochopit, zda vaši uživatelé mají aplikace, které používají starší verze ověřování a jak ovlivňuje celkový adresář. Protokoly přihlášení Azure AD lze použít k pochopení, pokud používáte starší verze ověřování.

1. Přejděte na **portál Azure** > **Azure Active Directory** > **Přihlášení**.
1. Přidejte sloupec Klientská aplikace, pokud se nezobrazuje kliknutím na **sloupce** > **klientské aplikace**.
1. **Přidejte filtry** > **Klientská aplikace** > vybrat všechny starší ověřovací protokoly a klepněte na tlačítko **Použít**.

Filtrování zobrazí pouze pokusy o přihlášení, které byly provedeny staršími ověřovacími protokoly. Kliknutím na každý jednotlivý pokus o přihlášení se zobrazí další podrobnosti. Pole **Klientská aplikace** na kartě **Základní informace** bude označovat, který starší ověřovací protokol byl použit.

Tyto protokoly budou označovat, kteří uživatelé jsou stále závislé na starší verze ověřování a které aplikace používají starší protokoly k požadavkům na ověření. Pro uživatele, kteří se nezobrazují v těchto protokolech a je potvrzeno, že nepoužívají starší verze ověřování, implementujte zásady podmíněného přístupu pouze pro tyto uživatele.

### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování 

V zásadách podmíněného přístupu můžete nastavit podmínku, která je vázána na klientské aplikace, které se používají pro přístup k prostředkům. Podmínka klientských aplikací umožňuje zúžit rozsah na aplikace pomocí staršíverze ověřování výběrem **jiných klientů** pro mobilní aplikace **a klienty pro stolní počítače**.

![Ostatní klienti](./media/block-legacy-authentication/01.png)

Chcete-li blokovat přístup k těmto aplikacím, musíte vybrat **možnost Blokovat přístup**.

![Blokovat přístup](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Výběr uživatelů a cloudových aplikací

Pokud chcete blokovat starší verze ověřování pro vaši organizaci, pravděpodobně si myslíte, že toho můžete dosáhnout výběrem:

- Všichni uživatelé
- Všechny cloudové aplikace
- Blokovat přístup

![Přiřazení](./media/block-legacy-authentication/03.png)

Azure má bezpečnostní funkci, která vám brání ve vytváření zásad, jako je tento, protože tato konfigurace porušuje [osvědčené postupy](best-practices.md) pro zásady podmíněného přístupu.
 
![Konfigurace zásad není podporována.](./media/block-legacy-authentication/04.png)

Bezpečnostní funkce je nezbytná, protože *blokovat všechny uživatele a všechny cloudové aplikace* má potenciál blokovat celou organizaci z přihlášení k tenantovi. Je nutné vyloučit alespoň jednoho uživatele, aby byl splněn požadavek na minimální osvědčené postupy. Můžete také vyloučit roli adresáře.

![Konfigurace zásad není podporována.](./media/block-legacy-authentication/05.png)

Tuto bezpečnostní funkci můžete splnit vyloučením jednoho uživatele ze zásad. V ideálním případě byste měli definovat několik [účtů pro správu nouzového přístupu ve službě Azure AD](../users-groups-roles/directory-emergency-access.md) a vyloučit je z vašich zásad.

Použití [režimu pouze pro sestavu](concept-conditional-access-report-only.md) při povolení zásad y blokovat starší verze ověřování poskytuje vaší organizaci příležitost sledovat, jaký dopad bude mít zásada.

## <a name="policy-deployment"></a>Nasazení zásad

Než vložíte svou politiku do výroby, postarejte se o:
 
- **Účty služeb** – Identifikujte uživatelské účty, které se používají jako účty služeb nebo zařízení, jako jsou telefony v konferenční místnosti. Ujistěte se, že tyto účty mají silná hesla a přidejte je do vyloučené skupiny.
- **Sestavy přihlášení** – zkontrolujte sestavu přihlášení a vyhledejte další provoz **klienta.** Identifikujte nejvyšší využití a zjistěte, proč se používá. Přenos je obvykle generován staršími klienty Office, kteří nepoužívají moderní ověřování, nebo některými poštovními aplikacemi jiných výrobců. Vytvořte plán, jak přesunout využití od těchto aplikací, nebo pokud je dopad nízký, upozorněte uživatele, že už tyto aplikace nemohou používat.
 
Další informace naleznete v tématu [Jak nasadit novou zásadu?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Co byste měli vědět

Blokování přístupu pomocí **ostatních klientů** také blokuje Exchange Online PowerShell a Dynamics 365 pomocí základní houštiny.

Konfigurace zásad pro **ostatní klienty** blokuje celou organizaci od určitých klientů, jako je SPConnect. Tento blok se stane, protože starší klienti ověřit neočekávaným způsobem. Problém se nevztahuje na hlavní aplikace Office, jako jsou starší klienti Office.

Může trvat až 24 hodin, než zásady vstoupí v platnost.

Můžete vybrat všechny dostupné ovládací prvky grantu pro podmínku **Ostatní klienti.** prostředí pro koncové uživatele je však vždy stejné - blokovaný přístup.

Pokud zablokujete starší verze ověřování pomocí podmínky **Ostatní klienti,** můžete také nastavit platformu zařízení a podmínku umístění. Pokud například chcete blokovat pouze starší verze ověřování pro mobilní zařízení, nastavte podmínku **platforem zařízení** tak, že vyberete:

- Android
- iOS
- Windows Phone

![Konfigurace zásad není podporována.](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Další kroky

- [Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)
- Pokud ještě nejste obeznámeni s konfigurací zásad podmíněného přístupu, například [najdete v tématu vyžadovat vícefaktorové informace pro konkrétní aplikace s podmíněným přístupem Služby Azure Active Directory.](app-based-mfa.md)
- Další informace o moderní podpoře ověřování najdete v tématu [Jak funguje moderní ověřování pro klientské aplikace Office 2013 a Office 2016.](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
