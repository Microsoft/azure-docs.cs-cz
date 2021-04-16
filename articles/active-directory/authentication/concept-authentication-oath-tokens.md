---
title: Metoda ověřování tokenů OATH – Azure Active Directory
description: Přečtěte si, jak používat tokeny OATH v Azure Active Directory k lepšímu zlepšení a zabezpečení přihlašovacích událostí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d0dd081e3e1a681ba55e3457b79a548d6b2bb7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530387"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Metody ověřování ve Azure Active Directory – tokeny OATH 

OATH TOTP (jednorázové heslo založené na čase) je otevřený standard, který určuje, jak se generují kódy jednorázového hesla (jednorázového hesla). TOTP OATH lze implementovat pomocí softwaru nebo hardwaru pro generování kódů. Azure AD nepodporuje HOTP OATH, což je jiný standard pro generování kódu.

## <a name="oath-software-tokens"></a>Tokeny softwaru OATH

Tokeny OATH softwaru jsou obvykle aplikace, jako je Microsoft Authenticator aplikace a další ověřovací aplikace. Azure AD vygeneruje tajný klíč neboli počáteční hodnotu, která je vstupem do aplikace a používá se ke generování každého jednorázového hesla.

Aplikace ověřovatele automaticky generuje kódy při nastavení nabízených oznámení, takže uživatel bude mít zálohu, i když jejich zařízení nemá připojení. Je také možné použít aplikace třetích stran, které používají OATH TOTP k vytváření kódů.

Některé hardwarové tokeny OATH TOTP jsou programovatelné, což znamená, že nepocházejí s tajným klíčem nebo předinstalovaným předplatným. Tyto programovatelné hardwarové tokeny je možné nastavit pomocí tajného klíče nebo počáteční hodnoty získané z toku nastavení softwarového tokenu. Zákazníci si můžou tyto tokeny koupit od dodavatele podle svého výběru a použít tajný klíč nebo osivo v procesu instalace jeho dodavatele.

## <a name="oath-hardware-tokens-preview"></a>Hardwarové tokeny OATH (Preview)

Azure AD podporuje použití tokenů SHA-1 TOTP OATH, které aktualizují kódy každých 30 nebo 60 sekund. Zákazníci si můžou tyto tokeny koupit od dodavatele dle svého výběru.

Tokeny OATH TOTP jsou obvykle dodávány s tajným klíčem neboli osivem předem naprogramované v tokenu. Tyto klíče musí být zadané ve službě Azure AD, jak je popsáno v následujících krocích. Tajné klíče jsou omezené na 128 znaků, které nemusí být kompatibilní se všemi tokeny. Tajný klíč může obsahovat pouze znaky *a-z* nebo *a-z* a číslice *2-7* a musí být kódovány v *Base32*.

Programovatelné hardwarové tokeny OATH TOTP, které je možné znovu vyhodnotit, můžete nastavit pomocí Azure AD v toku nastavení softwarového tokenu.

Hardwarové tokeny OATH jsou podporovány v rámci verze Public Preview. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

![Nahrávají se tokeny OATH do okna tokeny OATH MFA.](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po získání tokenů musí být nahrané ve formátu textového souboru s oddělovači (CSV), který obsahuje hlavní název uživatele (UPN), sériové číslo, tajný klíč, časový interval, výrobce a model, jak je znázorněno v následujícím příkladu:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Ujistěte se, že jste do souboru CSV zahrnuli řádek záhlaví. Pokud hlavní název uživatele (UPN) obsahuje jedinou uvozovku, vydejte ho s jinou jednoduchou uvozovkou. Pokud například hlavní název uživatele (UPN) má hodnotu My user@domain.com , změňte ho na My user@domain.com při nahrávání souboru.

Po správném formátování jako souboru CSV se může globální správce přihlásit k Azure Portal, přejít na **Azure Active Directory > zabezpečení > MFA > tokeny Oath** a nahrajte výsledný soubor CSV.

V závislosti na velikosti souboru CSV může zpracování trvat několik minut. Kliknutím na tlačítko **aktualizovat** zobrazíte aktuální stav. Pokud v souboru dojde k chybám, můžete si stáhnout soubor CSV se seznamem případných chyb, které můžete vyřešit. Názvy polí ve staženém souboru CSV se liší od nahrané verze.  

Až budou všechny chyby vyřešené, může správce aktivovat každý klíč tak, že vybere možnost **aktivovat** pro token a vstoupí do jednorázového hesla zobrazeného na tokenu. Každých 5 minut můžete aktivovat maximálně 200 tokenů OATH. 

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator, nakonfigurovaná pro použití kdykoli. Hardwarové tokeny OATH nelze přiřadit k uživatelům typu Host v tenantovi poskytujícího prostředky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o konfiguraci metod ověřování pomocí [REST API Microsoft Graph](/graph/api/resources/authenticationmethods-overview).
