---
title: Řešení potíží ve službě Azure AD hesla ochrana – Azure Active Directory
description: Principy Azure AD ochrany běžné řešení potíží s heslem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51f214688aa1e33bd58e8460baab75228d7c5d1a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317234"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Řešení potíží Azure AD ochrana heslem

Po nasazení ochrany hesel služby Azure AD řešení potíží se může vyžadovat. Tento článek obsahuje podrobnosti vám pomohou pochopit některé běžné kroky při řešení potíží.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agenta pro řadič domény nemůže najít proxy server v adresáři

Hlavním příznakem tento problém je 30017 události v protokolu událostí správce agenta řadiče domény.

Obvyklou příčinou tohoto problému je, že proxy serveru ještě není zaregistrovaný. Pokud byl zaregistrován proxy server, můžou existovat některých zpoždění kvůli latenci replikace AD dokud nebude moci zobrazit tento proxy agenta konkrétní řadič domény.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Řadič domény agent není schopen komunikovat s proxy server

Hlavním příznakem tento problém je 30018 události v protokolu událostí správce agenta řadiče domény. To může mít několik možných příčin:

1. Agenta pro řadič domény se nachází v izolované části sítě, síťové připojení k registrované proxy(s) nepovoluje. Tento problém může proto být expected\benign tak dlouho, dokud jiní agenti pro řadič domény může komunikovat s proxy(s), aby mohl stáhnout zásady pro hesla z Azure, které budou získávat izolované řadič domény prostřednictvím replikace zásad souborů ve sdílené složce sysvol.

1. Počítači hostitele proxy serveru blokuje přístup ke koncovému bodu mapovač koncových bodů protokolu RPC (port 135)

   Instalační služba Proxy ochrana hesel Azure AD automaticky vytvoří příchozí pravidlo brány Windows Firewall, která umožňuje přístup k portu 135. Pokud toto pravidlo je později odstranit nebo zakázán, bude schopen komunikovat se službou Proxy agentů DC. Pokud builtin brány Windows Firewall zakázaná namísto jiném produktu pro brány firewall, je nutné nakonfigurovat této brány firewall umožňující přístup k portu 135.

1. Počítači hostitele proxy serveru blokuje přístup ke koncovému bodu vzdáleného volání Procedur (dynamické nebo statické) naslouchali na službou Proxy

   Instalační program Proxy ochrana hesel Azure AD automaticky vytvoří bránu Windows Firewall příchozí pravidlo, které umožňuje přístup k žádné příchozí porty naslouchali službou Proxy ochrana hesel Azure AD. Pokud toto pravidlo je později odstranit nebo zakázán, bude schopen komunikovat se službou Proxy agentů DC. Pokud builtin brány Windows Firewall zakázaná namísto jiném produktu pro brány firewall, je nutné nakonfigurovat, aby umožňovala přístup k žádné příchozí porty naslouchali službou Proxy ochrana hesel Azure AD. Tato konfigurace se dají vytvořit konkrétnější Pokud službu proxy server nakonfigurovaný tak, aby naslouchala na konkrétním statický port vzdáleného volání Procedur (pomocí `Set-AzureADPasswordProtectionProxyConfiguration` rutiny).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Proxy služby můžou přijímat volání z agentů řadiče domény v doméně, ale nemůže komunikovat s Azure

Zkontrolujte připojení ke koncovým bodům uvedené v má počítač serveru proxy [požadavky na nasazení](howto-password-ban-bad-on-premises-deploy.md).

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>Agent řadiče domény se nemůže šifrovat nebo dešifrovat soubory zásad hesel a dalších stavu

Tento problém můžete manifest nastavení s širokou škálu příznaky, ale obvykle má původní příčiny běžných.

Azure AD hesla ochrany má kriticky závislé na funkci šifrování a dešifrování, získáte ho od služby distribuce klíčů Microsoft, který je dostupný na řadičích domény s Windows serverem 2012 a novější. Služby KDS musí být povolené a funkční na všech systému Windows Server 2012 a novější řadiče domény v doméně.

Ve výchozím nastavení KDS režim spouštění služby služby nastavená na ruční (aktivační událost spuštění). Tato konfigurace znamená, že klient se pokusí použít službu, při prvním spuštění na vyžádání. Tento režim spouštění služby výchozí je přijatelné pro ochranu hesel služby Azure AD pro práci.

Pokud je režim spuštění služby KDS není nakonfigurovaná na hodnotu zakázáno, tato konfigurace musí nejdřív opravit ochrana hesel Azure AD bude fungovat správně.

Jednoduchý test pro tento problém je ohledně ručního spuštění služby KDS, buď přes konzolu MMC služby pro správu, nebo pomocí jiné nástroje pro správu služby (například spuštění, "net start kdssvc" z konzoly příkazového řádku). Služby KDS očekává se úspěšně spustit a zůstanou spuštěné.

Nejběžnější příčina služby KDS se nepovedlo spustit je, že objektu řadiče domény služby Active Directory se nachází mimo výchozí organizační jednotce řadiče domény. Tato konfigurace není podporována službou KDS a není omezení mezijazyka ochrana hesel Azure AD. Oprava této podmínky je přesunout do umístění ve výchozí organizační jednotce řadiče domény objektu řadiče domény.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Slabá hesla procházejí procesem přijímání. ale neměly by být

Tento problém může mít několik příčin.

1. Vaši agenti řadič domény nemůže stáhnout zásady nebo nelze dešifrovat existující zásady. Zkontrolujte možné příčiny v výše uvedených tématech.

1. Režim vynucení zásad hesel stále nastavena na Audit. Pokud tato konfigurace je v platnosti, můžete ji překonfigurujte k vynucení ochrany hesel služby Azure AD na portálu. Zobrazit [ochrana heslem povolit](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Zásady hesel se zakázalo. Pokud tuto konfiguraci, překonfigurujte ho povolit pomocí portálu pro ochranu hesel služby Azure AD. Zobrazit [ochrana heslem povolit](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Jste dosud nenainstalovali softwaru agenta řadiče domény na všechny řadiče domény v doméně. V takovém případě je obtížné zajistit, že vzdálených klientů Windows cílit na konkrétní řadič domény během operace změny hesla. Pokud se domníváte, úspěšně jste zaměřili konkrétní řadič domény, kde je nainstalovaný software agenta řadiče domény, můžete ověřit tak, že dvojité kontroly protokolu událostí správce agenta řadiče domény: bez ohledu na výsledek, bude existovat alespoň jednu událost dokumentu výsledek heslo ověření. Pokud není žádná událost, která je k dispozici pro uživatele, jejichž heslo se změnilo, potom změnu hesla pravděpodobně zpracování jiný řadič domény.

   Jako alternativní test zkuste setting\changing hesla, zatímco jste přihlášeni přímo na řadiče domény, kde je nainstalovaný software agenta řadiče domény. Tato technika se nedoporučuje pro produkční domén služby Active Directory.

   I když se tato omezení v souladu s podporuje přírůstkové nasazení softwaru agenta řadiče domény, společnost Microsoft důrazně doporučuje, zda na všechny řadiče domény v doméně se co nejdříve je nainstalován software agenta řadiče domény.

1. Algoritmus pro ověření heslo může skutečně funguje podle očekávání. Zobrazit [jak se vyhodnocují hesla](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Režimu oprav adresářových služeb

Pokud řadič domény, který naběhne do režimu oprav adresářových služeb, službu agenta pro řadič domény rozpozná tuto podmínku a způsobí, že všechny ověření hesla nebo vynucení aktivity se deaktivuje, bez ohledu na aktuálně aktivní zásady konfigurace.

## <a name="emergency-remediation"></a>Nouzový nápravy

Pokud dojde k situaci, kdy služba agenta DC způsobuje problémy, službu agenta řadiče domény může okamžitě ukončena. Dll filtru hesel agenta DC stále pokusí volat službu bez spuštění a budou protokolovat události upozornění (10012, 10013), ale během této doby jsou přijímány příchozí všechna hesla. Služba agenta řadiče domény může také být nakonfigurována prostřednictvím Windows správce řízení služeb s typem spuštění "Zakázáno" podle potřeby.

Další míry nápravy může být nastavená na ne na portálu ochrany hesel služby Azure AD povolit režim. Po stažení aktualizované zásady, každá služba agenta DC začnou tichém režimu, kde jsou všechna hesla přijímány jako-je. Další informace najdete v tématu [režimu vynucení](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Snížení úrovně řadiče domény

Podporuje se degradujete řadič domény, na kterém běží pořád softwaru agenta řadiče domény. Správci by měli vědět ale, že software agenta DC i nadále vynucovat aktuální zásady hesel během postupu snížení úrovně. Nové heslo místního správce účtu (zadaná jako součást operace degradace) se ověří stejně jako jakékoli jiné heslo. Společnost Microsoft doporučuje zvolit zabezpečeného hesla pro místní účty správců jako součást postupu snížení úrovně řadiče domény; ověření nové heslo místního správce účtu agenta softwarem řadiče domény ale může být rušivé existující provozní postupy snížení úrovně.

Po degradování proběhla úspěšně, a po restartování řadiče domény a je znovu spuštěna jako normální členský server, k softwaru agenta řadiče domény se vrátí ke spouštění v pasivním režimu. Může být pak odinstalovali kdykoli.

## <a name="removal"></a>Odebrání

Pokud je se rozhodli odinstalovat software ochrany hesla Azure AD a vyčištění všech souvisejících stavu z domény a doménové struktury, můžete tento úkol provést pomocí následujících kroků:

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

4. Ručně odeberte všechny body připojení agenta řadiče domény v každé doméně názvový kontext. Může jich být tyto objekty na řadič domény v doménové struktuře, v závislosti na tom, jak často byl nasazen software. Umístění tohoto objektu může být nalezeny pomocí následujícího příkazu Powershellu pro Active Directory:

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
