---
title: Delegování rolí podle úlohy správce – Azure Active Directory | Dokumenty společnosti Microsoft
description: Role delegáta pro úlohy identity ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/03/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd24650c9bf0c4de155b5bfc8723cfa1fef01548
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755413"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Role správce podle úlohy správce ve službě Azure Active Directory

V tomto článku najdete informace potřebné k omezení oprávnění správce uživatele přiřazením nejméně privilegovaných rolí ve službě Azure Active Directory (Azure AD). Najdete zde úkoly správce uspořádané podle oblasti funkcí a nejméně privilegovanou roli potřebnou k provedení každého úkolu spolu s dalšími rolemi, které nejsou globálními správci, které mohou úkol provést.

## <a name="application-proxy"></a>Proxy aplikací

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace aplikace proxy aplikace | Správce aplikace | 
Konfigurace vlastností skupiny konektorů | Správce aplikace | 
Vytvořit registraci aplikace, když je schopnost zakázána pro všechny uživatele | Vývojář aplikací | Správce cloudových aplikací, správce aplikací
Vytvořit skupinu spojnic | Správce aplikace | 
Odstranit skupinu konektorů | Správce aplikace | 
Zákaz proxy aplikací | Správce aplikace | 
Služba stahování konektorů | Správce aplikace | 
Číst celou konfiguraci | Správce aplikace | 

## <a name="b2c"></a>B2C

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Vytvoření adresářů Azure AD B2C | Všichni uživatelé, kteří nehostují ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vytváření aplikací B2C | Globální správce | 
Vytváření podnikových aplikací | Správce cloudové aplikace | Správce aplikace
Vytváření, čtení, aktualizace a odstraňování zásad B2C | Správce zásad B2C IEF | 
Vytváření, čtení, aktualizace a odstraňování poskytovatelů identit | Správce externího zprostředkovatele identity | 
Vytváření, čtení, aktualizace a odstraňování toků uživatelů pro resetování hesla | Správce toku uživatelů B2C | 
Vytváření, čtení, aktualizace a odstraňování toků uživatelů pro úpravy profilu | Správce toku uživatelů B2C | 
Vytváření, čtení, aktualizace a odstraňování přihlašovacích uživatelských toků | Správce toku uživatelů B2C | 
Vytváření, čtení, aktualizace a odstraňování toku uživatelů registrace |Správce toku uživatelů B2C | 
Vytváření, čtení, aktualizace a odstraňování uživatelských atributů | Správce atributů toku uživatele B2C | 
Vytváření, čtení, aktualizace a odstraňování uživatelů | Správce uživatelů
Číst celou konfiguraci | Globální čtečka | 
Čtení protokolů auditu B2C | Globální čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Globální čtenáři Azure AD B2C nemají stejná oprávnění jako globální správci Azure AD. Pokud máte oprávnění globálního správce Azure AD B2C, ujistěte se, že jste v adresáři Azure AD B2C a ne v adresáři Azure AD.

## <a name="company-branding"></a>Branding společnosti

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace brandingu společnosti | Globální správce | 
Číst celou konfiguraci | Čtečky adresářů | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Vlastnosti společnosti

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace vlastností společnosti | Globální správce | 

## <a name="connect"></a>Připojit

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Ověření průchodu | Správce hybridní identity | 
Číst celou konfiguraci | Globální čtečka | Správce hybridní identity |
Bezproblémové jednotné přihlašování | Správce hybridní identity | 

## <a name="connect-health"></a>Stav připojení

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Přidání nebo odstranění služeb | Vlastník ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Použití oprav k chybě synchronizace | Přispěvatel ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Vlastník
Konfigurace oznámení | Přispěvatel ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Vlastník
Konfigurace nastavení | Vlastník ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurace oznámení synchronizace | Přispěvatel ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Vlastník
Čtení zpráv o zabezpečení systému ADFS | Čtečka zabezpečení | Přispěvatel, Vlastník
Číst celou konfiguraci | Čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, Vlastník
Čtení chyb synchronizace | Čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, Vlastník
Čtení synchronizačních služeb | Čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, Vlastník
Zobrazení metrik a výstrah | Čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, Vlastník
Zobrazení metrik a výstrah | Čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, Vlastník
Zobrazení metrik a výstrah synchronizační služby | Čtečka ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, Vlastník

## <a name="custom-domain-names"></a>Vlastní názvy domén

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Spravovat domény | Globální správce | 
Číst celou konfiguraci | Čtečky adresářů | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Vytvoření instance služby Azure AD Domain Services | Globální správce | 
Provádění všech úloh služby Azure AD Domain Services | Skupina správců řadiče domény služby Azure AD ([viz dokumentace](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Číst celou konfiguraci | Předplatné Reader on Azure obsahující službu AD DS | 

## <a name="devices"></a>Zařízení

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Zakázat zařízení | Správce cloudových zařízení | 
Povolení zařízení | Správce cloudových zařízení | 
Přečtěte si základní konfiguraci | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Čtení klíčů nástroje BitLocker | Čtečka zabezpečení | Správce hesel, správce zabezpečení

## <a name="enterprise-applications"></a>Podnikové aplikace

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Souhlas s případnými delegovanými oprávněními | Správce cloudových aplikací | Správce aplikace
Souhlas s oprávněními aplikací, která nezahrnuje microsoft graph | Správce cloudových aplikací | Správce aplikace
Souhlas s oprávněními aplikací pro Microsoft Graph | Správce privilegovaných rolí | 
Souhlas s přístupem k aplikacím s přístupem k vlastním údajům | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vytvoření podnikové aplikace | Správce cloudových aplikací | Správce aplikace
Správa serveru proxy aplikace | Správce aplikace | 
Správa uživatelských nastavení | Globální správce | 
Kontrola přístupu ke čtení skupiny nebo aplikace | Čtečka zabezpečení | Správce zabezpečení, správce uživatelů
Číst celou konfiguraci | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Aktualizace přiřazení podnikových aplikací | Vlastník podnikové aplikace ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudových aplikací, správce aplikací
Aktualizace vlastníků podnikových aplikací | Vlastník podnikové aplikace ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudových aplikací, správce aplikací
Aktualizace vlastností podnikových aplikací | Vlastník podnikové aplikace ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudových aplikací, správce aplikací
Aktualizovat zřizování podnikových aplikací | Vlastník podnikové aplikace ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudových aplikací, správce aplikací
Aktualizace samoobslužné aplikace pro podnik | Vlastník podnikové aplikace ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudových aplikací, správce aplikací
Aktualizace vlastností jednotného přihlášení | Vlastník podnikové aplikace ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudových aplikací, správce aplikací

## <a name="entitlement-management"></a>Správa nároků
Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Přidání zdrojů do katalogu | Správce uživatele | Pomocí správy nároků můžete delegovat tuto úlohu na vlastníka katalogu ([viz dokumentace](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Přidání webů SharePointu Online do katalogu | Globální správce


## <a name="groups"></a>Skupiny

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Přiřadit licenci | Správce uživatele | 
Vytvoření skupiny | Správce uživatele | 
Vytvoření, aktualizace nebo odstranění kontroly přístupu skupiny nebo aplikace | Správce uživatele | 
Správa vypršení platnosti skupiny | Správce uživatele | 
Správa nastavení skupin | Správce skupin | Správce uživatelů | 
Přečtěte si všechny konfigurace (kromě skrytého členství) | Čtečky adresářů | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Přečtěte si skryté členství | Člen skupiny | Vlastník skupiny, správce hesel, správce Exchange, správce SharePointu, správce teams, správce uživatelů
Přečtěte si členství ve skupinách se skrytým členstvím | Správce technické podpory | Správce uživatelů, správce teams
Odvolat licenci | Správce licence | Správce uživatele
Aktualizovat členství ve skupině | Vlastník skupiny ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce uživatele
Aktualizovat vlastníky skupin | Vlastník skupiny ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce uživatele
Aktualizovat vlastnosti skupiny | Vlastník skupiny ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce uživatele

## <a name="identity-protection"></a>Identity Protection

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace upozornění| Správce zabezpečení | 
Konfigurace a povolení nebo zakázání zásad vícefaktorové hod-| Správce zabezpečení | 
Konfigurace a povolení nebo zakázání zásad rizik přihlášení| Správce zabezpečení | 
Konfigurace a povolení nebo zakázání zásad rizik uživatelů | Správce zabezpečení | 
Konfigurace týdenních přehledů | Správce zabezpečení| 
Zrušení všech detekcí rizik | Správce zabezpečení | 
Oprava nebo zrušení chyby zabezpečení | Správce zabezpečení | 
Číst celou konfiguraci | Čtečka zabezpečení | 
Přečtěte si všechny detekce rizik | Čtečka zabezpečení | 
Přečtěte si chyby zabezpečení | Čtečka zabezpečení | 

## <a name="licenses"></a>Licence

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Přiřadit licenci | Správce licence | Správce uživatele
Číst celou konfiguraci | Čtečky adresářů | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Odvolat licenci | Správce licence | Správce uživatele
Vyzkoušet nebo koupit předplatné | Správce fakturace | 


## <a name="monitoring---audit-logs"></a>Monitorování – protokoly auditu

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Čtení protokolů auditu | Čtečka sestav | Čtečka zabezpečení, správce zabezpečení

## <a name="monitoring---sign-ins"></a>Monitorování – přihlášení

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Čtení přihlašovacích protokolů | Čtečka sestav | Čtečka zabezpečení, správce zabezpečení

## <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Odstranění všech existujících hesel aplikací generovaných vybranými uživateli | Globální správce | 
Zakázat vícefaktorové finanční hod- | Globální správce | 
Povolení MFA | Globální správce | 
Správa nastavení služby MFA | Globální správce | 
Vyžadovat, aby vybraní uživatelé znovu poskytli metody kontaktu | Správce ověřování | 
Obnovení vícefaktorového ověřování na všech zapamatovaných zařízeních  | Správce ověřování | 

## <a name="mfa-server"></a>Server MFA

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Blokovat/odblokovat uživatele | Globální správce | 
Konfigurace uzamčení účtu | Globální správce | 
Konfigurace pravidel ukládání do mezipaměti | Globální správce | 
Konfigurace upozornění na podvod | Globální správce
Konfigurace oznámení | Globální správce | 
Konfigurace jednorázového obtoku | Globální správce | 
Konfigurace nastavení telefonního hovoru | Globální správce | 
Konfigurace zprostředkovatelů | Globální správce | 
Konfigurace nastavení serveru | Globální správce | 
Čtení sestavy aktivit | Globální čtečka | 
Číst celou konfiguraci | Globální čtečka | 
Čtení stavu serveru | Globální čtečka |  

## <a name="organizational-relationships"></a>Organizační vztahy

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Správa poskytovatelů identit | Správce externího zprostředkovatele identity | 
Správa nastavení | Globální správce | 
Správa podmínek použití | Globální správce | 
Číst celou konfiguraci | Globální čtečka | 

## <a name="password-reset"></a>Resetování hesla

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace metod ověřování | Globální správce |
Konfigurace vlastního nastavení | Globální správce |
Konfigurace oznámení | Globální správce |
Konfigurace místní integrace | Globální správce |
Konfigurace vlastností pro obnovení hesla | Správce uživatelů | Globální správce
Konfigurace registrace | Globální správce |
Číst celou konfiguraci | Správce zabezpečení | Správce uživatelů |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Přiřazení uživatelů k rolím | Správce privilegovaných rolí | 
Konfigurace nastavení role | Správce privilegovaných rolí | 
Zobrazit aktivitu auditu | Čtenář zabezpečení | 
Zobrazit členství v rolích | Čtenář zabezpečení | 

## <a name="roles-and-administrators"></a>Role a správci

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Správa přiřazení rolí | Správce privilegovaných rolí | 
Kontrola přístupu ke čtení role Azure AD  | Čtečka zabezpečení | Správce zabezpečení, správce privilegovaných rolí
Číst celou konfiguraci | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Zabezpečení – metody ověřování

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace metod ověřování | Globální správce | 
Číst celou konfiguraci | Globální čtečka | 

## <a name="security---conditional-access"></a>Zabezpečení – podmíněný přístup

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace důvěryhodných adres IP mfa | Správce podmíněného přístupu | 
Vytvoření vlastních ovládacích prvků | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření pojmenovaných umístění | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření zásad | Správce podmíněného přístupu | Správce zabezpečení
Vytvořit podmínky použití | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření certifikátu připojení VPN | Správce podmíněného přístupu | Správce zabezpečení
Odstranit klasické zásady | Správce podmíněného přístupu | Správce zabezpečení
Odstranit podmínky použití | Správce podmíněného přístupu | Správce zabezpečení
Odstranit certifikát připojení VPN | Správce podmíněného přístupu | Správce zabezpečení
Zakázat klasické zásady | Správce podmíněného přístupu | Správce zabezpečení
Správa vlastních ovládacích prvků | Správce podmíněného přístupu | Správce zabezpečení
Správa pojmenovaných umístění | Správce podmíněného přístupu | Správce zabezpečení
Správa podmínek použití | Správce podmíněného přístupu | Správce zabezpečení
Číst celou konfiguraci | Čtenář zabezpečení | Správce zabezpečení
Čtení pojmenovaných míst | Čtenář zabezpečení | Správce podmíněného přístupu, správce zabezpečení

## <a name="security---identity-security-score"></a>Zabezpečení – skóre zabezpečení identity

Úkol | Nejméně privilegovaná role | Další role | 
---- | --------------------- | ----------------
Číst celou konfiguraci | Čtenář zabezpečení | Správce zabezpečení
Čtení skóre zabezpečení | Čtenář zabezpečení | Správce zabezpečení
Aktualizovat stav události | Správce zabezpečení | 

## <a name="security---risky-sign-ins"></a>Zabezpečení – riskantní přihlášení

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Číst celou konfiguraci | Čtečka zabezpečení | 
Přečtěte si riskantní přihlášení | Čtečka zabezpečení | 

## <a name="security---users-flagged-for-risk"></a>Zabezpečení – uživatelé označeni příznakem rizika

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Zavřít všechny události | Správce zabezpečení | 
Číst celou konfiguraci | Čtečka zabezpečení | 
Čtení uživatelů označených příznakem rizika | Čtečka zabezpečení | 

## <a name="users"></a>Uživatelé

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Přidání role uživatele do adresáře | Správce privilegovaných rolí | 
Přidat uživatele do skupiny | Správce uživatele | 
Přiřadit licenci | Správce licence | Správce uživatele
Vytvořit uživatele typu Host | Hostující pozvač | Správce uživatele
Vytvořit uživatele | Správce uživatele | 
Odstranění uživatelů | Správce uživatele | 
Zrušení ověření aktualizačních tokenů omezených správců (viz dokumentace) | Správce uživatele | 
Zrušení ověření obnovovacích tokenů nesprávců (viz dokumentace) | Správce hesel | Správce uživatele
Zrušení platnosti aktualizačních tokenů privilegovaných správců (viz dokumentace) | Správce privilegovaného ověřování | 
Přečtěte si základní konfiguraci | Výchozí role uživatele ([viz dokumentace](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Resetovat heslo pro omezené správce (viz dokumentace) | Správce uživatele | 
Resetování hesla nesprávců (viz dokumentace) | Správce hesel | Správce uživatele
Resetování hesla privilegovaných správců | Správce privilegovaného ověřování | 
Odvolat licenci | Správce licence | Správce uživatele
Aktualizovat všechny vlastnosti kromě hlavního uživatelského jména | Správce uživatele | 
Aktualizovat hlavní uživatelské jméno pro omezené správce (viz dokumentace) | Správce uživatele | 
Aktualizovat vlastnost Hlavní jméno uživatele u privilegovaných správců (viz dokumentace) | Globální správce | 
Aktualizace uživatelských nastavení | Globální správce | 


## <a name="support"></a>Podpora

Úkol | Nejméně privilegovaná role | Další role
---- | --------------------- | ----------------
Odeslat lístek podpory | Správce služeb | Správce aplikací, Správce ochrany informací Azure, Správce fakturace, Správce cloudových aplikací, Správce dodržování předpisů, Správce dynamics 365, Správce desktopové analýzy, Správce exchange, Správce hesel, Správce Intune, Správce Skypu pro firmy, Správce Power BI, Správce privilegovaného ověřování, Správce služby SharePoint, Správce komunikace teams, Správce teams, Správce uživatelů, Správce analýzy na pracovišti

## <a name="next-steps"></a>Další kroky

* [Jak přiřadit nebo odebrat role správce azure AD](directory-manage-roles-portal.md)
* [Odkaz na role správce Azure AD](directory-assign-admin-roles.md)
