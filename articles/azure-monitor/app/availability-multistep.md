---
title: Monitorování webové aplikace s vícekrokovými webovými testy a Azure Application Insights | Dokumentace Microsoftu
description: Nastavení vícekrokové webové testy ke sledování webových aplikací pomocí Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304976"
---
# <a name="multi-step-web-tests"></a>Vícekrokové webové testy

Můžete monitorovat zaznamenané posloupnost adres URL a interakce s webem prostřednictvím vícekrokovými webovými testy. Tento článek vás provede procesem vytvoření vícekrokový webový test Visual Studio Enterprise.

> [!NOTE]
> Vícekrokové webové testy mají další náklady spojené s nimi. Získat další informace [oficiální příručce cenové](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="pre-requisites"></a>Požadavky

* Visual Studio 2017 Enterprise nebo novější.
* Visual Studio webového výkonu a zátěžové testování nástroje.

Vyhledejte testovací nástroje nezbytné. Spusťte **instalační program sady Visual Studio** > **jednotlivé komponenty** > **ladění a testování**  >   **Výkonnosti webů a zátěžové testování nástroje**.

![Snímek obrazovky sady Visual Studio Instalační program uživatelského rozhraní pomocí jednotlivých komponent vybrané s zaškrtávací políčko vedle položky výkonnosti webu a zátěžového testování nástroje](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>Záznam vícekrokový webový test

Pro vytvoření vícekrokového testu uložte scénář pomocí sady Visual Studio Enterprise a pak nahrajte tento záznam do služby Application Insights. Application Insights přehrává scénář v nastavených intervalech a ověřuje odezvy.

> [!IMPORTANT]
> * V testech nelze použít programové funkce nebo smyčky. Test musí být zcela obsažený ve skriptu .webtest. Můžete však použít standardní moduly plug-in.
> * Vícekrokové webové testy podporují pouze anglické znaky. Pokud používáte sadu Visual Studio v jiném jazyce, aktualizujte definiční soubor webového testu pro překlad/vyloučení jiných než anglických znaků.

Slouží k zaznamenání relace webové aplikace Visual Studio Enterprise.

1. Vytvořte webový výkon a projekt zátěžového testu. **File** > **New** > **Project** > **Visual C#**  > **Test**

    ![Visual Studio nový projekt uživatelského rozhraní](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Otevřít `.webtest` soubor a spusťte záznam.

    ![Visual Studio test záznam uživatelského rozhraní](./media/availability-multistep/open-web-test.png)

3. Projděte jednotlivými kroky, chcete, aby váš test pro simulaci jako součást záznam.

    ![Prohlížeč záznam uživatelského rozhraní](./media/availability-multistep/record.png)

4. Upravte test na:

    * Přidání ověření ke kontrole přijatého textu a kódů odpovědi.
    * Odeberte všechny nežádoucích interakce. Může také odebrat závislé požadavky pro obrázky nebo přidat sledování lokalit, které nejsou relevantní pro vás vzhledem k tomu test úspěšný.
    
    Mějte na paměti, že lze upravit pouze zkušební skript – můžete přidat vlastní kód nebo volat jiné webové testy. Nevkládejte do testu smyčky. Můžete použít standardní zásuvné moduly webového testu.

5. Spuštění testu v sadě Visual Studio k ověření a ujistěte se, že funguje.

    Spouštěč webových testů otevře webový prohlížeč a zopakuje zaznamenané akce. Zajistěte, aby že všechno, co se chová podle očekávání.

## <a name="upload-the-web-test"></a>Nahrajte test webu

1. Na portálu služby Application Insights v podokně dostupnost vyberte **vytvořit Test** > **typ testu** > **vícekrokový webový test**.

2. Nastavte umístění testu, četnost a parametry výstrah.

### <a name="frequency--location"></a>Frekvence & umístění

|Nastavení| Vysvětlení
|----|----|----|
|**Frekvence testování**| Nastaví, jak často test spustí z umístění každého testu. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Místa testování**| Jsou místa, ze kterých naše servery odesílají webové požadavky na adresu URL. **Naše minimální počet umístění doporučené testu je pět** aby bylo možné zajistit, že možné rozlišit problémy ve vašem webu od problémů se sítí. Můžete vybrat až 16 umístění.

### <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový limit testu** |Zmenšete tuto hodnotu pro upozornění pomalé odezvy. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený kód stavu, který se počítá jako úspěšný. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, například "Vítejte!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **Shody obsahu podporují pouze anglické znaky** |

### <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Near-realtime (Preview)** | Doporučujeme použít v téměř reálném čase výstrahy. Po vytvoření testu dostupnosti se provádí konfigurace tohoto typu výstrahy.  |
|**Classic** | Už se nedoporučuje používat klasických upozornění pro nové testy dostupnosti.|
|**Mezní hodnota umístění upozornění**|Doporučujeme aspoň 3 nebo 5 umístění. Optimální vztah mezi mezní hodnota umístění upozornění a počet umístění testu je **mezní hodnota umístění upozornění** = **počet umístění testu - 2, přičemž minimum pěti testovanými místy.**|

## <a name="advanced-configuration"></a>Pokročilá konfigurace

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Připojením čas a náhodná čísla do testu

Předpokládejme, že testujete nástroj, který získá data závislá na čase, například akcie z externího kanálu. Při záznamu webového testu je nutné použít konkrétní časy, ale nastavit je jako parametry testu, čas spuštění a čas ukončení.

![Moje aplikace akcie snímek obrazovky](./media/availability-multistep/app-insights-72webtest-parameters.png)

Při spuštění testu chcete, aby čas ukončení vždy představoval aktuální čas a čas spuštění by měl začínat před 15 minutami.

Čas Plugin webového testu datum poskytuje způsob, jak zpracovat Parametrizace časů.

1. Přidejte zásuvný modul webového testu pro každou hodnotu parametru proměnné, kterou chcete. V panelu nástrojů webového testu zvolte **Přidat zásuvný modul pro testování webu**.
    
    ![Přidat modul webového testu](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    V tomto příkladu používáme dvě instance zásuvného modulu Datum čas. Jedna instance je „před 15 minutami“ a druhá „teď“.

2. Otevřete vlastnosti každého zásuvného modulu. Pojmenujte ho a nastavte ho na použití aktuálního času. Pro jeden z nich nastavte Přidat minuty = -15.

    ![Kontextové parametry](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. V parametrech webového testu použijte {{plug-in name}} pro odkazování na název zásuvného modulu.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Teď nahrajte svůj test na portál. Při každé spuštění testu se budou používat dynamické hodnoty.

### <a name="dealing-with-sign-in"></a>Vyřešení přihlášení

Pokud se uživatelé přihlásí do aplikace, máte několik možností pro simulaci přihlášení, takže můžete otestovat stránky následující po přihlášení. Použitý přístup závisí na typu zabezpečení poskytovaném aplikací.

Ve všech případech musíte v aplikaci vytvořit účet jenom pro účely testování. Pokud je to možné, omezte oprávnění tohoto testovacího účtu, aby webové testy nemohly žádným způsobem ovlivnit skutečné uživatele.

**Jednoduché uživatelské jméno a heslo** webový test zaznamenejte obvyklým způsobem. Nejprve odstraňte soubory cookie.

**Ověřování SAML** použijte zásuvný modul SAML, která je k dispozici pro webové testy. Přístup k modulu plug-in podle...

**Tajný kód klienta** Pokud vaše aplikace obsahuje trasu přihlášení, která zahrnuje tajný klíč klienta, použijte ji. Azure Active Directory (AAD) je příkladem služby, která poskytuje přihlašování pomocí tajného klíče klienta. Ve službě AAD je tajným klíčem klienta klíč aplikace.

Tady je ukázkový webový test webové aplikace v Azure pomocí klíče aplikace:

![Snímek obrazovky ukázky](./media/availability-multistep/client-secret.png)

Získejte token ze služby AAD pomocí tajného klíče klienta (AppKey).
Extrahujte nosný token z odpovědi.
Pomocí nosného tokenu v autorizační hlavičce zavolejte rozhraní API.
Ujistěte se, zda je webový test skutečným klientem – to znamená, že má vlastní aplikaci ve službě AAD – a použijte jeho clientId + aplikace klíč. Testovaná služba má také vlastní aplikaci ve službě AAD: identifikátor URI aplikace appID se projeví do webového testu v poli prostředků.

### <a name="open-authentication"></a>Otevřené ověřování
Příkladem otevřeného ověřování je přihlašování pomocí účtu Microsoft nebo Google. Velký počet aplikací, které používají OAuth, nabízí alternativní tajný klíč klienta, takže prvním cílem bude prozkoumání této možnosti.

Pokud váš test vyžaduje přihlášení pomocí OAuth, bude obecný postup následující:

K prozkoumání přenosu dat mezi webovým prohlížečem, webem ověřování a aplikací použijte například nástroj Fiddler.
Proveďte dvě nebo více přihlášení pomocí různých počítačů nebo prohlížečů nebo v dlouhých intervalech (aby mohla vypršet platnost tokenů).
Porovnáním různých relací identifikujte token předaný zpět z webu ověřování, který byl následně předán do aplikačního serveru po přihlášení.
Uložte webový test pomocí sady Visual Studio.
Parametrizujte tokeny, nastavte parametr při vrácení tokenu z ověřovatele a použijte ho v dotazu na web. (Sada Visual Studio se pokusí o parametrizaci testu, ale nebude tokeny parametrizovat správně.)

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazené [článek pro řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další postup

* [Upozornění na dostupnost](availability-alerts.md)
* [Adresa URL příkazem ping webové testy](monitor-web-app-availability.md)
