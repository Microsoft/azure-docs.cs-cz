---
title: Azure Active Directory rizikové události | Microsoft Docs
description: Tento artice poskytuje podrobný přehled o tom, jaké rizikové události jsou.
services: active-directory
keywords: Azure Active Directory Identity Protection, zabezpečení, riziko, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
author: cawrites
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5711d900653ae7786899ce1c53f22cf181f5b8bf
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988269"
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory rizikové události

Pokud útočníci získají přístup k prostředí tím, že ukrást identitu uživatele, dojde k převážné většině narušení zabezpečení. Zjišťování ohrožených identit není jednoduché. Azure Active Directory používá k detekci podezřelých akcí, které souvisejí s vašimi uživatelskými účty, algoritmus adaptivního strojového učení a heuristiky. Každá zjištěná podezřelá akce je uložená v záznamu s názvem **riziková událost**.

K dispozici jsou dvě místa, kde si prohlédnete hlášené rizikové události:

 - **Vytváření sestav Azure AD** – rizikové události jsou součástí sestav zabezpečení služby Azure AD. Další informace najdete v sestavě zabezpečení [rizikové uživatele](concept-user-at-risk.md) a [zabezpečení rizikových přihlášení](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** – rizikové události jsou také součástí možností vytváření sestav [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Kromě toho můžete pomocí [rozhraní API pro rizikové události Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) získat programový přístup k detekci zabezpečení pomocí Microsoft Graph. Další informace najdete v tématu [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](../identity-protection/graph-get-started.md). 

V současné době Azure Active Directory detekuje šest typů rizikových událostí:

- [Uživatelé s nevrácenými přihlašovacími údaji](#leaked-credentials) 
- [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) 
- [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) 
- [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) 
- [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) 

![Riziková událost](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> V některých případech můžete v [sestavě přihlášení](concept-sign-ins.md)najít rizikové události bez odpovídající položky přihlášení. Je to proto, že Identity Protection vyhodnocuje riziko pro **interaktivní** i neinteraktivní přihlášení, zatímco sestava přihlášení zobrazuje jenom interaktivní přihlášení.

Podrobnější informace o získání zjištěné rizikové události se váže k předplatnému služby Azure AD. 

* V případě **Azure AD Premiumho P2 Edition**získáte nejpodrobnější informace o všech základních detekcích. 
* V případě **edice Azure AD Premium P1**se rozšířené detekce (například neznámé vlastnosti přihlášení) nevztahují na vaši licenci a zobrazí se pod názvem **přihlášení s dalšími zjištěnými riziky**. Kromě toho jsou skryta pole úroveň rizika a podrobnosti rizika.

I když detekce rizikových událostí již představuje důležitý aspekt ochrany vašich identit, máte také možnost je buď ručně vyřešit, nebo implementovat automatické odpovědi konfigurací zásad podmíněného přístupu. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-event-types"></a>Typy rizikových událostí

Vlastnost **typ rizikové události** je identifikátor pro podezřelou akci, pro kterou byl vytvořen záznam rizikové události.

Průběžné investice společnosti Microsoft do procesu zjišťování vedou k těmto akcím:

- Vylepšení přesnosti detekce existujících rizikových událostí 
- Nové typy rizikových událostí, které budou v budoucnu přidány

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

Pokud cybercriminals ohrozit platná hesla legitimních uživatelů, často tyto přihlašovací údaje sdílejí. To se obvykle provádí publikováním veřejně na tmavém webu nebo vložením webů nebo obchodováním nebo prodejem přihlašovacích údajů na černém trhu. Služba nevrácených přihlašovacích údajů Microsoftu získává páry uživatelského jména a hesla, protože sleduje veřejné a tmavé weby a pracuje s těmito službami:

- Výzkumní pracovníci
- Vynucování zákonů
- Týmy zabezpečení v Microsoftu
- Jiné důvěryhodné zdroje 

Když služba získá páry uživatelské jméno a heslo, jsou zkontrolovány proti aktuálním platným přihlašovacím údajům uživatele AAD. Pokud je nalezena shoda, znamená to, že došlo k ohrožení hesla uživatele a je vytvořena **událost rizika nevrácených přihlašovacích údajů** .

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Tento typ rizikové události identifikuje uživatele, kteří se úspěšně přihlásili z IP adresy, která byla identifikována jako IP adresa anonymního proxy serveru. Tato proxy používají lidé, kteří mají skrýt IP adresu svého zařízení a může sloužit ke škodlivým činnostem.

### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Tento typ rizikové události identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde aspoň jedno z umístění může být pro uživatele neobvyklá, a to s ohledem na minulé chování. Kromě několika dalších faktorů tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a časem, kdy by uživatel musel přejít z prvního umístění do druhé, což značí, že jiný uživatel používá stejné. přihlašovací údaje.

Algoritmus ignoruje zjevné "falešně pozitivní", které přispívají k nemožným cestovním podmínkám, jako jsou sítě VPN a lokality pravidelně používané jinými uživateli v organizaci. Systém má počáteční období učení 14 dnů, během kterého se učí chování při přihlašování nového uživatele. 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých umístění

Tento typ rizikové události zohledňuje minulá místa přihlášení (IP, zeměpisná šířka/délka a ASN) k určení nových/neznámých umístění. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje se za "známá" umístění. Riziková událost se aktivuje, když se přihlásí z umístění, které ještě není v seznamu známých umístění. Systém má počáteční období učení 30 dní, během kterého neoznačí žádná nová umístění jako neznámou umístění. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého umístění. 

Identity Protection detekuje přihlášení z neznámých míst i pro základní ověřování/starší protokoly. Vzhledem k tomu, že tyto protokoly nemají moderní známé funkce, jako je ID klienta, není k dispozici dostatek telemetrie pro omezení falešně pozitivních hodnot. Pokud chcete snížit počet zjištěných rizikových událostí, měli byste přejít na moderní ověřování.   

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tento typ rizikové události identifikuje přihlášení ze zařízení nakažených malwarem, u kterých je známo, že aktivně komunikuje se serverem robot. To je určeno korelacemi IP adres zařízení uživatele proti IP adresám, které se nacházely v kontaktu se serverem bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou
Tento typ rizikové události identifikuje IP adresy, ze kterých se v krátké době v rámci několika uživatelských účtů objevil velký počet neúspěšných pokusů o přihlášení. Tato možnost odpovídá vzorům přenosu IP adres, které používají útočníci, a je silným indikátorem, že už účty buď jsou, nebo se chystá být ohroženo. Toto je algoritmus strojového učení, který ignoruje zjevné falešně pozitivních hodnot, jako jsou například IP adresy, které pravidelně používají jiní uživatelé v organizaci.  Systém má počáteční období učení 14 dnů, kde se učí chování při přihlašování nového uživatele a nového tenanta.

## <a name="detection-type"></a>Typ detekce

Vlastnost typ detekce je indikátorem (**v reálném čase** nebo **offline**) pro zjištění časového rámce rizikové události. V současné době jsou v operaci následného zpracování v případě, že dojde k rizikové události, zjištěny v režimu offline i největší rizikové události.

Následující tabulka uvádí dobu potřebnou pro zobrazení typu detekce v související sestavě:

| Typ detekce | Latence generování sestav |
| --- | --- |
| V reálném čase | 5 až 10 minut |
| Offline | 2 až 4 hodiny |


U typů rizikových událostí, které Azure Active Directory detekuje, jsou typy detekce:

| Typ rizikové události | Typ detekce |
| :-- | --- | 
| [Uživatelé s nevrácenými přihlašovacími údaji](#leaked-credentials) | Offline |
| [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) | V reálném čase |
| [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) | Offline |
| [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) | V reálném čase |
| [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) | Offline |
| [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Úroveň rizika

Vlastnost úroveň rizika rizikové události je indikátorem (**Vysoká**, **střední**nebo **Nízká**) pro závažnost a spolehlivost rizikové události. Tato vlastnost vám pomůže určit prioritu akcí, které je třeba provést. 

Závažnost rizikové události představuje sílu signálu jako prediktivního ohrožení zabezpečení identity. Jistota je indikátorem pro možnost falešně pozitivních hodnot. 

Například 

* **Vysoká**: Vysoká důvěra a riziková událost s vysokou závažností. Tyto události jsou silné indikátory, že došlo k ohrožení identity uživatele, a všechny ovlivněné uživatelské účty by měly být opraveny okamžitě.

* **Střední**: Vysoká závažnost, ale nižší riziková riziková událost nebo naopak. Tyto události jsou potenciálně rizikové a všechny ovlivněné uživatelské účty by měly být opravené.

* **Nízká úroveň**: Nízká důvěra a riziková událost s nízkou závažností. Tato událost nemusí vyžadovat okamžitou akci, ale v kombinaci s jinými rizikovými událostmi může poskytnout silný náznak, že dojde k ohrožení identity.

![Úroveň rizika](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

Nevrácené rizikové události jsou klasifikované jako **vysoké**, protože poskytují jasné označení toho, že uživatelské jméno a heslo jsou k dispozici útočníkovi.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Úroveň rizika pro tento typ rizikové události je **střední** , protože anonymní IP adresa není silné označení ohrožení bezpečnosti účtu. Doporučujeme, abyste okamžitě kontaktovali uživatele, aby ověřil, jestli používal anonymní IP adresy.


### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Nemožná cesta je obvykle dobrý indikátor, že se hacker mohl úspěšně přihlásit. Falešné – kladné hodnoty se ale můžou vyskytnout, když se uživatel na cestách na cestách pomocí nového zařízení nebo přes VPN, které obvykle nepoužívá jiní uživatelé v organizaci. Dalším zdrojem falešně pozitivních aplikací jsou aplikace, které nesprávně předávají IP adresy serveru jako klientské IP adresy. to může mít za výsledek přihlášení z datového centra, kde se hostuje back-end aplikace (často se jedná o datová centra Microsoftu, která může dát přednost zobrazení přihlášení z IP adres vlastněných společností Microsoft. V důsledku těchto falešně pozitivních rizik je úroveň rizika této rizikové události **střední**.

> [!TIP]
> V případě, že nakonfigurujete pojmenovaná [umístění](../active-directory-named-locations.md), můžete snížit množství hlášených falešně pozitivních hodnot pro tento typ rizikové události. 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých umístění

Neznámou umístění poskytují silné indikaci, že útočník může použít odcizenou identitu. False – kladné hodnoty můžou nastat, když se uživatel na cestách, vyzkouší nové zařízení nebo používá novou síť VPN. V důsledku těchto falešně pozitivních hodnot je úroveň rizika pro tento typ události **střední**.

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tato riziková událost identifikuje IP adresy, ne zařízení uživatele. Pokud je několik zařízení za jednou IP adresou a jenom některé jsou ovládány přes síť bot, přihlášení z jiných zařízení tuto událost bezpodmínečně vyvolalo, což znamená, proč je tato riziková událost klasifikována jako **Nízká**.  

Doporučujeme, abyste se obrátili na uživatele a kontrolovali všechna zařízení uživatele. Je také možné, že osobní zařízení uživatele je nakažené nebo někdo jiný používal nakažené zařízení ze stejné IP adresy jako uživatel. Nakažená zařízení jsou často nakažena malwarem, který ještě nebyl identifikovaný antivirovým softwarem, a může také indikovat jakékoli špatné uživatelské návyky, které mohly způsobit, že se zařízení zablokuje jako nakažené.

Další informace o tom, jak řešit napadení malwarem, najdete v [centru ochrany před malwarem](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou

Doporučujeme, abyste se obrátili na uživatele, aby ověřil, jestli jsou ve skutečnosti přihlášeni z IP adresy, která byla označena jako podezřelá. Úroveň rizika pro tento typ události je "**střední**", protože několik zařízení může být za stejnou IP adresou, zatímco pouze některé z nich mohou být zodpovědné za podezřelou aktivitu. 


## <a name="next-steps"></a>Další kroky

* [Sestava zabezpečení rizik uživatelů](concept-user-at-risk.md)
* [Sestava zabezpečení rizikových přihlášení](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
