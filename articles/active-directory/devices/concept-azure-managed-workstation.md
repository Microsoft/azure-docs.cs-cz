---
title: Vysvětlení stanic zabezpečené, spravované v Azure – Azure Active Directory
description: Další informace o pracovních stanic zabezpečené, spravované v Azure a pochopit, proč jsou to důležité.
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
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491596"
---
# <a name="understand-secure-azure-managed-workstations"></a>Principy pracovních stanic zabezpečené, spravované v Azure

Zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých role jako správci vývojářů a operátorů důležité služby. Pokud dojde k ohrožení zabezpečení pracovní stanice klienta, může selhat nebo být neúčinná mnoho kontrolních mechanismů pro zabezpečení a záruky.

Tento dokument popisuje, co potřebujete pro vytváření zabezpečených pracovních stanic, často označované jako privilegovaný přístup pracovní stanice s PRIVILEGOVANÝM přístupem. Tento článek také obsahuje podrobné pokyny k nastavení počátečního zabezpečení ovládací prvky. Tento návod popisuje, jak cloudové technologie může spravovat službu. Spoléhá na funkce zabezpečení, které byly zavedeny v 10RS5 Windows, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory a Intune.

> [!NOTE]
> Tento článek vysvětluje koncept zabezpečené pracovní stanice a jeho význam. Pokud jste již seznámení s konceptem a chcete přejít k nasazení, navštivte [nasadit zabezpečené pracovní stanice](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Proč je důležité přístup k zabezpečené pracovní stanice

Rychlé osvojení cloudových služeb a schopnost pracovat odkudkoli vytvořila novou metodu využívání. Využitím slabého zabezpečovací mechanizmy u zařízení, ve kterém správci pracovat útočníci získají přístup na oprávněné prostředky.

Omylem nepoužili špatně privilegovaný a dodavatelský řetězec útoky patří mezi hlavní pět metod, které útočníci používají k porušení zabezpečení organizace. Jsou to také druhý nejčastěji zjistil taktika případy hlášené v roce 2018 podle [sestava Verizon hrozeb](https://enterprise.verizon.com/resources/reports/dbir/)a [zpráva Security Intelligence Report](https://aka.ms/sir).

Většina útočníci postupujte takto:

1. Rekognoskace najít způsob, často specifické pro odvětví.
1. Analýza ke shromáždění informací a identifikovat nejlepší způsob, jak proniknout pracovní stanice, která bude považován za nízkou hodnotu.
1. Trvalost nakonfigurujte, tím vyhledejte prostředky k přesunutí [následně k laterálnímu](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Cílem důvěrná a citlivá data.

Během sondování útočníci často proniknout zařízení, která vypadá to, že s nízkým rizikem nebo podhodnoceni. Tyto zranitelných zařízení používají k vyhledání příležitost, laterální pohyb a najít administrativní uživatele a zařízení. Po získají přístup k privilegovaným uživatelem role, útočníci identifikaci cenných dat a úspěšně stáhnout data.

![Vzor typické ohrožení zabezpečení](./media/concept-azure-managed-workstation/typical-timeline.png)

Tento dokument popisuje řešení, které pomáhá chránit před takovým útokům laterální výpočetní zařízení. Řešení izoluje management a služby od méně důležitých zařízení produktivitu zásadní řetězci, předtím, než může být prostupující zařízení, která má přístup k citlivým cloudových prostředků. Toto řešení využívá nativní služby Azure, které jsou součástí Microsoft 365 Enterprise zásobníku:

* Intune pro správu zařízení a seznamu bezpečných aplikací a adresy URL
* AutoPilot pro nastavení zařízení, nasazování a aktualizace
* Azure AD pro správu uživatelů, podmíněný přístup a ověřování službou Multi-Factor Authentication
* Windows 10 (aktuální verze) pro zařízení health attestation a činnost koncového uživatele
* Ochrana ATP v programu Defender pro cloudově spravovaného řešení endpoint protection, detekce a reakce
* Azure AD PIM pro správu autorizace a just-in-time (JIT) privilegovaný přístup k prostředkům

## <a name="who-benefits-from-a-secure-workstation"></a>Který těží z zabezpečené pracovní stanice?

Všichni uživatelé a operátoři výhody při použití zabezpečených pracovních stanic. Útočník zneužije počítač nebo zařízení, můžete vydávat se za všechny uložené v mezipaměti účty. Při přihlášení k zařízení, může také používají přihlašovací údaje a tokeny. Toto riziko je důležité zabezpečení zařízení, které se používají pro privilegované role, včetně práva správce. Zařízení s privilegované účty jsou cíle taktiky Lateral Movement a útoky metodou zvýšení oprávnění. Tyto účty můžete použít pro širokou škálu prostředků, jako:

* Správce místních nebo cloudových systémů
* Pracovní stanice určená pro vývojáře pro důležité systémy
* Správce účtu sociálních médií s vysokým vystavení
* Vysoce citlivé a pracovní stanice, jako je například SWIFT platby terminálu
* Pracovní stanice zpracování tajemství

Chcete-li snížit riziko, měli byste implementovat kontrolní mechanismy zabezpečení se zvýšenými oprávněními pro privilegované pracovních stanic, které používají tyto účty. Další informace najdete v tématu [Průvodce nasazením funkce Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [plán Office 365](https://aka.ms/o365secroadmap), a [zabezpečení privilegovaného přístupu](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Proč používat vyhrazené pracovní stanice?

I když je možné přidat do stávající zařízení zabezpečení, je lepší začít s bezpečný základ. Vložit vaše organizace nejvhodnější místo k údržbě vysoká úroveň zabezpečení, začněte s zařízení, které už znáte, je zabezpečené a implementovat sadu ovládacích prvků zabezpečení.

Rostoucím počtem vektory útoku prostřednictvím e-mailu a procházení webu je stále obtížné Ujistěte se, že zařízení může považovat za důvěryhodné. Tento průvodce to předpokládá, že vyhrazené pracovní stanice je izolovaná od standardní produktivitu, procházení a e-mailu. Odebrání produktivitu, procházení webu a e-mailu ze zařízení může mít negativní vliv na produktivitu. Bezpečnostní pojistka je však obvykle přijatelné pro scénáře, ve kterém úlohy projektu nevyžadují explicitně ji a riziko bezpečnostních incidentů je vysoká.

> [!NOTE]
> Procházení webu tady odkazuje na obecné přístup k libovolné weby, které může být aktivitě s vysokým rizikem. Takové procházení se výrazně liší od pomocí webového prohlížeče pro přístup k malý počet dobře známé webů pro správu pro služby, jako je Azure, Office 365, dalších poskytovatelů cloudových služeb a aplikací SaaS.

Členství ve skupině strategie posílit zabezpečení zvýšením počtu a typu ovládací prvky, které útočníkovi zabránit v získání přístupu citlivá aktiva. Model popsaných v tomto článku používá oprávnění vrstvený návrh a omezuje oprávnění správce na konkrétní zařízení.

## <a name="supply-chain-management"></a>Správa dodavatelského řetězce

Zásadní pro zabezpečené pracovní stanice je dodavatelský řetězec řešení kde použít důvěryhodné pracovní stanice názvem kořenového vztahu důvěryhodnosti. Pro tohle řešení používá důvěryhodnému kořenovému [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technologie. K zabezpečení pracovní stanice, Autopilot umožňuje využívat Microsoft optimalizovaných pro výrobce OEM systému Windows 10 zařízení. Tato zařízení jsou v známého funkčního stavu od výrobce. Místo obnovování z Image nemusí být vždy bezpečná zařízení, můžete transformovat Autopilot zařízení s Windows do stavu "business ready". Použije zásady a nastavení, nainstaluje aplikace a dokonce i změny na edici Windows 10. Například Autopilot může změnit instalaci zařízení s Windows z Windows 10 Pro na Windows 10 Enterprise tak, aby ji používat pokročilé funkce.

![Úrovně zabezpečení pracovní stanice](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role zařízení a profily

Tento návod odkazuje na několik profilů zabezpečení a role, které vám pomůžou vytvářet bezpečnější řešení pro uživatele, vývojáři a pracovníci IT. Tyto profily vyrovnávání použitelnosti a rizika pro běžné uživatele, kteří mohou těžit z rozšířené nebo zabezpečené pracovní stanici. Nastavení konfigurace, které jsou tady k dispozici jsou založeny na přijetí oborových standardů. Tento návod ukazuje, jak k posílení zabezpečení Windows 10 a snižuje rizika spojená s ohrožení zařízení nebo uživatele. Dělá to pomocí zásad a technologie pro usnadnění správy funkce zabezpečení a rizika.
![Úrovně zabezpečení pracovní stanice](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Nízká zabezpečení** – spravované, standardní pracovní stanici, která poskytuje dobrým výchozím bodem pro většinu domácích a malých pracovní účely. Tato zařízení jsou zaregistrované ve službě Azure AD a spravovaná pomocí Intune. Tento profil umožňuje uživatelům spouštět všechny aplikace a procházet všechny weby. Antimalwarové řešení, jako jsou [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) by měla být povolená.

* **Zvýšené zabezpečení** – toto řešení základní úrovně, chráněné je výhodná pro domácí uživatele, malým firmám a obecné vývojáře.

   Vylepšené pracovní stanice je založená na zásadách způsob, jak zvýšit zabezpečení profil s nízkým zabezpečením. Poskytuje zabezpečené prostředky pro práci se zákaznickými a zároveň využívat kancelářským nástrojům, jako je e-mail a procházení webu. Zásady auditu a Intune můžete použít k monitorování Vylepšené pracovní stanice určená pro použití chování a profilu uživatele. Nasaďte profil Vylepšené pracovní stanici s Windows 10 (1809) skriptu a využívá pokročilý malware ochrany pomocí [pokročilé ochrany před internetovými útoky (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Vysoké zabezpečení** – nejúčinnějším způsobem pro omezení možností útoku na pracovní stanice je k odebrání schopnost self-administer pracovní stanici. Odebrání oprávnění místního správce je krok, který zlepšuje zabezpečení, ale to může ovlivnit produktivitu správně implementovaná. Profil vysoké zabezpečení je založena na profilu zvýšené zabezpečení jeden značné změně: odebrání místního správce. Tento profil je určený pro vysoce profilu uživatele: členové vedení, mezd a citlivá data uživatelů, schvalovatele pro služby a procesy.

   Vysoké zabezpečení uživatele vyžadují více řízeném prostředí ale stále mít možnost provádět aktivity, jako je e-mailu a procházení v jednoduché použití prostředí webu. Uživatelé očekávají funkce, jako jsou soubory cookie, oblíbených sestav a ostatní klávesové zkratky pro práci. Tito uživatelé však nemusí vyžadovat schopnost upravovat nebo ladění zařízení. Také není nutné instalovat ovladače. Profil vysoké zabezpečení se nasazuje pomocí vysoké zabezpečení – skript Windows 10 (1809).

* **Specializované** – útočníci cílí vývojáře a IT správce, protože upravují systémy, které vás zajímají útočníci. Specializované pracovní stanice rozšiřuje možnosti zásad vysoce zabezpečené pracovní stanice správou místních aplikací a omezení webů. Omezuje také možnosti s vysokým rizikem zvýšení produktivity, jako je například ActiveX, Java, pluginy prohlížeče a další ovládací prvky Windows. Nasadíte tento profil s DeviceConfiguration_NCSC - skript SecurityBaseline Windows 10 (1803).

* **Zabezpečené** – útočník zneužije účet správce, mohou způsobit poškození důležité obchodní krádežemi dat změny dat nebo přerušení služeb. V tomto stavu posílené pracovní stanici umožňuje všechny kontrolní mechanismy zabezpečení a zásady, které omezují přímou kontrolu správy místních aplikací. Zabezpečené pracovní stanici, která nemá žádné nástroje pro produktivitu tak obtížnější ohrozit zabezpečení zařízení. Blokuje nejběžnější vektor útoky typu phishing: e-mailových a sociálních médií.  Zabezpečené pracovní stanice je možné nasadit s zabezpečení pracovní stanice – Windows 10 (1809) SecurityBaseline skriptu.

   ![Zabezpečené pracovní stanice](./media/concept-azure-managed-workstation/secure-workstation.png)

   Zabezpečené pracovní stanici, která poskytuje správci posílené pracovní stanici, který má ovládací prvek vymazat aplikace a ochrany application guard. Pracovní stanice používá k ochraně hostitele z škodlivého chování funkce credential guard, funkce device guard a ochrany exploit guard. Všechny místní disky jsou také šifrované pomocí Bitlockeru.

* **Izolované** – v tomto scénáři offline, vlastní představuje extrémní konci spektra. Žádné instalační skripty jsou k dispozici pro tento případ. Můžete potřebovat ke správě nejzásadnější funkce, která vyžaduje nepodporované nebo bez opravy zabezpečení starší verze operačního systému. Například výrobní linky vysoké hodnoty nebo života – podpora systému. Protože je velmi důležité zabezpečení a cloudové služby jsou k dispozici, můžete spravovat a aktualizovat tyto počítače ručně nebo pomocí izolovaného architektuře doménové struktury služby Active Directory jako je například vylepšené zabezpečení správce prostředí (ESAE). Za těchto okolností zvažte odebrání všech přístupu kromě základní kontroly stavu Intune a ochrana ATP v programu.

  * [Požadavek komunikace sítě Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Požadavek komunikace sítě ochrany ATP v programu](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Další postup

[Nasazení zabezpečených pracovních stanic spravovanými Azure](howto-azure-managed-workstation.md).
