---
title: Blokování starší verze ověřování do Azure Active Directory (Azure AD) s podmíněným přístupem | Dokumentace Microsoftu
description: Zjistěte, jak zlepšit tak stav zabezpečení zákonné zodpovědnosti organizací blokováním starší verze ověřování pomocí podmíněného přístupu Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2120466652db363206ec20c2303ad56670228c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164789"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Postup: Blok starší verze ověřování do služby Azure AD s podmíněným přístupem   

Azure Active Directory (Azure AD) a poskytovat uživatelům snadný přístup k vašim cloudovým aplikacím, podporuje množství různých ověřovacích protokolů včetně starší verze ověřování. Ale nepodporují starších verzí protokolů ověřování službou Multi-Factor Authentication (MFA). MFA je v mnoha prostředích běžné požadavky na krádež identity adres. 

Pokud je vaše prostředí připravené na blok starší verze ověřování ke zlepšení ochrany vašeho tenanta, můžete pomocí podmíněného přístupu dosažení tohoto cíle. Tento článek vysvětluje, jak nakonfigurovat zásady podmíněného přístupu, které blokují starší verze ověřování pro vašeho tenanta.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s: 

- [Základní koncepty](overview.md) z podmíněného přístupu Azure AD 
- [Osvědčené postupy](best-practices.md) ke konfiguraci zásad podmíněného přístupu na webu Azure Portal

## <a name="scenario-description"></a>Popis scénáře

Azure AD podporuje některé z nejčastěji používaných ověřovací a autorizační protokoly včetně starší verze ověřování. Starší verze ověřování se odkazuje na protokoly, které používají základní ověřování. Obvykle tyto protokoly nelze vynutit libovolného typu ověřování pomocí druhého faktoru. Příkladem aplikací, které jsou založeny na starší verze ověřování jsou:

- Starší aplikace Microsoft Office

- Aplikace pomocí protokolů e-mailu, jako jsou SMTP, POP a IMAP

Jednomu faktoru ověřování (například uživatelské jméno a heslo) není dostatek těchto dnů. Hesla je špatné, jako jsou snadno uhodnutelné a jsme (lidé) je špatné za výběr vhodné hesla. Hesla jsou také citlivé na celou řadu útoků, jako jsou útoky phishing a heslo zařízení. Jedním z nejjednodušší věcí, které vám pomůžou chránit před hesla hrozbami je implementovat vícefaktorové ověřování. Se používá služba MFA i v případě, že útočník získá ve vlastnictví uživatele heslo heslo samostatně není dostatečná k úspěšnému ověření a přístup k datům.

Jak můžete zabránit aplikacím pomocí starší verze ověřování přístup k prostředkům vašeho tenanta? Doporučuje se jen zablokovat zásadami podmíněného přístupu. V případě potřeby můžete povolit pouze určité uživatele a konkrétní síťová umístění používat aplikace, které jsou založeny na starší verze ověřování.

Po dokončení první dvojúrovňové ověřování se vynucují zásady podmíněného přístupu. Proto podmíněného přístupu není určen jako první linií obrany řádku pro scénáře, jako jsou útoky s cílem odepření služeb (DoS), ale můžete využít signály z těchto událostí (například úroveň rizika přihlášení, umístění žádosti a tak dále) a zjistí přístup k.

## <a name="implementation"></a>Implementace

Tato část vysvětluje postup konfigurace zásad podmíněného přístupu pro starší verze ověřování bloku. 

### <a name="identify-legacy-authentication-use"></a>Identifikovat pomocí starší verze ověřování

Předtím, než ve svém adresáři můžete zablokovat starší verze ověřování, musíte nejprve porozumět, pokud mají vaši uživatelé aplikace, které používají starší verze ověřování a o jejím dopadu na vaše celkové adresáře. Azure AD přihlášení protokoly je možné pochopit, pokud používáte starší verzi ověřování.

1. Přejděte **webu Azure portal** > **Azure Active Directory** > **přihlášení**.
1. Přidat sloupec klientskou aplikaci, pokud se zobrazí po kliknutí na **sloupce** > **klientskou aplikaci**.
1. Filtrovat podle **klientskou aplikaci** > **ostatní klienti** a klikněte na tlačítko **použít**.

Filtrování se pouze pokusí zobrazit přihlášení, které byly provedeny ve starší verzi ověřovacích protokolů. Kliknutím na jednotlivé jednotlivé pokusy o přihlášení se zobrazí další podrobnosti. **Klientskou aplikaci** pole **základní informace o** kartu označí, který starší verze ověřování protokol byl použit.

Tyto protokoly budou indikovat, kteří uživatelé jsou stále v závislosti na tom starší verze ověřování a aplikace, které používají starší protokoly k podání žádostí o ověření. Pro uživatele, kteří se nezobrazují v těchto protokolech a potvrzena nelze pomocí starší verze ověřování implementujte zásady podmíněného přístupu pro pouze tito uživatelé.

### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování 

V zásadách podmíněného přístupu můžete nastavit podmínku, která se váže na klientské aplikace, které se používají pro přístup k prostředkům. Stav klienta aplikace umožňuje zúžit rozsah pro aplikace s využitím ověřování na starší verzi výběrem **ostatní klienty** pro **mobilní aplikace a desktopoví klienti**.

![Ostatní klienti](./media/block-legacy-authentication/01.png)

Zablokovat přístup pro tyto aplikace, budete muset vybrat **blokovat přístup**.

![Blokovat přístup](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Vyberte uživatele a cloudové aplikace

Pokud chcete blokovat starší verze ověřování pro vaši organizaci, pravděpodobně myslíte, že můžete to udělat tak, že vyberete:

- Všichni uživatelé

- Všechny cloudové aplikace

- Blokovat přístup
 

![Přiřazení](./media/block-legacy-authentication/03.png)



Azure má bezpečnostní funkce, která brání ve vytvoření takovouto zásadu, protože tato konfigurace je v rozporu [osvědčené postupy](best-practices.md) pro zásady podmíněného přístupu.
 
![Konfigurace zásad není podporována](./media/block-legacy-authentication/04.png)


Funkci bezpečnosti je nezbytné protože *blokovat všechny uživatele a všechny cloudové aplikace* hrozí riziko blokování celé organizace z přihlášení do vašeho tenanta. Je nutné vyloučit aspoň jednoho uživatele splňovat minimální požadavky osvědčených postupů. Může také vyloučit roli adresáře.

![Konfigurace zásad není podporována](./media/block-legacy-authentication/05.png)


Tuto funkci zabezpečení můžou splňovat vyloučením jednoho uživatele z vaší zásady. V ideálním případě byste měli definovat několik [nouzovou přístup pro správu účtů ve službě Azure AD](../users-groups-roles/directory-emergency-access.md) a vyloučit z vašich zásad.
 

## <a name="policy-deployment"></a>Nasazení zásad

Před přepnutím do produkčního prostředí zásady, aby se postaral:
 
- **Účty služeb** -identifikaci uživatelských účtů, které se používají jako účty služeb nebo zařízení, jako jsou telefony místnosti konference. Ujistěte se, že tyto účty používat silná hesla a přidat je do vyloučená skupina.
 
- **Přihlašování zprávy** – přihlášení sestavu zkontrolovat a vyhledejte **klienta** provoz. Identifikovat hlavní využití a zjistěte, proč se používá. Obvykle je provoz generovaný starší klienti Office, které nepoužívají moderní ověřování, nebo některých jiných výrobců e-mailové aplikace. Vytvořte plán k přesunutí využití mimo tyto aplikace nebo pokud se s nízkou, uživatelům předem oznámili, že nemohou používat tyto aplikace už.
 
Další informace najdete v tématu [by měl nasazení nových zásad?](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>Co byste měli vědět

Blokování přístupu k pomocí **ostatní klienty** také blokuje Powershellu v Exchangi Online pomocí základní ověřeného

Konfigurace zásad pro **ostatní klienty** blokuje určité klientů jako SPConnect v celé organizaci. Tento blok se stane, protože starší klienti ověřování neočekávaným způsobem. Problém se nevztahuje na hlavní aplikací Office, jako je starší klienti Office.

Může trvat až 24 hodin pro zásady tak, aby projeví.

Můžete vybrat všechny dostupné udělení ovládacích prvků pro ostatní klienty podmínka. ale činnost koncového uživatele je vždy stejný - blokovaný přístup.

Pokud zablokujete starší verze ověřování s použitím jiných klientů podmínek, můžete také nastavit podmínku zařízení platformy a umístění. Například pokud chcete blokovat starší verze ověřování pro mobilní zařízení, nastavit **platformy zařízení** podmínku tak, že vyberete:

- Android

- iOS

- telefon se systémem Windows

![Konfigurace zásad není podporována](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Další postup

- Pokud nejste obeznámeni s konfigurací zásad podmíněného přístupu ještě, přečtěte si téma [vyžadovat vícefaktorové ověřování pro konkrétní aplikace pomocí Azure Active Directory podmíněného přístupu](app-based-mfa.md) příklad.

- Další informace o podpoře moderní ověřování najdete v tématu [jak moderní ověřování funguje pro klientské aplikace Office 2013 a Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
