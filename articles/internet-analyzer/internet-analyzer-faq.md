---
title: Nejčastější dotazy k internetovému analyzátoru | Dokumenty společnosti Microsoft
description: Nejčastější dotazy k nástroji Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184254"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Nejčastější dotazy k analyzátoru Internetu Azure (preview)

Toto je nejčastější dotazy k Azure Internet Analyzer– pokud máte další otázky, přejděte na [fórum pro zpětnou vazbu](https://aka.ms/internetAnalyzerFeedbackForum) a pošlete svůj dotaz. Když je otázka často kladena, přidáme ji do tohoto článku, aby ji bylo možné rychle a snadno najít.

## <a name="how-do-i-participate-in-the-preview"></a>Jak se mohu zúčastnit náhledu?

Náhled je k dispozici pro vybrané zákazníky. Máte-li zájem o připojení k náhledu, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com).
2. Přejděte na stránku **Předplatná.**
3. Klikněte na předplatné Azure, se kterým chcete používat Internet Analyzer.
4. Přejděte do nastavení **zprostředkovatelů prostředků** pro předplatné.
5. Vyhledejte **soubor Microsoft.Network** a klikněte na tlačítko **Registrovat** (nebo **Znovu zaregistrovat).**
![žádost o přístup](./media/ia-faq/request-preview-access.png)

6. [Požádejte o schválení](https://aka.ms/internetAnalyzerContact) tím, že nám poskytnete svou e-mailovou adresu a ID předplatného Azure, které bylo použito k vytvoření žádosti o přístup.
7. Po schválení vaší žádosti obdržíte potvrzení e-mailem a budete moci vytvářet/aktualizovat/upravovat prostředky internetového analyzátoru z nově povoleného předplatného Azure.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Musím vložit klienta ke spuštění testu?

Ano, klient Internet Analyzer musí být nainstalován ve vaší aplikaci shromažďovat metriky specifické pro vaše uživatele. [Přečtěte si, jak nainstalovat klienta.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Účtují se mi za účast ve verzi Preview?
Ne, Azure Internet Analyzer je zdarma k použití ve verzi Preview. Neexistuje žádná smlouva o úrovni služeb během náhledu.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Jaké scénáře je internet analyzer určen k řešení?

Nástroj Internet Analyzer je navržen tak, aby vám poskytl přehledy o výkonu sítě na základě vaší populace uživatelů. Chcete-li pomoci učinit nejlepší rozhodnutí o výkonu pro uživatele, Nástroj Internet Analyzer porovnává výkon dvou koncových bodů Internetu pomocí odlišné populace uživatelů. Zatímco Internet Analyzer může odpovědět na velké množství otázek, některé z nejběžnějších jsou:

* Jaký dopad migrace do cloudu má na výkon migrace do cloudu? 
    * *Navrhovaný test: Vlastní (vaše aktuální místní infrastruktura) vs. Azure (jakýkoli předkonfigurovaný koncový bod)*
* Jaká je hodnota umístění mých dat na hraniční hodnotu vs. v datovém centru? 
    *  *Navrhovaný test: Azure vs. Azure Front Door, Azure vs. Azure CDN od Microsoftu*
* Jaké jsou výhody výkonu Azure Front Door?
    *  *Navrhovaný test: Vlastní/ Azure/ CDN vs. Přední dveře Azure*
* Jaké jsou výhody výkonu Azure CDN od Microsoftu? 
    *  *Navrhovaný test: Vlastní/ Azure/ AFD vs Azure CDN od Microsoftu*
* Jak azure cdn od Microsoftu zásobníku nahoru? 
    *  *Navrhovaný test: Vlastní (jiný koncový bod CDN) vs. Azure CDN od Microsoftu*
* Jaký je nejlepší cloud pro vaši populaci koncových uživatelů v jednotlivých oblastech? 
    *  *Navrhovaný test: Vlastní (jiná cloudová služba) vs. Azure (jakýkoli předkonfigurovaný koncový bod)*

## <a name="which-tests-can-i-run-in-preview"></a>Jaké testy lze spustit ve verzi Preview?

Každý test, který vytvoříte v nástroji Internet Analyzer, se skládá ze dvou koncových bodů – koncového bodu A a koncového bodu B. Jako testy lze spustit libovolnou z následujících kombinací:  
* Dva předkonfigurované koncové body,
* Jeden vlastní a jeden předkonfigurovaný koncový bod nebo
* Dva [vlastní koncové body](internet-analyzer-custom-endpoint.md) (jeden vlastní koncový bod musí být umístěn v Azure).

Během náhledu jsou k dispozici následující předkonfigurované koncové body:
* **Oblast Azure**
    * Brazílie – jih
    * Indie – střed
    * USA – střed
    * Východní Asie
    * USA – východ
    * Japonsko – západ
    * Severní Evropa
    * Jižní Afrika – sever
    * Jihovýchodní Asie
    * SAE Sever
    * Spojené království – západ  
    * Západní Evropa
    * USA – západ
    * USA – západ 2
* **Více kombinací oblastí Azure**
    * Východní USA, Brazílie – jih
    * Východní USA, Východní Asie
    * Západní Evropa, Brazílie – jih
    * Západní Evropa, Jihovýchodní Asie
    * Západní Evropa, SAE Sever
    * Západní USA, Východní USA
    * Západní USA, Západní Evropa
    * Západní USA, SAE Sever
    * Západní Evropa, SAE Sever, Jihovýchodní Asie
    * Západní USA, Západní Evropa, Východní Asie
    * Západní USA, Severní Evropa, Jihovýchodní Asie, SAE – sever, Jižní Afrika – sever 
* **Azure + Azure Front Door** – nasazené na libovolné kombinaci oblasti Azure, které jsou uvedeny výše
* **Azure + Azure CDN od Microsoftu** – nasazené na libovolné kombinaci oblasti Azure uvedené výše
* **Azure + Azure Traffic Manager** – nasazený na libovolné kombinaci oblastí Azure, která je uvedena výše

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Jak se internetový analyzátor liší od ostatních monitorovacích služeb poskytovaných Azure?

Nástroj Internet Analyzer vám pomůže porozumět výkonu koncových uživatelů a pomůže při rozhodování o zlepšení jejich výkonu. Zatímco jiné nástroje pro monitorování Azure poskytují přehled o vašich službách Azure, Internet Analyzer se zaměřuje na měření výkonu internetu od konce pro vaše uživatele.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Jak naměřená data zpracovává internetový analyzátor?

Azure má [silné procesy zabezpečení a splňuje širokou škálu standardů dodržování předpisů](https://azure.microsoft.com/support/trust-center/). Pouze vy a váš určený tým máte přístup k vašim údajům. Pracovníci společnosti Microsoft mohou mít omezený přístup k němu pouze za určitých omezených okolností s vašimi znalostmi. Je to zašifrované při přepravě a v klidu.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v našem [přehledu nástroje Internet Analyzer](internet-analyzer-overview.md).
