---
title: Co je nového v Azure Defenderu pro IoT
description: Tento článek vám umožní zjistit, co je nového v nejnovější verzi programu Defender pro IoT.
ms.topic: how-to
ms.date: 03/14/2021
ms.openlocfilehash: 970d46a76f125dcaf82491fdea877e6874e2384f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778691"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Co je nového v Azure Defenderu pro IoT?

Tento článek obsahuje seznam nových funkcí a vylepšení funkcí pro program Defender pro IoT.

Vyznačené funkce jsou ve verzi PREVIEW. [Doplňkové podmínky Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.
## <a name="march-2021"></a>Březen 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Senzorem rozšířená vlastní pravidla výstrah (verze Public Preview)

Nyní můžete vytvořit vlastní pravidla upozornění na základě dne, skupiny dnů a doby, po kterou byla zjištěna síťová aktivita.  Práce s podmínkami pravidla dne a času je užitečná, například v případech, kdy je Závažnost výstrahy odvozena v době, kdy dojde k události výstrahy. Můžete například vytvořit vlastní pravidlo, které aktivuje výstrahu s vysokou závažností při zjištění síťové aktivity na víkendu nebo večer.

Tato funkce je k dispozici na senzoru s vydáním verze 10,2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Místní Konzola pro správu – výstrahy exportu (Public Preview)

Informace o výstrahách se teď dají exportovat do souboru. CSV z místní konzoly pro správu. Můžete exportovat informace o všech zjištěných výstrahách nebo informace o exportu na základě filtrovaného zobrazení.

Tato funkce je k dispozici v místní konzole pro správu s vydáním verze 10,2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Přidat druhé síťové rozhraní do místní konzoly pro správu (Public Preview)

Zabezpečení nasazení teď můžete rozšířit přidáním druhého síťového rozhraní do místní konzoly pro správu. Tato funkce umožňuje, aby vaše místní správa měla připojené senzory v jedné zabezpečené síti, a zároveň umožňuje uživatelům přístup k místní konzole pro správu prostřednictvím druhého samostatného síťového rozhraní.

Tato funkce je k dispozici v místní konzole pro správu s vydáním verze 10,2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Tvůrce zařízení – nový Micro Agent (Public Preview)

K dispozici je nový modul pro sestavovatele zařízení. Modul, který je označován jako mikroagent, umožňuje:

- **Integrace s azure IoT Hub a Azure Defenderem pro IoT** – Sestavte si silnější řešení Endpoint Security přímo do zařízení IoT tím, že je integrujte s možností monitorování, kterou poskytuje Azure IoT Hub a Azure Defender pro IoT.
- **Flexibilní možnosti nasazení s podporou pro standardní operační systémy IoT** – je možné je nasadit jako binární balíček nebo jako upravitelný zdrojový kód s podporou standardních operačních systémů IoT, jako je Linux a Azure RTO.
- **Minimální požadavky na prostředky, které neobsahují závislosti jádra operačního systému** – malé nároky, nízké nároky na procesor a žádné závislosti jádra operačního systému.
- **Stav Security Management** – proaktivně monitoruje stav zabezpečení vašich zařízení IoT.
- **Průběžné zjišťování hrozeb IoT/ot v reálném čase – detekce** hrozeb, jako je botnety, pokusy o útok hrubou silou, kryptografická dolování hlásí a podezřelá síťová aktivita

Vystaralá dokumentace k programu Defender – IoT-Micro-Agent se přesune do *řešení založeného na agentovi pro sestavovatele zařízení>klasické* složce.

Tato sada funkcí je dostupná ve verzi Public Preview Cloud.

## <a name="january-2021"></a>Leden 2021

- [Zabezpečení](#security)
- [Onboarding](#onboarding)
- [Použitelnost](#usability)
- [Další aktualizace](#other-updates)
### <a name="security"></a>Zabezpečení

Pro tuto verzi byla provedena vylepšení pro obnovení certifikátu a hesla.

#### <a name="certificates"></a>Certifikáty
  
Tato verze vám umožní:

- Nahrajte certifikáty SSL přímo do senzorů a místních konzol pro správu.
- Proveďte ověření mezi místní konzolou pro správu a připojenými snímači a mezi konzolou pro správu a konzolou pro správu s vysokou dostupností. Ověřování je založeno na datech vypršení platnosti, na certifikátech kořenové certifikační autority a seznam odvolaných certifikátů.  Pokud se ověření nezdaří, relace nebude pokračovat.

Pro upgrady:

- Během upgradu se nezměnil certifikát SSL ani funkce ověřování.
- Po upgradu můžou správci senzorů a místních správců pro správu nahradit certifikáty SSL nebo aktivovat ověřování certifikátů SSL z nastavení systému a certifikátu SSL.  

Pro nové instalace:

- Při prvním přihlášení se uživatelé musí buď použít certifikát SSL (doporučeno) nebo místně generovaný certifikát podepsaný svým držitelem (nedoporučuje se).
- Ověřování certifikátu je ve výchozím nastavení zapnuté pro nové instalace.

#### <a name="password-recovery"></a>Obnovení hesla
  
Správce senzorů a místních konzol pro správu teď může obnovit hesla z Azure Defenderu pro IoT Portal. Předchozí obnovení hesla vyžadovalo zásah týmu podpory.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>Místní Konzola pro správu – potvrzená zařízení

Po počátečním přihlášení k místní konzole pro správu se teď uživatelům vyžaduje, aby nahráli aktivační soubor. Tento soubor obsahuje agregovaný počet zařízení, která se mají monitorovat v síti organizace. Toto číslo se označuje jako počet potvrzených zařízení.
Potvrzená zařízení se definují během procesu připojování na portálu Azure Defender pro IoT, kde se vygeneruje aktivační soubor.
Pro nahrání aktivačního souboru se vyžadují první upgrade uživatelů a uživatelů.
Po počáteční aktivaci může počet zařízení zjištěných v síti překročit počet potvrzených zařízení. K této události může dojít například v případě, že se k konzole pro správu připojíte více senzorů. Pokud dojde k nesouladu mezi počtem zjištěných zařízení a počtem potvrzených zařízení, zobrazí se v konzole pro správu upozornění. Pokud k této události dojde, měli byste nahrát nový soubor aktivace.

#### <a name="pricing-page-options"></a>Možnosti stránky s cenami

Stránka s cenami umožňuje připojit nové odběry do Azure Defenderu pro IoT a definovat potvrzená zařízení ve vaší síti.  
Stránka s cenami teď také umožňuje spravovat existující předplatná přidružená ke senzoru a aktualizovat závazek zařízení.

#### <a name="view-and-manage-onboarded-sensors"></a>Umožňuje zobrazit a spravovat senzory zprovoznění.

Stránka s novým portálem web a senzorů vám umožní:

- Přidejte popisné informace o senzoru. Například zóna přidružená ke senzoru nebo k bezplatným textovým značkám.
- Zobrazení a filtrování informací o senzorech. Můžete například zobrazit podrobnosti o senzorech, které jsou připojené ke cloudu nebo lokálně spravované, nebo zobrazit informace o senzorech v určité zóně.  

### <a name="usability"></a>Použitelnost

#### <a name="azure-sentinel-new-connector-page"></a>Stránka nového konektoru služby Azure Sentinel

Změnila se stránka konektoru dat Azure Defender pro IoT ve službě Azure Sentinel. Data Connector je teď založený na předplatných, nikoli v centrech IoT. umožnění zákazníkům lépe spravovat připojení konfigurace do Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Aktualizace oprávnění Azure Portal  

Byla přidána podpora čtecího modulu zabezpečení a správce zabezpečení.

### <a name="other-updates"></a>Další aktualizace

#### <a name="access-group---zone-permissions"></a>Přístup k oprávněním pro zónu skupin
  
Místní pravidla skupiny přístupu konzoly pro správu nebudou zahrnovat možnost udělit přístup ke konkrétní zóně. Neexistují žádné změny v definování pravidel, která používají lokality, oblasti a obchodní jednotky.   Po upgradu budou přístupové skupiny, které obsahují pravidla umožňující přístup ke konkrétním zónám, upraveny tak, aby povolovaly přístup ke své nadřazené lokalitě, včetně všech svých zón.

#### <a name="terminology-changes"></a>Terminologické změny

Pojem Asset byl přejmenován na zařízení v senzoru a místní konzole pro správu, v sestavách a v dalších rozhraních řešení.
V okně senzory a místní konzoly pro správu se jako termín Správa této události jmenovaly kroky pro nápravu.

## <a name="next-steps"></a>Další kroky

[Začínáme s Defenderem pro IoT](getting-started.md)
