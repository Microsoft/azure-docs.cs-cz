---
title: Proč je důležité – zabezpečených pracovních stanic Azure Active Directory
description: Zjistěte, proč organizace si měl vytvořit zabezpečené pracovní stanice pro spravované v Azure
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357053"
---
# <a name="building-secure-workstations"></a>Vytváření zabezpečených pracovních stanic

Zabezpečené izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých role jako správci vývojářů a operátorů důležitých služeb. Mnoho dalších kontrolních mechanismů pro zabezpečení a záruky bude selhat nebo nemají žádný vliv, pokud došlo k napadení základního zabezpečení pracovní stanice klienta.

Tento dokument popisuje, co je potřeba k vytvoření zabezpečené klientské pracovní stanice s podrobnými pokyny krok za krokem, jak nastavit spuštění kontrolních mechanismů pro zabezpečení. Tento typ pracovních stanic v některých případech se nazývá pracovní stanice privilegovaným přístupem (PAW), které tento odkaz se budou používat a postavené na. Pokyny ale vypadat na cloudové technologie pro správu služby a zavádí zabezpečení funkce zavedena od verze Windows 10RS5, ochrana ATP v programu Defender Microsoft, Azure Active Directory a Intune.

## <a name="why-securing-workstation-access-is-important"></a>Proč je důležité zabezpečení přístupu pracovní stanice

Rychlé osvojení cloudových služeb a schopnost pracovat odkudkoli vytvořil nový způsob využití. Útočníci využívají slabé zabezpečovací mechanizmy u zařízení, ve kterém správci pracovat a budou moct získat přístup k privilegovaným prostředky.

Jak je uvedeno v [sestava Verizon hrozeb](https://enterprise.verizon.com/resources/reports/dbir/), a [zpráva Security Intelligence Report](https://aka.ms/sir) omylem nepoužili špatně privilegovaný a útoky dodavatelského řetězce jsou mezi pěti hlavních mechanismus používaný k porušení zabezpečení organizace a nejčastěji zjištěna druhá taktika případy hlášené v roce 2018.

Většina útočník postupovat na cestě uvedené níž:

* Začněte s rekognoskace, často specifické pro odvětví, najít způsob v
* Analýza shromážděných informací při identifikaci nejlepší způsob, jak získat přístup (po infiltraci) nad pracovní stanici vnímaná nízká hodnota
* Trvalost a podívejte se na způsob, jak přesunout [následně k laterálnímu](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Stáhnout důvěrných a citlivých dat

Útočníci často proniknout zařízení, u kterých se zdá, že s nízkým rizikem nebo podhodnoceni rekognoskace. Tyto zranitelných zařízení pak umožňují vyhledat příležitost, laterální pohyb, vyhledá pro správu uživatelů a zařízení a identifikovat vysokou cenná data na úspěšně exfiltrovat informace po získají tyto role privilegovaných uživatelů.

![Vzor typické ohrožení zabezpečení](./media/concept-azure-managed-workstation/typical-timeline.png)

Tento dokument poskytuje řešení, které pomáhají chránit vaše výpočetní zařízení tak, že izoluje management a služby, které pomáhá chránit před taktiky Lateral Movement nebo útoků z méně důležitých produktivitu zařízení. Návrh pomáhá omezit možnost byl úspěšně spuštěn porušení zabezpečení tím, že rozkládají řetězec před jeho infiltraci nežádoucí osobou zařízení používat ke správě nebo přístup k citlivým cloudovým prostředkům. Toto řešení popisuje budou využívat nativní služby Azure, které jsou součástí Microsoft 365 Enterprise zásobníku včetně:

* Intune pro správu zařízení, včetně aplikace a adresu URL na seznam povolených
* AutoPilot pro nastavení zařízení a nasazení a aktualizace 
* Azure AD pro správu uživatelů, podmíněný přístup a ověřování službou Multi-Factor Authentication
* Windows 10 (aktuální verze) pro zařízení health attestation a činnost koncového uživatele
* Microsoft Defender Advanced Threat Protection (ATP) pro službu endpoint protection, detekce a reakce prostřednictvím správy cloudových
* Azure AD PIM pro správu autorizaci, včetně právě In Time (JIT) privilegovaný přístup k prostředkům

## <a name="who-benefit-from-using-a-secure-workstation"></a>Kdo s výhodou využít zabezpečené pracovní stanice

Všechny uživatele a operátory s výhodou využít zabezpečené pracovní stanice. Útočník, který ohrožení počítač nebo zařízení můžete provést několik věcí, včetně zosobnit všechny účty v mezipaměti a použijte přihlašovací údaje a tokeny používané na tomto zařízení, když jsou přihlášení. Toto riziko díky zabezpečení zařízení, použít pro všechny privilegované role, včetně práva pro správu tak důležitý jako zařízení, ve kterém se používá privilegovaného účtu nejsou cíle taktiky Lateral Movement a útoky metodou zvýšení oprávnění. Tyto účty mohou použít pro širokou škálu prostředků, jako:

* Správci o místních i cloudových systémů
* Vývojářské pracovní stanice pro kritické systémy
* Správce účtů sociálních médií s vysokým vystavení
* Vysoce citlivé a pracovních stanic, jako jsou terminály SWIFT platby
* Zpracování tajemství pracovní stanice

Společnost Microsoft doporučuje implementace ovládacích prvků se zvýšenými oprávněními zabezpečení pro privilegované pracovní stanice, kde jsou tyto účty používá ke snížení rizika. Další informace najdete v [Průvodce nasazením funkce Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [plán Office 365](https://aka.ms/o365secroadmap), a [zabezpečení privilegovaného přístupu](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Proč vyhrazená pracovní stanice

I když je možné přidat do stávající zařízení zabezpečení, je lepší začít s bezpečný základ. Počínaje známé dobré zařízení a sadu vloží ovládací prvky zabezpečení vaší organizace v nejvhodnější místo k údržbě, která zvýšil úroveň zabezpečení. S neustále rostoucím počtem dovolují příležitostné e-mailu a procházení webu vektory útoku je stále obtížné zajistit, že zařízení může považovat za důvěryhodné. Tento průvodce funguje v rámci předpokladů vyhrazené pracovní stanice oddělené od standardní produktivitu, procházení, a e-mailové úlohy dokončeny. Odebrání produktivitu, procházení webu a e-mailu ze zařízení může mít negativní vliv na produktivitu, ale tato ochrana je obvykle přijatelné pro scénáře, ve kterém úlohy projektu nevyžadují explicitně ji a riziko bezpečnostních incidentů je vysoká.

> [!NOTE]
> Procházení webu tady odkazuje na obecné přístup k libovolné webů, ke kterým se výrazně liší od pomocí webového prohlížeče pro přístup k malý počet dobře známé webů pro správu pro služby, jako je Azure, Office 365, dalších poskytovatelů cloudových služeb a SaaS vysoké riziko aplikace.

Členství ve skupině strategie poskytuje záruku zvýšené zabezpečení zvýšením počtu a typu ovládací prvky, které má nežádoucí osoba překonat aby bylo možné získat přístup k citlivým prostředkům. Model vyvinutý zde poskytuje členství ve skupině pro správu oprávnění ke konkrétním zařízením pomocí modelu vrstveného oprávnění.

## <a name="supply-chain-management"></a>Správa dodavatelského řetězce

Zásadní pro zabezpečené pracovní stanice je dodavatelský řetězec řešení kde je důvěryhodné, můžete použít pracovní stanici "root důvěry". Toto řešení bude zabývat kořenové důvěryhodnosti pomocí [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technologie. Pro zabezpečené pracovní stanice Autopilot Microsoftu umožňuje využívat Microsoft optimalizovaných pro výrobce OEM systému Windows 10 zařízení, která poskytují známého funkčního stavu od výrobce. Místo obnovování z Image zařízení, které nemusí být důvěryhodný, Microsoft Autopilot můžete transformovat zařízení s Windows do stavu "business ready", použití nastavení a zásady, instalace aplikací, a dokonce i Změna edice Windows 10 používá (například z Windows 10 Pro na Windows 10 Enterprise, pro podporu pokročilých funkcí).

![Úrovně zabezpečení pracovní stanice](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role zařízení a profily

V průběhu doprovodné materiály se a získejte víc zabezpečených řešení pro uživatele, vývojáři a IT provozní personál řešit více profily zabezpečení a rolí. Tyto profily se přizpůsobily pro podporu běžných uživatelů v organizacích, které můžete těžit z rozšířené nebo zabezpečené pracovní stanice, zatímco vyvažuje rizika a použitelnost. Pokyny poskytne konfigurace nastavení na základě přijetí oborových standardů. Tyto pokyny slouží k objasnění metoda ve Windows 10 na posílení zabezpečení a snižuje rizika spojená s ohrožení zabezpečení uživatele nebo zařízení pomocí zásad a technologie pro usnadnění správy funkce zabezpečení a rizika.
![Úrovně zabezpečení pracovní stanice](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Nízká zabezpečení** – spravované standardní pracovní stanici, která poskytuje dobrým výchozím bodem pro většinu domácích a malých pracovní účely. Tato zařízení jsou registrováno v Azure AD a spravovaná pomocí Intune. Profil, který umožňuje uživatelům spouštět všechny aplikace a procházet všechny weby. Antimalwarové řešení, jako jsou [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) by měla být povolená.
* **Zvýšené zabezpečení** – kvalitní je položka úrovně chráněné řešení, pro domácí uživatele, malým firmám, jakož i obecné vývojáře.
   * Vylepšený pracovní stanice nabízí zásadu na základě prostředky pro zvýšení zabezpečení na profil zabezpečení s nízkou. Tento profil umožňuje zabezpečený způsob, jak pracovat s daty zákazníka a použít kancelářským nástrojům, jako je například kontrola e-mailu a procházení webu. Auditovat chování uživatelů a používání profilu nad pracovní stanici povolením zásady auditování a protokolování do Intune je možné k pracovní stanici rozšířená. V tomto profilu pracovní stanice vám umožní kontrolních mechanismů pro zabezpečení a zásady je popsáno v obsahu a nasazený v rozšířené pracovní stanice – Windows 10 (1809) skriptu. Nasazení využívá taky výhod ochrany pomocí pokročilý malware [pokročilé ochrany před internetovými útoky (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Vysoké zabezpečení** – nejúčinnějším způsobem pro omezení možností útoku na pracovní stanice je k odebrání schopnost self-administer pracovní stanici. Odebrání oprávnění místního správce se krok, který zvyšuje zabezpečení a může ovlivnit produktivitu správně implementovaná. Profil vysoké zabezpečení je založena na profilu zvýšené zabezpečení díky jeden značné změně odebrání místního správce. Tento profil je účelem je pomoci s uživateli, kteří možná vysoce profilu jako je výkonný pracovník či více uživatelů, které můžou mít kontaktovat s citlivými daty, jako je například výplatních pásek, nebo schválení služeb a procesů.
   * Profil uživatele vysoké zabezpečení vyžaduje vyšší řízeném prostředí ale stále mít možnost provádět jejich produktivitu aktivity, jako je například e-mailu a procházení při zachování snadno využít svoje zkušenosti. Uživatelé očekávají funkce, jako jsou soubory cookie, oblíbených sestav a k dispozici pro provoz ostatní klávesové zkratky. Ale tito uživatelé nemusí vyžadovat schopnost upravovat nebo ladění svých zařízení a není třeba instalovat ovladače. Profil vysoké zabezpečení se nasazuje pomocí vysoké zabezpečení – skript Windows 10 (1809).
* **Specializované** – vývojářům a správcům IT jsou atraktivním cílem útočníci, jak tyto role můžete změnit systémy, které vás zajímají útočníci. Úsilí nasazené ve vysoce zabezpečené pracovní stanice a další emphases jeho bezpečnostní správu místních aplikací, omezení internetových webových serverů a omezení možností produktivitu, které jsou vysoce riziko, jako je například ActiveX, trvá specializované pracovní stanice Java, modul plug-in prohlížeče a několik dalších známá vysokým rizikem ovládacích prvků na zařízení s Windows. V tomto profilu pracovní stanice vám umožní kontrolních mechanismů pro zabezpečení a zásady je popsáno v obsahu a nasazený v DeviceConfiguration_NCSC – Windows 10 (1803) SecurityBaseline skriptu.
* **Zabezpečené** – útočník, který může ohrozit účet správce můžete obvykle způsobí poškození důležité obchodní krádežemi dat změny dat nebo přerušení služeb. V tomto stavu posílené pracovní stanice vám umožní všechny kontrolní mechanismy zabezpečení a zásady, které omezují přímou kontrolu správy místních aplikací a kancelářským nástrojům se odeberou. V důsledku toho ohrožení zařízení se provádí obtížnější jako e-mailu a sociálních médií jsou blokovány, které odpovídají nejběžnější způsob útoky typu phishing mohlo být úspěšné.  Zabezpečené pracovní stanice je možné nasadit s zabezpečení pracovní stanice – Windows 10 (1809) SecurityBaseline skriptu.

   ![Zabezpečené pracovní stanice](./media/concept-azure-managed-workstation/secure-workstation.png)

   Zabezpečené pracovní stanici, která poskytuje správce posílené pracovní stanici, který má ovládací prvek vymazat aplikace a ochrany application guard. Pracovní stanice pomocí přihlašovacích údajů, zařízení a ochrana exploit guard v chránit hostitele z škodlivého chování. Kromě toho všechny místní disky jsou šifrované pomocí šifrování nástrojem Bitlocker.

* **Izolované** – tento vlastní offline scénář představuje extrémní konci spektra (žádné instalační skripty jsou k dispozici pro tento případ). Organizace může třeba spravovat izolované obchodní kritické funkce jako je například výrobní linky vysoké hodnoty nebo životnosti podpory systémů, která vyžaduje nepodporované/bez opravy zabezpečení starší operační systémy. Protože je velmi důležité zabezpečení a cloudové služby jsou k dispozici, organizace může ručně správu a aktualizaci těchto počítačů nebo k jejich správě použít izolované architektuře doménové struktury služby Active Directory (jako rozšířené zabezpečení správce prostředí (ESAE)). Za těchto okolností odebírání veškerého přístupu kromě základní Intune a ochrana ATP v programu kontroluje se stav má brát.
   * [Požadavek komunikace sítě Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Požadavek komunikace sítě ochrany ATP v programu](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Další postup

[Nasazení zabezpečených pracovních stanic spravované v Azure](howto-azure-managed-workstation.md)
