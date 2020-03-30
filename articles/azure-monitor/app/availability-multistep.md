---
title: Monitorování pomocí vícekrokových webových testů – Azure Application Insights
description: Nastavení vícekrokových webových testů pro sledování webových aplikací pomocí Azure Application Insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655936"
---
# <a name="multi-step-web-tests"></a>Vícekrokové webové testy

Zaznamenanou sekvenci adres URL a interakcí s webovou stránkou můžete sledovat pomocí vícestupňových webových testů. Tento článek vás provede procesem vytváření vícekrokového webového testu pomocí sady Visual Studio Enterprise.

> [!NOTE]
> Vícekrokové webové testy závisí na souborech webových testů sady Visual Studio. Bylo [oznámeno,](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) že Visual Studio 2019 bude poslední verze s funkcí webtest. Je důležité si uvědomit, že zatímco nebudou přidány žádné nové funkce, funkce webtest v sadě Visual Studio 2019 je stále podporována a bude i nadále podporována během životního cyklu podpory produktu. Produktový tým Azure Monitor unásobil otázky týkající se budoucnosti testů dostupnosti ve více krocích [zde](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  

## <a name="pre-requisites"></a>Požadavky

* Visual Studio 2017 Enterprise nebo vyšší.
* Nástroje pro webové výkon a testování zatížení sady Visual Studio.

Chcete-li vyhledat testovací nástroje pre-požadovaný. Spusťte **instalační službu** > sady Visual Studio**Jednotlivé součásti** > **Ladění a testování** > **výkonu webu a nástrojů pro testování zatížení**.

![Snímek obrazovky s uživatelském rozhraním instalačního programu sady Visual Studio s vybranými jednotlivými součástmi se zaškrtávacím políčkem vedle položky pro nástroje pro testování výkonu webu a zatížení](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Vícekrokové webové testy mají s sebou spojené další náklady. Další informace naleznete v [oficiálním cenovém průvodci](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Záznam vícekrokového webového testu 

> [!WARNING]
> Již nedoporučujeme používat vícekrokový rekordér. Rekordér byl vyvinut pro statické HTML stránky se základními interakcemi a neposkytuje funkční zážitek pro moderní webové stránky.

Pokyny k vytváření webových testů sady Visual Studio naleznete v [oficiální dokumentaci k sadě Visual Studio 2019](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019).

## <a name="upload-the-web-test"></a>Nahrání webového testu

1. Na portálu Application Insights v podokně Dostupnost vyberte **vytvořit typ testu** > **vícekrokový****Test type** > webový test .

2. Nastavte testovací umístění, frekvenci a parametry výstrahy.

### <a name="frequency--location"></a>Umístění & frekvence

|Nastavení| Vysvětlení
|----|----|----|
|**Četnost zkoušek**| Nastavuje, jak často je test spuštěn z každého testovacího umístění. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Testovací místa**| Jsou místa, odkud naše servery posílají webové požadavky na vaši adresu URL. **Náš minimální počet doporučených testovacích míst je pět,** abychom se ujistili, že můžete odlišit problémy na svých webových stránkách od problémů se sítí. Můžete vybrat až 16 umístění.

### <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový čas testu** |Snižte tuto hodnotu, abyste byli upozorněni na pomalé odpovědi. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený stavový kód, který se počítá jako úspěšný. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, jako "Vítejte!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **Pouze anglické znaky jsou podporovány s obsahem odpovídající** |

### <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Téměř v reálném čase (náhled)** | Doporučujeme používat výstrahy v reálném čase. Konfigurace tohoto typu výstrahy se provádí po vytvoření testu dostupnosti.  |
|**Classic** | Už nedoporučujeme používat klasické výstrahy pro nové testy dostupnosti.|
|**Prahová hodnota umístění výstrahy**|Doporučujeme minimálně 3/5 lokalit. Optimální vztah mezi prahovou hodnotou umístění výstrahy a počtem testovacích míst je **výstražný počet prahových míst** = **umístění testovacích umístění - 2, s minimálně pěti testovacími místy.**|

## <a name="configuration"></a>Konfigurace

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Připojení času a náhodných čísel do testu

Předpokládejme, že testujete nástroj, který získá data závislá na čase, například akcie z externího kanálu. Při záznamu webového testu je nutné použít konkrétní časy, ale nastavit je jako parametry testu, čas spuštění a čas ukončení.

![Můj úžasný stock app screenshot](./media/availability-multistep/app-insights-72webtest-parameters.png)

Při spuštění testu chcete, aby čas ukončení vždy představoval aktuální čas a čas spuštění by měl začínat před 15 minutami.

Webový testovací čas ový modul poskytuje způsob zpracování časů parametrize.

1. Přidejte zásuvný modul webového testu pro každou hodnotu parametru proměnné, kterou chcete. V panelu nástrojů webového testu zvolte **Přidat zásuvný modul pro testování webu**.
    
    ![Přidat modul plug-in webového testu](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    V tomto příkladu používáme dvě instance zásuvného modulu Datum čas. Jedna instance je „před 15 minutami“ a druhá „teď“.

2. Otevřete vlastnosti každého zásuvného modulu. Pojmenujte ho a nastavte ho na použití aktuálního času. Pro jeden z nich nastavte Přidat minuty = -15.

    ![Kontextové parametry](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. V parametrech webového testu použijte {{plug-in name}} pro odkazování na název zásuvného modulu.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Teď nahrajte svůj test na portál. Při každé spuštění testu se budou používat dynamické hodnoty.

### <a name="dealing-with-sign-in"></a>Vyřešení přihlášení

Pokud se uživatelé přihlásí do aplikace, máte několik možností pro simulaci přihlášení, takže můžete otestovat stránky následující po přihlášení. Použitý přístup závisí na typu zabezpečení poskytovaném aplikací.

Ve všech případech musíte v aplikaci vytvořit účet jenom pro účely testování. Pokud je to možné, omezte oprávnění tohoto testovacího účtu, aby webové testy nemohly žádným způsobem ovlivnit skutečné uživatele.

**Jednoduché uživatelské jméno a heslo** Zaznamenejte webový test obvyklým způsobem. Nejprve odstraňte soubory cookie.

**Ověřování SAML**

|Název vlastnosti| Popis|
|----|-----|
| Publikum Uri | Identifikátor URI cílové skupiny pro token SAML.  Toto je identifikátor URI pro službu ACS (Access Control Service) – včetně oboru názvů služby ACS a názvu hostitele. |
| Heslo certifikátu | Heslo pro klientský certifikát, který udělí přístup k vložený soukromý klíč. |
| Klientský certifikát  | Hodnota klientského certifikátu s privátním klíčem v zakódovaném formátu Base64. |
| Identifikátor jména | Identifikátor názvu tokenu |
| Neplatný po | Časový rozsah, pro který bude platný token.  Výchozí hodnota je 5 minut. |
| Neplatný před | Časový rozsah, pro který bude platný token vytvořený v minulosti (k řešení času zkosení).  Výchozí hodnota je (záporná) 5 minut. |
| Název parametru cílového kontextu | Parametr kontextu, který obdrží generované kontrolní výraz. |


**Tajný klíč klienta** Pokud má vaše aplikace přihlašovací trasu, která zahrnuje tajný klíč klienta, použijte tuto trasu. Azure Active Directory (AAD) je příkladem služby, která poskytuje přihlašování pomocí tajného klíče klienta. Ve službě AAD je tajným klíčem klienta klíč aplikace.

Tady je ukázkový webový test webové aplikace v Azure pomocí klíče aplikace:

![Ukázkový snímek obrazovky](./media/availability-multistep/client-secret.png)

Získejte token ze služby AAD pomocí tajného klíče klienta (AppKey).
Extrahujte nosný token z odpovědi.
Pomocí nosného tokenu v autorizační hlavičce zavolejte rozhraní API.
Ujistěte se, že webový test je skutečný klient - to znamená, že má vlastní aplikaci v AAD - a používat jeho clientId + app key. Testovaná služba má také vlastní aplikaci v AAD: identifikátor URI aplikace této aplikace se projeví ve webovém testu v poli prostředku.

### <a name="open-authentication"></a>Otevřené ověřování
Příkladem otevřeného ověřování je přihlašování pomocí účtu Microsoft nebo Google. Velký počet aplikací, které používají OAuth, nabízí alternativní tajný klíč klienta, takže prvním cílem bude prozkoumání této možnosti.

Pokud váš test vyžaduje přihlášení pomocí OAuth, bude obecný postup následující:

K prozkoumání přenosu dat mezi webovým prohlížečem, webem ověřování a aplikací použijte například nástroj Fiddler.
Proveďte dvě nebo více přihlášení pomocí různých počítačů nebo prohlížečů nebo v dlouhých intervalech (aby mohla vypršet platnost tokenů).
Porovnáním různých relací identifikujte token předaný zpět z webu ověřování, který byl následně předán do aplikačního serveru po přihlášení.
Uložte webový test pomocí sady Visual Studio.
Parametrizujte tokeny, nastavte parametr při vrácení tokenu z ověřovatele a použijte ho v dotazu na web. (Sada Visual Studio se pokusí o parametrizaci testu, ale nebude tokeny parametrizovat správně.)

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek o řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Upozornění na dostupnost](availability-alerts.md)
* [Webové testy příkazu URL](monitor-web-app-availability.md)
