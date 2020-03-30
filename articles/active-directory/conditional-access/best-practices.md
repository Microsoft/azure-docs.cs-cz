---
title: Doporučené postupy pro podmíněný přístup ve službě Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o věcech, které byste měli vědět, a co byste se měli vyhnout při konfiguraci zásad podmíněného přístupu.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295363"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Doporučené postupy pro podmíněný přístup ve službě Azure Active Directory

S [podmíněným přístupem Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)můžete řídit, jak oprávnění uživatelé přistupují k vašim cloudovým aplikacím. Tento článek obsahuje informace o:

- Věci, které byste měli vědět 
- Co je třeba se vyhnout při konfiguraci zásad podmíněného přístupu. 

Tento článek předpokládá, že jste obeznámeni s koncepty a terminologií uvedenou v části [Co je podmíněný přístup ve službě Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Co je potřeba k tomu, aby politika fungovala?

Když vytvoříte novou zásadu, nebudou vybráni žádní uživatelé, skupiny, aplikace ani ovládací prvky přístupu.

![Cloudové aplikace](./media/best-practices/02.png)

Chcete-li, aby vaše zásady fungovaly, je nutné nakonfigurovat:

| Co           | Postup                                  | Proč |
| :--            | :--                                  | :-- |
| **Cloudové aplikace** |Vyberte jednu nebo více aplikací.  | Cílem zásad podmíněného přístupu je umožnit vám řídit, jak mohou oprávnění uživatelé přistupovat ke cloudovým aplikacím.|
| **Uživatelé a skupiny** | Vyberte alespoň jednoho uživatele nebo skupinu, která má oprávnění k přístupu k vybraným cloudovým aplikacím. | Zásada podmíněného přístupu, která nemá přiřazeny žádné uživatele a skupiny, se nikdy neaktivuje. |
| **Řízení přístupu** | Vyberte alespoň jeden ovládací prvek přístupu. | Pokud jsou vaše podmínky splněny, procesor zásad musí vědět, co dělat. |

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="how-are-conditional-access-policies-applied"></a>Jak se zásady podmíněného přístupu používají?

Při přístupu ke cloudové aplikaci se může použít více zásad podmíněného přístupu. V takovém případě musí být splněny všechny zásady, které platí. Například pokud jedna zásada vyžaduje vícefaktorové ověřování (MFA) a jiné vyžaduje kompatibilní zařízení, musíte dokončit vícefaktorové ověřování a používat kompatibilní zařízení. 

Všechny zásady jsou vynuceny ve dvou fázích:

- Fáze 1: 
   - Kolekce podrobností: Shromážděte podrobnosti k identifikaci zásad, které by již byly splněny.
   - Během této fáze se uživatelům může zobrazit výzva k certifikátu, pokud je dodržování předpisů zařízení součástí zásad podmíněného přístupu. Tato výzva může nastat pro aplikace prohlížeče, pokud operační systém zařízení není Windows 10.
   - Fáze 1 vyhodnocení zásad probíhá pro všechny povolené zásady a zásady v [režimu pouze pro sestavu](concept-conditional-access-report-only.md).
- Fáze 2:
   - Vynucení: S přihlédnutím k údajům shromážděným ve fázi 1 požádejte uživatele, aby splnil všechny další požadavky, které nebyly splněny.
   - Použít výsledky relace. 
   - Fáze 2 vyhodnocení zásad probíhá pro všechny povolené zásady.

### <a name="how-are-assignments-evaluated"></a>Jak se vyhodnocují přiřazení?

Všechna přiřazení jsou logicky **anded**. Pokud máte nakonfigurováno více než jedno přiřazení, musí být všechna přiřazení splněna, aby byla zásada nastavena.  

Pokud potřebujete nakonfigurovat podmínku umístění, která se vztahuje na všechna připojení z mimo síť vaší organizace:

- Zahrnout **všechna místa**
- Vyloučit **všechny důvěryhodné IP adresy**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co dělat, když jste uzamčeni z portálu pro správu Azure AD?

Pokud jste uzamčeni z portálu Azure AD z důvodu nesprávné nastavení v zásadách podmíněného přístupu:

- Zkontrolujte, zda jsou ve vaší organizaci další správci, kteří ještě nejsou blokováni. Správce s přístupem k portálu Azure můžete zakázat zásady, které má vliv na vaše přihlášení. 
- Pokud žádný ze správců ve vaší organizaci nemůže zásadu aktualizovat, je třeba odeslat žádost o podporu. Podpora společnosti Microsoft může kontrolovat a aktualizovat zásady podmíněného přístupu, které brání přístupu.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co se stane, když máte nakonfigurované zásady na klasickém portálu Azure a na webu Azure?  

Obě zásady jsou vynuceny službou Azure Active Directory a uživatel získá přístup pouze v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co se stane, když máte zásady na portálu Intune Silverlight a na portálu Azure?

Obě zásady jsou vynuceny službou Azure Active Directory a uživatel získá přístup pouze v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co se stane, když mám nakonfigurované více zásad pro stejného uživatele?  

Pro každé přihlášení služba Azure Active Directory vyhodnotí všechny zásady a zajistí, že všechny požadavky budou splněny před udělením přístupu k uživateli. Blokový přístup trumfne všechna ostatní nastavení konfigurace. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Funguje podmíněný přístup se službou Exchange ActiveSync?

Ano, Exchange ActiveSync můžete použít v zásadách podmíněného přístupu.

Některé cloudové aplikace, jako je SharePoint Online a Exchange Online, taky podporují starší ověřovací protokoly. Když klientská aplikace může používat starší ověřovací protokol pro přístup ke cloudové aplikaci, Azure AD nemůže vynutit zásady podmíněného přístupu na tento pokus o přístup. Chcete-li zabránit klientské aplikaci v vynechání vynucení zásad, měli byste zkontrolovat, zda je možné povolit pouze moderní ověřování v ohrožených cloudových aplikacích.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Jak byste měli nakonfigurovat podmíněný přístup pomocí aplikací Office 365?

Vzhledem k tomu, že aplikace Office 365 jsou propojené, doporučujeme při vytváření zásad přiřazovat běžně používané aplikace společně.

Mezi běžné propojené aplikace patří Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online a Office 365 Yammer.

Je důležité pro zásady, které vyžadují interakce uživatele, jako je vícefaktorové ověřování, při řízení přístupu na začátku relace nebo úkolu. Pokud tak nechcete, uživatelé nebudou moci v aplikaci dokončit některé úkoly. Pokud například pro přístup ke SharePointu vyžadujete vícefaktorové ověřování na nespravovaných zařízeních, ale ne k e-mailu, uživatelé pracující v e-mailu nebudou moct ke zprávě připojit soubory SharePointu. Další informace najdete v článku [Co jsou závislosti služeb v podmíněném přístupu služby Azure Active Directory?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Co byste se měli vyhnout dělat

Rozhraní podmíněného přístupu poskytuje velkou flexibilitu konfigurace. Velká flexibilita však také znamená, že byste měli pečlivě zkontrolovat každou zásadu konfigurace před uvolněním, abyste se vyhnuli nežádoucím výsledkům. V této souvislosti byste měli věnovat zvláštní pozornost přiřazením, která ovlivňují kompletní sady, jako jsou **všichni uživatelé / skupiny / cloudové aplikace**.

Ve vašem prostředí byste se měli vyhnout následujícím konfiguracím:

**Pro všechny uživatele všechny cloudové aplikace:**

- **Blokovat přístup** – tato konfigurace blokuje celou organizaci, což rozhodně není dobrý nápad.
- **Vyžadovat kompatibilní zařízení** – pro uživatele, kteří ještě nezaregistrovali svá zařízení, tato zásada blokuje veškerý přístup včetně přístupu k portálu Intune. Pokud jste správce bez zaregistrovaného zařízení, tato zásada vám zablokuje návrat na portál Azure a zásady změníte.
- **Vyžadovat připojení k doméně** – Tento přístup k blokování zásad má také potenciál blokovat přístup pro všechny uživatele ve vaší organizaci, pokud ještě nemáte zařízení spojené s doménou.
- **Vyžadovat zásady ochrany aplikací** – Tento přístup k blokování zásad má také potenciál blokovat přístup pro všechny uživatele ve vaší organizaci, pokud nemáte zásady Intune. Pokud jste správce bez klientské aplikace, která má zásady ochrany aplikací Intune, tato zásada vám zabrání dostat se zpět na portály, jako je Intune a Azure.

**Pro všechny uživatele, všechny cloudové aplikace, všechny platformy zařízení:**

- **Blokovat přístup** – tato konfigurace blokuje celou organizaci, což rozhodně není dobrý nápad.

## <a name="how-should-you-deploy-a-new-policy"></a>Jak byste měli nasadit novou zásadu?

Jako první krok byste měli vyhodnotit zásady pomocí [nástroje co když](what-if-tool.md).

Když jsou nové zásady připraveny pro vaše prostředí, nasazujte je ve fázích:

1. Použijte zásadu pro malou sadu uživatelů a ověřte, že se chová podle očekávání. 
1. Když rozbalíte zásadu tak, aby zahrnovala více uživatelů. Pokračujte v vyloučení všech správců ze zásady, abyste zajistili, že budou mít stále přístup, a v případě potřeby můžete zásadu aktualizovat.
1. Zásadu použijte pro všechny uživatele pouze v případě potřeby. 

Osvědčeným postupem je vytvořit uživatelský účet, který je:

- Věnováno správě politik 
- Vyloučeno ze všech vašich zásad

## <a name="policy-migration"></a>Migrace zásad

Zvažte migraci zásad, které jste na webu Azure Portal nevytvořili, protože:

- Nyní můžete řešit scénáře, které jste dříve nemohli zpracovat.
- Můžete snížit počet zásad, které je třeba spravovat jejich sloučením.   
- Všechny zásady podmíněného přístupu můžete spravovat v jednom centrálním umístění.
- Klasický portál Azure byl vyřazen.   

Další informace najdete v článku [Migrace zásad z portálu Classic na portálu Azure Portal](policy-migration.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete vědět:

- Jak nakonfigurovat zásady podmíněného přístupu, najdete [v tématu Vyžadovat vícefaktorové informace pro konkrétní aplikace s podmíněným přístupem Služby Azure Active Directory](app-based-mfa.md).
- Jak naplánovat zásady podmíněného přístupu, najdete v [tématu Jak naplánovat nasazení podmíněného přístupu ve službě Azure Active Directory](plan-conditional-access.md).
