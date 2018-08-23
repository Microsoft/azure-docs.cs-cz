---
title: Azure Active Directory rizikových událostí | Dokumentace Microsoftu
description: Tohoto článku poskytuje podrobný přehled o rizikové události jsou.
services: active-directory
keywords: Azure active directory identity protection, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
author: priyamohanram
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/14/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fe5bcce06ef02bae75bf9fdaf5702a99f485885c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054206"
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory rizikových událostí

Většinu narušení zabezpečení se použijí při útočníci získají přístup k prostředí krádeží identity uživatele. Zjišťování ohrožení zabezpečení identit je žádný snadný úkol. Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik ke zjištění podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každou zjištěnou podezřelé akce, které jsou uloženy v záznam nazvaný *riziková událost*.

V současné době Azure Active Directory zjistí šest typy rizikových událostí:

- [Uživatelé s uniklými přihlašovacími údaji](#leaked-credentials) 
- [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) 
- [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) 
- [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) 
- [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) 


![Riziková událost](./media/concept-risk-events/91.png)

Podrobnější informace o získání zjištěné rizikové události se váže k předplatnému služby Azure AD. V edici Azure AD Premium P2 získáte nejpodrobnější informace o základní všechna nalezení. S edicí Azure AD Premium P1 detekcí, které nejsou pokryty všemi vaše licence se zobrazí jako rizikovou událost **přihlášení s dalšími riziky zjistil**.


Tento článek obsahuje vás podrobnější přehled o jaké rizikové události jsou, a jak je můžete využít k ochraně svých identit Azure AD.


## <a name="risk-event-types"></a>Typy rizikových událostí

Vlastnost typu události rizika je, že se vytvořil identifikátor podezřelé akce záznamu události rizika.

Průběžné investice Microsoftu do procesu zjišťování vést k:

- Vylepšení přesnost detekce existujících rizikových událostí 
- Nové typy rizikových událostí, které se přidají v budoucnu

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

Když internetovým zločincům ohrozit platný hesla oprávněným uživatelům, pachatelů často sdílí své přihlašovací údaje. To se obvykle provádí účtování je veřejně na tmavě web nebo vložit lokalit nebo obchodování nebo prodej přihlašovací údaje na černém trhu. Microsoft úniku přihlašovacích údajů služby získá uživatelské jméno / heslo dvojice monitorování veřejné a tmavé webů a práce s:

- Výzkumní pracovníci
- Vymáhání zákona
- Zabezpečení týmů v Microsoftu
- Jiných důvěryhodných zdrojů 

Když služba získá uživatelské jméno / heslo dvojice, jsou porovnávána s aktuální platné přihlašovací údaje uživatelů AAD. Když se najde shoda, znamená to, že heslo uživatele došlo k napadení a *úniku přihlašovacích údajů riziková událost* se vytvoří.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Tento typ rizikové události identifikuje uživatele, kteří úspěšně přihlášení z IP adresy, která se identifikovala jako IP adresa anonymního proxy serveru. Tato proxy používají lidé, kteří mají skrýt IP adresu svého zařízení a může sloužit ke škodlivým činnostem.


### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Tento typ rizikové události identifikuje dvě přihlášení pocházející z geograficky vzdáleném umístění, kde aspoň jedno z umístění může být také netypických pro uživatele, daný za chování. Mezi několik dalších faktorů tohoto algoritmu strojového učení bere v úvahu čas mezi dvěma přihlášení a čas, kdy by trvalo pro uživatele k dopravě z první umístění do druhého, označující, že jiný uživatel používá stejný přihlašovací údaje.

Algoritmus ignoruje zřejmé "počet falešně pozitivních výsledků" přispívání do podmínky neuskutečnitelná cesta, například sítě VPN a umístění pravidelně používat další uživatelé v organizaci. Systém má období učení 14 dní, během kterých se naučí nového uživatele přihlásit chování. 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Tento typ rizikové události ohledem na minulou přihlášení umístění (IP, zeměpisné šířky / délky a čísla ASN) k určení nového / neznámého umístění. Systém uchovává informace o předchozí umístění použitá uživatelem a bere v úvahu tyto "známé" umístění. Riziková událost se aktivuje, dojde li přihlásit z umístění, které již není v seznamu známých umístění. Systém má období učení 30 dní, během kterého neoznačuje žádné nové umístění jako neznámých míst. Systém také ignoruje přihlášení ze známé zařízení a umístění, které jsou geograficky umístěné blízko známé umístění. 

Identity Protection zjistí přihlášení z neznámých míst také pro základní ověřování nebo starších verzí protokolů. Protože tyto protokoly nejsou moderní známých funkcí, jako je například id klienta, není dost telemetrických dat do snížil počet falešných poplachů. Pokud chcete snížit počet zjištěné rizikové události, byste měli přejít na moderní ověřování.   

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tento typ rizikové události identifikuje přihlášení z napadený malwarem, zařízení, která se ví, že aktivně komunikovat se serverem robota. To se určují podle korelace IP adresy zařízení uživatele s IP adresami, které byly v kontaktu s bot serveru. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou
Tento typ rizikové události identifikuje IP adresy, ze kterých vysoký počet neúspěšných pokusů o přihlášení se zjistily, několika uživatelským účtům během krátké doby času. Shoduje se vzorem provozu IP adres používaných útočníky a je silným indikátorem, že účty již jsou nebo se chystáte dojít k ohrožení bezpečnosti. Toto je algoritmu strojového učení, které ignoruje zřejmé "*false pozitivní*", jako jsou IP adresy, které se pravidelně používají jinými uživateli v organizaci.  Systém má období učení 14 dní, ve kterém se naučí chování přihlášení z nového uživatele a nového tenanta.


## <a name="detection-type"></a>Typ detekce

Vlastnost typu zjišťování je indikátorem (v reálném čase nebo Offline) pro zjišťování časovém rámci rizikové události. V současné době většina rizikové události jsou rozpoznána offline v operaci následného zpracování rizikové události došlo.

Následující tabulka uvádí dobu potřebnou pro typ zjišťování se zobrazí v sestavě:

| Typ zjištění | Latence generování sestav |
| --- | --- |
| V reálném čase | 5 až 10 minut |
| Offline | 2 až 4 hodiny |


Pro typy rizikových událostí, které Azure Active Directory zjistí jsou tyto typy detekce:

| Typ rizikové události | Typ zjištění |
| :-- | --- | 
| [Uživatelé s uniklými přihlašovacími údaji](#leaked-credentials) | Offline |
| [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) | V reálném čase |
| [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) | Offline |
| [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) | V reálném čase |
| [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) | Offline |
| [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Úroveň rizika

Vlastnost úrovně rizika rizikové události je indikátor (vysoká, střední nebo nízká) pro závažnost a spolehlivosti rizikové události. Tato vlastnost vám umožní určit prioritu akce, které je třeba provést. 

Závažnost riziková událost představuje sílu signálu jako prediktorem identita ohrozit zabezpečení. Důvěra je indikátorem možnost počet falešně pozitivních výsledků. 

Například: 

* **Vysoká**: události rizika vysokou závažností a vysokou spolehlivostí. Tyto události jsou silné indikátory, které byly k ohrožení identity uživatele a všechny uživatelské účty vliv by měl okamžitě opravit.

* **Střední**: vysokou závažností, ale nižší důvěru riziková událost nebo naopak. Tyto události jsou potenciálně nebezpečné a všechny uživatelské účty vliv měli opravit.

* **Nízká**: s nízkou spolehlivostí a s nízkou závažností riziková událost. Tato událost nevyžadují okamžitý zásah, ale v kombinaci s další rizikové události mohou poskytnout silné indikace, že dojde k ohrožení identity.

![Úroveň rizika](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

Nevrácení rizikové události jsou klasifikovány jako přihlašovací údaje **vysokou**, protože poskytují jasný náznak uživatelského jména a hesla jsou dostupné pro útočníky.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Úroveň rizika pro tento typ rizikové události se **střední** protože anonymní IP adresy není silné údaj o ohrožení účtu. Doporučujeme vám, okamžitě kontaktujte uživatele k ověření, pokud používají anonymní IP adresy.


### <a name="impossible-travel-to-atypical-locations"></a>Nemožná cesta do netypických míst

Neuskutečnitelná cesta je obvykle vhodný indikátor, který se hacker se podařilo úspěšně přihlásit. False – pozitivní však může dojít k uživatele je cestách pomocí nové zařízení nebo pomocí sítě VPN, která se obvykle nepoužívá jinými uživateli v organizaci. Dalším zdrojem tohoto pozitivní false je aplikace, které se nesprávně předat serveru IP adresy jako klient IP adresy, což může mít vzhled přihlášení dochází v datovém centru, ve kterém tato aplikace je back-end hostovaný (často jsou to datových center Microsoftu, které může dát vzhled přihlášení probíhat od Microsoftu vlastní IP adresy). V důsledku těchto pozitivní hodnotu false, je úroveň rizika pro tuto rizikovou událost **střední**.

> [!TIP]
> Můžete snížit množství ohlášené pozitivní false pro tento typ rizikové události nakonfigurováním [pojmenovaná umístění](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Neznámé umístění může poskytovat silné indikace, že se útočník použít krádež identity. False – pozitivní může dojít, když uživatel je na cestách, vyzkoušejte si nové zařízení nebo pomocí nové připojení VPN. V důsledku těchto počet falešně pozitivních výsledků, úroveň rizika pro tento typ události je **střední**.

### <a name="sign-ins-from-infected-devices"></a>Přihlášení z nakažených zařízení

Tato riziková událost identifikuje IP adresy, nikoli zařízením uživatele. Pokud jsou několik zařízení za jednu IP adresu pouze některé jsou dané sítě robota, přihlášení z jiných zařízení Moje aktivační události Tato událost zbytečně, což je důvod pro tuto rizikovou událost klasifikaci jako **nízká**.  

Doporučujeme kontaktovat uživatele a prohledávání všech jeho zařízeních. Je také možné, že je nakažená osobní zařízení uživatele, nebo jak bylo zmíněno dříve, že používal někdo nakažené zařízení ze stejné IP adresy jako uživatel. Nakažených zařízení jsou často infikované malwarem, který dosud nebyly určeny pomocí antivirového softwaru a může také označovat jako chybných uživatelských návyky, které může způsobit nakaženy zařízení.

Další informace o tom, jak adresu napadení malwarem, najdete v článku [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Přihlášení z IP adres s podezřelou aktivitou

Doporučujeme, abyste se obrátili uživatele k ověření, pokud jsou ve skutečnosti přihlášení z IP adresy, která byla označena jako podezřelá. Úroveň rizika pro tento typ události je "**střední**" protože několika zařízeními může být za stejnou IP adresu, zatímco jen některé mohou být za podezřelé aktivity. 


 
## <a name="next-steps"></a>Další postup

Rizikové události jsou základem ochrany identit Azure AD. Azure AD můžete zjistit aktuálně šest rizikových událostí: 


| Typ rizikové události | Úroveň rizika | Typ zjištění |
| :-- | --- | --- |
| [Uživatelé s uniklými přihlašovacími údaji](#leaked-credentials) | Vysoký | Offline |
| [Přihlášení z anonymních IP adres](#sign-ins-from-anonymous-ip-addresses) | Střednědobé používání | V reálném čase |
| [Nemožná cesta do netypických míst](#impossible-travel-to-atypical-locations) | Střednědobé používání | Offline |
| [Přihlášení z neznámých míst](#sign-in-from-unfamiliar-locations) | Střednědobé používání | V reálném čase |
| [Přihlášení z nakažených zařízení](#sign-ins-from-infected-devices) | Nízká | Offline |
| [Přihlášení z IP adres s podezřelou aktivitou](#sign-ins-from-ip-addresses-with-suspicious-activity) | Střednědobé používání | Offline|

Kde lze najít rizikové události, které byly zjištěny v prostředí?
Existují dvě místa, kde můžete zkontrolovat rizikové události oznámené:

 - **Generování sestav v Azure AD** – rizikové události jsou součástí zabezpečení Azure AD sestavy. Další informace najdete v tématu [zabezpečení Sestava ohrožených uživatelů](concept-user-at-risk.md) a [sestavy rizikových přihlášení zabezpečení](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** – rizikové události jsou také součástí [Azure Active Directory Identity Protection od](../active-directory-identityprotection.md) funkce vytváření sestav.
    

Při zjišťování rizikových událostí již představuje důležitou součástí ochrany identit, máte také možnost ručně řešení nebo dokonce implementovat automatické odpovědi prostřednictvím konfigurace zásad podmíněného přístupu. Další informace najdete v tématu o [Azure Active Directory Identity Protection od](../active-directory-identityprotection.md).
 
