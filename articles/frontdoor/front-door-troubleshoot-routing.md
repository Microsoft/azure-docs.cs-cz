---
title: Řešení potíží – řešení potíží s konfigurací branou služby Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak k samoobslužné řešení běžných problémů, které mohou nastat pro vaše branou.
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
ms.openlocfilehash: 78370b004d18f70ae4d485f3ad7cfd910e6dd70a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047294"
---
# <a name="troubleshooting-common-routing-issues"></a>Řešení běžných potíží směrování
Tento článek popisuje, jak řešení běžných problémů směrování, které mohou nastat pro vaši konfiguraci služby Azure branou. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Název hostitele není směrování do back-end a vrátí stavový kód 400


### <a name="symptom"></a>Příznak
- Přední dveře jste vytvořili, ale požadavek na hostitele front-endu vrací stavový kód HTTP 400.

 - Jste vytvořili DNS mapování z vlastní domény pro hostování front-endu jste nakonfigurovali. Ale odesílá požadavek na název hostitele vlastní domény vrátí stavový kód HTTP 400 a zřejmě směrovat backend(s) nakonfigurovali.

### <a name="cause"></a>Příčina
- K tomuto problému může dojít, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, ke které jste přidali jako hostitele front-endu. Pravidlo směrování musí být explicitně přidán pro tohoto hostitele front-endu i v případě, že již byla nakonfigurována pro hostitele front-endu v rámci branou subdoménu (*. azurefd.net), vaše vlastní doména má mapování DNS.

### <a name="troubleshooting-steps"></a>Řešení potíží
- Přidáte pravidlo směrování z vlastní domény do požadované back-endový fond.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Požadavek na názvu hostitele front-endu stavový kód 404 vrátí

### <a name="symptom"></a>Příznak
- Vytvoření branou a nakonfigurovat hostitele front-endu, back-endový fond s aspoň jeden back-endem v něm a pravidel směrování, která se připojuje hostitele front-endu do back-endový fond. Váš obsah se nezdá být k dispozici při odesílání požadavku do hostitele nakonfigurovaného front-endu, protože se vrátí stavový kód HTTP 404.

### <a name="cause"></a>Příčina
Existuje několik možných příčin pro tento příznak:
 - Back-end není veřejný internetový back-endu a není viditelný pro službu branou.

- Back-end není správně nakonfigurovaný, který je příčinou branou služby umožňující odesílání chybného požadavku (to znamená, back-endu přijímá jenom HTTP, ale ne není zaškrtnuto, což požadavky HTTPS, takže branou se pokouší předávat HTTPS).
- Back-endu zamítá hlavičku hostitele, která byla předána s požadavkem na back-end.
- Konfigurace back-endu nebyla ještě nasazena plně.

### <a name="troubleshooting-steps"></a>Řešení potíží
1. Čas nasazení
    - Ujistěte se, že mají čekat přibližně 10 minut, než se konfigurace mají být nasazeny.

2. Zkontrolujte nastavení back-endu
     - Přejděte do back-endový fond, který požadavek by měl směrování (závisí na tom, jak máte nakonfigurované pravidlo směrování) a ověřte, zda _typ hostitele back-endu_ a název hostitele back-end jsou správné. Pokud back-end je vlastního hostitele, ujistěte se, že jste zadali správně. 

     - Zkontrolujte vaše portech HTTP a HTTPS. Ve většině případů 80 a 443 (v uvedeném pořadí), jsou správné a se nevyžaduje žádné změny. Ale je pravděpodobné, že back-endu není nakonfigurována tímto způsobem a naslouchá na jiném portu.

    - Zkontrolujte _hlavičku hostitele back-endu_ nakonfigurovaný pro back-EndY, který by měl směrování hostitele front-endu. Ve většině případů toto záhlaví by měl být stejný jako _název hostitele back-endu_. Nesprávná hodnota však může způsobit různé stavové kódy HTTP 4xx, pokud back-end očekává, že něco jiného. Pokud zadáte IP adresu vaší back-end, je nutné nastavit _hlavičku hostitele back-endu_ na název hostitele z back-endu.


3. Zkontrolujte nastavení pravidla směrování
     - Přejděte na pravidlo směrování, který by měl směrovat z dotyčný názvu hostitele front-endu do back-endový fond. Ujistěte se, že jsou správně nakonfigurované přijaté protokoly nebo pokud ne, ujistěte se, že je správně nakonfigurovaný protokol, který branou bude používat při předání požadavku. _Přijato protokoly_ Určuje, který požaduje branou by měla přijímat a _předávání protokolu_ pod _Upřesnit_ kartu Určuje, který protokol branou používejte k předání požadavku do back-endu.
          - Například pokud back-endu přijímá pouze požadavky HTTP následující konfigurace by platné:
               - _Přijata protokoly_ jsou HTTP a HTTPS. _Předávání protokolu_ je HTTP. Shoda požadavek nebude fungovat, protože je povolený protokol HTTPS a pokud se žádost o byli zaznamenáni v jako protokol HTTPS, branou mistranslation: předat dál pomocí protokolu HTTPS.

               - _Přijata protokoly_ protokol HTTP. _Předávání protokolu_ je buď odpovídá požadavku nebo HTTPS.

   - Klikněte na _Upřesnit_ kartě v horní části podokna směrování konfigurační pravidla. _Přepisování adres URL_ je ve výchozím nastavení zakázán a toto pole by měly používat jenom Pokud chcete-li zúžit rozsah back-end hostovaný prostředky, které mají být dostupné. Pokud je zakázán, předá branou stejnou cestu požadavku, které obdrží. Je možné, že je špatně nakonfigurovaný. Toto pole a branou žádá o prostředku z back-endu, který není k dispozici, proto vrátí stavový kód HTTP 404.

