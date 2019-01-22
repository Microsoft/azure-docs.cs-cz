---
title: Azure AD hesla protection ve verzi preview
description: Zákaz Slabá hesla do místní služby Active Directory pomocí služby Azure AD hesla protection ve verzi preview
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 45c77aa63159a8e62f65c0e250c0ace95b0d344e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429901"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Verze Preview: Vynucení ochrany hesla Azure AD pro Windows Server Active Directory

|     |
| --- |
| Ochrana hesel Azure AD a v seznamu vlastních zakázaných hesel jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrana hesel Azure AD je nová funkce ve verzi public preview technologii Azure Active Directory (Azure AD) k vylepšení zásady hesel v organizaci. V místním nasazení ochrany hesla Azure AD používá obě globální a vlastní seznamy hesla uložená ve službě Azure AD je váš účet zakázaný a provede stejné kontroly místní změny cloudové služby Azure AD.

Existují tři softwarové komponenty, které tvoří ochrana hesel Azure AD:

* Proxy služby Azure AD hesla ochrany běží na jakýkoli počítač připojený k doméně v aktuální doménové struktuře služby Active Directory. Předává požadavky z řadičů domény do Azure AD a vrátí odpověď ze služby Azure AD zpátky na řadič domény.
* Agenta služby Azure AD hesla ochranu řadič domény obdrží žádostí o ověření hesla z knihovny dll filtru hesel agenta DC, je pomocí aktuální zásady hesel místně dostupné procesy a vrátí výsledek (pass\fail). Tato služba je zodpovědná za pravidelně (jednou za hodinu) volání proxy služby Azure AD hesla ochrany k načtení nové verze zásad hesel. Komunikace pro volání do a z proxy serveru služby Azure AD hesla ochrany probíhá přes RPC (vzdálené volání procedur) přes protokol TCP. Při načítání nové zásady se ukládají do složky sysvol, kde můžete replikovat do ostatních řadičů domény. Služba agenta DC také monitoruje složku sysvol na změny v případě, že jiné řadiče domény napsali nové zásady hesel, pokud vhodně nejnovější zásady už je k dispozici se přeskočí kontrolu proxy služby Azure AD hesla ochrany.
* Dll filtru hesel agenta pro řadič domény obdrží žádostí o ověření hesla z operačního systému a předává je Azure AD hesla řadiče domény Služba agenta ochrany místně spuštěná na řadiči domény.

![Jak spolupracují součásti ochrany hesla Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="license-requirements"></a>Licenční požadavky

Výhody seznamu zakázaných hesel globální platí pro všechny uživatele služby Azure Active Directory (Azure AD).

Seznam zakázaných hesel vlastní vyžaduje licence Azure AD Basic.

Ochrana hesel Azure AD pro Windows Server Active Directory vyžaduje licence Azure AD Premium.

Další licenční informace, včetně nákladů, můžete najít na [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Ke stažení

Existují dva instalační programy požadovaných k ochraně heslem služby Azure AD, který si můžete stáhnout z [stažení softwaru společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Odpovědi na nejčastější dotazy

* Bez připojení k Internetu vyžaduje z řadičů domény. Počítače proxy službou Azure AD hesla ochrany jsou jenom počítače, které vyžadují připojení k Internetu.
* Nejsou žádné síťové porty otevřené v řadičích domény.
* Nejsou potřeba žádné změny schématu služby Active Directory.
* Software používá existující kontejner služby Active Directory a objekty serviceConnectionPoint schématu.
* Neexistuje žádný minimální domény služby Active Directory nebo doménové struktuře funkční úrovni (DFL\FFL) požadavek.
* Software nevytváří ani vyžadovat některé účty domén služby Active Directory, které chrání.
* Za cenu, že zásady pro hesla bude vynucováno tak, kde je nainstalovaný agent řadič domény je podporováno přírůstkové nasazení.
* Doporučujeme nainstalovat agenta řadiče domény na všechny řadiče domény k zajištění ochrany vynucení hesla. 
* Ochrana hesel Azure AD není modul aplikace v reálném čase zásad. Může docházet k prodlevám v době mezi změna konfigurace zásad hesel a čas dosáhne a vynucování na všechny řadiče domény.

## <a name="next-steps"></a>Další postup

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
