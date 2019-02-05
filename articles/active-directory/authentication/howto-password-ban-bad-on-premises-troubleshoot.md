---
title: Řešení potíží ve službě Azure AD hesla protection ve verzi preview
description: Principy Azure AD protection ve verzi preview běžné řešení potíží s heslem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 7474027368949d5ad2202881ac68096fac2b8bd2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693900"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Verze Preview: Řešení potíží Azure AD ochrana heslem

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po nasazení ochrany hesel služby Azure AD řešení potíží se může vyžadovat. Tento článek obsahuje podrobnosti vám pomohou pochopit některé běžné kroky při řešení potíží.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>Slabá hesla nejsou získávání odmítnuta, podle očekávání

To může mít několik možných příčin:

1. Vaše řadiče domény agentům ještě nestáhli zásadu. Příznaky tohoto objektu je 30001 události v protokolu událostí správce agenta řadiče domény.

    Možné příčiny tohoto problému patří:

    1. Doménová struktura ještě není zaregistrované.
    2. Proxy serveru ještě není zaregistrované.
    3. Problémy se síťovým připojením brání službě Proxy komunikaci s Azure (proxy server HTTP Zkontrolujte požadavky na)

2. Režim vynucení zásad hesel stále nastavena na Audit. Pokud je to tento případ, můžete ji překonfigurujte k vynucení ochrany hesel služby Azure AD na portálu. Podrobnosti najdete na [ochrana heslem povolit](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

3. Zásady hesel se zakázalo. Pokud je to tento případ, překonfigurujte ji povolit pomocí portálu pro ochranu hesel služby Azure AD. Podrobnosti najdete na [ochrana heslem povolit](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

4. Algoritmus pro ověření heslo může fungovat podle očekávání. Podrobnosti najdete na [jak se vyhodnocují hesla](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Režimu oprav adresářových služeb

Pokud řadič domény, který naběhne do režimu oprav adresářových služeb, službu agenta řadiče domény to zjistí a způsobí, že všechny ověření hesla nebo vynucení aktivity se deaktivuje, bez ohledu na aktuálně aktivní zásady konfigurace.

## <a name="emergency-remediation"></a>Nouzový nápravy

Pokud dojde k situaci, kdy služba agenta DC způsobuje problémy, službu agenta řadiče domény může okamžitě ukončena. Dll filtru hesel agenta DC stále pokusí volat službu bez spuštění a budou protokolovat události upozornění (10012, 10013), ale během této doby jsou přijímány příchozí všechna hesla. Služba agenta řadiče domény může také být nakonfigurována prostřednictvím Windows správce řízení služeb s typem spuštění "Zakázáno" podle potřeby.

Další míry nápravy může být nastavená na ne na portálu ochrany hesel služby Azure AD povolit režim. Po stažení aktualizované zásady, každá služba agenta DC začnou tichém režimu, kde jsou všechna hesla přijímány jako-je. Další informace najdete v tématu [režimu vynucení](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Snížení úrovně řadiče domény

Podporuje se degradujete řadič domény, na kterém běží pořád softwaru agenta řadiče domény. Správci by měli vědět ale, že software agenta DC i nadále vynucovat aktuální zásady hesel během postupu snížení úrovně. Nové heslo místního správce účtu (zadaná jako součást operace degradace) se ověří stejně jako jakékoli jiné heslo. Společnost Microsoft doporučuje zvolit zabezpečeného hesla pro místní účty správců jako součást postupu snížení úrovně řadiče domény; ověření nové heslo místního správce účtu agenta softwarem řadiče domény ale může být rušivé existující provozní postupy snížení úrovně.

Po degradování proběhla úspěšně, a po restartování řadiče domény a je znovu spuštěna jako normální členský server, k softwaru agenta řadiče domény se vrátí ke spouštění v pasivním režimu. Může být pak odinstalovali kdykoli.

## <a name="removal"></a>Odebrání

Pokud je se rozhodli odinstalovat software ve verzi public preview a vyčištění všech souvisejících stavu z domény a doménové struktury, můžete tento úkol provést pomocí následujících kroků:

> [!IMPORTANT]
> Je potřeba provést tyto kroky v pořadí. Pokud všechny instance služby serveru Proxy, zůstane spuštěn pravidelně znovu vytvoří jeho serviceConnectionPoint objekt. Pokud všechny instance služby agenta řadiče domény, zůstane spuštěn pravidelně znovu vytvoří jeho objekt serviceConnectionPoint a stav sysvol.

1. Proxy software odinstalujte ze všech počítačů. Tento krok provádí **není** vyžadují restartování.
2. Odinstalace softwaru agenta řadiče domény ze všech řadičů domény. Tento krok **vyžaduje** restartovat počítač.
3. Ručně odeberte všechny body připojení proxy server služby v každé doméně názvový kontext. Umístění tyto objekty mohou být zjištěny pomocí následujícího příkazu Powershellu pro Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Není vynechat hvězdičku ("*") na konci $keywords hodnotu proměnné.

   Výsledné objekty vyhledat přes `Get-ADObject` příkazu, můžete pak rourou do `Remove-ADObject`, nebo odstranit ručně. 

4. Ručně odeberte všechny body připojení agenta řadiče domény v každé doméně názvový kontext. Může jich být tyto objekty na řadič domény v doménové struktuře, v závislosti na tom, jak často byl nasazen software ve verzi public preview. Umístění tohoto objektu může být nalezeny pomocí následujícího příkazu Powershellu pro Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Výsledné objekty vyhledat přes `Get-ADObject` příkazu, můžete pak rourou do `Remove-ADObject`, nebo odstranit ručně.

   Není vynechat hvězdičku ("*") na konci $keywords hodnotu proměnné.

5. Ručně odeberte stavu konfigurace na úrovni doménové struktury. Stav konfigurace doménové struktuře se udržuje v kontejneru v názvovém kontextu konfigurace služby Active Directory. Lze zjistit a odstranit následujícím způsobem:

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ručně odeberte všechny adresáře sysvol související s stavu tím, že ručně odstranit následující složku a veškerý jeho obsah:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   V případě potřeby tuto cestu můžete také získat přístup na místně na daný řadič domény; Výchozí umístění by měl vypadat následující cestu:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Tato cesta se liší, pokud není nakonfigurovaná sdílená složka sysvol v jiné než výchozí umístění.

## <a name="next-steps"></a>Další postup

[Nejčastější dotazy ke službě ochrana hesel Azure AD](howto-password-ban-bad-on-premises-faq.md)

Další informace o seznamech globálních a vlastních zakázaných hesel, najdete v článku [zakázat chybná hesla](concept-password-ban-bad.md)
