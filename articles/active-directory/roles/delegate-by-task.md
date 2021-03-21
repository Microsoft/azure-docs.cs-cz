---
title: Delegovat role podle úlohy správce – Azure Active Directory | Microsoft Docs
description: Role, které se mají delegovat pro úlohy identity v Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d096bcd15254df4081a005b268934659e43037d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380300"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Role správce podle úlohy správce v Azure Active Directory

V tomto článku můžete najít informace potřebné k omezení oprávnění správce uživatele, a to tak, že v Azure Active Directory (Azure AD) přiřadíte nejméně privilegované role. Úlohy správce uspořádané podle oblasti funkcí a nejnižší privilegovaná role potřebná k provedení jednotlivých úloh, spolu s dalšími rolemi, které nepatří mezi globální správce, které mohou provádět úlohy, najdete v části.

## <a name="application-proxy"></a>Proxy aplikací

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace aplikace proxy aplikací | Správce aplikace | 
Konfigurace vlastností skupiny konektorů | Správce aplikace | 
Vytvořit registraci aplikace, když je možnost zakázána pro všechny uživatele | Vývojář aplikace | Správce cloudové aplikace, Správce aplikací
Vytvořit skupinu konektorů | Správce aplikace | 
Odstranit skupinu konektorů | Správce aplikace | 
Zákaz proxy aplikací | Správce aplikace | 
Stažení služby konektoru | Správce aplikace | 
Načíst veškerou konfiguraci | Správce aplikace | 

## <a name="external-identitiesb2c"></a>Externí identity/B2C

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Vytvoření adresářů Azure AD B2C | Všichni uživatelé bez hosta ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | 
Vytváření B2Cch aplikací | Globální správce | 
Vytváření podnikových aplikací | Správce cloudové aplikace | Správce aplikace
Vytváření, čtení, aktualizace a odstraňování zásad B2C | Správce zásad IEF B2C | 
Vytváření, čtení, aktualizace a odstraňování zprostředkovatelů identity | Správce externích zprostředkovatelů identity | 
Vytváření, čtení, aktualizace a odstraňování uživatelských toků pro resetování hesel | Správce toku externího ID uživatele | 
Vytváření, čtení, aktualizace a odstraňování uživatelských toků upravujících profily | Správce toku externího ID uživatele | 
Vytváření, čtení, aktualizace a odstraňování toků přihlašování uživatelů | Správce toku externího ID uživatele | 
Vytváření, čtení, aktualizace a odstraňování toku uživatelů při registraci |Správce toku externího ID uživatele | 
Vytváření, čtení, aktualizace a odstraňování atributů uživatele | Správce atributů toku uživatele externího ID | 
Vytváření, čtení, aktualizace a odstraňování uživatelů | Správce uživatelů
Načíst veškerou konfiguraci | Globální čtenář | 
Čtení protokolů auditu B2C | Globální čtenář ([Viz dokumentace](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C globální čtenáři nemají stejná oprávnění jako globální Správci služby Azure AD. Pokud máte Azure AD B2C oprávnění globálního správce, ujistěte se, že jste v adresáři služby Azure AD B2C, a ne v adresáři Azure AD.

## <a name="company-branding"></a>Branding společnosti

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace brandingu společnosti | Globální správce | 
Načíst veškerou konfiguraci | Čtečky adresářů | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>Vlastnosti společnosti

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace vlastností společnosti | Globální správce | 

## <a name="connect"></a>Připojit

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Předávací ověřování | Globální správce  | 
Načíst veškerou konfiguraci | Globální čtenář | Globální správce  |
Bezproblémové jednotné přihlašování | Globální správce  | 

## <a name="cloud-provisioning"></a>Zřizování cloudu

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Předávací ověřování | Správce hybridní identity  | 
Načíst veškerou konfiguraci | Globální čtenář | Správce hybridní identity  |
Bezproblémové jednotné přihlašování | Správce hybridní identity  | 

## <a name="connect-health"></a>Stav připojení

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Přidat nebo odstranit služby | Vlastník ([Viz dokumentace](../hybrid/how-to-connect-health-operations.md)) | 
Použít opravy pro chybu synchronizace | Přispěvatel ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Vlastník
Konfigurace oznámení | Přispěvatel ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Vlastník
Konfigurace nastavení | Vlastník ([Viz dokumentace](../hybrid/how-to-connect-health-operations.md)) | 
Konfigurace oznámení o synchronizaci | Přispěvatel ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Vlastník
Čtení sestav zabezpečení služby ADFS | Čtenář zabezpečení | Přispěvatel, vlastník
Načíst veškerou konfiguraci | Čtenář ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Přispěvatel, vlastník
Čtení chyb synchronizace | Čtenář ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Přispěvatel, vlastník
Číst služby synchronizace | Čtenář ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Přispěvatel, vlastník
Zobrazit metriky a výstrahy | Čtenář ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Přispěvatel, vlastník
Zobrazit metriky a výstrahy | Čtenář ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Přispěvatel, vlastník
Zobrazení metrik a výstrah služby synchronizace | Čtenář ([Viz dokumentace](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Přispěvatel, vlastník

## <a name="custom-domain-names"></a>Vlastní názvy domén

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Spravovat domény | Správce názvů domén | 
Načíst veškerou konfiguraci | Čtečky adresářů | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Domain Services

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Vytvořit instanci Azure AD Domain Services | Globální správce | 
Provedení všech úloh Azure AD Domain Services | Skupina správců řadičů domény Azure AD ([Viz dokumentace](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Načíst veškerou konfiguraci | Čtečka v předplatném Azure obsahující službu služba AD DS | 

## <a name="devices"></a>Zařízení

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Zakázat zařízení | Správce cloudového zařízení | 
Povolit zařízení | Správce cloudového zařízení | 
Přečíst základní konfiguraci | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | 
Čtení klíčů nástroje BitLocker | Čtenář zabezpečení | Správce hesel, správce zabezpečení

## <a name="enterprise-applications"></a>Podnikové aplikace

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Vyjádření souhlasu s všemi delegovanými oprávněními | Správce cloudové aplikace | Správce aplikace
Souhlas s oprávněními aplikace bez zahrnutí Microsoft Graph | Správce cloudové aplikace | Správce aplikace
Souhlas s oprávněním aplikace k Microsoft Graph | Správce privilegovaných rolí | 
Vyjádření souhlasu s aplikacemi, které přistupují k vlastním datům | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | 
Vytvořit podnikovou aplikaci | Správce cloudové aplikace | Správce aplikace
Spravovat proxy aplikace | Správce aplikace | 
Spravovat uživatelská nastavení | Globální správce | 
Kontrola přístupu ke skupině nebo aplikaci v režimu čtení | Čtenář zabezpečení | Správce zabezpečení, Správce uživatelů
Načíst veškerou konfiguraci | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | 
Aktualizovat přiřazení podnikových aplikací | Vlastník podnikové aplikace ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce cloudové aplikace, Správce aplikací
Aktualizovat vlastníky podnikových aplikací | Vlastník podnikové aplikace ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce cloudové aplikace, Správce aplikací
Aktualizovat vlastnosti podnikové aplikace | Vlastník podnikové aplikace ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce cloudové aplikace, Správce aplikací
Aktualizace zřizování podnikové aplikace | Vlastník podnikové aplikace ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce cloudové aplikace, Správce aplikací
Aktualizovat samoobslužnou službu podnikových aplikací | Vlastník podnikové aplikace ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce cloudové aplikace, Správce aplikací
Aktualizovat vlastnosti jednotného přihlašování | Vlastník podnikové aplikace ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce cloudové aplikace, Správce aplikací

## <a name="entitlement-management"></a>Správa nároků
Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Přidání prostředků do katalogu | Správce uživatelů | Díky správě nároků můžete tuto úlohu delegovat na vlastníka katalogu ([Viz dokumentace](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)).
Přidat weby SharePointu Online do katalogu | Globální správce


## <a name="groups"></a>Skupiny

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Přiřadit licenci | Správce uživatelů | 
Vytvoření skupiny | Správce skupin | Správce uživatelů
Vytvoření, aktualizace nebo odstranění kontroly přístupu skupiny nebo aplikace | Správce uživatelů | 
Správa vypršení platnosti skupiny | Správce uživatelů | 
Správa nastavení skupin | Správce skupin | Správce uživatelů | 
Načíst veškerou konfiguraci (s výjimkou skrytého členství) | Čtečky adresářů | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md))
Číst skryté členství | Člen skupiny | Vlastník skupiny, správce hesel, správce serveru Exchange, Správce služby SharePoint, správce týmů, Správce uživatelů
Číst členství skupin s skrytým členstvím | Správce helpdesku | Správce uživatele, správci týmů
Odvolat licenci | Správce licencí | Správce uživatelů
Aktualizace členství ve skupině | Vlastník skupiny ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce uživatelů
Aktualizovat vlastníky skupiny | Vlastník skupiny ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce uživatelů
Aktualizovat vlastnosti skupiny | Vlastník skupiny ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | Správce uživatelů
Odstranění skupiny | Správce skupin | Správce uživatelů

## <a name="identity-protection"></a>Identity Protection

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace oznámení výstrah| Správce zabezpečení | 
Konfigurace a povolení nebo zakázání zásad vícefaktorového ověřování| Správce zabezpečení | 
Konfigurace a povolení nebo zákaz zásad rizik přihlašování| Správce zabezpečení | 
Konfigurace a povolení nebo zákaz zásad rizik uživatelů | Správce zabezpečení | 
Konfigurovat týdenní souhrny | Správce zabezpečení| 
Zavřít všechna zjišťování rizik | Správce zabezpečení | 
Opravit nebo přeskočit chybu zabezpečení | Správce zabezpečení | 
Načíst veškerou konfiguraci | Čtenář zabezpečení | 
Čtení všech detekcí rizik | Čtenář zabezpečení | 
Číst chyby zabezpečení | Čtenář zabezpečení | 

## <a name="licenses"></a>Licenses

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Přiřadit licenci | Správce licencí | Správce uživatelů
Načíst veškerou konfiguraci | Čtečky adresářů | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md))
Odvolat licenci | Správce licencí | Správce uživatelů
Vyzkoušet nebo koupit předplatné | Správce fakturace | 


## <a name="monitoring---audit-logs"></a>Monitorování – protokoly auditu

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Čtení protokolů auditu | Čtečka sestav | Čtenář zabezpečení, správce zabezpečení

## <a name="monitoring---sign-ins"></a>Monitorování – přihlášení

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Čtení protokolů přihlášení | Čtečka sestav | Čtenář zabezpečení, správce zabezpečení

## <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Odstranit všechna existující hesla aplikací vytvořená vybranými uživateli | Globální správce | 
Zakázat MFA | Správce ověřování (prostřednictvím PowerShellu) | Správce privilegovaného ověřování (prostřednictvím PowerShellu)
Povolení MFA | Správce ověřování (prostřednictvím PowerShellu) | Správce privilegovaného ověřování (prostřednictvím PowerShellu) 
Správa nastavení služby MFA | Správce zásad ověřování | 
Vyžadovat, aby vybraní uživatelé znovu poskytovali metody kontaktu | Správce ověřování | 
Obnovení Multi-Factor Authentication na všech zapamatovaných zařízeních  | Správce ověřování | 

## <a name="mfa-server"></a>Server MFA

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Blokovat/odblokovat uživatele | Správce zásad ověřování | 
Konfigurace uzamčení účtu | Správce zásad ověřování | 
Konfigurace pravidel ukládání do mezipaměti | Správce zásad ověřování | 
Konfigurace výstrahy na podvod | Správce zásad ověřování
Konfigurace oznámení | Správce zásad ověřování | 
Konfigurace jednorázového přihlášení | Správce zásad ověřování | 
Konfigurace nastavení telefonního hovoru | Správce zásad ověřování | 
Konfigurovat zprostředkovatele | Správce zásad ověřování | 
Konfigurovat nastavení serveru | Správce zásad ověřování | 
Číst sestavu aktivity | Globální čtenář | 
Načíst veškerou konfiguraci | Globální čtenář | 
Číst stav serveru | Globální čtenář |  

## <a name="organizational-relationships"></a>Organizační vztahy

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Správa zprostředkovatelů identity | Správce externích zprostředkovatelů identity | 
Správa nastavení | Globální správce | 
Správa podmínek použití | Globální správce | 
Načíst veškerou konfiguraci | Globální čtenář | 

## <a name="password-reset"></a>Resetování hesla

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace metod ověřování | Globální správce |
Konfigurace přizpůsobení | Globální správce |
Konfigurovat oznámení | Globální správce |
Konfigurace místní integrace | Globální správce |
Konfigurovat vlastnosti resetování hesla | Správce uživatelů | Globální správce
Konfigurace registrace | Globální správce |
Načíst veškerou konfiguraci | Správce zabezpečení | Správce uživatelů |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Přiřazení uživatelů k rolím | Správce privilegovaných rolí | 
Konfigurace nastavení role | Správce privilegovaných rolí | 
Zobrazit aktivitu auditu | Čtenář zabezpečení | 
Zobrazit členství v rolích | Čtenář zabezpečení | 

## <a name="roles-and-administrators"></a>Role a správci

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Správa přiřazení rolí | Správce privilegovaných rolí | 
Kontrola přístupu pro čtení role Azure AD  | Čtenář zabezpečení | Správce zabezpečení, správce privilegovaných rolí
Načíst veškerou konfiguraci | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>Zabezpečení – metody ověřování

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace metod ověřování | Globální správce | 
Konfigurace ochrany heslem | Správce zabezpečení
Konfigurace inteligentního uzamčení | Správce zabezpečení
Načíst veškerou konfiguraci | Globální čtenář | 

## <a name="security---conditional-access"></a>Zabezpečení – podmíněný přístup

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Konfigurace důvěryhodných IP adres MFA | Správce podmíněného přístupu | 
Vytváření vlastních ovládacích prvků | Správce podmíněného přístupu | Správce zabezpečení
Vytvořit pojmenovaná umístění | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření zásad | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření podmínek použití | Správce podmíněného přístupu | Správce zabezpečení
Vytvořit certifikát připojení VPN | Správce podmíněného přístupu | Správce zabezpečení
Odstranit klasické zásady | Správce podmíněného přístupu | Správce zabezpečení
Odstranit podmínek použití | Správce podmíněného přístupu | Správce zabezpečení
Odstranit certifikát připojení VPN | Správce podmíněného přístupu | Správce zabezpečení
Zakázat klasické zásady | Správce podmíněného přístupu | Správce zabezpečení
Správa vlastních ovládacích prvků | Správce podmíněného přístupu | Správce zabezpečení
Spravovat pojmenovaná umístění | Správce podmíněného přístupu | Správce zabezpečení
Správa podmínek použití | Správce podmíněného přístupu | Správce zabezpečení
Načíst veškerou konfiguraci | Čtenář zabezpečení | Správce zabezpečení
Číst pojmenovaná umístění | Čtenář zabezpečení | Správce podmíněného přístupu, správce zabezpečení

## <a name="security---identity-security-score"></a>Security – skóre zabezpečení identity

Úkol | Nejnižší privilegovaná role | Další role | 
---- | --------------------- | ----------------
Načíst veškerou konfiguraci | Čtenář zabezpečení | Správce zabezpečení
Přečíst skóre zabezpečení | Čtenář zabezpečení | Správce zabezpečení
Aktualizovat stav události | Správce zabezpečení | 

## <a name="security---risky-sign-ins"></a>Zabezpečení – rizikové přihlašovací Doplňky

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Načíst veškerou konfiguraci | Čtenář zabezpečení | 
Čtení rizikových přihlášení | Čtenář zabezpečení | 

## <a name="security---users-flagged-for-risk"></a>Zabezpečení – Uživatelé označení příznakem rizika

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Zavřít všechny události | Správce zabezpečení | 
Načíst veškerou konfiguraci | Čtenář zabezpečení | 
Čtení uživatelů označených příznakem rizika | Čtenář zabezpečení | 

## <a name="users"></a>Uživatelé

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Přidat uživatele do role adresáře | Správce privilegovaných rolí | 
Přidat uživatele do skupiny | Správce uživatelů | 
Přiřadit licenci | Správce licencí | Správce uživatelů
Vytvořit uživatele typu Host | Pozvánka hosta | Správce uživatelů
Vytvořit uživatele | Správce uživatelů | 
Odstranění uživatelů | Správce uživatelů | 
Zrušení platnosti aktualizačních tokenů omezených správců (viz dokumentace) | Správce uživatelů | 
Zrušení platnosti aktualizačních tokenů bez oprávnění správce (viz dokumentace) | Správce hesel | Správce uživatelů
Zrušení platnosti aktualizačních tokenů privilegovaných správců (viz dokumentace) | Správce privilegovaného ověřování | 
Přečíst základní konfiguraci | Výchozí role uživatele ([Viz dokumentace](../fundamentals/users-default-permissions.md) | 
Resetování hesla pro omezené správce (viz dokumentace) | Správce uživatelů | 
Resetování hesla bez správců (viz dokumentace) | Správce hesel | Správce uživatelů
Resetování hesla privilegovaných správců | Správce privilegovaného ověřování | 
Odvolat licenci | Správce licencí | Správce uživatelů
Aktualizovat všechny vlastnosti kromě hlavního názvu uživatele | Správce uživatelů | 
Aktualizace hlavního názvu uživatele pro omezené správce (viz dokumentace) | Správce uživatelů | 
Aktualizace vlastnosti hlavního názvu uživatele u privilegovaných správců (viz dokumentace) | Globální správce | 
Aktualizace uživatelských nastavení | Globální správce | 
Aktualizovat metody ověřování | Správce ověřování | Správce privilegovaného ověřování, globální správce


## <a name="support"></a>Podpora

Úkol | Nejnižší privilegovaná role | Další role
---- | --------------------- | ----------------
Odeslat lístek podpory | Správce služeb | Správce aplikace, Azure Information Protection správce, správce fakturace, správce cloudové aplikace, správce dodržování předpisů, Správce služby Dynamics 365, Správce služby Desktop Analytics, správce serveru Exchange, správce hesel, Správce služby Intune, správce Skypu pro firmy, správce Power BI, správce privilegovaného ověřování, Správce služby SharePoint, správce komunikace s týmem, správce týmů, Správce uživatelů, Správce služby Analytics na pracovišti

## <a name="next-steps"></a>Další kroky

* [Jak přiřadit nebo odebrat role správce Azure AD](manage-roles-portal.md)
* [Reference k rolím správce Azure AD](permissions-reference.md)
