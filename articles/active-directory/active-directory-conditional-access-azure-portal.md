---
title: Podmíněný přístup pro Azure Active Directory | Microsoft Docs
description: Zjistěte, jak podmíněný přístup v Azure Active Directory může pomoct při správě řízení přístupu z centrálního umístění.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 19878b7fa023ecf0d9c2a62001a5ea93ca3eb6db
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Podmíněný přístup v Azure Active Directory

Zabezpečení je velmi důležité pro organizace pomocí cloudu. Klíčovým prvkem zabezpečení cloudu je identit a přístupu, pokud jde o správu prostředků cloudu. V první mobilní, cloudové první world uživatelé mohly přistupovat k prostředkům vaší organizace pomocí různých zařízení a aplikací z libovolného místa. V důsledku toho stačí zaměřené na kdo má přístup k prostředku není dostatečná už. Chcete-li hlavní rovnováhu mezi zabezpečením a produktivitu, odborníci v oblasti IT muset zohlednit, jak se přístupu k prostředku do rozhodnutí o řízení přístupu. Pomocí podmíněného přístupu Azure AD které můžete vyřešit tento požadavek. Podmíněný přístup je funkce služby Azure Active Directory, která umožňuje vynutit ovládacích prvků na přístup k aplikacím ve vašem prostředí na základě určitých podmínek z centrálního umístění. 


![Řízení](./media/active-directory-conditional-access-azure-portal/81.png)

Tento článek vám poskytne koncepční přehled podmíněného přístupu ve službě Azure AD.


## <a name="common-scenarios"></a>Obvyklé scénáře

První mobilní, cloudové první světě Azure Active Directory umožňuje jednotné přihlašování k zařízení, aplikacím a službám odkudkoli. S jak narůstá počet zařízení (včetně BYOD), pracovní vypnout firemním sítím a třetích stran aplikace SaaS, odborníci v oblasti IT potýkají s dva dosáhnout cíle:

- Posílení produktivity uživatelů k dosažení produktivity. kdykoli a kdekoli
- Ochrana podnikových prostředků kdykoli

Pomocí zásady podmíněného přístupu můžete použít ovládací prvky správná přístupová práva v rámci požadované podmínky. Podmíněný přístup pro Azure AD poskytuje dodatečné zabezpečení potřeby a zůstane mimo způsob, jak vaše uživatele, když není. 

Tady jsou některé běžné aspekty přístupu, které podmíněného přístupu vám může pomoct s:



- **[Přihlášení riziko](active-directory-conditional-access-conditions.md#sign-in-risk)**: Azure AD Identity Protection zjistí přihlášení rizika. Jak můžete omezit přístup, pokud zjištěné riziko přihlášení označuje objektu actor chybný? Co dělat, když chcete získat silnější důkaz, že přihlášení byla provedena legitimní uživatel vaší pochybnosti jsou nebo dostatečně pevné i blokovat konkrétním uživatelům v přístupu k aplikaci?

- **[Síťové umístění](active-directory-conditional-access-locations.md)**: Azure AD je přístupná odkudkoli. Co dělat, když se provádí pokus o přístup ze síťové umístění, které není pod kontrolou oddělení IT? Pomocí kombinace uživatelského jména a hesla může být dostatečně dobrý pokusí ověření identity pro přístup k prostředkům z vaší podnikové sítě. Co dělat, když jste vyžádání silnější ověření identity pro přístup pokusí který se spouští z jiných neočekávané zemích nebo oblastech světa? Co když i chcete zablokovat přístup pokusí z určitých umístění?  

- **[Správa zařízení](active-directory-conditional-access-conditions.md#device-platforms)**: ve službě Azure AD, uživatelé můžou používat cloudové aplikace z široké škály zařízení, včetně mobilních a také osobní zařízení. Co dělat, pokud je požadavek, který pokusy o přístup musí provést pouze pomocí zařízení, které spravuje vaše IT oddělení? Co dělat, když chcete i zablokovat určité typy zařízení přístup cloudových aplikací ve vašem prostředí? 

- **[Klientská aplikace](active-directory-conditional-access-conditions.md#client-apps)**: v současné době můžete získat přístup k mnoha cloudových aplikací pomocí různých typů aplikací jako webové aplikace, mobilní aplikace nebo aplikace klasické pracovní plochy. Pokus o přístup jak postupovat, pokud se provádí pomocí typ aplikace klienta, který způsobuje, že známé problémy? Co když potřebujete zařízení, které spravuje vaše IT oddělení pro určité typy aplikací? 

Tyto otázky a odpovědi související představují obvyklé scénáře přístupu podmíněného přístupu Azure AD. Podmíněný přístup je funkce služby Azure Active Directory, která umožňuje zpracovávat scénáře přístup přístup na základě zásad.


## <a name="conditional-access-policies"></a>Zásady podmíněného přístupu

Zásady podmíněného přístupu je definice scénářem přístup pomocí následujícího vzorce:

![Řízení](./media/active-directory-conditional-access-azure-portal/10.png)

**Udělejte to** definuje odpověď vaše zásady. Je důležité si uvědomit, že cílem zásad podmíněného přístupu není k udělení přístupu k cloudové aplikace. Ve službě Azure AD udělení přístupu do cloudových aplikací je předmětem přiřazení uživatele. Se zásadami podmíněného přístupu, řízení jak oprávněným uživatelům (uživatelů, kteří mají udělen přístup ke cloudové aplikaci) můžete získat přístup k aplikacím cloudu pro určité podmínky. V odpovědi vynutit další požadavky, jako je vícefaktorové ověřování, spravované zařízení a dalších. V kontextu podmíněného přístupu Azure AD se nazývají požadavky, které vynucuje zásady řízení přístupu. Ve formuláři nejvíc omezující zásady může blokovat přístup. Další informace najdete v tématu [přístup k ovládacím prvkům v Azure Active Directory podmíněného přístupu](active-directory-conditional-access-controls.md).
     

**V takovém případě** definuje důvod aktivován vaší zásady. Z tohoto důvodu je charakterizovaná skupinu podmínek, které byly splněny. V Azure AD podmíněný přístup podmínek dvě přiřazení důležitou úlohu:

- **[Uživatelé](active-directory-conditional-access-conditions.md#users-and-groups)**: provádění pokus o přístupu uživatele (**kdo**). 

- **[Cloudových aplikací](active-directory-conditional-access-conditions.md#cloud-apps)**: cíle pokus o přístup (**co**).    

Tyto dvě podmínky jsou povinné v zásadách podmíněného přístupu. Kromě dvě povinné podmínky můžete použít také další podmínky, které popisují, jak se provádí pokus o přístup. Běžných příkladů používají mobilní zařízení nebo umístění, které jsou mimo podnikovou síť. Další informace najdete v tématu [podmínky v Azure Active Directory podmíněného přístupu](active-directory-conditional-access-conditions.md).   

Kombinace podmínky s ovládacími prvky přístup představuje zásady podmíněného přístupu. 

![Řízení](./media/active-directory-conditional-access-azure-portal/51.png)

Pomocí podmíněného přístupu Azure AD, můžete určit, jak Autorizovaní uživatelé můžou používat vaše cloudové aplikace. Cílem zásad podmíněného přístupu je vynutit ovládací prvky další přístup při pokusu o přístup cloudové aplikace, které vycházejí z jak se provádí pokus o přístup.

Jednou z výhod použití o přístupu na základě zásad k ochraně přístup k vaší cloudové aplikace je, že můžete spustit konceptu požadavky zásad pro vaše prostředí pomocí strukturu uvedených v tomto článku bez obav o technickou implementaci. 

## <a name="what-you-need-to-know"></a>Co potřebujete vědět

### <a name="general-requirements-for-using-conditional-access"></a>Obecné požadavky pro použití podmíněného přístupu

Azure AD podmíněného přístupu můžete použít k ochraně aplikací cloudu při pokusu o ověření pochází z:

- Webový prohlížeč

- Klientská aplikace používající moderní ověřování

- Exchange ActiveSync

Další informace najdete v tématu [klientské aplikace](active-directory-conditional-access-conditions.md#client-apps).

Některé [cloudových aplikací](active-directory-conditional-access-conditions.md#cloud-apps) také podporují starší verze ověřovací protokoly. To platí, například pro SharePoint Online a Exchange Online. Když klientské aplikace můžete použít starší verze ověřovací protokol pro přístup k cloudové aplikace, Azure AD nelze vynutit zásady podmíněného přístupu na tento pokus o přístup. Abyste zabránili obcházení vynucení zásad klientskou aplikaci, byste měli zkontrolovat, zda je možné pouze povolit moderní ověřování na ohroženým cloudových aplikací.

Příklady pro klienta, které aplikace podmíněného přístupu se nevztahuje na jsou:

- Office 2010 a starší

- Office 2013, když není povolené moderní ověřování

Další informace najdete v tématu [nastavení služby SharePoint Online a Exchange Online pro Azure Active Directory podmíněného přístupu](active-directory-conditional-access-no-modern-authentication.md).


### <a name="license-requirements-for-using-conditional-access"></a>Licenční požadavky pro použití podmíněného přístupu

Použití podmíněného přístupu vyžaduje licenci Azure AD Premium. Pravé licencí pro vaše požadavky, najdete v tématu [porovnávání všeobecně dostupná funkce edice Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory/).


## <a name="next-steps"></a>Další postup

- Pokud chcete vědět více o:
    - Podmínky, najdete v části [podmínky v Azure Active Directory podmíněného přístupu](active-directory-conditional-access-conditions.md).

    - Přístup k ovládacím prvkům najdete v tématu [přístup k ovládacím prvkům v Azure Active Directory podmíněného přístupu](active-directory-conditional-access-controls.md).

- Pokud chcete získat nějaké zkušenosti s konfigurace zásad podmíněného přístupu najdete v tématu [Začínáme s podmíněným přístupem v Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Pokud chcete plán podrobný postup nasazení s doporučenou zásady, najdete v článku [plán nasazení podmíněného přístupu](http://aka.ms/conditionalaccessdeploymentplan)
