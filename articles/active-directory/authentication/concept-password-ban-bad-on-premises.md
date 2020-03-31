---
title: Ochrana heslem Azure AD – Azure Active Directory
description: Zákaz slabých hesel v místní službě Active Directory pomocí ochrany heslem Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848642"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Vynucování ochrany hesel Azure AD pro Windows Server Active Directory

Azure AD ochrana heslem je funkce, která vylepšuje zásady hesel v organizaci. Místní nasazení ochrany heslem používá globální i vlastní seznamy zakázaných hesel, které jsou uložené ve službě Azure AD. Provádí stejné kontroly v místním prostředí jako Azure AD dělá pro změny na základě cloudu. Tyto kontroly jsou prováděny během změn hesla a scénářů obnovení hesla.

## <a name="design-principles"></a>Principy návrhu

Ochrana heslem Azure AD je navržena s ohledem na tyto zásady:

* Řadiče domény nikdy nemusí komunikovat přímo s internetem.
* V řadičích domény nejsou otevřeny žádné nové síťové porty.
* Nejsou vyžadovány žádné změny schématu služby Active Directory. Software používá existující **kontejner** služby Active Directory a objekty schématu **připojení k webu služby.**
* Není vyžadována žádná minimální úroveň funkčnosti domény nebo doménové struktury služby Active Directory (DFL/FFL).
* Software nevytváří ani nevyžaduje účty v doménách služby Active Directory, které chrání.
* Uživatelská hesla ve veřejném textu nikdy neopustí řadič domény, a to ani během operací ověření hesla, ani kdykoli jindy.
* Software není závislý na jiných funkcích Azure AD; Například synchronizace hash hesla Azure AD nesouvisí a není vyžadována, aby fungovala ochrana heslem Azure AD.
* Přírůstkové nasazení je podporováno, ale zásady hesel jsou vynuceny pouze v případě, že je nainstalován agent řadiče domény (AGENT řadiče domény). Další podrobnosti najdete v dalším tématu.

## <a name="incremental-deployment"></a>Přírůstkové nasazení

Ochrana heslem Azure AD podporuje přírůstkové nasazení napříč řadiči domény v doméně služby Active Directory, ale je důležité pochopit, co to ve skutečnosti znamená a jaké jsou kompromisy.

Software agenta řadiče domény ochrany hesel Azure AD může ověřit hesla pouze v případě, že je nainstalován na řadiči domény a pouze pro změny hesel, které jsou odesílány do tohoto řadiče domény. Není možné určit, které řadiče domény jsou vybrány klientskými počítači systému Windows pro zpracování změn uživatelských hesel. Aby bylo zaručeno konzistentní chování a vynucení zabezpečení při všeobecné ochraně heslem, musí být software agenta řadiče domény nainstalován na všech řadičích domény v doméně.

Mnoho organizací bude chtít provést pečlivé testování ochrany heslem Azure AD na podmnožinu svých řadičů domény před provedením úplné nasazení. Ochrana heslem Azure AD podporuje částečné nasazení, tj. Částečná nasazení tohoto typu nejsou zabezpečená a nejsou doporučena jinak než pro účely testování.

## <a name="architectural-diagram"></a>Architektonický diagram

Je důležité pochopit základní koncepty návrhu a funkcí před nasazením ochrany heslem Služby Azure AD v místním prostředí služby Active Directory. Následující diagram znázorňuje, jak součásti ochrany heslem spolupracují:

![Jak komponenty ochrany heslem Azure AD spolupracují](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Služba Proxy ochrany heslem Azure AD běží na libovolném počítači přilehlém k doméně v aktuální doménové struktuře služby Active Directory. Jeho primárním účelem je předávat požadavky na stažení zásad hesel z řadičů domény do Azure AD. Potom vrátí odpovědi z Azure AD do řadiče domény.
* DLL filtru hesel agenta řadiče domény přijímá požadavky na ověření hesla uživatele z operačního systému. Předává je službě AGENT řadiče domény, která je spuštěna místně v řadiči domény.
* Služba dc agent ochrany heslem obdrží požadavky na ověření hesla z dll filtru hesel řadiče domény agenta. Zpracovává je pomocí aktuální (místně dostupné) zásady hesla a vrátí výsledek: *pass* nebo *fail*.

## <a name="how-password-protection-works"></a>Jak funguje ochrana heslem

Každá instance proxy služby Azure AD Password Protection se inzeruje do řadičů domény v doménové struktuře vytvořením objektu **connectionpoint služby** ve službě Active Directory.

Každá služba agenta řadiče domény pro ochranu heslem také vytvoří objekt **služby ConnectionPoint** ve službě Active Directory. Tento objekt se používá především pro vytváření sestav a diagnostiky.

Služba agent a dc je zodpovědná za zahájení stahování nové zásady hesel z Azure AD. Prvním krokem je vyhledání služby proxy ochrany heslem Azure AD dotazem na objekty doménové struktury pro objekty proxy **službyConnectionPoint.** Je-li nalezena dostupná služba proxy, odešle agent řadiče domény službě proxy požadavek na stažení zásad hesel. Služba proxy zase odešle požadavek do Služby Azure AD. Služba proxy pak vrátí odpověď na službu agenta řadiče domény.

Poté, co služba AGENT řadiče domény obdrží nové zásady hesel z Azure AD, služba ukládá zásady ve vyhrazené složce v kořenovém adresáři sdílené složky *sysvol* domény. Služba agent a dc agent také monitoruje tuto složku v případě, že se replikují novější zásady z jiných služeb agenta řadiče domény v doméně.

Služba agent a řadič domény vždy požaduje nové zásady při spuštění služby. Po spuštění služby AGENT ŘADIČE DOMÉNY zkontroluje stáří aktuální místně dostupné zásady každou hodinu. Pokud je zásada starší než jedna hodina, agent řadiče domény požaduje novou zásadu z Azure AD prostřednictvím služby proxy, jak je popsáno výše. Pokud aktuální zásady není starší než jednu hodinu, agent řadiče domény nadále používat tuto zásadu.

Při každém stažení zásady hesel ochrany heslem Azure AD je tato zásada specifická pro klienta. Jinými slovy zásady hesel jsou vždy kombinací seznamu globálních zakázaných hesel společnosti Microsoft a seznamu vlastních zakázaných hesel pro jednotlivé klienty.

Agent řadiče domény komunikuje se službou proxy prostřednictvím vzdáleného volání procedur přes Protokol TCP. Služba proxy naslouchá těmto voláním na dynamickém nebo statickém portu Vzdáleného volání procedur v závislosti na konfiguraci.

Agent řadiče domény nikdy nenaslouchá na portu dostupném v síti.

Služba proxy nikdy nevolá službu AGENT ŘADIČE DOMÉNY.

Služba proxy je bezstavová. Nikdy ukládá do mezipaměti zásady nebo jiný stav stažený z Azure.

Služba agent a řadič domény vždy používá nejnovější místně dostupné zásady hesel k vyhodnocení hesla uživatele. Pokud v místním řadiči domény nejsou k dispozici žádné zásady hesel, bude heslo automaticky přijato. V takovém případě je zaznamenána zpráva o události, která upozorní správce.

Azure AD ochrana heslem není modul aplikace zásad v reálném čase. Může dojít ke zpoždění mezi při změně konfigurace zásad hesla ve službě Azure AD a když tato změna dosáhne a je vynucena na všech řadičích domény.

Ochrana heslem služby Azure AD funguje jako doplněk k existujícím zásadám hesel služby Active Directory, nikoli jako náhrada. To zahrnuje všechny ostatní dll filtru hesel třetích stran, které mohou být nainstalovány. Služba Active Directory vždy vyžaduje, aby všechny součásti ověření hesla souhlasily před přijetím hesla.

## <a name="foresttenant-binding-for-password-protection"></a>Vazba doménové struktury/klienta pro ochranu heslem

Nasazení ochrany heslem Azure AD v doménové struktuře služby Active Directory vyžaduje registraci této doménové struktury pomocí služby Azure AD. Každá služba proxy, která je nasazená musí být také registrována ve službě Azure AD. Tyto registrace doménové struktury a proxy jsou přidruženy k určitému tenantovi Služby Azure AD, který je implicitně identifikován přihlašovacími údaji, které se používají při registraci.

Doménová struktura služby Active Directory a všechny nasazené služby proxy v rámci doménové struktury musí být registrovány u stejného klienta. Není podporováno, aby byla doménová struktura služby Active Directory nebo jakékoli služby proxy v této doménové struktuře registrovány na různé klienty Azure AD. Příznaky takového chybně nakonfigurovaného nasazení zahrnují neschopnost stahovat zásady hesel.

## <a name="download"></a>Stáhnout

Dva instalátory požadovaných agentů pro ochranu heslem Azure AD jsou k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Další kroky
[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
