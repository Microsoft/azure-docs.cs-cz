---
title: Monitorování webové aplikace pomocí webových testů s více kroky a Azure Application Insights | Microsoft Docs
description: Nastavení více kroků pro sledování webových aplikací s využitím Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/25/2019
ms.reviewer: sdash
ms.openlocfilehash: f34695cb4a92fbed285ba8c56764606a124194a4
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678240"
---
# <a name="multi-step-web-tests"></a>Vícekrokové webové testy

Zaznamenanou posloupnost adres URL a interakcí můžete sledovat na webu prostřednictvím webových testů s více kroky. Tento článek vás provede procesem vytvoření webového testu ve více krocích pomocí Visual Studio Enterprise.

> [!NOTE]
> Webové testy s více kroky závisejí na souborech WebTest sady Visual Studio. Bylo [oznámeno](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) , že Visual Studio 2019 bude poslední verzí s funkcí webového testu. Je důležité si uvědomit, že i když nebudou přidány žádné nové funkce, funkce webového testu v aplikaci Visual Studio 2019 je stále nadále podporována a bude nadále podporována během životního cyklu podpory produktu. Tento Azure Monitor produktový tým se zabývá otázkami ohledně [budoucích testů dostupnosti](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)s více kroky.  

## <a name="pre-requisites"></a>Požadavky

* Visual Studio 2017 Enterprise nebo vyšší.
* Nástroje pro testování výkonu a zátěže webu sady Visual Studio.

Pro vyhledání požadovaných testovacích nástrojů. Spusťte **Instalační program pro Visual Studio**  > **jednotlivé komponenty**  > **ladění a testování**  > **nástroje webového výkonu a zátěžového testování**.

![Snímek obrazovky s uživatelským rozhraním instalačního programu sady Visual Studio s jednotlivými součástmi vybranými vedle položky pro nástroje pro testování výkonu webu a zátěžové testování](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> U více kroků jsou k těmto webovým testům přidruženy další náklady. Další informace najdete v [oficiálních cenových příručkách](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Záznam webového testu s více kroky

Pro vytvoření vícekrokového testu uložte scénář pomocí sady Visual Studio Enterprise a pak nahrajte tento záznam do služby Application Insights. Application Insights přehraje scénář v nastavených intervalech a ověří odpověď.

> [!IMPORTANT]
> * V testech nelze použít programové funkce nebo smyčky. Test musí být zcela obsažený ve skriptu .webtest. Můžete však použít standardní moduly plug-in.
> * Vícekrokové webové testy podporují pouze anglické znaky. Pokud používáte sadu Visual Studio v jiném jazyce, aktualizujte definiční soubor webového testu pro překlad/vyloučení jiných než anglických znaků.

Slouží k zaznamenání relace webové aplikace Visual Studio Enterprise.

1. Vytvořte projekt webového výkonu a zátěžového testu. **Soubor**  > **Nový**  > **projekt**  > **test** **Visual C#**   > 

    ![Nové uživatelské rozhraní projektu sady Visual Studio](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Otevřete soubor `.webtest` a začněte nahrávat.

    ![Uživatelské rozhraní záznamu testů sady Visual Studio](./media/availability-multistep/open-web-test.png)

3. Klikněte na kroky, které má test simulovat jako součást záznamu.

    ![Uživatelské rozhraní pro záznam v prohlížeči](./media/availability-multistep/record.png)

4. Upravte test na:

    * Přidání ověření ke kontrole přijatého textu a kódů odpovědi.
    * Odeberte jakékoli uneccesary interakce. Můžete také odebrat závislé požadavky na obrázky nebo přidat sledovací lokality, které nejsou relevantní pro vaši žádost o úspěšnost testu.
    
    Mějte na paměti, že můžete upravit pouze testovací skript – můžete přidat vlastní kód nebo zavolat jiné webové testy. Nevkládejte do testu smyčky. Můžete použít standardní zásuvné moduly webového testu.

5. Spusťte test v sadě Visual Studio a ověřte ho a ujistěte se, že funguje.

    Spouštěč webových testů otevře webový prohlížeč a zopakuje zaznamenané akce. Ujistěte se, že vše se chová podle očekávání.

## <a name="upload-the-web-test"></a>Nahrát webový test

1. Na portálu Application Insights v podokně dostupnost vyberte **vytvořit testovací**  > **Typ testu**  > **webový test s více kroky**.

2. Nastavte umístění testu, četnost a parametry výstrahy.

### <a name="frequency--location"></a>Frekvence & umístění

|Nastavení| Vysvětlení
|----|----|----|
|**Frekvence testování**| Nastaví, jak často se test spouští z každého umístění testu. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Testovací umístění**| Jsou místa, odkud naše servery odesílají webové požadavky na adresu URL. **Náš minimální počet doporučených testovacích umístění je pět** , abyste měli jistotu, že můžete odlišit problémy na webu od problémů se sítí. Můžete vybrat až 16 umístění.

### <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový limit testu** |Snižte tuto hodnotu, aby se zobrazila výstraha týkající se pomalých odpovědí. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený stavový kód, který se počítá jako úspěch. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, například "Welcome!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **U shody obsahu se podporují jenom anglické znaky.** |

### <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Téměř v reálném čase (Preview)** | Doporučujeme používat upozornění téměř v reálném čase. Konfigurace tohoto typu upozornění se provádí po vytvoření testu dostupnosti.  |
|**Classic** | Pro nové testy dostupnosti už nedoporučujeme používat klasické výstrahy.|
|**Prahová hodnota umístění výstrahy**|Doporučujeme minimálně 3/5 umístění. Optimální vztah mezi prahovou hodnotou umístění výstrahy a počtem umístění testu je **prahová hodnota pro umístění výstrahy**  = **počet umístění testu – 2, minimálně pět umístění testu.**|

## <a name="advanced-configuration"></a>Pokročilá konfigurace

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Doba připojení a náhodné číslování do testu

Předpokládejme, že testujete nástroj, který získá data závislá na čase, například akcie z externího kanálu. Při záznamu webového testu je nutné použít konkrétní časy, ale nastavit je jako parametry testu, čas spuštění a čas ukončení.

![Snímek obrazovky aplikace Super Stock](./media/availability-multistep/app-insights-72webtest-parameters.png)

Při spuštění testu chcete, aby čas ukončení vždy představoval aktuální čas a čas spuštění by měl začínat před 15 minutami.

Modul plug-in data a času webového testu poskytuje způsob, jak zpracovávat parametrizovat časy.

1. Přidejte zásuvný modul webového testu pro každou hodnotu parametru proměnné, kterou chcete. V panelu nástrojů webového testu zvolte **Přidat zásuvný modul pro testování webu**.
    
    ![Přidat modul plugin webového testu](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    V tomto příkladu používáme dvě instance zásuvného modulu Datum čas. Jedna instance je „před 15 minutami“ a druhá „teď“.

2. Otevřete vlastnosti každého zásuvného modulu. Pojmenujte ho a nastavte ho na použití aktuálního času. Pro jeden z nich nastavte Přidat minuty = -15.

    ![Kontextové parametry](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. V parametrech webového testu použijte {{plug-in name}} pro odkazování na název zásuvného modulu.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Teď nahrajte svůj test na portál. Při každé spuštění testu se budou používat dynamické hodnoty.

### <a name="dealing-with-sign-in"></a>Vyřešení přihlášení

Pokud se uživatelé přihlásí do aplikace, máte několik možností pro simulaci přihlášení, takže můžete otestovat stránky následující po přihlášení. Použitý přístup závisí na typu zabezpečení poskytovaném aplikací.

Ve všech případech musíte v aplikaci vytvořit účet jenom pro účely testování. Pokud je to možné, omezte oprávnění tohoto testovacího účtu, aby webové testy nemohly žádným způsobem ovlivnit skutečné uživatele.

**Jednoduché uživatelské jméno a heslo** Záznam webového testu obvyklým způsobem. Nejprve odstraňte soubory cookie.

**Ověřování SAML**

|Název vlastnosti| Popis|
|----|-----|
| Identifikátor URI cílové skupiny | Identifikátor URI cílové skupiny pro token SAML.  Toto je identifikátor URI pro Access Control Service (ACS) – včetně oboru názvů služby ACS a názvu hostitele. |
| Heslo certifikátu | Heslo pro klientský certifikát, kterým bude udělen přístup k vloženému privátnímu klíči. |
| Certifikát klienta  | Hodnota certifikátu klienta s privátním klíčem ve formátu kódování Base64. |
| Identifikátor názvu | Identifikátor názvu pro token |
| Není po | Hodnota TimeSpan, pro kterou bude token platný  Výchozí hodnota je 5 minut. |
| Ne před | Hodnota TimeSpan, pro kterou byl token vytvořený v minulosti platný (k adresování časových intervalů).  Výchozí hodnota je (negativní) 5 minut. |
| Název cílového kontextového parametru | Kontextový parametr, který získá generovaný kontrolní výraz. |


**Tajný kód klienta** Pokud vaše aplikace obsahuje trasu přihlášení, která zahrnuje tajný klíč klienta, použijte tuto trasu. Azure Active Directory (AAD) je příkladem služby, která poskytuje přihlašování pomocí tajného klíče klienta. Ve službě AAD je tajným klíčem klienta klíč aplikace.

Tady je ukázkový webový test webové aplikace v Azure pomocí klíče aplikace:

![Ukázka snímku obrazovky](./media/availability-multistep/client-secret.png)

Získejte token ze služby AAD pomocí tajného klíče klienta (AppKey).
Extrahujte nosný token z odpovědi.
Pomocí nosného tokenu v autorizační hlavičce zavolejte rozhraní API.
Ujistěte se, že webový test je skutečný klient – to znamená, že má vlastní aplikaci v AAD – a používá svůj klíč clientId + App. Testovaná služba má také svou vlastní aplikaci v AAD: identifikátor URI appID této aplikace se odrazí ve webovém testu v poli prostředků.

### <a name="open-authentication"></a>Otevřené ověřování
Příkladem otevřeného ověřování je přihlašování pomocí účtu Microsoft nebo Google. Velký počet aplikací, které používají OAuth, nabízí alternativní tajný klíč klienta, takže prvním cílem bude prozkoumání této možnosti.

Pokud váš test vyžaduje přihlášení pomocí OAuth, bude obecný postup následující:

K prozkoumání přenosu dat mezi webovým prohlížečem, webem ověřování a aplikací použijte například nástroj Fiddler.
Proveďte dvě nebo více přihlášení pomocí různých počítačů nebo prohlížečů nebo v dlouhých intervalech (aby mohla vypršet platnost tokenů).
Porovnáním různých relací identifikujte token předaný zpět z webu ověřování, který byl následně předán do aplikačního serveru po přihlášení.
Uložte webový test pomocí sady Visual Studio.
Parametrizujte tokeny, nastavte parametr při vrácení tokenu z ověřovatele a použijte ho v dotazu na web. (Sada Visual Studio se pokusí o parametrizaci testu, ale nebude tokeny parametrizovat správně.)

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek týkající se řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Výstrahy dostupnosti](availability-alerts.md)
* [Testování webových testů adresy URL](monitor-web-app-availability.md)
