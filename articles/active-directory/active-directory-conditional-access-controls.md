---
title: Jaké jsou řízení přístupu v Azure Active Directory podmíněného přístupu? | Dokumenty Microsoft
description: Zjistěte, jak řízení přístupu v pracovním podmíněného přístupu Azure Active Directory.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 924c41b060f56b4a21e4d29e059eb2968fa19330
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232307"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Jaké jsou řízení přístupu v Azure Active Directory podmíněného přístupu? 

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), jak oprávněným uživatelům přístup můžete řídit cloudových aplikací. V zásadách podmíněného přístupu které definujete odpovědi ("to") důvod aktivován vaší zásady ("v takovém případě"). 

![Řízení](./media/active-directory-conditional-access-controls/10.png)


V kontextu podmíněného přístupu 

- "**v takovém případě**" se nazývá **podmínky**

- "**Udělejte to**" se nazývá **přístup k ovládacím prvkům**


Kombinace příkaz podmínky s ovládacími prvky představuje zásady podmíněného přístupu.

![Řízení](./media/active-directory-conditional-access-controls/61.png)

Každý ovládací prvek je buď požadavek, který musí být splněny osoba nebo systému přihlášení nebo omezení na to, co uživatel provést po přihlášení. 

Existují dva typy ovládacích prvků: 

- **Ovládací prvky grant** – Pokud chcete přístup brány

- **Ovládací prvky relace** – Pokud chcete omezit přístup v rámci relace

Toto téma vysvětluje různé ovládacích prvků, které jsou k dispozici v Azure AD podmíněného přístupu. 

## <a name="grant-controls"></a>Udělit řízení

Udělení kontrolní prvky můžete buď úplně blokovat přístup nebo povolit přístup s další požadavky výběrem požadované ovládací prvky. Pro více ovládacích prvků můžete požadovat:

- Všechny vybrané ovládací prvky, musí být splněny (*a*) 
- Vybrán jeden ovládací prvek musí být splněny (*nebo*)

![Řízení](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

Tento ovládací prvek můžete požadovat použití vícefaktorového ověřování pro přístup k zadané cloudové aplikace. Tento ovládací prvek podporuje následující zprostředkovatelé vícefaktorového: 

- Azure Multi-Factor Authentication 

- Poskytovatele služby Multi-Factor authentication na místě v kombinaci s Active Directory Federation Services (AD FS).
 
Pomocí služby Multi-Factor authentication pomáhá chránit prostředky z přistupuje neoprávněný uživatel, který může mít získal přístup k primární pověření platného uživatele.



### <a name="compliant-device"></a>Odpovídající zařízení

Můžete nakonfigurovat zásady podmíněného přístupu, které jsou založené na zařízení. Cílem zásad podmíněného přístupu na základě zařízení je k udělení přístupu k prostředkům nakonfigurované jenom z [zařízení spravovaná](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Vyžadování vyhovující zařízení je jednou z možností je nutné definovat spravované zařízení. Pokud je vybraná tato možnost, zásady podmíněného přístupu uděluje pokusy o přístup k přístupu pomocí zařízení, které jsou [zaregistrován](device-management-introduction.md) do vaší Azure Active Directory a jsou označeny jako kompatibilní řešení pro správu.

Další informace najdete v tématu [nastavit zásady podmíněného přístupu na základě zařízení služby Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="hybrid-azure-ad-joined-device"></a>Zařízení připojeného k hybridní Azure AD

Vyžadování hybridním zařízení připojeného k Azure AD je další možností je nutné konfigurovat zásady podmíněného přístupu podle zařízení. Tento požadavek se odkazuje na stolních počítačů, laptopů a tablety enterprise, které jsou připojeny k místní službě Active Directory systému Windows. Pokud je vybraná tato možnost, zásady podmíněného přístupu uděluje pokusy o přístup k přístupu pomocí zařízení, které jsou připojené k vaší místní Active Directory a Azure Active Directory.  

Další informace najdete v tématu [nastavit zásady podmíněného přístupu na základě zařízení služby Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Schválené klientské aplikace

Vzhledem k tomu, že vaši zaměstnanci používají mobilní zařízení i osobní a pracovní úkoly, můžete chtít mít možnost chránit firemní data při přístupu pomocí zařízení i v případě, kde nejsou spravovaná vámi.
Můžete použít [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) k ochraně dat vaší společnosti, nezávisle na řešení správy mobilních zařízení (MDM).


Schválené klientských aplikací, můžete vyžadovat klientskou aplikaci, která se pokusí o přístup k vaší cloudové aplikace pro podporu [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy). Například můžete omezit přístup na Exchange Online k aplikaci Outlook. Zásady podmíněného přístupu, která vyžaduje schválené klientské aplikace je také označován jako [zásady podmíněného přístupu na základě aplikace](active-directory-conditional-access-mam.md). Seznam podporovaných schválené klientských aplikací, naleznete v části [schválení klienta aplikace požadavek](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Podmínky použití

Uživatel může vyžadovat ve vašem klientovi o souhlas s podmínkami použití před udělením přístupu k prostředku. Jako správce můžete nakonfigurovat a upravit podmínky použití tím, že nahrajete dokument PDF. Pokud uživatel spadá do rozsahu toto řízení přístupu k aplikaci je udělit pouze tehdy, pokud byl souhlas s podmínkami použití. 


### <a name="custom-controls"></a>Vlastní ovládací prvky 

Můžete vytvořit vlastní ovládací prvky v podmíněného přístupu, který přesměruje uživatele na kompatibilní služby splňovat další požadavky mimo Azure Active Directory. To umožňuje použití určitých externí služby Multi-Factor authentication a zprostředkovatelů ověření vynutit pravidla podmíněného přístupu, nebo vytvořit vlastní služby. Prohlížeč vyhovět tento ovládací prvek, je přesměrován na externí služba, provede požadované ověřování nebo ověřování aktivity a je pak přesměrován zpět do Azure Active Directory. Pokud uživatel byl úspěšně ověřen nebo ověřit, uživatel bude pokračovat v toku podmíněného přístupu. 

## <a name="custom-controls"></a>Vlastní ovládací prvky

Vlastní ovládací prvky jsou funkce Azure Active Directory Premium P2 edice. Pokud používáte vlastní ovládací prvky, uživatelé jsou přesměrovány do kompatibilní služby splňovat další požadavky mimo Azure Active Directory. Prohlížeč vyhovět tento ovládací prvek, je přesměrován na externí služba, provede požadované ověřování nebo ověřování aktivity a je pak přesměrován zpět do Azure Active Directory. Azure Active Directory zkontroluje odpověď, a pokud uživatel byl úspěšně ověřen nebo ověřit, uživatel bude pokračovat v toku podmíněného přístupu.

Tyto ovládací prvky umožňují použití určité externí nebo vlastní služby jako řízení podmíněného přístupu a obecně rozšiřují možnosti podmíněného přístupu.

Zprostředkovatelé aktuálně nabízející kompatibilní službu patří:

- [Duo zabezpečení](https://duo.com/docs/azure-ca)

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Další informace o těchto služeb obraťte se na zprostředkovatele přímo.

### <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Pokud chcete vytvořit vlastní ovládací prvek, je měli kontaktovat zprostředkovatele, který chcete využívat. Každý poskytovatel jiných společností než Microsoft má svou vlastní proces a požadavky registrace, přihlášení k odběru nebo v opačném případě se stanou součástí služby a k označení, které chcete integrovat s podmíněným přístupem. V tomto bodě zprostředkovatele vám poskytne bloku dat ve formátu JSON. Tato data umožňuje zprostředkovatele a podmíněného přístupu, aby spolu spolupracovaly pro vašeho klienta, vytvoří nový ovládací prvek a definuje, jak podmíněný přístup zjistíte, pokud vaši uživatelé provedli úspěšně ověřit pomocí zprostředkovatele.

Zkopírujte JSON data a pak ji vložit do textového pole související. Neprovádějte změny do formátu JSON, pokud nerozumíte explicitně změny, které děláte. Provedení jakékoli změny může zrušit propojení mezi poskytovatelem a Microsoft a potenciálně zamknout vás a uživatele mimo vaši účty.

Možnost vytvořit vlastní ovládací prvek je v **spravovat** části **podmíněného přístupu** stránky.

![Řízení](./media/active-directory-conditional-access-controls/82.png)

Kliknutím na tlačítko **nové vlastní ovládací prvek**, otevře se okno s textové pole pro data JSON ovládacího prvku.  


![Řízení](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Odstranění vlastní ovládací prvky

Chcete-li odstranit vlastní ovládací prvek, třeba nejprve zajistit, že se již nepoužívá v žádné zásady podmíněného přístupu. Po dokončení:

1. Přejděte do vlastní ovládací prvky seznamu

2. Klikněte na tlačítko...  

3. Vyberte **Odstranit**.

### <a name="editing-custom-controls"></a>Vlastní ovládací prvky úprav

Upravit vlastní ovládací prvek, musí odstranit aktuální ovládací prvek a vytvořte nový ovládací prvek s aktualizované informace.




## <a name="session-controls"></a>Ovládací prvky relace

Ovládací prvky relací umožňují používat v cloudové aplikaci omezené možnosti. Ovládací prvky relace vynucuje cloudových aplikací a spoléhá na další informace, které poskytuje Azure AD do aplikace o relaci.

![Řízení](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Používat omezení vynucená aplikací

Tento ovládací prvek můžete použít tak, aby vyžadovala Azure AD k předání informací o zařízení ke cloudové aplikaci. To pomáhá cloudové aplikaci vědět, pokud uživatel pochází ze zařízení připojených k doméně nebo kompatibilní zařízení. Tento ovládací prvek je aktuálně podporuje jenom s SharePoint jako cloudové aplikace. SharePoint používá informace o zařízení a poskytuje uživatelům úplné nebo omezené prostředí v závislosti na stavu zařízení.
Další informace o tom, jak vyžadovat omezený přístup se službou SharePoint naleznete v tématu [řízení přístupu z nespravovaných zařízení](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 
