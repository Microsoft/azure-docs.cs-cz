---
title: Řešení potíží s konfigurací služby předních dveří Azure
description: V tomto kurzu se naučíte, jak sami řešit některé běžné problémy, které se dají pro vaše přední dveře vytvářet.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184560"
---
# <a name="troubleshooting-common-routing-issues"></a>Řešení běžných potíží se směrováním
Tento článek popisuje, jak řešit některé běžné problémy s směrováním, které se můžou u konfigurace služby front-dveří pro Azure provést. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Název hostitele nesměruje do back-endu a vrátí stavový kód 400.


### <a name="symptom"></a>Příznak
- Vytvořili jste přední dveře, ale požadavek na hostitele front-end vrací stavový kód HTTP 400.

  - Vytvořili jste mapování DNS z vlastní domény na hostitele front-endu, kterého jste nakonfigurovali. Odesláním žádosti o název hostitele vlastní domény ale vrátí stavový kód HTTP 400 a nezobrazuje se směrování na back-endové servery, které jste nakonfigurovali.

### <a name="cause"></a>Příčina
- K tomuto problému může dojít, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, kterou jste přidali jako hostitele s front-endu. Pravidlo směrování se musí explicitně přidat pro tohoto hostitele front-endu, a to i v případě, že už je nakonfigurovaný pro hostitele front-end v rámci subdomény front-endu (*. azurefd.net), na kterou má vaše vlastní doména mapování DNS.

### <a name="troubleshooting-steps"></a>Postup řešení potíží
- Přidejte pravidlo směrování z vlastní domény do požadovaného back-end fondu.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Požadavek na název hostitele front-endu vrátí stavový kód 404.

### <a name="symptom"></a>Příznak
- Vytvořili jste přední dvířka a nakonfigurovali hostitele front-endu, back-end fond s alespoň jedním back-end serverem a pravidlo směrování, které připojuje hostitele front-end k back-endovému fondu. Váš obsah nemusí být k dispozici při odesílání žádosti do nakonfigurovaného hostitele front-endu, protože je vrácen stavový kód HTTP 404.

### <a name="cause"></a>Příčina
U tohoto příznaku existuje několik možných příčin:
 - Back-end není veřejným přístupem k back-endu a není viditelný pro službu front-dveří.

- Back-end je nesprávně nakonfigurovaný, což způsobuje, že služba front-endu odesílá nesprávný požadavek (to znamená, že back-end přijímá pouze protokol HTTP, ale nejste u něj nezkontrolovali povolení protokolu HTTPS, aby se při pokusu o přeposílání požadavků HTTPS předaly dveře.
- Back-end odmítá hlavičku hostitele předanou žádostí do back-endu.
- Konfigurace pro back-end ještě není plně nasazená.

### <a name="troubleshooting-steps"></a>Postup řešení potíží
1. Čas nasazení
    - Před nasazením konfigurace se ujistěte, že jste čekali přibližně 10 minut.

2. Kontrolovat nastavení back-endu
   - Přejděte do fondu back-end, do kterého se má žádost směrovat (závisí na tom, jak máte nakonfigurované pravidlo směrování), a ověřte, že _typ hostitele back-endu_ a název hostitele back-endu jsou správné. Pokud je back-end vlastním hostitelem, ujistěte se, že je správně napsaný. 

   - Ověřte porty HTTP a HTTPS. Ve většině případů jsou 80 a 443 (v uvedeném pořadí) správné a nevyžadují se žádné změny. Existuje však možnost, že back-end není nakonfigurován tímto způsobem a naslouchá na jiném portu.

     - Ověřte _hlavičku hostitele back-end_ nakonfigurovanou pro back-endy, do kterých se má hostitel front-endu směrovat. Ve většině případů by tato hlavička měla být stejná jako _název hostitele back-endu_. Nesprávná hodnota ale může způsobit různé stavové kódy 4xx HTTP, pokud back-end očekává něco jiného. Pokud zadáte IP adresu vašeho back-endu, možná budete muset nastavit _hlavičku hostitele back-end_ na název hostitele back-endu.


3. Ověřte nastavení pravidla směrování.
     - Přejděte do pravidla směrování, které by mělo směrovat z daného názvu hostitele front-endu do back-endového fondu. Zajistěte, aby byly přijaté protokoly správně nakonfigurované, nebo pokud ne, zajistěte, aby se při předávání žádosti správně nakonfigurovala Tato dvířka protokolu. _Přijaté protokoly_ určují, které požadavky musí přední dveře přijmout, a _protokol předávání_ určuje, jaké přední dveře protokolu mají použít k předání požadavku do back-endu.
          - Příklad: Pokud back-end akceptuje pouze požadavky HTTP, budou platné následující konfigurace:
               - _Přijaté protokoly_ jsou HTTP a HTTPS. _Protokol předávání_ je http. Požadavek shody nebude fungovat, protože HTTPS je povolený protokol, a pokud požadavek přišel jako HTTPS, přední dveře se pokusí o přeposlání pomocí protokolu HTTPS.

               - _Přijaté protokoly_ jsou http. _Protokol předávání_ buď odpovídá požadavku, nebo protokolu HTTPS.

   - _Přepsání adresy URL_ je ve výchozím nastavení zakázáno a toto pole byste měli použít pouze v případě, že chcete zúžit rozsah prostředků hostovaných hostovaným serverem, které mají být k dispozici. Pokud je tato akce zakázána, předá přední dveře stejnou cestu požadavku, kterou obdrží. Je možné, že toto pole je špatně nakonfigurované a přední dveře vyžadují prostředek z back-endu, který není k dispozici, a vrátí stavový kód HTTP 404.

