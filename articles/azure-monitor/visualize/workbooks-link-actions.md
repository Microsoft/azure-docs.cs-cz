---
title: Akce propojení Azure Monitor sešity
description: Použití akcí propojení v sešitech Azure Monitor
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610187"
---
# <a name="link-actions"></a>Akce s odkazy

K akcím propojení lze připojovat prostřednictvím kroků propojení s sešity nebo prostřednictvím nastavení sloupců [mřížek](../visualize/workbooks-grid-visualizations.md), [názvů](../visualize/workbooks-tile-visualizations.md)nebo [grafů](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Obecné akce odkazů

| Akce propojení | Akce při kliknutí |
|:------------- |:-------------|
| `Generic Details` | Zobrazuje hodnoty řádků v zobrazení kontextu mřížky vlastností. |
| `Cell Details` | Zobrazuje hodnotu buňky v zobrazení kontextu mřížky vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například JSON s vlastnostmi žádosti, jako je umístění, instance role atd.). |
| `Url` | Očekává se, že hodnota buňky bude platná adresa URL http a buňka bude odkaz, který otevře tuto adresu URL na nové kartě.|

## <a name="application-insights"></a>Application Insights

| Akce propojení | Akce při kliknutí |
|:------------- |:-------------|
| `Custom Event Details` | Otevře Application Insights podrobnosti hledání s ID vlastní události ( `itemId` ) v buňce. |
| `* Details` | Podobně jako v podrobnostech o vlastních událostech, s výjimkou závislostí, výjimek, zobrazení stránky, požadavků a trasování. |
| `Custom Event User Flows` | Otevře Application Insights Toky uživatelů prostředí, které se v buňce Překlopí na název vlastní události. |
| `* User Flows` | Podobně jako vlastní událost Toky uživatelů s výjimkou výjimek, zobrazení stránek a požadavků. |
| `User Timeline` | Otevře časovou osu uživatele s ID uživatele ( `user_Id` ) v buňce. |
| `Session Timeline` | Otevře Application Insights vyhledávání hodnoty v buňce (například vyhledat text ' ABC ', kde ABC je hodnota v buňce). |

`*` označuje zástupný znak pro výše uvedenou tabulku.

## <a name="azure-resource"></a>Prostředek Azure

| Akce propojení | Akce při kliknutí |
|:------------- |:-------------|
| `ARM Deployment` | Nasaďte šablonu Azure Resource Manager.  Když je vybraná tato položka, zobrazí se další pole, která autorovi umožní nakonfigurovat, který Azure Resource Manager šablonu otevřít, parametry pro šablonu atd. [Viz nastavení odkazu na nasazení Azure Resource Manager](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Vytvoří pravidlo výstrahy pro prostředek.  |
| `Custom View` | Otevře vlastní zobrazení. Když je vybraná tato položka, zobrazí se další pole, která autorovi umožní nakonfigurovat rozšíření zobrazení, název zobrazení a všechny parametry, které se používají k otevření zobrazení. [Viz vlastní zobrazení](#custom-view-link-settings). |
| `Metrics` | Otevře zobrazení metrik.  |
| `Resource overview` | Otevřete zobrazení prostředku na portálu na základě hodnoty ID prostředku v buňce. Autor může také volitelně nastavit `submenu` hodnotu, která otevře konkrétní položku nabídky v zobrazení zdrojů. |
| `Workbook (template)` | Otevřete šablonu sešitu.  Když je vybraná tato položka, zobrazí se další pole, která autorovi umožní nakonfigurovat, která šablona se má otevřít atd.  |

## <a name="link-settings"></a>Nastavení propojení

Při použití zobrazovacího panelu odkazů jsou k dispozici následující nastavení:

![Snímek obrazovky s nastavením odkazů](./media/workbooks-link-actions/link-settings.png)

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `View to open` | Umožňuje autorovi vybrat jednu z výše uvedených akcí. |
| `Menu item` | Pokud je vybraná možnost "Přehled prostředků", jedná se o položku nabídky v přehledu prostředku, která se má otevřít. Dá se použít k otevření výstrah nebo protokolů aktivit místo přehledu pro daný prostředek. Hodnoty položek nabídky se u každého Azure liší `Resourcetype` .|
| `Link label` | Je-li tento parametr zadán, bude tato hodnota zobrazena ve sloupci Grid. Pokud tato hodnota není zadaná, zobrazí se hodnota buňky. Pokud chcete zobrazit jinou hodnotu, jako je například heatmapu nebo Icon, nepoužívejte nástroj pro `Link` vykreslování, místo toho použijte příslušný zobrazovací jednotku a vyberte `Make this item a link` možnost. |
| `Open link in Context Blade` | Je-li tento parametr zadán, bude odkaz otevřen jako místní zobrazení "kontext" na pravé straně okna místo otevření jako úplné zobrazení. |

Při použití `Make this item a link` Možnosti jsou k dispozici následující nastavení:

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Link value comes from` | Když se zobrazuje buňka jako zobrazovací jednotka s odkazem, toto pole určuje, kde se má v odkazu použít hodnota "odkaz", která umožňuje autorovi vybrat z rozevíracího seznamu ostatních sloupců v mřížce. Například buňka může být heatmapu hodnota, ale chcete, aby odkaz otevřel Přehled prostředků pro ID prostředku na řádku. V takovém případě byste nastavili hodnotu propojení, která se má pocházet z `Resource Id` pole.
| `View to open` | stejné jako výše. |
| `Menu item` | stejné jako výše. |
| `Open link in Context Blade` | stejné jako výše. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Nastavení odkazu na nasazení Azure Resource Manager

Pokud je vybraný typ propojení `ARM Deployment` , musí autor zadat další nastavení pro otevření Azure Resource Manager nasazení. Existují dvě hlavní karty konfigurace.

### <a name="template-settings"></a>Nastavení šablony

Tato část definuje, odkud má šablona pocházet, a parametry, které se používají ke spuštění nasazení Azure Resource Manager.

| Zdroj | Vysvětlení |
|:------------- |:-------------|
|`Resource group id comes from` | ID prostředku se používá ke správě nasazených prostředků. Předplatné se používá ke správě nasazených prostředků a nákladů. Skupiny prostředků se používají jako složky k organizování a správě všech vašich prostředků. Pokud tato hodnota není zadaná, nasazení se nezdaří. Vyberte možnost z `Cell` , `Column` , `Static Value` nebo `Parameter` ve [zdrojích odkazů](#link-sources).|
|`ARM template URI from` | Identifikátor URI pro šablonu Azure Resource Manager sám sebe. Identifikátor URI šablony musí být přístupný uživatelům, kteří šablonu nasadí. Vyberte možnost z `Cell` , `Column` , `Parameter` nebo `Static Value`  ve [zdrojích odkazů](#link-sources). V případě startů si prohlédněte naše [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Tato část definuje parametry šablony použité pro identifikátor URI šablony, který je definován výše. Tyto parametry budou použity k nasazení šablony na stránce spuštění. Mřížka obsahuje tlačítko Rozbalit panel nástrojů, které vám umožní vyplnit parametry pomocí názvů definovaných v identifikátoru URI šablony a nastavit je na statické prázdné hodnoty. Tuto možnost lze použít pouze v případě, že v mřížce nejsou žádné parametry a byl nastaven identifikátor URI šablony. Dolní část je náhled toho, co vypadá jako výstup parametru. Výběrem aktualizovat aktualizujete náhled aktuálními změnami. Parametry jsou obvykle hodnoty, zatímco odkazy jsou to něco, co může ukazovat na tajné klíče trezoru klíčů, ke kterým má uživatel přístup. <br/><br/> **Omezení okna prohlížeče šablon** – nevykresluje parametry odkazu správně a zobrazí se jako hodnota null/hodnota, takže uživatelé nebudou moci správně nasadit referenční parametry z karty Prohlížeč šablon.|

![Snímek obrazovky s nastavením šablony Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Nastavení uživatelského rozhraní

V této části se nakonfigurují informace o tom, co uživatelé uvidí před spuštěním nasazení Azure Resource Manager.

| Zdroj | Vysvětlení |
|:------------- |:-------------|
|`Title from` | Nadpis použitý v zobrazení spuštění Vyberte možnost z `Cell` , `Column` , `Parameter` nebo `Static Value` ve [zdrojích odkazů](#link-sources).|
|`Description from` | Tento text Markdownu slouží k poskytnutí užitečného popisu uživatelům, kteří chtějí šablonu nasadit. Vyberte možnost z `Cell` , `Column` , `Parameter` nebo `Static Value`  ve [zdrojích odkazů](#link-sources). <br/><br/> **Poznámka:** Pokud `Static Value` je zaškrtnuto, zobrazí se víceřádkové textové pole. V tomto textovém poli můžete parametry přeložit pomocí `{paramName}` . Sloupce můžete také považovat za parametry tím, že `_column` se připojíte za název sloupce, jako je například `{columnName_column}` . V následujícím příkladu obrázku můžeme na sloupec odkazovat `VMName` pomocí zápisu `{VMName_column}` . Hodnota za dvojtečkou je [formátovací modul parametru](../visualize/workbooks-parameters.md#parameter-options), v tomto případě je to `value` .|
|`Run button text from` | Popisek, který se používá pro tlačítko spustit (spustit) k nasazení šablony Azure Resource Manager. Tím se uživatelé budou moct začít nasazovat šablonu Azure Resource Manager.|

![Snímek obrazovky s nastavením Azure Resource Manager uživatelského rozhraní](./media/workbooks-link-actions/ux-settings.png)

Po nastavení těchto konfigurací, když uživatelé vyberou odkaz, otevře se zobrazení pomocí uživatelského rozhraní popsaného v nastavení uživatelského rozhraní. Pokud uživatel tuto možnost vybere, `Run button text from` nasadí šablonu Azure Resource Manager s použitím hodnot z [nastavení šablony](#template-settings). Zobrazení šablony otevře kartu Prohlížeč šablon pro uživatele, aby před nasazením prověřil šablonu a parametry.

![Snímek obrazovky s zobrazením pro spuštění Azure Resource Manager](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Nastavení odkazu na vlastní zobrazení

Tuto položku použijte k otevření vlastních zobrazení v Azure Portal. Ověřte všechna nastavení a konfiguraci. Nesprávné hodnoty způsobí chyby na portálu nebo se nedaří otevřít zobrazení správně. Existují dva způsoby, jak nakonfigurovat nastavení prostřednictvím `Form` nebo `URL` .

> [!NOTE]
> Zobrazení s nabídkou nelze otevřít na kartě kontextu. Pokud je zobrazení s nabídkou nakonfigurované tak, aby se otevřelo na kartě kontextu, nezobrazí se při výběru odkazu žádná karta kontextu.

### <a name="form"></a>Formulář

| Zdroj | Vysvětlení |
|:------------- |:-------------|
|`Extension name` | Název rozšíření, které hostuje název zobrazení.|
|`View name` | Název zobrazení, které se má otevřít|

#### <a name="view-inputs"></a>Zobrazit vstupy

Existují dva typy vstupů, mřížek a formátu JSON. Slouží `Grid` k zadání jednoduchých klíčových a hodnotových vstupů na kartách nebo `JSON` k určení vloženého vstupu JSON.

- Mřížka
    - `Parameter Name`: Název vstupního parametru zobrazení.
    - `Parameter Comes From`: Kde hodnota parametru zobrazení musí pocházet z. Vyberte možnost z `Cell` , `Column` , `Parameter` nebo `Static Value` ve [zdrojích odkazů](#link-sources).
    > [!NOTE]
    > Pokud `Static Value` je zaškrtnuto, parametry lze vyřešit pomocí propojení závorek `{paramName}` v textovém poli. Sloupce lze považovat za sloupce parametrů tak, že `_column` se připojíte za název sloupce, jako je například `{columnName_column}` .

    - `Parameter Value`: v závislosti na `Parameter Comes From` tom bude toto rozevírací seznam dostupných parametrů, sloupců nebo statických hodnot.

    ![Snímek obrazovky s nastavením sloupce pro úpravy zobrazit nastavení vlastního zobrazení z formuláře](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Zadejte svůj vstup na kartě ve formátu JSON v editoru. Podobně jako `Grid` režim, parametry a sloupce mohou být odkazovány pomocí `{paramName}` parametrů pro parametry a `{columnName_column}` pro sloupce. Při výběru se zobrazí `Show JSON Sample` očekávaný výstup všech vyřešených parametrů a sloupců pro zobrazení vstupu.

    ![Snímek obrazovky s otevřeným nastavením vlastního zobrazení se zobrazením vstupu ve formátu JSON](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Vložte adresu URL portálu obsahující rozšíření, název zobrazení a všechny vstupy potřebné k otevření zobrazení. Po výběru se `Initialize Settings` naplní `Form` autorovi, který přidá/upraví nebo odebere všechny vstupy zobrazení.

![Snímek obrazovky zobrazující nastavení sloupce zobrazit nastavení vlastního zobrazení z adresy URL ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Nastavení odkazu v sešitu (šabloně)

Pokud je zvolený typ propojení `Workbook (Template)` , autor musí zadat další nastavení pro otevření správné šablony sešitu. Následující nastavení mají možnosti, jak bude mřížka najít odpovídající hodnotu pro každé nastavení.

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Toto je ID prostředku Azure, který je vlastníkem sešitu. Běžně se jedná o prostředek Application Insights nebo pracovní prostor Log Analytics. Uvnitř Azure Monitor může to být také literální řetězec `"Azure Monitor"` . Když je sešit uložený, bude sešit propojený s. |
| `Workbook resources` | Pole ID prostředků Azure, které určuje výchozí prostředek použitý v sešitu. Pokud se například otevřená šablona zobrazuje metriky virtuálního počítače, pak tyto hodnoty budou identifikátory prostředků virtuálních počítačů.  V mnoha případech jsou vlastníkem a prostředky nastaveny stejné nastavení. |
| `Template Id` | Zadejte ID šablony, která se má otevřít. Pokud se jedná o šablonu komunity z galerie (Nejběžnější případ), prefixujte cestu k šabloně s `Community-` podobně jako `Community-Workbooks/Performance/Apdex` pro `Workbooks/Performance/Apdex` šablonu. Pokud se jedná o odkaz na uložený sešit nebo šablonu, jedná se o úplné ID prostředku Azure této položky. |
| `Workbook Type` | Zadejte druh šablony sešitu, která se má otevřít. Nejběžnější případy používají `Default` `Workbook` možnost nebo k použití hodnoty v aktuálním sešitu. |
| `Gallery Type` | Určuje typ galerie, který se zobrazí v zobrazení galerie šablony, která se otevře. Nejběžnější případy používají `Default` `Workbook` možnost nebo k použití hodnoty v aktuálním sešitu. |
|`Location comes from` | Pole umístění by mělo být zadáno, pokud otevíráte konkrétní prostředek sešitu. Pokud umístění není zadáno, bude hledání obsahu sešitu mnohem pomalejší. Pokud znáte umístění, zadejte ho. Pokud umístění neznáte nebo otevíráte šablonu, která nemá žádné konkrétní umístění, ponechte toto pole jako výchozí.|
|`Pass specific parameters to template` | Tuto možnost vyberte, pokud chcete předat konkrétní parametry do šablony. Pokud je tato možnost vybrána, budou do šablony předány pouze zadané parametry. všechny parametry v aktuálním sešitu jsou předány šabloně a v takovém případě musí být *názvy* parametrů v obou sešitech stejné, aby tato hodnota parametru fungovala.|
|`Workbook Template Parameters` | Tato část definuje parametry, které jsou předány cílové šabloně. Název by měl odpovídat názvu parametru v cílové šabloně. Vyberte hodnotu z `Cell` , `Column` , a `Parameter` `Static Value` . Hodnota name a Value nesmí být prázdná, aby bylo možné tento parametr předat cílové šabloně.|

Pro každé z výše uvedených nastavení musí autor vybrat, odkud bude hodnota v odkazovaném sešitu pocházet. Zobrazit [zdroje odkazů](#link-sources)

Po otevření odkazu na sešit budou nové zobrazení sešitu předány všechny hodnoty nakonfigurované z výše uvedených nastavení.

![Snímek obrazovky s nastavením odkazu na sešit](./media/workbooks-link-actions/workbook-link-settings.png)

![Snímek obrazovky s nastavením parametrů šablony sešitu](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Propojit zdroje

| Zdroj | Vysvětlení |
|:------------- |:-------------|
| `Cell` | Tato hodnota použije hodnotu v této buňce v mřížce jako hodnotu odkazu. |
| `Column` | Když se tato možnost vybere, zobrazí se další pole, které autorovi umožní vybrat jiný sloupec v mřížce.  Hodnota sloupce daného řádku bude použita v hodnotě odkazu. To se běžně používá k tomu, aby každý řádek mřížky mohl otevřít jinou šablonu, nastavením `Template Id` pole na `column` nebo pro otevření stejné šablony sešitu pro různé prostředky, pokud `Workbook resources` je pole nastavené na sloupec, který obsahuje ID prostředku Azure. |
| `Parameter` | Když je tato možnost vybrána, zobrazí se další pole, které autorovi umožní vybrat parametr. Hodnota tohoto parametru bude použita pro hodnotu při kliknutí na odkaz. |
| `Static value` | Když se tato možnost vybere, zobrazí se další pole, které umožní typ autora ve statické hodnotě, která se použije v propojeném sešitu. To se běžně používá v případě, že všechny řádky v mřížce budou pro pole používat stejnou hodnotu. |
| `Step` | Použijte hodnotu nastavenou v aktuálním kroku sešitu. To je běžné v krocích dotazů a metrik pro nastavení prostředků sešitu v propojeném sešitu na ty, které se používají *v kroku dotazování a metrik*, ne v aktuálním sešitu. |
| `Workbook` | Použijte hodnotu nastavenou v aktuálním sešitu. |
| `Default` | Použijte výchozí hodnotu, která by se použila, pokud nebyla zadána žádná hodnota. To je běžné pro typ galerie, kde se výchozí Galerie nastaví podle typu prostředku vlastníka. |

## <a name="next-steps"></a>Další kroky

- [Řízení](../visualize/workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.
- Naučte se používat [skupiny v sešitech](../visualize/workbooks-groups.md).