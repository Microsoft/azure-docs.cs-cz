---
title: Nejčastější dotazy k Internet Analyzer | Microsoft Docs
description: Nejčastější dotazy k Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74184254"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Nejčastější dotazy k Azure Internet Analyzer (Preview)

Toto jsou nejčastější dotazy ke službě Azure Internet Analyzer – Pokud máte další dotazy, navštivte fórum pro [názory](https://aka.ms/internetAnalyzerFeedbackForum) a odešlete svůj dotaz. V případě častého dotazu přidáme Tento článek do tohoto článku, aby bylo možné ho rychle a snadno najít.

## <a name="how-do-i-participate-in-the-preview"></a>Návody se účastní ve verzi Preview?

K dispozici je verze Preview pro výběr zákazníků. Pokud vás zajímá připojení k verzi Preview, udělejte prosím toto:

1. Přihlaste se k [portálu Azure Portal](https://ms.portal.azure.com).
2. Přejděte na stránku **předplatná** .
3. Klikněte na předplatné Azure, u kterého plánujete používat Internet Analyzer.
4. Přejít na nastavení **poskytovatelé prostředků** pro předplatné.
5. Vyhledejte **Microsoft. Network** a klikněte na tlačítko **Registrovat** (nebo **znovu zaregistrovat**).
![žádost o přístup](./media/ia-faq/request-preview-access.png)

6. [Požádejte o schválení](https://aka.ms/internetAnalyzerContact) e-mailové adresy a ID předplatného Azure, které jste použili k vytvoření žádosti o přístup.
7. Po schválení vaší žádosti obdržíte e-mailem potvrzovací e-mail a budete moct vytvářet, aktualizovat a upravovat prostředky Internet Analyzer z nově povoleného předplatného Azure.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Potřebuji pro spuštění testu vložit klienta?

Ano, v aplikaci musí být nainstalován klient nástroje Internet Analyzer, aby bylo možné shromáždit metriky specifické pro vaše uživatele. [Přečtěte si, jak nainstalovat klienta.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Účtuje se mi účast ve verzi Preview?
Ne, Azure Internet Analyzer je zdarma používat ve verzi Preview. Během období Preview není k dispozici žádná smlouva o úrovni služeb.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>V jakých scénářích je Internet Analyzer navržený pro řešení?

Internet Analyzer je navržený tak, aby vám poskytl přehled o výkonu sítě v závislosti na naplnění vašeho uživatele. Aby bylo možné zajistit co nejlepší rozhodnutí o výkonu pro vaše uživatele, nástroj Internet Analyzer porovná výkon dvou koncových bodů Internetu pomocí různých naplnění uživatelů. I když může Internet Analyzer odpovědět na velké množství otázek, jsou některé z nejběžnějších:

* Jaký je dopad migrace na Cloud? 
    * *Navrhovaný test: vlastní (vaše aktuální místní infrastruktura) vs. Azure (libovolný nakonfigurovaný koncový bod)*
* Jaká je hodnota umístění dat na hranici vs. v datovém centru? 
    *  *Navrhovaný test: Azure vs. přední vrátka Azure, Azure vs. Azure CDN od Microsoftu*
* Jaké jsou výhody pro výkon front-end služby Azure?
    *  *Navrhovaný test: Custom/Azure/CDN vs. přední dvířka Azure*
* Jaké jsou výhody Azure CDN od Microsoftu? 
    *  *Navrhovaný test: Custom/Azure/AFD vs. Azure CDN od Microsoftu*
* Jak se Azure CDN ze služby Microsoft Stack? 
    *  *Navrhovaný test: vlastní (jiný koncový bod CDN) vs. Azure CDN od Microsoftu*
* Jaký je nejlepší Cloud pro naplnění vašich koncových uživatelů v jednotlivých oblastech? 
    *  *Navrhovaný test: vlastní (jiná cloudová služba) vs. Azure (libovolný nakonfigurovaný koncový bod)*

## <a name="which-tests-can-i-run-in-preview"></a>Které testy lze spustit ve verzi Preview?

Každý test, který vytvoříte v nástroji Internet Analyzer, se skládá ze dvou koncových bodů – koncový bod a a koncový bod B. Některé z následujících kombinací lze spustit jako testy:  
* Dva předem nakonfigurované koncové body,
* Jeden vlastní a jeden předem konfigurovaný koncový bod nebo
* Dva [vlastní koncové body](internet-analyzer-custom-endpoint.md) (jeden vlastní koncový bod se musí nacházet v Azure).

Během verze Preview jsou k dispozici následující předem nakonfigurované koncové body:
* **Oblasti Azure**
    * Brazil South
    * Indie – střed
    * Střední USA
    * Východní Asie
    * East US
    * Japonsko – západ
    * Severní Evropa
    * Jižní Afrika – sever
    * Southeast Asia
    * Spojené arabské emiráty sever
    * Spojené království – západ  
    * West Europe
    * USA – západ
    * Západní USA 2
* **Několik kombinací oblastí Azure**
    * Východní USA, Brazílie – jih
    * Východní USA Východní Asie
    * Západní Evropa, Brazílie – jih
    * Západní Evropa, jihovýchodní Asie
    * Západní Evropa, Spojené arabské emiráty sever
    * Západní USA Východní USA
    * Západní USA Západní Evropa
    * Západní USA, Spojené arabské emiráty sever
    * Západní Evropa, Spojené arabské emiráty sever, jihovýchodní Asie
    * Západní USA, Západní Evropa Východní Asie
    * Západní USA, Severní Evropa, jihovýchodní Asie, Spojené arabské emiráty sever, Jižní Afrika – sever 
* **Azure a přední dvířka** Azure – nasazené v jedné nebo několika kombinacích oblastí Azure, které jsou uvedené výše
* **Azure + Azure CDN od Microsoftu** nasazené na libovolnou kombinaci oblastí Azure uvedenou výše
* **Azure a azure Traffic Manager** – nasazené ve více kombinacích oblastí Azure uvedených výše

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Jak se Internet Analyzer liší od jiných monitorovacích služeb poskytovaných Azure?

Internet Analyzer vám pomůže pochopit výkon koncových uživatelů a pomáhat při rozhodování o vylepšení výkonu. I když další nástroje pro monitorování Azure poskytují přehled o vašich službách Azure, Internet Analyzer se zaměřuje na měření kompletního internetového výkonu pro vaše uživatele.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Jak nástroj Internet Analyzer zpracovává data měření?

Azure má [silné procesy zabezpečení a splňuje širokou škálu standardů dodržování předpisů](https://azure.microsoft.com/support/trust-center/). Pouze vy a váš určený tým mají přístup k vašim datům. Zaměstnanci Microsoftu můžou mít omezený přístup jenom za konkrétní omezené okolnosti se svým vědomím. Šifrované při přenosu a v klidovém stavu.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Internet Analyzer](internet-analyzer-overview.md).
