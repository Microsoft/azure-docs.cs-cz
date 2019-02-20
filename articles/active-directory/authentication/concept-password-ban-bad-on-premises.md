---
title: Azure AD hesla protection ve verzi preview
description: Zákaz Slabá hesla do místní služby Active Directory pomocí služby Azure AD hesla protection ve verzi preview
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
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415744"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Verze Preview: Vynucení ochrany hesla Azure AD pro Windows Server Active Directory

|     |
| --- |
| Ochrana hesel Azure AD a v seznamu vlastních zakázaných hesel jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrana hesel Azure AD je nová funkce ve verzi public preview technologii Azure Active Directory (Azure AD) k vylepšení zásady hesel v organizaci. V místním nasazení ochrany hesla Azure AD používá obě globální a vlastní seznamy hesla uložená ve službě Azure AD je váš účet zakázaný a provede stejné kontroly místní změny cloudové služby Azure AD.

## <a name="design-principles"></a>Principy návrhu

Azure AD hesla ochranu pro službu Active Directory je navržen s těmito zásadami v úvahu:

* Řadiče domény se nikdy požadované k přímé komunikaci s Internetem
* Nejsou žádné nové síťové porty otevřené v řadičích domény.
* Nejsou potřeba žádné změny schématu služby Active Directory. Software používá existující kontejner služby Active Directory a objekty serviceConnectionPoint schématu.
* Žádná minimální domény služby Active Directory nebo doménové struktuře funkční úroveň (DFL\FFL) není povinné.
* Software nevytváří ani vyžadovat některé účty domén služby Active Directory, které chrání.
* Hesla uživatelů nešifrovaný text nikdy neopustí řadičem domény (zda během operace ověřování hesla nebo kdykoliv).
* Za cenu, že zásady pro hesla bude vynucováno tak, kde je nainstalovaný agent řadič domény je podporováno přírůstkové nasazení.
* Doporučuje se pro instalaci agenta řadiče domény na všech řadičích domény zajistit vynucení zabezpečení ochrany všudypřítomná hesla.

## <a name="architectural-diagram"></a>Diagram architektury

Je důležité porozumět základní návrhu a funkční koncepty před nasazením ochrany hesel služby Azure AD v prostředí místní služby Active Directory. Následující diagram znázorňuje, jak spolu součásti ochrany hesel služby Azure AD fungují:

![Jak spolupracují součásti ochrany hesla Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Výše uvedené diagram znázorňuje tři základní softwarové komponenty, které tvoří ochrana hesel Azure AD:

* Spouští službu Proxy ochrana hesel Azure AD na jakýkoli počítač připojený k doméně v aktuální doménové struktuře služby Active Directory. Jeho hlavním účelem je předat požadavků na stažení zásady hesla z řadiče domény do Azure AD a vrátí odpověď z Azure AD zpátky na řadič domény.
* Dll filtru hesel služby Azure AD ochranu řadič domény agenta heslo přijímá žádostí o ověření hesla uživatelů z operačního systému a předává je službě hesel služby Azure AD ochranu řadič domény agenta místně spuštěná na řadiči domény.
* Služba agenta ochrany DC hesel služby Azure AD obdrží žádostí o ověření hesla z knihovny dll filtru hesel agenta DC, zpracuje pomocí aktuální zásady hesel (místně dostupné) a vrátí výsledek (pass\fail).

## <a name="theory-of-operations"></a>Teorie operací

Takže uvedené výše diagramu a návrhu zásad, jak ochrana hesel Azure AD ve skutečnosti funguje?

Každá služba Proxy ochrana hesel Azure AD inzeruje na řadiče domény v doménové struktuře tak, že vytvoříte objekt serviceConnectionPoint ve službě Active Directory.

Každá služba agenta ochrany DC hesel služby Azure AD také vytvoří objekt serviceConnectionPoint ve službě Active Directory. To se ale používá primárně pro generování sestav a Diagnostika.

Služba agenta ochrany DC hesel služby Azure AD zodpovídá za inicializaci stáhnout nové zásady hesla ze služby Azure AD. Prvním krokem je najít službu Proxy ochrana hesel Azure AD pomocí dotazu na doménovou strukturu pro objekty serviceConnectionPoint proxy. Po nalezení služby k dispozici proxy žádost o stažení zásady hesla přijde z řadiče domény Služba agenta ve službě proxy, který pak odesílá do služby Azure AD a pak vrací odpověď služby agenta řadiče domény. Po přijetí nové zásady hesla ze služby Azure AD, ukládá služba agenta DC zásady vyhrazených složek v kořenovém adresáři sdílené složky jeho doméně adresáře sysvol. Služba agenta DC také sleduje tuto složku, v případě, že novější zásady replikace v jiných službách agenta řadiče domény v doméně.

Služba agenta ochrany DC hesel služby Azure AD se vždy požádat o novou zásadu na spuštění služby. Po spuštění služby agenta řadiče domény se pravidelně (jednou za hodinu) zkontrolujte stáří aktuální místně dostupné zásady Pokud aktuální zásady je starší než jednu hodinu službu agent řadič domény bude požadovat nové zásady ze služby Azure AD jak je popsáno výše, jinak agenta pro řadič domény bude dál používat aktuální zásady.

Služba agenta řadiče domény ochrana hesel Azure AD komunikuje se službou Proxy ochrana hesel Azure AD pomocí protokolu RPC (vzdálené volání procedur) přes protokol TCP. Službu Proxy naslouchá těchto volání na buď dynamickou nebo statickou RPC portu (protože je nakonfigurovaná).

Agenta ochrany DC hesel služby Azure AD nikdy naslouchá na síti dostupný port a službu Proxy nikdy pokusí volat službu agenta řadiče domény.

Služba Proxy ochrana hesel Azure AD je Bezstavová; nikdy mezipaměti zásad nebo jakéhokoli jiného stavu stáhli z Azure.

Služba agenta ochrany DC hesel služby Azure AD se vždy jen vyhodnotí jako heslo uživatele pomocí nejnovější zásady hesel místně dostupné. Pokud žádné zásady hesel je k dispozici na místní řadič domény, automaticky přijme heslo a zprávy do protokolu událostí se budou protokolovat do upozornit správce.

Azure AD hesla ochrany není modul aplikace v reálném čase zásad. Může existovat zpoždění v době mezi změnu hesla zásad konfigurace se provádí v Azure AD a čas dosáhne a vynucování na všechny řadiče domény.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Vazba Forest\tenant ochranu hesel služby Azure AD

Nasazení ochrany hesel služby Azure AD v doménové struktuře služby Active Directory vyžaduje registraci doménové struktury služby Active Directory a všech nasazených služeb Proxy ochrana hesel Azure AD, službou Azure AD. Obě tyto registrace (doménové struktury a proxy servery) jsou spojeny s konkrétním tenanta Azure AD, který je určený implicitně přes přihlašovací údaje použité během registrace. Kdykoli se stáhne heslo zásadu ochrany hesel služby Azure AD, je vždy specifický pro tohoto tenanta (to znamená, že zásady vždy být kombinací globální společnosti Microsoft zakázané heslo a seznamu vlastních zakázaných hesel na tenanta). Není možné konfigurovat různých doménách nebo proxy servery v doménové struktuře jako vázaný na různých služeb Azure AD tenantů.

## <a name="license-requirements"></a>Licenční požadavky

Výhody seznamu zakázaných hesel globální platí pro všechny uživatele služby Azure Active Directory (Azure AD).

Seznam zakázaných hesel vlastní vyžaduje licence Azure AD Basic.

Azure AD hesla Protection pro Windows Server Active Directory vyžaduje licence Azure AD Premium.

Další licenční informace, včetně nákladů, můžete najít na [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ke stažení

Dva vyžadované pro ochranu hesel služby Azure AD, který si můžete stáhnout z instalace agenta [stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Další postup

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
