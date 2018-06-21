---
title: Azure AD heslo protection verze preview
description: Zakázat slabý hesla v místní službě Active Directory pomocí protection verze preview služby Azure AD heslo
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 0819a947229e633331253923654de56638a6c76a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296110"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Ve verzi Preview: Vynuťte heslo ochranu Azure AD pro Windows Server Active Directory

|     |
| --- |
| Ochrana heslem Azure AD a seznamu zakázaných vlastního hesla jsou funkce verze public preview služby Azure Active Directory. Další informace o verze Preview najdete v tématu [doplňkové podmínky použití pro Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrana heslem Azure AD je nová funkce ve verzi public preview používá technologii služby Azure Active Directory (Azure AD) k vylepšení zásady pro hesla v organizaci. Místní nasazení služby Azure AD heslo ochrany používá i globální a vlastní zakázané heslo seznamy uložené ve službě Azure AD a provede stejnou kontroly místní jako změny cloudové služby Azure AD.

Existují tři softwarové komponenty, které tvoří ochrana heslem Azure AD:

* Službu Azure AD ochrany proxy se spouští z jakéhokoli počítače připojené k doméně v aktuální doménové struktuře služby Active Directory. Ho předá požadavky z řadičů domény do Azure AD a vrátí odpověď z Azure AD zpátky na řadič domény.
* Agent služby Azure AD heslo ochrany řadič domény přijímá žádosti o ověření hesla z knihovny dll filtru hesel řadiče domény agenta, je pomocí aktuálních zásad místně dostupné heslo zpracuje a vrátí výsledek (pass\fail). Tato služba je zodpovědná za pravidelně (jednou za hodinu) volání služby Azure AD heslo ochrany proxy načíst nové verze zásady hesel. Komunikace pro volání do a ze služby Azure AD ochrany heslo proxy se zpracovává prostřednictvím protokolu RPC (vzdálené volání procedur) přes protokol TCP. Při načítání nové zásady ukládají do složky sysvol, kde můžete replikovat do jiných řadičů domény. Služba agenta řadič domény také monitoruje složku sysvol na změny v případě, že ostatní řadiče domény mají zapisovat nové zásady hesel, pokud vhodně poslední zásad je již k dispozici kontrola proxy služby Azure AD heslo ochrany bude přeskočeno.
* Knihovny dll filtru hesel agenta řadič domény přijímá žádosti o ověření hesla z operačního systému a předává je ke službě Azure AD heslo ochrana řadič domény agenta musí být spuštěn místně na řadiči domény.

![Jak spolupracují součásti ochrany hesel Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Požadavky

* Všechny počítače, kde jsou nainstalovány součásti ochrany hesel Azure AD včetně řadičů domény musí být systém Windows Server 2012 nebo novějším.
* Připojení k síti musí existovat mezi aspoň jeden řadič domény v každé doméně a alespoň jeden server hostující službu Azure AD ochrany heslo proxy.
* Libovolné doméně Active Directory, který je spuštěn řadič domény software agenta služby musíte použít DFSR k replikování adresáře sysvol.
* Účet globálního správce zaregistrovat službu Azure AD ochrany proxy serveru se službou Azure AD.
* Účet s oprávněními správce domény služby Active Directory v kořenové doméně doménové struktury.

### <a name="license-requirements"></a>Licenční požadavky

Výhody seznamu globální zakázaného heslo platí pro všechny uživatele služby Azure Active Directory (Azure AD).

Seznam zakázaných vlastního hesla vyžaduje Azure AD Basic licence.

Heslo ochrany služby Azure AD pro Windows Server Active Directory vyžaduje licence Azure AD Premium. 

Další licenční informace, včetně náklady, naleznete na [Azure Active Directory ceny lokality](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ke stažení

Existují dvě požadované instalační programy pro ochranu heslem Azure AD, který si můžete stáhnout z [stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Odpovědi na nejčastější dotazy

* Žádné připojení k Internetu vyžaduje z řadičů domény. Počítač(e) proxy službou Azure AD heslo ochrany jsou jenom počítače, které vyžadují připojení k Internetu.
* Na řadičích domény jsou otevřené porty žádné sítě.
* Nejsou vyžadovány žádné změny schématu služby Active Directory.
   * Software používá existující kontejner služby Active Directory a objekty serviceConnectionPoint schématu.
* Neexistuje žádné minimální domény služby Active Directory nebo doménové struktury funkční úrovně (DFL\FFL) požadavek.
* Software nevytváří ani vyžadovat účtů v doménách služby Active Directory, které chrání.
* Přírůstkové nasazení je podporována u kompromis, že zásady hesel se vynucují jenom v kterých je nainstalován agent pro řadič domény.
* Ochrana heslem Azure AD není modul aplikace v reálném čase zásad. Doba mezi změnu hesla zásad konfigurace a času se dosáhne a vynucování na všechny řadiče domény může dojít ke zpoždění.

## <a name="next-steps"></a>Další postup

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises.md)