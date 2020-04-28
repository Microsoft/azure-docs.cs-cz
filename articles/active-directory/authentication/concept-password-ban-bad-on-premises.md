---
title: Ochrana heslem v Azure AD – Azure Active Directory
description: Zákaz slabého hesla v místní službě Active Directory pomocí ochrany heslem v Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74848642"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Vynucování ochrany hesel Azure AD pro Windows Server Active Directory

Ochrana heslem Azure AD je funkce, která vylepšuje zásady hesel v organizaci. Místní nasazení ochrany heslem používá globální i vlastní seznamy zakázaných hesel, které jsou uložené ve službě Azure AD. Provádí stejné kontroly v místním prostředí, protože služba Azure AD funguje pro cloudové změny. Tyto kontroly se provádějí během změn hesla a scénářů resetování hesla.

## <a name="design-principles"></a>Principy návrhu

Ochrana heslem Azure AD je navržená s ohledem na tyto principy:

* Řadiče domény nikdy nemusí komunikovat přímo s internetem.
* Na řadičích domény se neotevírají žádné nové síťové porty.
* Nevyžadují se žádné změny schématu služby Active Directory. Software používá existující **kontejner** služby Active Directory a objekty schématu **serviceConnectionPoint** .
* Není nutná žádná minimální funkční úroveň domény nebo doménové struktury služby Active Directory (úrovni funkčnosti domény/FFL).
* Software nevytváří nebo nevyžaduje účty v doménách služby Active Directory, které chrání.
* Uživatel s nešifrovaným textem heslo nikdy neopouští řadič domény, a to buď během operací ověřování hesla, nebo v jiné době.
* Software není závislý na jiných funkcích služby Azure AD. například synchronizace hodnot hash hesel služby Azure AD není v relaci a není nutná, aby funkce Ochrana hesel Azure AD fungovala.
* Přírůstkové nasazení je podporované, ale zásady hesel se vynutily jenom tam, kde je nainstalovaný agent řadiče domény (DC Agent). Další podrobnosti najdete v dalším tématu.

## <a name="incremental-deployment"></a>Přírůstkové nasazení

Ochrana heslem Azure AD podporuje přírůstkové nasazení napříč řadiči domény v doméně služby Active Directory, ale je důležité pochopit, co to skutečně znamená a jaké jsou kompromisy.

Software agenta řadiče domény Azure AD pro ochranu heslem může ověřovat hesla jenom v případě, že je nainstalovaný na řadiči domény a jenom pro změny hesel, které se odesílají na tento řadič domény. Není možné určit, které řadiče domény jsou vybrány klientskými počítači Windows pro zpracování změn hesel uživatele. Aby bylo zajištěno jednotné chování a vynucení zabezpečení univerzální ochrany heslem, musí být software agenta DC nainstalován na všech řadičích domény v doméně.

Mnoho organizací bude chtít provést pečlivé testování ochrany heslem Azure AD na podmnožinu svých řadičů domény před provedením úplného nasazení. Ochrana heslem Azure AD podporuje částečné nasazení, IE software agenta DC na daném řadiči domény aktivně ověřuje hesla i v případě, že jiné řadiče domény v doméně nemají nainstalovaný software agenta DC. Částečná nasazení tohoto typu nejsou bezpečná a nejsou doporučována jinak než pro účely testování.

## <a name="architectural-diagram"></a>Diagram architektury

Před nasazením ochrany heslem Azure AD v místním prostředí Active Directory je důležité pochopit základní koncepty návrhu a funkcí. Následující diagram znázorňuje, jak komponenty ochrany heslem spolupracují:

![Jak spolupracují komponenty ochrany hesel Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Služba proxy ochrany heslem Azure AD běží na jakémkoli počítači připojeném k doméně v aktuální doménové struktuře služby Active Directory. Hlavním účelem je přeposlání požadavků na stažení zásad hesel z řadičů domény do Azure AD. Pak vrátí odpovědi z Azure AD do řadiče domény.
* Knihovna DLL filtru hesel agenta řadiče domény přijímá požadavky na ověření hesla uživatele z operačního systému. Předá je službě agenta řadiče domény, která je spuštěná místně na řadiči domény.
* Služba agenta řadiče domény ochrana heslem přijímá žádosti o ověření hesla z knihovny DLL filtru hesla agenta řadiče domény. Zpracovává je pomocí aktuálních (místně dostupných) zásad hesel a vrací výsledek: *Pass* nebo *selhat*.

## <a name="how-password-protection-works"></a>Jak funguje ochrana heslem

Každá instance služby proxy ochrany heslem Azure AD se sama inzeruje k řadičům domény v doménové struktuře vytvořením objektu **serviceConnectionPoint** ve službě Active Directory.

Každá služba agenta řadiče domény pro ochranu heslem také vytvoří objekt **serviceConnectionPoint** ve službě Active Directory. Tento objekt se používá hlavně pro vytváření sestav a diagnostiku.

Služba agenta řadiče domény zodpovídá za zahájení stahování nových zásad hesel ze služby Azure AD. Prvním krokem je vyhledání služby proxy ochrany heslem Azure AD pomocí dotazu na doménovou strukturu pro objekty proxy **serviceConnectionPoint** . Po nalezení dostupné služby proxy odešle agent DC požadavek na stažení zásady hesla do služby proxy serveru. Služba proxy pak odešle požadavek do služby Azure AD. Služba proxy pak vrátí odpověď službě agenta řadiče domény.

Jakmile služba agenta řadiče domény obdrží od Azure AD nové zásady pro hesla, Služba ukládá zásady do vyhrazené složky v kořenovém adresáři sdílené složky v doméně *adresáře SYSVOL* . Služba agenta řadiče domény tuto složku sleduje i v případě, že novější zásady replikují v rámci jiných služeb agenta řadiče domény v doméně.

Služba agenta DC vždy při spuštění služby vyžádá novou zásadu. Po spuštění služby agenta řadiče domény zkontroluje stáří aktuálně dostupných zásad místně dostupné hodiny. Pokud je zásada starší než jedna hodina, agent DC si vyžádá novou zásadu z Azure AD prostřednictvím proxy služby, jak je popsáno výše. Pokud aktuální zásada není starší než jedna hodina, agent řadiče domény tuto zásadu nadále používá.

Pokaždé, když se stáhnou zásady hesla ochrany heslem Azure AD, jsou tyto zásady specifické pro tenanta. Jinými slovy: zásady hesel jsou vždycky kombinací globálního seznamu zakázaných hesel Microsoftu a vlastního seznamu zakázaných hesel pro jednotlivé klienty.

Agent DC komunikuje se službou proxy prostřednictvím protokolu RPC přes protokol TCP. Služba proxy naslouchá těmto voláním na dynamickém nebo statickém portu RPC v závislosti na konfiguraci.

Agent řadiče domény nikdy nenaslouchá na portu, který je k dispozici pro síť.

Proxy služba nikdy nevolá službu agenta řadiče domény.

Proxy služba je Bezstavová. Nikdy neukládá do mezipaměti zásady ani žádný jiný stav stažený z Azure.

Služba agenta řadiče domény vždy používá nejnovější zásady hesel dostupné místně k vyhodnocení hesla uživatele. Pokud na místním řadiči domény nejsou k dispozici žádné zásady hesla, heslo se automaticky přijme. Pokud k tomu dojde, je zaznamenána zpráva události upozorňující správce.

Ochrana heslem Azure AD není modulem aplikace zásad v reálném čase. Může dojít ke zpoždění mezi tím, kdy se ve službě Azure AD provede Změna konfigurace zásad hesel a když se tato změna dosáhne a vynutila na všech řadičích domény.

Ochrana heslem Azure AD funguje jako doplněk k existujícím zásadám hesel služby Active Directory, nikoli k náhradě. To zahrnuje jakékoli jiné knihovny DLL filtru třetích stran, které mohou být nainstalovány. Služba Active Directory vždy vyžaduje, aby všechny součásti pro ověření hesla souhlasily před přijetím hesla.

## <a name="foresttenant-binding-for-password-protection"></a>Vazba doménové struktury/tenanta pro ochranu heslem

Nasazení ochrany heslem Azure AD v doménové struktuře služby Active Directory vyžaduje registraci této doménové struktury pomocí Azure AD. Každá nasazená proxy služba musí být také zaregistrovaná ve službě Azure AD. Tato doménová struktura a registrace proxy jsou přidružené ke konkrétnímu tenantovi služby Azure AD, který implicitně identifikuje přihlašovací údaje, které se používají během registrace.

Doménová struktura služby Active Directory a všechny nasazené proxy služby v rámci doménové struktury musí být zaregistrované u stejného tenanta. Není podporované, aby byla doménová struktura služby Active Directory ani žádná služba proxy v této doménové struktuře zaregistrovaná v různých klientech Azure AD. Mezi příznaky takového nasazení, které je chybné, patří nemožnost stahovat zásady hesel.

## <a name="download"></a>Stáhnout

Dvě požadované instalační programy agenta pro ochranu hesel Azure AD jsou k dispozici na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Další kroky
[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
