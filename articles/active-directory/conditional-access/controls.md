---
title: Co jsou řízení přístupu v Azure Active Directory podmíněného přístupu? | Dokumenty Microsoft
description: Zjistěte, jak řízení přístupu v Azure Active Directory podmíněný přístup do práce.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ba770415d7371463f1370d982f179c2a6f41ee65
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629554"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Co jsou řízení přístupu v Azure Active Directory podmíněného přístupu? 

S [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), jak ověřeného přístupu uživatele můžete řídit vašich cloudových aplikacích. V zásadách podmíněného přístupu definujte odpověď ("to") důvod Aktivace zásady ("Když taková situace nastane"). 

![Řízení](./media/controls/10.png)


V souvislosti s podmíněným přístupem 

- "**v takovém případě**" se nazývá **podmínky**

- "**Proveďte to**" se nazývá **ovládací prvky přístupu**


Kombinace příkaz podmínky s ovládacími prvky představuje zásad podmíněného přístupu.

![Řízení](./media/controls/61.png)

Každý ovládací prvek je požadavek, který musí být splněny osoba nebo systém přihlášení nebo omezení na to, co uživatel můžete provést po přihlášení. 

Existují dva typy ovládacích prvků: 

- **Udělit řízení** – Pokud chcete přístup k bráně

- **Ovládací prvky relací** – Pokud chcete omezit přístup v rámci relace

Toto téma vysvětluje různé ovládací prvky, které jsou k dispozici v podmíněný přístup Azure AD. 

## <a name="grant-controls"></a>Udělit řízení

Pomocí udělení ovládacích prvků můžete zablokovat přístup úplně nebo povolení přístupu Další požadavky tak, že vyberete požadované ovládací prvky. Pro více ovládacích prvků můžete vyžadovat, aby:

- Všechny vybrané ovládací prvky musí být splněny (*a*) 
- Jeden vybraný ovládací prvek musí být splněny (*nebo*)

![Řízení](./media/controls/17.png)



### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

Tento ovládací prvek můžete vyžadovat vícefaktorové ověřování pro přístup k zadané cloudové aplikace. Tento ovládací prvek podporuje následující poskytovatele služby Multi-Factor Authentication: 

- Azure Multi-Factor Authentication 

- Poskytovatele služby Multi-Factor authentication v místním v kombinaci s Active Directory Federation Services (AD FS).
 
Pomocí služby Multi-Factor authentication pomáhá chránit prostředky přístup neautorizovanému uživateli, který může být získali přístup k primární přihlašovací údaje platného uživatele.



### <a name="compliant-device"></a>Odpovídající zařízení

Můžete nakonfigurovat zásady podmíněného přístupu, které jsou založené na zařízení. Cílem zásad podmíněného přístupu podle zařízení je udělit přístup k nakonfigurované prostředky jenom z [spravovaných zařízeních](require-managed-devices.md). Vyžadování vyhovující zařízení je jedna možnost, budete muset definovat se spravované zařízení. Pokud je vybraná tato možnost, své zásady podmíněného přístupu uděluje pokusů o přístup k zařízení, které jsou [zaregistrovaný](../devices/overview.md) k Azure Active Directory a jsou označené jako vyhovující, pomocí Intune (pro libovolné zařízení, operačního systému) nebo vaší systém MDM třetí strany pro zařízení s Windows 10. Systémy MDM třetí strany pro typy zařízení, operačního systému než Windows 10 nejsou podporovány.

Další informace najdete v tématu [nastavit zásady podmíněného přístupu podle zařízení Azure Active Directory](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Zařízení připojené k hybridní službě Azure AD

Vyžaduje hybridní zařízení připojeného k Azure AD je další možností je třeba nakonfigurovat zásady podmíněného přístupu podle zařízení. Tento požadavek se vztahuje na Windows stolní počítače, přenosné počítače a organizace tablety, které jsou připojené k místní Active Directory. Pokud je vybraná tato možnost, své zásady podmíněného přístupu uděluje pokusů o přístup k zařízení, které jsou připojené k vaší místní Active Directory a Azure Active Directory.  

Další informace najdete v tématu [nastavit zásady podmíněného přístupu podle zařízení Azure Active Directory](require-managed-devices.md).





### <a name="approved-client-app"></a>Klientem schválenou aplikaci

Vzhledem k tomu, že vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly, můžete chtít mít možnost chránit firemní data při přístupu pomocí zařízení i v případě, kdy nejsou spravujete sami.
Můžete použít [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) k ochraně dat vaší společnosti není závislá na řešení správy mobilních zařízení (MDM).


S klientem schválených aplikací, můžete vyžadovat, aby klientská aplikace, která se pokusí o přístup k vašim cloudovým aplikacím pro podporu [zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy). Například můžete omezit přístup k Exchangi Online k aplikaci Outlook. Zásady podmíněného přístupu, která vyžaduje schválené klientské aplikace se také označuje jako [zásady podmíněného přístupu na základě aplikace](app-based-conditional-access.md). Seznam podporovaných klientem schválených aplikací najdete v tématu [schválené klientské aplikace požadavek](technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Podmínky použití

Uživatel může vyžadovat ve vašem tenantovi vyjádřit souhlas s podmínkami použití před udělením přístupu k prostředku. Jako správce můžete nakonfigurovat a upravit podmínky použití nahráním dokumentu PDF. Pokud uživatel spadá do rozsahu tato řízení přístupu k aplikaci je udělit pouze tehdy, pokud bylo dohodnuto podmínky použití. 


### <a name="custom-controls"></a>Vlastní ovládací prvky 

Můžete vytvořit vlastní ovládací prvky podmíněného přístupu, který přesměrovat uživatele na kompatibilní služby splňovat další požadavky mimo službu Azure Active Directory. To umožňuje použít některé externí ověřování službou Multi-Factor Authentication a zprostředkovatelů ověření vynutit pravidla podmíněného přístupu, nebo vytvářet vlastní služby. Tím se uspokojí tento ovládací prvek, prohlížeče uživatele přesměruje na externí služby, provede všechny požadované ověřování nebo ověřování aktivity a je následně přesměrován zpět do Azure Active Directory. Pokud si uživatel byl úspěšně ověřen nebo ověřit, uživatel bude pokračovat tok podmíněného přístupu. 

## <a name="custom-controls"></a>Vlastní ovládací prvky

Vlastní ovládací prvky jsou funkcí edice Azure Active Directory Premium P1. Při použití vlastních ovládacích prvků, vaši uživatelé přesměrovaní na kompatibilní služby splňovat další požadavky mimo službu Azure Active Directory. Tím se uspokojí tento ovládací prvek, prohlížeče uživatele přesměruje na externí služby, provede všechny požadované ověřování nebo ověřování aktivity a je následně přesměrován zpět do Azure Active Directory. Azure Active Directory ověří odpověď, a pokud si uživatel byl úspěšně ověřen nebo ověřit, uživatel bude pokračovat tok podmíněného přístupu.

Tyto ovládací prvky umožňují používat některé externí nebo vlastní služby jako řízení podmíněného přístupu a obecně rozšířit možnosti podmíněného přístupu.

Poskytovatelé aktuálně nabízí kompatibilní služby patří:

- [Duo zabezpečení](https://duo.com/docs/azure-ca)

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Další informace o těchto služeb obraťte se na zprostředkovatele přímo.

### <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Chcete-li vytvořit vlastní ovládací prvek, obrátíte nejprve poskytovatele, který chcete využívat. Každý poskytovatel jiného subjektu než Microsoft má vlastní proces a požadavky registrace, přihlášení k odběru nebo v opačném případě se stanou součástí služby a k označení, kterou chcete integrovat s podmíněným přístupem. V tomto okamžiku poskytovateli vám poskytne bloku dat ve formátu JSON. Tato data umožňuje zprostředkovatele a podmíněného přístupu spolupráce pro vašeho tenanta, vytvoří nový ovládací prvek a definuje, jak podmíněný přístup můžete říct, pokud vaši uživatelé úspěšně provedly ověřování s poskytovateli.

Zkopírujte JSON data a vložte ho do souvisejícího textového pole. Neprovádějte žádné změny ve formátu JSON pokud rozumíte explicitně změnu, kterou děláte. Provedení jakékoli změny může přerušit spojení mezi poskytovatelem a Microsoft a potenciálně uzamčení vy a vaši uživatelé z vašich účtů.

Možnost vytvořit vlastní ovládací prvek je v **spravovat** část **podmíněného přístupu** stránky.

![Řízení](./media/controls/82.png)

Kliknutím na **nový vlastní ovládací prvek**, otevře se okno s textové pole pro data JSON ovládacího prvku.  


![Řízení](./media/controls/81.png)


### <a name="deleting-custom-controls"></a>Odstraňuje se vlastní ovládací prvky

Pokud chcete odstranit vlastní ovládací prvek, musíte nejdřív zajistit, že ho nepoužívá v jakékoli zásady podmíněného přístupu. Po dokončení:

1. Přejděte k seznamu vlastní ovládací prvky

2. Klikněte na tlačítko...  

3. Vyberte **Odstranit**.

### <a name="editing-custom-controls"></a>Vlastní ovládací prvky úprav

Chcete-li upravit vlastní ovládací prvek, musíte odstranit aktuální ovládací prvek a vytvořit nový ovládací prvek aktualizované informace.




## <a name="session-controls"></a>Ovládací prvky relací

Ovládací prvky relací umožňují používat v cloudové aplikaci omezené možnosti. Ovládací prvky relací neprosazují cloudových aplikací a Spolehněte se na další informace, které poskytuje Azure AD do aplikace o relaci.

![Řízení](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Používat omezení vynucená aplikací

Tento ovládací prvek můžete použít tak, aby vyžadovala Azure AD k předání informací o zařízení ke cloudové aplikaci. To pomáhá cloudovou aplikaci vědět, pokud uživatel pochází ze zařízení připojených k doméně a vyhovující zařízení. Tento ovládací prvek je momentálně podporovaná jenom s SharePoint jako cloudové aplikace. SharePoint používá informace o zařízení můžete poskytnout uživatelům omezené nebo úplné možnosti v závislosti na stavu zařízení.
Další informace o tom, jak vyžadovat omezený přístup se Sharepointem najdete v tématu [řízení přístupu z nespravovaných zařízení](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 
