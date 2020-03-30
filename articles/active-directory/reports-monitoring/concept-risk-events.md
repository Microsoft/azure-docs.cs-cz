---
title: Zjišťování rizik služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Tento artice vám poskytuje podrobný přehled o tom, co jsou detekce rizik.
services: active-directory
keywords: ochrana identity služby Azure Active Directory, zabezpečení, riziko, úroveň rizika, zranitelnost, zásady zabezpečení
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383952"
---
# <a name="azure-active-directory-risk-detections"></a>Detekce rizik služby Azure Active Directory

K převážné většině narušení zabezpečení dochází, když útočníci získají přístup k prostředí krádeží identity uživatele. Zjišťování kompromitovaných identit není snadný úkol. Azure Active Directory používá adaptivní algoritmy strojového učení a heuristiky k detekci podezřelých akcí, které souvisejí s vašimi uživatelskými účty. Každá zjištěná podezřelá akce je uložena v záznamu nazývaném **detekce rizik**.

Existují dvě místa, kde kontrolujete nahlášené detekce rizik:

 - **Sestavy Azure AD** – zjišťování rizik jsou součástí sestav zabezpečení Azure AD. Další informace naleznete [v protokolu o zabezpečení uživatelů v ohrožení](concept-user-at-risk.md) a v zprávě o zabezpečení [rizikových přihlášení](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** – detekce rizik jsou také součástí možností vytváření sestav [azure active directory identity protection](../active-directory-identityprotection.md).

Kromě toho můžete pomocí [rozhraní API detekce rizik ochrany identity](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) získat programový přístup k detekci zabezpečení pomocí aplikace Microsoft Graph. Další informace najdete [v tématu Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](../identity-protection/graph-get-started.md). 

Služba Azure Active Directory v současné době detekuje šest typů detekcí rizik:

- [Uživatelé s uniklými přihlašovacími údaji](#leaked-credentials) 
- [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) 
- [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) 
- [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) 
- [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) 

![Detekce rizik](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> V některých případě můžete najít detekci rizik bez odpovídající přihlášení v [sestavě přihlášení](concept-sign-ins.md). Je to proto, že ochrana identity vyhodnocuje riziko pro **interaktivní** i **neinteraktivní** přihlášení, zatímco sestava přihlášení zobrazuje pouze interaktivní přihlášení.

Přehled, který získáte pro detekci zjištěných rizik, je vázaný na vaše předplatné Azure AD. 

* S **edicí Azure AD Premium P2**získáte nejpodrobnější informace o všech základních detekcích. 
* S **edicí Azure AD Premium P1**se pokročilá zjišťování (například neznámé přihlašovací služby) nevztahuje vaše licence a zobrazí se pod názvem **Přihlášení s dalším zjištěným rizikem**. Kromě toho jsou skryta pole úrovně rizika a podrobností rizika.

Zatímco detekce detekce rizik již představuje důležitý aspekt ochrany vašich identit, máte také možnost ručně je řešit nebo implementovat automatické odpovědi konfigurací zásad podmíněného přístupu. Další informace najdete v článku [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Typy detekce rizik

Vlastnost **typu detekce rizika** je identifikátor pro podezřelou akci, pro kterou byl vytvořen záznam detekce rizik.

Neustálé investice společnosti Microsoft do procesu zjišťování vedou k:

- Zlepšení detekční přesnosti stávajících detekcí rizik 
- Nové typy detekce rizik, které budou přidány v budoucnu

### <a name="leaked-credentials"></a>Uniklá pověření

Když počítačoví zločinci ohrožují platná hesla legitimních uživatelů, často tyto přihlašovací údaje sdílejí. To se obvykle provádí tím, že vysílání je veřejně na tmavém webu nebo vložit stránky nebo obchodování nebo prodej pověření na černém trhu. Služba Microsoft leaked credentials získává dvojice uživatelského jména a hesla sledováním veřejných a tmavých webových stránek a prací s:

- Výzkumníci
- Donucovacími
- Security teams ve společnosti Microsoft
- Jiné důvěryhodné zdroje 

Když služba získá dvojice uživatelského jména a hesla, jsou kontrolovány podle aktuálních platných pověření uživatelů AAD. Pokud je nalezena shoda, znamená to, že heslo uživatele bylo ohroženo a je vytvořena **detekce rizika uniklých pověření.**

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Tento typ zjišťování rizik identifikuje uživatele, kteří se úspěšně přihlásili z adresy IP, která byla identifikována jako anonymní adresa IP serveru proxy. Tyto proxy servery používají uživatelé, kteří chtějí skrýt IP adresu svého zařízení, a mohou být použity pro škodlivé úmysly.

### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Tento typ zjišťování rizika identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde alespoň jedno z umístění může být také atypické pro uživatele, vzhledem k minulému chování. Mezi několika dalšími faktory tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a čas, který by uživateli trvalo, než cestoval z prvního místa do druhého, což naznačuje, že jiný uživatel používá stejné Pověření.

Algoritmus ignoruje zjevné "falešně pozitivní výsledky", které přispívají k nemožným podmínkám cestování, jako jsou virtuální ny a místa pravidelně používaná ostatními uživateli v organizaci. Systém má počáteční období učení 14 dnů, během kterého se učí chování nového uživatele přihlášení. 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Tento typ detekce rizik a určuje nová / neznámá místa. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje tato "známá" umístění. Zjišťování rizika se aktivuje, když dojde k přihlášení z umístění, které ještě není v seznamu známých umístění. Systém má počáteční období učení 30 dnů, během kterého neoznačuje žádná nová místa jako neznámá místa. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého místa. 

Ochrana identity detekuje přihlášení z neznámých umístění také pro základní ověřování / starší protokoly. Vzhledem k tomu, že tyto protokoly nemají moderní známé funkce, jako je například ID klienta, není dostatek telemetrie ke snížení falešně pozitivních hodnot. Chcete-li snížit počet zjištěných detekcí rizik, měli byste přejít na moderní ověřování.   

> [!NOTE]
> Pokud se přihlašovací uživatelské jméno a heslo neshodují, přihlášení se nezdaří a nedojde k detekci rizika. Přihlášení z neznámých zjišťování rizik umístění se aktivuje pouze při úspěšných přihlášeních.

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tento typ detekce rizik identifikuje přihlášení ze zařízení infikovaných malwarem, o kterých je známo, že aktivně komunikují se serverem robotů. To je určeno korelaceip adres zařízení uživatele s IP adresami, které byly v kontaktu se serverem robota. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou
Tento typ zjišťování rizik identifikuje IP adresy, ze kterých byl v krátkém časovém období zaznamenán vysoký počet neúspěšných pokusů o přihlášení napříč více uživatelskými účty. To odpovídá vzorcům provozu IP adres používaných útočníky a je silným ukazatelem toho, že účty jsou již nebo budou ohroženy. Jedná se o algoritmus strojového učení, který ignoruje zřejmé falešně pozitivní výsledky, jako jsou ip adresy, které jsou pravidelně používány jinými uživateli v organizaci.  Systém má počáteční období učení 14 dnů, kdy se učí chování přihlášení nového uživatele a nového klienta.

## <a name="detection-type"></a>Typ detekce

Vlastnost typu detekce je indikátor **(Real-time** nebo **Offline)** pro časový rámec detekce detekce rizik. V současné době je většina detekcí rizik zjištěna offline v operaci následného zpracování po zjištění rizika.

V následující tabulce je uvedena doba, kterou je potřeba k zobrazení typu zjišťování v související sestavě:

| Typ detekce | Latence hlášení |
| --- | --- |
| Reálný čas | 5 až 10 minut |
| Offline | 2 až 4 hodiny |


Pro typy detekce rizik Azure Active Directory zjistí, typy detekce jsou:

| Typ detekce rizik | Typ detekce |
| :-- | --- | 
| [Uživatelé s uniklými přihlašovacími údaji](#leaked-credentials) | Offline |
| [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) | Reálný čas |
| [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) | Offline |
| [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) | Reálný čas |
| [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) | Offline |
| [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Úroveň rizika

Vlastnost detekce rizika na úrovni rizika je indikátor **(Vysoká**, **Střední**nebo **Nízká)** pro závažnost a spolehlivost detekce rizik. Tato vlastnost vám pomůže určit prioritu akcí, které je třeba provést. 

Závažnost detekce rizik představuje sílu signálu jako prediktor ohrožení identity. Důvěra je ukazatelem možnosti falešně pozitivních hodnot. 

Například: 

* **Vysoká**: Vysoká spolehlivost a detekce rizika vysoké závažnosti. Tyto události jsou silné ukazatele, že identita uživatele byla ohrožena a všechny uživatelské účty ovlivněné by měly být okamžitě opraveny.

* **Střední**: Vysoká závažnost, ale nižší detekce rizika spolehlivosti, nebo naopak. Tyto události jsou potenciálně rizikové a všechny ovlivněné uživatelské účty by měly být opraveny.

* **Nízká**: Nízká spolehlivost a detekce rizika nízké závažnosti. Tato událost nemusí vyžadovat okamžitou akci, ale v kombinaci s jinými detekcemi rizik může poskytnout silný náznak ohrožení identity.

![Úroveň rizika](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Uniklá pověření

Leaked credentials risk detections are classified as a **High**, protože poskytují jasný údaj, že uživatelské jméno a heslo jsou k dispozici útočníkovi.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Úroveň rizika pro tento typ zjišťování rizika je **střední,** protože anonymní adresa IP není silným ukazatelem ohrožení zabezpečení účtu. Doporučujeme, abyste okamžitě kontaktovali uživatele a ověřili, zda používali anonymní IP adresy.


### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Nemožné cestování je obvykle dobrým ukazatelem toho, že se hacker úspěšně přihlásil. K falešně pozitivním vlivům však může dojít, když uživatel cestuje pomocí nového zařízení nebo pomocí sítě VPN, která obvykle nepoužívá jiní uživatelé v organizaci. Dalším zdrojem falešně pozitivních hodnot jsou aplikace, které nesprávně předávají IP adresy serveru jako klientské IP adresy, což může vyvolat vzhled přihlášení z datového centra, kde je hostována back-end této aplikace (často se jedná o datová centra Společnosti Microsoft, která může vyvolat vzhled přihlášení, ke které dochází z IP adres vlastněných společností Microsoft). V důsledku těchto falešně pozitivních výsledků je úroveň rizika pro tuto detekci **rizika střední**.

> [!TIP]
> Počet hlášených falešných poplachů pro tento typ zjišťování rizika můžete snížit konfigurací [pojmenovaných umístění](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Neznámá místa mohou být silným ukazatelem toho, že útočník může použít ukradenou identitu. Falešně pozitivní výsledky mohou nastat, když uživatel cestuje, zkouší nové zařízení nebo používá novou síť VPN. V důsledku těchto falešně pozitivních výsledků je úroveň rizika pro tento typ události **Střední**.

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Toto zjišťování rizik identifikuje IP adresy, nikoli uživatelská zařízení. Pokud několik zařízení jsou za jednu IP adresu, a jen některé jsou řízeny bot sítě, přihlášení z jiných zařízení můj trigger tuto událost zbytečně, což je důvod, proč tato detekce rizik a je klasifikován jako **low**.  

Doporučujeme kontaktovat uživatele a proskenovat všechna uživatelská zařízení. Je také možné, že je infikované osobní zařízení uživatele nebo že někdo jiný používal infikované zařízení ze stejné IP adresy jako uživatel. Infikovaná zařízení jsou často infikována malwarem, který dosud nebyl identifikován antivirovým softwarem, a může také naznačovat špatné uživatelské návyky, které mohly způsobit napadení zařízení.

Další informace o řešení malwarových infekcí naleznete v [Centru ochrany proti malwaru](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou

Doporučujeme kontaktovat uživatele a ověřit, zda se skutečně přihlásil z IP adresy, která byla označena jako podezřelá. Úroveň rizika pro tento typ události je "**Střední**", protože za stejnou IP adresou může být několik zařízení, zatímco pouze některá mohou být zodpovědná za podezřelou aktivitu. 


## <a name="next-steps"></a>Další kroky

* [Zpráva o zabezpečení ohrožených uživatelů](concept-user-at-risk.md)
* [Sestava zabezpečení rizikového přihlášení](concept-risky-sign-ins.md)
* [Ochrana identity Azure AD](../active-directory-identityprotection.md).
