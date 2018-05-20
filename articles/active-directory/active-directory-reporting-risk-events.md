---
title: Azure Active Directory rizikových událostí | Microsoft Docs
description: Tato článku poskytuje podrobný přehled o jaké jsou rizikových událostech.
services: active-directory
keywords: ochrany identit Azure active directory, zabezpečení, rizik, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
author: MarkusVi
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e883caa63bde26e13234dde949ce4517b328e3a5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory rizikových událostí

Velká většina narušení zabezpečení se provede při útočník přístupu do prostředí tak, že krádež identity uživatele. Zjišťování ohrožení zabezpečení identity je bez snadno úlohy. Azure Active Directory používá algoritmy adaptivní strojového učení a heuristiky ke zjištění podezřelé akce, které se vztahují k vaší uživatelské účty. Každý zjistil podezřelé akce je uložený v záznamu názvem *riziko událostí*.

V současné době Azure Active Directory zjistí šesti typy rizikových událostí:

- [Uživatelé s uniklé přihlašovací údaje](#leaked-credentials) 
- [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) 
- [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) 
- [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) 
- [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) 


![Riziko událostí](./media/active-directory-reporting-risk-events/91.png)

Přehledy, které máte pro událost zjištěnou riziko je vázaný na vaše předplatné Azure AD. S Azure AD Premium P2 edition získáte nejvíce podrobné informace o všech základní detekce. S Azure AD Premium P1 edition, detekce, které nejsou předmětem licence na aplikaci zobrazit jako událost riziko **přihlášení s další riziko zjistil**.


Tento článek poskytuje můžete podrobný přehled o událostech, které riziko jsou a jak je používat k ochraně vašich identit Azure AD.


## <a name="risk-event-types"></a>Typy rizikových událostí

Vlastnost typu události riziko je že identifikátor pro podezřelé akce záznam riziko událostí byl vytvořen pro.

Průběžné investice do společnosti Microsoft do procesu zjišťování vést k:

- Vylepšení detekce přesnost existující rizikových událostí 
- Nové typy událostí rizik, které budou přidány v budoucnosti

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

Když internetovým zločincům ohrožení platný hesla oprávněných uživatelů, pachatelů často sdílet tyto přihlašovací údaje. To se obvykle provádí zveřejněním je veřejně tmavý webové nebo vložení lokalit nebo obchodování nebo prodávají na černém trhu přihlašovací údaje. Microsoft úniku přihlašovacích údajů služby získá uživatelské jméno / heslo páry monitorování veřejné a tmavým webů a práce s:

- Výzkumní pracovníci
- Policie a bezpečnostních složek
- Zabezpečení týmy v Microsoftu
- Další důvěryhodných zdrojů 

Když služba zakoupí uživatelské jméno / heslo páry je provedena kontrola proti aktuální platné přihlašovací údaje uživatele AAD. Když je nalezena shoda, znamená to, že byl napaden heslo uživatele a *úniku přihlašovacích údajů riziko událostí* je vytvořena.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Tento typ události riziko identifikuje uživatele, kteří mají úspěšném přihlášení z IP adresy, která byla zjištěna jako IP adresa anonymního proxy serveru. Tyto servery proxy jsou používány osob, které chcete skrýt IP adresu své zařízení a mohou být použity pro zlými úmysly.


### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Tento typ události riziko identifikuje dvě přihlášení pocházející z geograficky vzdáleným umístění, kde alespoň jeden z umístění může být také pro uživatele netypické, dřívějšímu chování. Tento algoritmus machine learning mezi několik dalších faktorech, bere v úvahu čas mezi dvě přihlášení a čas, kdy by trvaly k cestují z první umístění do druhého, která určuje, že jiný uživatel používá stejný uživatelem přihlašovací údaje.

Algoritmus ignoruje zřejmé "falešně pozitivních" přispívání do neuskutečnitelná cesta podmínky, jako jsou sítě VPN a umístění pravidelně používat další uživatelé v organizaci. Systém má po počáteční learning dobu 14 dnů, za které se zjišťuje chování přihlášení nového uživatele. 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Tento typ události riziko považovat za přihlášení umístění (IP, zeměpisné šířky nebo zeměpisnou délku a ASN) k určení nového / neznámého umístění. Systém ukládá informace o předchozích umístění používaných uživatelem a zvažuje těchto "známých" umístění. Při přihlášení z umístění, které již nejsou v seznamu známých umístění je aktivována událost riziko. Systém má počáteční learning období 30 dní, za které není příznak jiných umístění jako neznámých míst. Systém také ignoruje přihlášení ze známé zařízení a umístění, které jsou geograficky blízko známé umístění. 

Identity Protection zjistí přihlášení z neznámých míst také pro základní ověřování / starší protokoly. Protože tyto protokoly nemají moderní známé funkce, jako je například id klienta, není dost telemetrických dat k snížil počet falešných poplachů. Omezte počet zjištěných rizikových událostí, měli byste přejít na moderní ověřování.   

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tento typ události riziko identifikuje přihlášení ze zařízení nakažená malwarem, které jsou známé aktivně komunikovat se serverem robota. To se nastavuje pomocí souvztažnosti IP adres zařízení uživatele vůči IP adresy, které byly v kontaktu se serverem robota. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou
Tento typ události riziko identifikuje IP adresy, ze kterých vysoký počet neúspěšných pokusů o přihlášení se zobrazily, několika uživatelským účtům během krátké doby času. To odpovídá vzorem provozu IP adres používaných útočníky a je silný důkaz, že účty jsou buď již nebo se chystáte dojít k ohrožení. Toto je algoritmu strojového učení, který ignoruje zřejmé "*false pozitivních*", jako jsou například IP adresy, které jsou pravidelně používat další uživatelé v organizaci.  Systém má po počáteční learning dobu 14 dnů, kde se zjišťuje chování přihlášení nového uživatele a nového klienta.


## <a name="detection-type"></a>Typ detekce

Vlastnost type detekce představuje indikátor (v reálném čase nebo Offline) pro časový rámec detekce události riziko. V současné době většina riziko jsou detekovány události do režimu offline v operaci následného zpracování po došlo k události riziko.

Následující tabulka uvádí dobu potřebnou pro typ detekce objeví v související sestavy:

| Detekce typu | Latence sestav |
| --- | --- |
| V reálném čase | 5 až 10 minut |
| Offline | 2 až 4 hodiny |


Pro typy událostí rizik, které zjistí Azure Active Directory detekce typy jsou:

| Typ události rizik | Detekce typu |
| :-- | --- | 
| [Uživatelé s uniklé přihlašovací údaje](#leaked-credentials) | Offline |
| [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) | V reálném čase |
| [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) | Offline |
| [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) | V reálném čase |
| [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) | Offline |
| [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Úroveň rizika

Vlastnost úrovně rizika události riziko je indikátor (vysoká, střední nebo nízká) pro závažnost a spolehlivosti riziko události. Tato vlastnost umožňuje nastavit prioritu akce, které je nutné provést. 

Závažnost události riziko představuje sílu signálu jako předpověď identity ohrožení. Je jistota je možnost falešně pozitivních slouží jako ukazatel. 

Například: 

* **Vysoká**: vysokou spolehlivostí a riziko událostí vysokou závažností. Tyto události jsou silné indikátory, které identitu uživatele došlo k ohrožení, a všechny uživatelské účty dopad by měl být opraven okamžitě.

* **Střední**: vysokou závažností, ale nižší událostí spolehlivosti riziko, a naopak. Tyto události jsou potenciálně rizikové a všechny uživatelské účty dopad by bylo možno opravit.

* **Nízká**: nízkou spolehlivosti a nízkou závažností riziko událostí. Tato událost nevyžadují okamžitý zásah, ale v kombinaci s jinými událostmi riziko může poskytnout silné ukazatel toho, že je identita ohrožená.

![Úroveň rizika](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

Úniku rizikových událostech, které jsou klasifikovány jako přihlašovací údaje **vysokou**, protože poskytují jasný náznak uživatelské jméno a heslo jsou k dispozici pro útočníka.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Úroveň rizika pro tento typ události riziko je **střední** protože anonymní IP adresa není silné údaj o ohrožení účtu. Doporučujeme vám, že okamžitě kontaktujte uživatele k ověření, pokud používají anonymních IP adres.


### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Neuskutečnitelná cesta je obvykle dobrou ukazatele, který se hacker bylo možné úspěšně přihlásit. False pozitivních však může dojít, když se uživatel při cestě pomocí nového zařízení nebo pomocí sítě VPN, který se obvykle nepoužívá jinými uživateli v organizaci. Jiný zdroj pozitivních false je aplikace, které nesprávně jako klient IP adresy, která by mohla vzhled předat serveru IP adresy z přihlášení je hostována dochází v datovém centru, kde aplikace je back-end (často jsou to Microsoft umístit datových center, které mohou vzhled trvá přihlášení z IP adresy ve vlastnictví společnosti Microsoft). V důsledku těchto false pozitivních úroveň rizika pro tuto událost riziko je **střední**.

> [!TIP]
> Můžete snížit množství hlášené pozitivních false pro tento typ události riziko podle konfigurace [s názvem umístění](active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Neznámých míst nabízejí silné označení, že útočník je možné použít odcizené identity. False pozitivních může dojít, když uživatel při cestě, je vyzkoušení nové zařízení nebo pomocí nové připojení VPN. V důsledku těchto falešně pozitivních zjištění úroveň rizika pro tento typ události je **střední**.

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tato událost riziko identifikuje IP adresy, nikoli zařízením uživatele. Pokud jsou několika zařízeními za jednu IP adresu a jenom některé jsou řízeny robota síť, přihlášení z jiných zařízení Moje aktivační událost Tato událost zbytečně, což je z důvodu pro klasifikaci této události riziko jako **nízká**.  

Doporučujeme kontaktovat uživatele a kontrolovat všechny uživatele zařízení. Je také možné, že je nakažená osobní zařízení uživatele, nebo jak už bylo zmíněno dříve, že používal někdo zařízení s nakažené ze stejné IP adresy jako uživatel. Nakažených zařízení jsou často napadené malwarem, které dosud nebyly určeny antivirový software, může také označují jako chybných uživatelských návyky, které mohly způsobit zařízení stane nakažené.

Další informace o tom, jak adresu napadení malwarem, najdete v článku [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou

Doporučujeme kontaktovat uživatele ověřit, pokud se ve skutečnosti přihlášení z IP adresy, která byla označena jako podezřelá. Úroveň rizika pro tento typ události je "**střední**" protože několik zařízení může být za stejnou IP adresu, při jen některé může být zodpovědná za podezřelou aktivitu. 


 
## <a name="next-steps"></a>Další postup

Riziko události jsou základem pro ochranu identity služby Azure AD. Azure AD můžete zjistit aktuálně šesti rizikových událostí: 


| Typ události rizik | Úroveň rizika | Detekce typu |
| :-- | --- | --- |
| [Uživatelé s uniklé přihlašovací údaje](#leaked-credentials) | Vysoký | Offline |
| [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) | Střednědobé používání | V reálném čase |
| [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) | Střednědobé používání | Offline |
| [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) | Střednědobé používání | V reálném čase |
| [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) | Nízká | Offline |
| [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) | Střednědobé používání | Offline|

Kde lze najít rizikových událostech, které byly nalezeny ve vašem prostředí?
Existují dvě místa, kde můžete zkontrolovat hlášené rizikových událostí:

 - **Generování sestav služby Azure AD** -rizikových událostí jsou součástí zabezpečení Azure AD sestavy. Další informace najdete v tématu [uživatele na riziko zabezpečení sestavy](active-directory-reporting-security-user-at-risk.md) a [sestavy zabezpečení rizikové přihlášení](active-directory-reporting-security-risky-sign-ins.md).

 - **Azure AD Identity Protection** -rizikových událostí je také součástí [Azure Active Directory Identity Protection](active-directory-identityprotection.md) funkce vytváření sestav.
    

Při zjišťování rizikových událostí již představuje důležitou součást ochraně vaší identity, máte také možnost buď ručně adresa je nebo i implementovat automatické odpovědi konfigurací zásady podmíněného přístupu. Další informace najdete v tématu o [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 
