---
title: Ochrana heslem v Azure AD – Azure Active Directory
description: Zakázání slabých hesel v místních Active Directory Domain Services prostředí pomocí ochrany heslem Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09ad2991d552cb9886911ac75ea23c690204a71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116644"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Vynutilit místní ochranu heslem Azure AD pro Active Directory Domain Services

Ochrana heslem Azure AD detekuje a blokuje známá slabá hesla a jejich varianty a může také blokovat další slabé výrazy, které jsou specifické pro vaši organizaci. Místní nasazení ochrany heslem služby Azure AD používá stejné globální a vlastní seznamy zakázaných hesel, které jsou uložené ve službě Azure AD, a provádí stejné kontroly u místních změn hesel, protože Azure AD funguje pro cloudové změny. Tyto kontroly se provádějí při změnách hesla a událostech resetování hesla pro řadiče domény s místními Active Directory Domain Services (služba AD DS).

## <a name="design-principles"></a>Principy návrhu

Ochrana heslem Azure AD je navržená s ohledem na následující principy:

* Řadiče domény (DCs) nikdy nemusejí komunikovat přímo s internetem.
* Na řadičích domény nejsou otevřeny žádné nové síťové porty.
* Nevyžadují se žádné změny schématu služba AD DS. Software používá existující *kontejnery* služba AD DS a objekty schématu *serviceConnectionPoint* .
* Není nutná žádná minimální úroveň funkčnosti služba AD DS domény nebo doménové struktury (úrovni funkčnosti domény/FFL).
* Software nevytváří nebo nevyžaduje účty v služba AD DSch doménách, které chrání.
* Uživatel s nešifrovaným textem heslo nikdy neopouští řadič domény, a to buď během operací ověřování hesla, nebo v jiné době.
* Software není závislý na jiných funkcích služby Azure AD. Například synchronizace hodnot hash hesel služby Azure AD (KOSMETICE) není v relaci nebo není vyžadována pro ochranu heslem služby Azure AD.
* Přírůstkové nasazení je podporované, ale zásady hesel se vynutily jenom tam, kde je nainstalovaný agent řadiče domény (DC Agent).

## <a name="incremental-deployment"></a>Přírůstkové nasazení

Ochrana heslem Azure AD podporuje přírůstkové nasazení napříč řadiči domény v doméně služba AD DS. Je důležité pochopit, co to skutečně znamená a jaké jsou kompromisy.

Software agenta řadiče domény Azure AD pro ochranu heslem může ověřovat hesla jenom v případě, že je nainstalovaný na řadiči domény a jenom pro změny hesel, které se na tento řadič domény odesílají. Není možné určit, které řadiče domény jsou vybrány klientskými počítači Windows pro zpracování změn hesel uživatele. K zajištění konzistentního chování a vynucení zabezpečení ochrany heslem služby Azure AD musí být software agenta DC nainstalovaný na všech řadičích domény v doméně.

Mnoho organizací chce pečlivě otestovat ochranu heslem Azure AD na podmnožinu svých řadičů domény před úplným nasazením. Pro podporu tohoto scénáře podporuje ochrana heslem Azure AD částečné nasazení. Software agenta DC na daném řadiči domény aktivně ověřuje hesla i v případě, že jiné řadiče domény v doméně nemají nainstalovaný software agenta DC. Částečná nasazení tohoto typu nejsou bezpečná a nejsou doporučována jinak než pro účely testování.

## <a name="architectural-diagram"></a>Diagram architektury

Před nasazením ochrany heslem Azure AD v místním prostředí služba AD DS je důležité pochopit základní koncepty návrhu a funkcí. Následující diagram znázorňuje, jak se komponenty ochrany heslem služby Azure AD vzájemně spolupracují:

![Jak spolupracují komponenty ochrany hesel Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Služba proxy ochrany heslem Azure AD běží na jakémkoli počítači připojeném k doméně v aktuální služba AD DS doménové struktuře. Hlavním účelem služby je přeposílání požadavků na stažení zásad hesel z řadičů domény do služby Azure AD a následnému vrácení odpovědí z Azure AD na řadič domény.
* Knihovna DLL filtru hesel agenta řadiče domény přijímá požadavky na ověření hesla uživatele z operačního systému. Filtr je přepošle na službu agenta řadiče domény, která běží místně na řadiči domény.
* Služba agenta řadiče domény služby Azure AD heslem přijímá žádosti o ověření hesla z knihovny DLL filtru hesel agenta řadiče domény. Služba agenta DC je zpracuje pomocí zásady hesel aktuální (místně dostupná) a vrátí výsledek *úspěšného* nebo *neúspěchu*.

## <a name="how-azure-ad-password-protection-works"></a>Jak funguje ochrana heslem Azure AD

Místní součásti ochrany heslem pro Azure AD fungují takto:

1. Každá instance služby proxy ochrany heslem Azure AD se sama inzeruje k řadičům domény v doménové struktuře vytvořením objektu *serviceConnectionPoint* ve službě Active Directory.

    Každá služba agenta řadiče domény pro ochranu heslem Azure AD také vytvoří objekt *serviceConnectionPoint* ve službě Active Directory. Tento objekt se používá hlavně pro vytváření sestav a diagnostiku.

1. Služba agenta řadiče domény zodpovídá za zahájení stahování nových zásad hesel ze služby Azure AD. Prvním krokem je vyhledání služby proxy ochrany heslem Azure AD pomocí dotazu na doménovou strukturu pro objekty proxy *serviceConnectionPoint* .

1. Po nalezení dostupné služby proxy odešle agent DC požadavek na stažení zásady hesla do služby proxy serveru. Služba proxy zase odešle požadavek do služby Azure AD a vrátí odpověď službě agenta řadiče domény.

1. Jakmile služba agenta řadiče domény obdrží od Azure AD nové zásady pro hesla, Služba ukládá zásady do vyhrazené složky v kořenovém adresáři sdílené složky v doméně *adresáře SYSVOL* . Služba agenta řadiče domény tuto složku sleduje i v případě, že novější zásady replikují v rámci jiných služeb agenta řadiče domény v doméně.

1. Služba agenta DC vždy při spuštění služby vyžádá novou zásadu. Po spuštění služby agenta řadiče domény zkontroluje stáří aktuálně dostupných zásad místně dostupné hodiny. Pokud je zásada starší než jedna hodina, agent DC si vyžádá novou zásadu z Azure AD prostřednictvím proxy služby, jak je popsáno výše. Pokud aktuální zásada není starší než jedna hodina, agent řadiče domény tuto zásadu nadále používá.

1. Když řadič domény obdrží události změny hesla, použije se k určení, jestli se nové heslo přijme nebo odmítne, pomocí zásad uložených v mezipaměti.

### <a name="key-considerations-and-features"></a>Klíčové pokyny a funkce

* Pokaždé, když se stáhnou zásady hesla ochrany heslem Azure AD, jsou tyto zásady specifické pro tenanta. Jinými slovy: zásady hesel jsou vždycky kombinací globálního seznamu zakázaných hesel Microsoftu a vlastního seznamu zakázaných hesel pro jednotlivé klienty.
* Agent DC komunikuje se službou proxy prostřednictvím protokolu RPC přes protokol TCP. Služba proxy naslouchá těmto voláním na dynamickém nebo statickém portu RPC v závislosti na konfiguraci.
* Agent řadiče domény nikdy nenaslouchá na portu, který je k dispozici pro síť.
* Proxy služba nikdy nevolá službu agenta řadiče domény.
* Proxy služba je Bezstavová. Nikdy neukládá do mezipaměti zásady ani žádný jiný stav stažený z Azure.
* Služba agenta řadiče domény vždy používá nejnovější zásady hesel dostupné místně k vyhodnocení hesla uživatele. Pokud na místním řadiči domény nejsou k dispozici žádné zásady hesla, heslo se automaticky přijme. Pokud k tomu dojde, je zaznamenána zpráva události upozorňující správce.
* Ochrana heslem Azure AD není modulem aplikace zásad v reálném čase. Může dojít ke zpoždění mezi tím, kdy se ve službě Azure AD provede Změna konfigurace zásad hesel a když se tato změna dosáhne a vynutila na všech řadičích domény.
* Ochrana heslem Azure AD funguje jako doplněk k existujícím zásadám pro služba AD DS hesla, nikoli k náhradě. To zahrnuje jakékoli jiné knihovny DLL filtru třetích stran, které mohou být nainstalovány. Služba AD DS vždy vyžaduje, aby všechny součásti pro ověření hesla souhlasily před přijetím hesla.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Vazba doménové struktury/tenanta pro ochranu hesel Azure AD

Nasazení ochrany heslem Azure AD v služba AD DS doménové struktuře vyžaduje registraci této doménové struktury pomocí Azure AD. Každá nasazená proxy služba musí být také zaregistrovaná ve službě Azure AD. Tato doménová struktura a registrace proxy jsou přidružené ke konkrétnímu tenantovi služby Azure AD, který implicitně identifikuje přihlašovací údaje, které se používají během registrace.

Služba AD DS doménová struktura a všechny nasazené proxy služby v rámci doménové struktury musí být zaregistrované u stejného tenanta. Není podporovaná služba AD DS doménová struktura nebo žádné proxy služby v této doménové struktuře, které se registrují v různých klientech Azure AD. Mezi příznaky takového nasazení, které je chybné, patří nemožnost stahovat zásady hesel.

> [!NOTE]
> Zákazníci, kteří mají více tenantů Azure AD, musí proto zvolit jednoho rozlišujícího tenanta pro registraci jednotlivých doménových struktur pro účely ochrany hesel Azure AD.

## <a name="download"></a>Stáhnout

Dvě požadované instalační programy agenta pro ochranu hesel Azure AD jsou k dispozici na [webu služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s používáním místní ochrany heslem služby Azure AD, proveďte následující kroky:

> [!div class="nextstepaction"]
> [Nasazení místní ochrany heslem Azure AD](howto-password-ban-bad-on-premises-deploy.md)
