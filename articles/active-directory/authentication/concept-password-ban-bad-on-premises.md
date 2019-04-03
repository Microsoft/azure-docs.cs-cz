---
title: Ochrana hesel Azure AD – Azure Active Directory
description: Zakázat Slabá hesla v místní službě Active Directory s použitím ochrany hesla Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cd9f6112cbca78b323e0a14818b06f891a3f673
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862883"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Vynucení ochrany hesla Azure AD pro Windows Server Active Directory

Ochrana hesel Azure AD je funkce, která vylepšuje zásady hesel v organizaci. Ochrana heslem v místním nasazení se používá i globálních a vlastních zakázané heslo seznamy, které jsou uložené ve službě Azure AD. Provádí stejné kontroly místní jako Azure AD pro změny založené na cloudu.

## <a name="design-principles"></a>Principy návrhu

Ochrana hesel Azure AD je navržen s těmito zásadami v úvahu:

* Řadiče domény se už nikdy nemusíte přímo komunikují po Internetu.
* Nejsou žádné nové síťové porty otevřené v řadičích domény.
* Nejsou potřeba žádné změny schématu služby Active Directory. Software používá existující služby Active Directory **kontejneru** a **serviceConnectionPoint** objekty schématu.
* Žádné minimální služby Active Directory domény nebo doménová struktura úroveň funkčnosti (funkčnosti domény/FFL) se vyžaduje.
* Software nelze vytvořit nebo vyžadují účtů v doménách Active Directory, které chrání.
* Hesla uživatelů nešifrovaný text nenechávejte řadič domény během operací ověření hesla nebo kdykoli.
* Přírůstkové nasazení je podporováno, ale zásady hesel se vynucují jenom, kde je nainstalovaný Agent řadič domény (DC agenta). Naleznete v části Další podrobnosti.

## <a name="incremental-deployment"></a>Přírůstkové nasazení

Ochrana hesel Azure AD podporuje přírůstkové nasazení řadiče domény v doméně služby Active Directory, ale je důležité pochopit, co to vlastně znamená a jaké jsou nevýhody.

Software agenta ochrany řadič domény hesla Azure AD můžete pouze ověření hesel, když je nainstalovaný na řadiči domény a pouze pro změny hesla, které se odesílají do řadiče domény. Není možné do ovládacího prvku, které řadiče domény se zvolí klientské počítače Windows pro zpracování změn hesla uživatele. Aby bylo možné zaručit konzistenci a prosazování zabezpečení ochrany univerzální heslo, musí být nainstalován software agenta řadiče domény na všechny řadiče domény v doméně.

Mnoho organizací bude chtít provést opatrní testování ochrany hesla Azure AD na podmnožinu svých řadičů domény před tím úplné nasazení. Ochrana hesel Azure AD podporuje částečné nasazení, ie softwaru agenta řadiče domény v dané řadiči domény se aktivně ověření hesla i v případě, že jiné řadiče domény v doméně nemají nainstalovaného softwaru agenta řadiče domény. Částečné nasazení tohoto typu nejsou zabezpečení a jsou jiné než nedoporučuje pro účely testování.

## <a name="architectural-diagram"></a>Diagram architektury

Je důležité pochopit základní návrhu a funkci koncepty před nasazením služby Azure AD ochrana heslem v prostředí místní služby Active Directory. Následující diagram znázorňuje, jak spolu součásti ochrany heslem fungují:

![Jak spolupracují součásti ochrany hesla Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Spouští službu Proxy ochrana hesel Azure AD na jakýkoli počítač připojený k doméně v aktuální doménové struktuře služby Active Directory. Jeho hlavním účelem je přesměrování požadavků na stažení zásady hesla z řadiče domény do služby Azure AD. Vrátí pak odpovědi ze služby Azure AD k řadiči domény.
* Knihovny DLL agenta DC filtru hesel přijímá žádosti o ověření hesla uživatele z operačního systému. Předává je do služby agenta řadiče domény, na kterém běží místně na řadiči domény.
* Službu řadiče domény agenta ochrany pomocí hesla přijímá žádosti o ověření hesla z knihovny DLL agenta DC filtru hesel. Zpracuje pomocí aktuální zásady hesel (místně dostupné) a vrátí výsledek: *předat* nebo *selhání*.

## <a name="how-password-protection-works"></a>Jak funguje ochrana heslem

Každá instance služby Proxy ochrana hesel Azure AD inzeruje na řadiče domény v doménové struktuře tak, že vytvoříte **serviceConnectionPoint** objektu ve službě Active Directory.

Každá služba řadiče domény agenta k ochraně heslem také vytvoří **serviceConnectionPoint** objektu ve službě Active Directory. Tento objekt se používá hlavně pro vytváření sestav a Diagnostika.

Služba agenta řadiče domény zodpovídá za inicializaci stáhnout nové zásady hesla ze služby Azure AD. Prvním krokem je najít pomocí dotazu na doménovou strukturu pro proxy server služby Proxy ochrana hesel Azure AD **serviceConnectionPoint** objekty. Po nalezení službu proxy serveru k dispozici řadiče domény Agent odešle žádost o stažení zásady hesla ve službě proxy. Služba proxy pak odešle požadavek do služby Azure AD. Služba proxy vrátí odpověď služby agenta řadiče domény.

Až službu agenta pro řadič domény obdrží nové zásady hesla ze služby Azure AD, služba ukládá zásady vyhrazené složky v kořenovém adresáři domény *sysvol* složku sdílenou složku. Služba agenta DC také sleduje tuto složku, v případě, že novější zásady replikace v od jiných služeb agenta pro řadič domény v doméně.

Služba agenta řadiče domény vždy požádá o novou zásadu na spuštění služby. Po spuštění služby agenta pro řadič domény zkontroluje aktuální místně dostupné zásady stáří po hodinách. Pokud tato zásada je starší než jednu hodinu, řadič domény Agent si vyžádá nové zásady ze služby Azure AD, jak je popsáno výše. Pokud aktuální zásady není starší než jednu hodinu, řadič domény agenta dál používat tuto zásadu.

Pokaždé, když se zásady služby Azure AD protection heslo heslo se stáhne, tuto zásadu je specifická pro tenanta. Jinými slovy zásady pro hesla jsou vždy globální seznam zakázaných hesel Microsoft i vlastní seznam zakázaných hesel na tenanta.

Řadič domény agenta komunikuje se službou proxy přes RPC přes protokol TCP. Proxy služby čeká na těchto volání na dynamické nebo statické RPC portu, v závislosti na konfiguraci.

Řadič domény agenta nikdy naslouchá na síti dostupný port.

Služba proxy nikdy nevolá službu agenta řadiče domény.

Služba proxy jsou bezstavové. Nikdy mezipaměti zásad nebo jakéhokoli jiného stavu stáhli z Azure.

Služba agenta řadiče domény vždy používá nejnovější zásady hesel místně dostupné k vyhodnocení hesla uživatele. Pokud žádné zásady hesel je k dispozici na místní řadič domény, je automaticky přijat heslo. Pokud k tomu dojde, zprávu o události je zaznamenána upozornit správce.

Ochrana hesel Azure AD není modul aplikace v reálném čase zásad. Může existovat zpoždění mezi při provedení změny konfigurace zásady hesla ve službě Azure AD a když se změní dosáhne a vynucování na všechny řadiče domény.

## <a name="foresttenant-binding-for-password-protection"></a>Vazba doménové struktury/tenanta k ochraně heslem

Nasazení služby Azure AD ochrana heslem v doménové struktuře služby Active Directory vyžaduje registraci této doménové struktury s Azure AD. Azure AD musí být zaregistrovaný také každou službu proxy, která je nasazena. Tyto registrace doménové struktury a proxy serveru jsou spojeny s konkrétním tenanta Azure AD, který je identifikován implicitně pomocí přihlašovacích údajů, které se používají během registrace.

Doménové struktury služby Active Directory a všechny nasazené proxy služby v rámci doménové struktury musí být zaregistrovaná pomocí stejného tenanta. Není možné mít doménové struktury služby Active Directory nebo všechny služby serveru proxy v tom, že klienti doménové struktury registruje na různých služeb Azure AD. Správně konfigurováno nasazení mezi příznaky patří nemožnost stáhnout zásady pro hesla.

## <a name="download"></a>Ke stažení

Dva instalační programy požadovaných agenta pro ochranu hesel Azure AD jsou k dispozici [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Další postup
[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
