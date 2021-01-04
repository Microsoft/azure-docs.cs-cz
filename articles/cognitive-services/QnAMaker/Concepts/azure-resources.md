---
title: Prostředky Azure – QnA Maker
description: QnA Maker používá několik zdrojů Azure, z nichž každý má jiný účel. Porozumět tomu, jak se používají samostatně, vám umožní naplánovat a vybrat správnou cenovou úroveň nebo zjistit, kdy se má změnit cenová úroveň. Princip použití v kombinaci vám pomůže najít a opravit problémy, když k nim dojde.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b5f7f494c9d0969fbf9431d0b552dafa21a5eace
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705400"
---
# <a name="azure-resources-for-qna-maker"></a>Prostředky Azure pro QnA Maker

QnA Maker používá několik zdrojů Azure, z nichž každý má jiný účel. Porozumět tomu, jak se používají samostatně, vám umožní naplánovat a vybrat správnou cenovou úroveň nebo zjistit, kdy se má změnit cenová úroveň. Princip použití _v kombinaci_ vám pomůže najít a opravit problémy, když k nim dojde.

## <a name="resource-planning"></a>Plánování prostředků

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Při prvním vývoji QnA Maker znalostní báze ve fázi prototypu je běžné mít jeden prostředek QnA Maker pro testování i pro produkční prostředí.

Když přejdete do vývojové fáze projektu, měli byste zvážit:

* Kolik jazyků systém znalostní báze bude obsahovat?
* Kolik oblastí potřebujete k dispozici ve znalostní bázi?
* Kolik dokumentů v každé doméně bude váš systém obsahovat?

Naplánujte, aby jeden prostředek QnA Maker obsahoval všechny znalostní báze, které mají stejný jazyk, stejnou oblast a stejnou kombinaci domény předmětu.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Při prvním vývoji QnA Maker spravované znalostní bázi je ve fázi prototypu běžné mít jeden QnA Maker spravovaný prostředek pro testování i produkci.

Když přejdete do vývojové fáze projektu, měli byste zvážit:

* Kolik jazyků systém znalostní báze bude obsahovat?
* Kolik oblastí potřebujete k dispozici ve znalostní bázi?
* Kolik dokumentů v každé doméně bude váš systém obsahovat?

---

## <a name="pricing-tier-considerations"></a>Doporučení cenové úrovně

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Obvykle existují tři parametry, které je třeba vzít v úvahu:

* **Propustnost, kterou potřebujete ze služby**:
    * Podle svých potřeb vyberte vhodný [plán aplikace](https://azure.microsoft.com/pricing/details/app-service/plans/) pro vaši službu App Service. Aplikaci můžete [škálovat směrem nahoru](../../../app-service/manage-scale-up.md) nebo dolů.
    * Mělo by to mít vliv i na výběr skladové položky v Azure **kognitivní hledání** . Další podrobnosti najdete [tady](../../../search/search-sku-tier.md). Kromě toho možná budete muset nastavit [kapacitu](../../../search/search-capacity-planning.md) kognitivní hledání pomocí replik.

* **Velikost a počet znalostí znalostní báze**: vyberte příslušnou skladovou položku [Azure Search](https://azure.microsoft.com/pricing/details/search/) pro váš scénář. Obvykle se na základě počtu různých domén předmětu rozhodnete, že potřebujete požadovaný počet znalostí. Doména předmětu (pro jeden jazyk) by měla být v jedné znalostní bázi.

    Můžete publikovat N-1 znalostní báze v určité úrovni, kde N je maximální počet indexů povolených v této vrstvě. Také ověřte maximální velikost a počet dokumentů povolených na jednu úroveň.

    Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování.

* **Počet dokumentů jako zdrojů**: bezplatná SKU služby QnA maker Management omezuje počet dokumentů, které můžete spravovat přes portál, a rozhraní API na 3 (velikost 1 MB). Standardní SKU neomezuje počet dokumentů, které můžete spravovat. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedeny některé pokyny vysoké úrovně.

|                            | Správa QnA Maker | App Service | Azure Cognitive Search | Omezení                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentování**        | SKU zdarma             | Úroveň Free   | Úroveň Free    | Publikovat až 2 aktualizací KB velikost, 50 MB  |
| **Vývojové a testovací prostředí**   | Standardní SKU         | Shared      | Základní        | Publikování až o 14 aktualizací KB velikosti 2 GB    |
| **Produkční prostředí** | Standardní SKU         | Basic       | Standard     | Publikování až 49 aktualizací KB, velikost 25 GB |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Obvykle existují tři parametry, které je třeba vzít v úvahu:

* **Propustnost, kterou potřebujete ze služby**:
    * QnA Maker Managed (Preview) je bezplatná služba a propustnost se momentálně omezené na 10 TPS pro rozhraní API pro správu i pro předpovědi rozhraní API.
    * Mělo by to mít vliv i na výběr skladové položky v Azure **kognitivní hledání** . Další podrobnosti najdete [tady](../../../search/search-sku-tier.md). Kromě toho možná budete muset nastavit [kapacitu](../../../search/search-capacity-planning.md) kognitivní hledání pomocí replik.

* **Velikost a počet znalostí znalostní báze**: vyberte příslušnou skladovou položku [Azure Search](https://azure.microsoft.com/pricing/details/search/) pro váš scénář. Obvykle se na základě počtu různých domén předmětu rozhodnete, že potřebujete požadovaný počet znalostí. Doména předmětu (pro jeden jazyk) by měla být v jedné znalostní bázi.

    V QnA Maker spravovaném (ve verzi Preview) máte možnost nastavit službu QnA Maker pro aktualizací KB v jednom jazyce nebo v několika jazycích. Tento výběr můžete provést při vytvoření první znalostní báze ve službě QnA Maker Managed (Preview).

    ![QnA Maker spravované (Preview) výběr vícejazykové znalostní báze](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Můžete publikovat N-1 znalostní bázi jednoho jazyka nebo N/2 znalostní báze různých jazyků v určité úrovni, kde N je maximální počet indexů povolených v této vrstvě. Také ověřte maximální velikost a počet dokumentů povolených na jednu úroveň.

    Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 základních znalostí stejného jazyka (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování. Pokud se rozhodnete, že budete mít znalostní bázi v různých jazycích, můžete publikovat jenom 7 znalostí znalostní báze.

* **Počet dokumentů jako zdrojů**: QnA maker Managed (Preview) je bezplatná služba a neexistují žádná omezení počtu dokumentů, které můžete přidat jako zdroje. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedeny některé pokyny vysoké úrovně.

|                            |Azure Cognitive Search | Omezení                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentování**        |Úroveň Free    | Publikovat až 2 aktualizací KB velikost, 50 MB  |
| **Vývojové a testovací prostředí**   |Základní        | Publikování až o 14 aktualizací KB velikosti 2 GB    |
| **Produkční prostředí** |Standard     | Publikování až 49 aktualizací KB, velikost 25 GB |

---

## <a name="recommended-settings"></a>Doporučené nastavení

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

|Cílový QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 replika   | S1, 1 replika    |
| 50         | S3, 10 replik       | S1, 12 replik         |
| 80         | S3, 10 replik      |  S3, 12 replik  |
| 100         | P3V2, 10 replik  | S3, 12 replik, 3 oddíly   |
| 200 až 250         | P3V2, 20 replik | S3, 12 replik, 3 oddíly    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

QnA Maker spravovaná je bezplatná služba a propustnost se momentálně omezené za 10 transakcí za sekundu pro rozhraní API pro správu i pro předpovědi rozhraní API. Aby bylo možné cílit na 10 transakcí za sekundu pro vaši službu, doporučujeme pro Azure Kognitivní hledání skladové položky S1 (1 instance).

---

## <a name="when-to-change-a-pricing-tier"></a>Kdy změnit cenovou úroveň

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

|Upgrade|Důvod|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) SKU správy QnA Maker|Chcete mít ve znalostní bázi více QnA párů nebo zdrojů dokumentů.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU a zkontroluje Kognitivní hledání vrstvu a [vytvoří kognitivní hledání repliky](../../../search/search-capacity-planning.md) .|Vaše znalostní báze potřebuje k obsluze více požadavků z klientské aplikace, jako je například robot pro chat.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Služba Azure Kognitivní hledání|Máte v plánu, abyste měli spoustu znalostní báze.|

Pomocí [aktualizace App Service v Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)získáte nejnovější aktualizace modulu runtime.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Služba Azure Kognitivní hledání, pokud plánujete mít mnoho znalostí znalostní báze.

---

## <a name="resource-naming-considerations"></a>Požadavky na pojmenovávání prostředků

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Název prostředku QnA Maker prostředku, například `qna-westus-f0-b` , se používá také k pojmenování dalších prostředků.

Okno Azure Portal vytvořit umožňuje vytvořit prostředek QnA Maker a vybrat cenové úrovně pro ostatní prostředky.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky Azure Portal pro vytváření prostředků QnA Maker](../media/concept-azure-resource/create-blade.png)

Po vytvoření prostředků mají stejný název, s výjimkou volitelného prostředku Application Insights, který postpends znaky na název.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s Azure Portalm seznamem prostředků](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Při vytváření prostředku QnA Maker vytvořte novou skupinu prostředků. To vám umožní zobrazit všechny prostředky přidružené k QnA Maker prostředku při hledání podle skupiny prostředků.

> [!TIP]
> Pomocí konvence vytváření názvů můžete označovat cenové úrovně v rámci názvu prostředku nebo skupiny prostředků. Když obdržíte chyby při vytváření nové znalostní báze nebo přidávání nových dokumentů, je běžným problémem omezení cenové úrovně Kognitivní hledání.

### <a name="resource-purposes"></a>Účely prostředků

Každý prostředek Azure vytvořený pomocí QnA Maker má určitý účel:

* Prostředek QnA Maker
* Prostředek Kognitivní hledání
* App Service
* Služba plánu aplikací
* Služba Application Insights


### <a name="cognitive-search-resource"></a>Prostředek Kognitivní hledání

Prostředek [kognitivní hledání](../../../search/index.yml) se používá pro:

* Uložit páry QnA
* Zadejte počáteční hodnocení (#1 hodnocení) párů QnA za běhu.

#### <a name="index-usage"></a>Využití indexu

Prostředek udržuje jeden index, který bude fungovat jako index testu, a zbývající indexy jsou v relaci k jedné publikované znalostní bázi.

Cena za prostředek se zablokuje 15 indexů, bude obsahovat 14 publikovaných znalostní báze a jeden index se používá k testování všech znalostí. Tento index testu je rozdělený ve znalostní bázi, takže dotaz používající interaktivní testovací podokno použije index testu, ale vrátí jenom výsledky z konkrétního oddílu přidruženého ke konkrétní znalostní bázi.

#### <a name="language-usage"></a>Použití jazyka

První znalostní báze vytvořená v prostředku QnA Maker slouží k určení _jedné_ sady jazyků pro prostředek kognitivní hledání a všech jeho indexů. Pro QnA Maker službu můžete mít jenom _jednu sadu jazyků_ .

### <a name="qna-maker-resource"></a>Prostředek QnA Maker

Prostředek QnA Maker poskytuje přístup k rozhraním API pro vytváření a publikování a také ke vrstvě pro zpracování v přirozeném jazyce (NLP) na základě druhé vrstvy hodnocení (přiřazení #2) párů QnA za běhu.

Druhé hodnocení používá inteligentní filtry, které mohou zahrnovat metadata a výzvy pro následné zpracování.

#### <a name="qna-maker-resource-configuration-settings"></a>Nastavení konfigurace prostředků QnA Maker

Když vytvoříte novou znalostní bázi na [portálu QnA maker](https://qnamaker.ai), nastavení **jazyka** bude jediným nastavením, které se použije na úrovni prostředků. Vyberte jazyk, když vytvoříte první znalostní bázi pro daný prostředek.

### <a name="app-service-and-app-service-plan"></a>Služba App Service a plán služby App Service

[Službu App Service](../../../app-service/index.yml) používá klientská aplikace pro přístup k publikovaným znalostní bázi prostřednictvím koncového bodu modulu runtime.

Aby se publikovala publikovaná znalostní báze, všechny publikované znalostní báze používají stejný koncový bod adresy URL, ale v rámci této trasy zadejte **ID znalostní báze** .

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) se používá ke shromažďování protokolů a telemetrie chatu. Projděte si nejčastější [dotazy Kusto](../how-to/get-analytics-knowledge-base.md) , kde najdete informace o vaší službě.

## <a name="share-services-with-qna-maker"></a>Sdílení služeb pomocí QnA Maker

QnA Maker vytvoří několik prostředků Azure. Pokud chcete snížit úroveň správy a výhod sdílení nákladů, použijte následující tabulku, která vám pomůže pochopit, co můžete a nemůžete sdílet:

|Služba|Sdílení|Důvod|
|--|--|--|
|Cognitive Services|X|Není možné podle návrhu|
|Plán služby App Service|✔|Pevné místo na disku přidělené pro plán App Service. Pokud jiné aplikace sdílející stejný plán App Service používají významné místo na disku, dojde k problémům s instancí App Service Qnamakerem.|
|App Service|X|Není možné podle návrhu|
|Application Insights|✔|Může být sdíleno|
|Služba Search|✔|1. `testkb` je rezervovaný název pro službu qnamakerem; nemůže ji použít jiný.<br>2. `synonym-map` pro službu qnamakerem je vyhrazená mapa synonym podle názvu.<br>3. počet publikovaných znalostní báze je omezený na úrovni služby vyhledávání. Pokud jsou dostupné bezplatné indexy, můžou je používat i jiné služby.|

### <a name="using-a-single-cognitive-search-service"></a>Použití jedné Kognitivní hledání služby

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena s QnA Makerovou službou. Po vytvoření těchto prostředků můžete aktualizovat nastavení App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

Naučte [se, jak nakonfigurovat](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA maker pro použití jiného prostředku služby rozpoznávání, než který je vytvořený jako součást procesu vytváření prostředků QnA maker.

## <a name="management-service-region"></a>Oblast služby správy

Služba správy QnA Maker se používá jenom pro QnA Maker portál a pro počáteční zpracování dat. Tato služba je k dispozici pouze v **západní USA** oblasti. V této Západní USA službě se neukládají žádná zákaznická data.

## <a name="keys-in-qna-maker"></a>Klíče v QnA Maker

Vaše služba QnA Maker se zabývá dvěma druhy klíčů: **vytváření klíčů** a **klíčů koncových bodů dotazů** , které se používají s modulem runtime hostovaným ve službě App Service.

Pokud hledáte svůj **klíč předplatného**, [terminologie se změnila](#subscription-keys).

Tyto klíče použijte při provádění požadavků na službu prostřednictvím rozhraní API.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

|Název|Umístění|Účel|
|--|--|--|
|Vytváření klíče|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Tyto klíče se používají pro přístup k [rozhraním API služby QnA maker Management](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Tato rozhraní API umožňují upravovat otázky a odpovědi ve znalostní bázi a publikovat znalostní bázi. Tyto klíče se vytvoří při vytvoření nové služby QnA Maker.<br><br>Tyto klíče najdete na **Cognitive Services** prostředku na stránce **klíče** .|
|Klíč koncového bodu dotazu|[Portál QnA Makeru](https://www.qnamaker.ai)|Tyto klíče se používají k dotazování publikovaného koncového bodu znalostní báze, aby se zobrazila odpověď pro otázku uživatele. Tento koncový bod dotazu obvykle používáte v robotovi chatu nebo v kódu klientské aplikace, který se připojuje ke službě QnA Maker. Tyto klíče se vytvoří při publikování QnA Maker znalostní báze Knowledge Base.<br><br>Tyto klíče najdete na stránce **nastavení služby** . Vyhledá tuto stránku v nabídce uživatele v pravém horním rohu stránky v rozevírací nabídce.|

### <a name="subscription-keys"></a>Klíče předplatného

Klíčovým bodem pro vytváření a dotazování podmínek jsou opravné výrazy. Předchozí termín byl **klíč předplatného**. Pokud se vám v dokumentaci k klíčům předplatného zobrazí další dokumentace, jsou ekvivalentní klíčům koncových bodů pro vytváření a dotazování (používá se v modulu runtime).

Musíte znát, k čemu má klíč přístup, správu znalostní báze nebo dotazování znalostní báze, abyste věděli, který klíč potřebujete najít.

### <a name="recommended-settings-for-network-isolation"></a>Doporučené nastavení pro izolaci sítě

* Chraňte prostředek služby Service od veřejného přístupu [konfigurací virtuální sítě](../../cognitive-services-virtual-networks.md?tabs=portal).
* Chránit App Service (QnA Runtime) z veřejného přístupu:
    * Povoluje provoz jenom z IP adres služeb rozpoznávání. Ty jsou už součástí značky služby "CognitiveServicesManagement". To se vyžaduje pro vytváření rozhraní API (vytvořit/aktualizovat KB) pro vyvolání služby App Service a aktualizaci služby Azure Search.
    * Ujistěte se, že taky povolíte další vstupní body, jako je robot Service, QnA Maker Portal (může to být váš Corpnet) atd. předpověď přístupu k rozhraní API GenerateAnswer.
    * Podívejte se na [Další informace o značkách služby.](../../../virtual-network/service-tags-overview.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Název prostředku pro prostředek QnA Maker Managed (Preview), například `qna-westus-f0-b` , se používá také k pojmenování dalších prostředků.

Okno Azure Portal vytvořit umožňuje vytvořit prostředek QnA Maker Managed (Preview) a vybrat cenové úrovně pro ostatní prostředky.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky Azure Portal pro vytváření prostředků QnA Maker spravovaném (ve verzi Preview) ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) po vytvoření prostředků mají stejný název.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky Azure Portal seznamu prostředků QnA Maker spravovaného (Preview)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> Při vytváření prostředku QnA Maker vytvořte novou skupinu prostředků. Díky tomu můžete při hledání podle skupiny prostředků Zobrazit všechny prostředky přidružené k prostředku QnA Maker Managed (Preview).

> [!TIP]
> Pomocí konvence vytváření názvů můžete označovat cenové úrovně v rámci názvu prostředku nebo skupiny prostředků. Když obdržíte chyby při vytváření nové znalostní báze nebo přidávání nových dokumentů, je běžným problémem omezení cenové úrovně Kognitivní hledání.

### <a name="resource-purposes"></a>Účely prostředků

Každý prostředek Azure vytvořený pomocí spravovaného QnA Maker (Preview) má určitý účel:

* Prostředek QnA Maker
* Prostředek Kognitivní hledání

### <a name="azure-cognitive-search-resource"></a>Prostředek Azure Kognitivní hledání

Prostředek [kognitivní hledání](../../../search/index.yml) se používá pro:

* Uložit páry QnA
* Zadejte počáteční hodnocení (#1 hodnocení) párů QnA za běhu.

#### <a name="index-usage"></a>Využití indexu

Můžete publikovat N-1 znalostní bázi jednoho jazyka nebo N/2 znalostní báze různých jazyků v určité úrovni, kde N je maximální počet indexů povolený ve vrstvě Azure Kognitivní hledání. Také ověřte maximální velikost a počet dokumentů povolených na jednu úroveň.

Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 základních znalostí stejného jazyka (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování. Pokud se rozhodnete, že budete mít znalostní bázi v různých jazycích, můžete publikovat jenom 7 znalostí znalostní báze.

#### <a name="language-usage"></a>Použití jazyka

S QnA Maker spravované (Preview) máte možnost nastavit službu QnA Maker pro znalostní báze v jednom nebo několika jazycích. Tuto volbu uděláte při vytváření prvního znalostní báze ve službě QnA Maker. V [této](#pricing-tier-considerations) části najdete postup povolení nastavení jazyka pro jednotlivé znalostní báze.

### <a name="qna-maker-resource"></a>Prostředek QnA Maker

Prostředek QnA Maker Managed (Preview) poskytuje přístup k rozhraním API pro vytváření a publikování, hostuje modul runtime hodnocení a poskytuje telemetrii.

## <a name="region-support"></a>Podpora oblastí

V QnA Maker spravovaném (ve verzi Preview) je Správa i předpovědi služeb společně umístěné ve stejné oblasti. Aktuálně QnA Maker spravované (Preview) je k dispozici v **střed USA – jih, Severní Evropa a Austrálii – východ**.

### <a name="keys-in-qna-maker-managed-preview"></a>Klíče ve spravovaném QnA Maker (Preview)

Vaše služba QnA Maker spravovaná (ve verzi Preview) se zabývá dvěma druhy klíčů: **vytváření klíčů** a **klíče Azure kognitivní hledání** používané pro přístup ke službě v rámci předplatného zákazníka.

Pokud hledáte svůj **klíč předplatného**, [terminologie se změnila](#subscription-keys).

Tyto klíče použijte při provádění požadavků na službu prostřednictvím rozhraní API.

![Správa klíčů – Managed Preview](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Název|Umístění|Účel|
|--|--|--|
|Vytváření klíče|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Tyto klíče se používají pro přístup k [rozhraním API služby QnA maker Management](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Tato rozhraní API umožňují upravovat otázky a odpovědi ve znalostní bázi a publikovat znalostní bázi. Tyto klíče se vytvoří při vytvoření nové služby QnA Maker.<br><br>Tyto klíče najdete na **Cognitive Services** prostředku na stránce **klíče** .|
|Klíč správce Azure Kognitivní hledání|[Azure Portal](../../../search/search-security-api-keys.md)|Tyto klíče se používají ke komunikaci se službou Azure vnímání Search nasazenou v předplatném Azure uživatele. Pokud přidružíte vyhledávání rozpoznávání Azure ke službě QnA Maker Managed (Preview), klíč správce se automaticky předává službě QnA Maker. <br><br>Tyto klíče najdete na prostředku **kognitivní hledání Azure** na stránce **klíče** .|

### <a name="subscription-keys"></a>Klíče předplatného

Klíčovým bodem pro vytváření a dotazování podmínek jsou opravné výrazy. Předchozí termín byl **klíč předplatného**. Pokud se vám v dokumentaci k klíčům předplatného zobrazí další dokumentace, jsou ekvivalentní klíčům koncových bodů pro vytváření a dotazování (používá se v modulu runtime).

Musíte znát, k čemu má klíč přístup, správu znalostní báze nebo dotazování znalostní báze, abyste věděli, který klíč potřebujete najít.

### <a name="recommended-settings-for-network-isolation"></a>Doporučené nastavení pro izolaci sítě 

Chraňte prostředek služby Service od veřejného přístupu [konfigurací virtuální sítě](../../cognitive-services-virtual-networks.md?tabs=portal).

---

## <a name="next-steps"></a>Další kroky

* Další informace o QnA Maker [znalostní bázi Knowledge Base](../index.yml)
* Pochopení [životního cyklu znalostní báze](development-lifecycle-knowledge-base.md)
* Kontrola [omezení](../limits.md) služby a znalostí znalostní báze
