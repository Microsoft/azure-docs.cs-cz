---
title: Správa zdrojového kódu a vývojové větve – LUIS
description: Postup udržování aplikace Language Understanding (LUIS) v rámci správy zdrojového kódu. Jak použít aktualizace aplikace LUIS při práci ve vývojové větvi.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: e49b713aca23c0373fa71d772ef7567372abe456
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990563"
---
# <a name="devops-practices-for-luis"></a>DevOps postupy pro LUIS

Technici softwaru, kteří vyvíjí aplikaci Language Understanding (LUIS), mohou uplatnit DevOps postupy kolem [správy zdrojového kódu](luis-concept-devops-sourcecontrol.md), [automatizované sestavení](luis-concept-devops-automation.md), [testování](luis-concept-devops-testing.md)a [správy](luis-concept-devops-automation.md#release-management) vydaných verzí pomocí následujících pokynů.

## <a name="source-control-and-branch-strategies-for-luis"></a>Správa zdrojového kódu a strategie větvení pro LUIS

Jedním z klíčových faktorů, na kterých je úspěch DevOps závislý, je [Správa zdrojového kódu](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops). Systém správy zdrojového kódu umožňuje vývojářům spolupracovat na kódu a sledovat změny. Použití větví umožňuje vývojářům přepínat mezi různými verzemi základu kódu a pracovat nezávisle na jiných členech týmu. Když vývojáři vyvolají [žádost o](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) přijetí změn (PR), aby navrhli aktualizace z jedné větve do druhé nebo když jsou změny sloučeny, mohou být triggerem pro [automatizované sestavení](luis-concept-devops-automation.md) pro sestavení a průběžný testování kódu.

Pomocí konceptů a návodů, které jsou popsány v tomto dokumentu, můžete vyvíjet aplikaci LUIS při sledování změn v systému správy zdrojů a postupovat podle osvědčených postupů pro Software Engineering:

- **Správa zdrojového kódu**
  - Zdrojový kód aplikace pro LUIS je ve formátu čitelném pro člověka.
  - Model může být sestavený ze zdroje způsobem, který lze opakovaně vytvořit.
  - Zdrojový kód lze spravovat pomocí úložiště zdrojového kódu.
  - Přihlašovací údaje a tajné klíče, jako jsou vytváření a klíče předplatného, se ve zdrojovém kódu nikdy neukládají.

- **Větvení a slučování**
  - Vývojáři mohou pracovat z nezávislých větví.
  - Vývojáři mohou současně pracovat v několika větvích.
  - Je možné integrovat změny do aplikace LUIS z jedné větve do jiné prostřednictvím přenesení změn nebo sloučení.
  - Vývojáři mohou sloučit žádosti o přijetí změn s nadřazenou větví.

- **Správa verzí**
  - Jednotlivé komponenty ve velkých aplikacích by měly být nezávislé na verzi a umožňují vývojářům detekovat zásadní změny nebo aktualizace pouhým vyhledáním čísla verze.

- **Revize kódu**
  - Změny v žádosti o přijetí změn se zobrazí jako zdrojový kód pro čtení, který je možné před přijetím žádosti o přijetí změn zkontrolovat.

## <a name="source-control"></a>Správa zdrojového kódu

Chcete-li zachovat [definici schématu aplikace](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition) aplikace Luis v systému správy zdrojového kódu, použijte reprezentaci aplikace [LUDown Format ( `.lu` )](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  . `.lu` formát je preferovaný `.json` , protože je čitelný pro člověka, což usnadňuje provádění a kontrolu změn v pr.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Uložení aplikace LUIS pomocí formátu LUDown

Pokud chcete uložit aplikaci LUIS ve `.lu` formátu a umístit ji pod správu zdrojového kódu:

- BUĎ: [exportujte verzi aplikace](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) `.lu` z [portálu Luis](https://www.luis.ai/) a přidejte ji do úložiště správy zdrojového kódu.

- NEBO: pomocí textového editoru vytvořte `.lu` soubor pro aplikaci Luis a přidejte ji do úložiště správy zdrojového kódu.

> [!TIP]
> Pokud pracujete s exportem JSON aplikace LUIS, můžete [ji převést na LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) pomocí rozhraní [příkazového řádku BOTBUILDER-Tools Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS). Tuto `--sort` možnost použijte, chcete-li zajistit, aby byly záměry a projevy seřazené abecedně.  
> Všimněte si, že **. Funkce exportu logické jednotky** , která je součástí portálu Luis, již seřadí výstup.

### <a name="build-the-luis-app-from-source"></a>Sestavení aplikace LUIS ze zdroje

Pro aplikaci LUIS, která se má vytvořit *ze zdrojové* [verze, vytvoří novou verzi aplikace Luis importováním `.lu` zdroje](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) , [vyškolí verzi](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) a [publikuje ji](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app). Můžete to provést na portálu LUIS nebo na příkazovém řádku:

- Portál LUIS slouží k [importu `.lu` verze](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) aplikace ze správy zdrojového kódu a k jejímu [zaškolení](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) a [publikování](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) aplikace.

- Použijte [rozhraní příkazového řádku bot Framework pro Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) v příkazovém řádku nebo v pracovním postupu CI/CD pro [Import](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) `.lu` verze aplikace ze správy zdrojového kódu do aplikace Luis a [školení](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) a [publikování](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) aplikace.

### <a name="files-to-maintain-under-source-control"></a>Soubory, které se mají spravovat pod správou zdrojových kódů

V rámci správy zdrojového kódu by měly být zachovány následující typy souborů pro aplikaci LUIS:

- `.lu` soubor pro aplikaci LUIS

- [Soubory definice testu jednotek](luis-concept-devops-testing.md#writing-tests) (projevy a očekávané výsledky)

- [Soubory dávkového testu](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format) (projevy a očekávané výsledky) použité pro testování výkonu

### <a name="credentials-and-keys-are-not-checked-in"></a>Přihlašovací údaje a klíče nejsou vráceny se změnami.

Nezahrnujte klíče předplatného ani podobné důvěrné hodnoty do souborů, které jste se změnami navrátit do úložiště, kde můžou být viditelné pro neoprávněné zaměstnance. Mezi klíče a další hodnoty, které byste měli zabránit vrácení se změnami, patří:

- LUIS vytváření a předpověď klíčů
- Koncové body vytváření a předpovědi LUIS
- Klíče předplatného Azure
- Přístupové tokeny, jako je token pro [instanční objekt](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) Azure, který se používá pro ověřování služby Automation

#### <a name="strategies-for-securely-managing-secrets"></a>Strategie pro bezpečnou správu tajných kódů

Mezi strategie pro bezpečnou správu tajných klíčů patří:

- Pokud používáte správu verzí Gitu, můžete ukládat tajné klíče za běhu do místního souboru a zabránit vrácení souboru se změnami, a to přidáním vzoru, který bude odpovídat názvu souboru [. gitignore.](https://git-scm.com/docs/gitignore)
- V pracovním postupu automatizace můžete tajné kódy bezpečně ukládat do konfigurace parametrů, kterou nabízí tato technologie automatizace. Pokud například používáte [Akce GitHubu](https://github.com/features/actions), můžete tajné kódy bezpečně ukládat do [tajných kódů GitHubu](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Větvení a slučování

Distribuované systémy správy verzí, jako je git, poskytují flexibilitu v tom, jak členové týmu publikují, sdílejí, kontrolují a iterují změny kódu prostřednictvím větví vývoje sdílených s ostatními. Proveďte [strategii větvení Git](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance) , která je vhodná pro váš tým.

Jakékoli strategie větvení, kterou přijmete, je klíčovým principem všech z nich, že členové týmu mohou pracovat na řešení v rámci *větve funkcí* nezávisle na práci, která se nachází v jiných větvích.

Podpora nezávislého fungování v větvích s projektem LUIS:

- **Hlavní větev má svou vlastní aplikaci pro LUIS.** Tato aplikace představuje aktuální stav vašeho řešení pro váš projekt a jeho aktuální aktivní verze by měla být vždy namapována na `.lu` zdroj, který je ve větvi Master. Všechny aktualizace `.lu` zdroje pro tuto aplikaci by měly být přezkoumány a testovány, aby bylo možné tuto aplikaci nasadit do prostředí pro vytváření prostředí, jako je například výroba. Pokud jsou aktualizace do `.lu` hlavní větve sloučeny z větve funkcí, měli byste v aplikaci Luis vytvořit novou verzi a přeložit [číslo verze](#versioning).

- **Každá větev funkce musí používat svou vlastní instanci aplikace Luis**. Vývojáři pracují s touto aplikací ve větvi funkcí bez rizika ovlivnění vývojářů, kteří pracují v jiných větvích. Tato aplikace pro vývojovou větev je pracovní kopie, která se má odstranit při odstranění větve funkce.

![Větev funkcí Git](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Vývojáři můžou pracovat z nezávislých větví

Vývojáři můžou pracovat na aktualizacích aplikace v LUIS nezávisle na jiných větvích:

1. Vytvoření větve funkce z hlavní větve (v závislosti na strategii vaší větve, obvykle na hlavním nebo vývojovém).

1. [Vytvořte novou aplikaci Luis na portálu Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app) (*aplikace pro vývojovou větev*), která podporuje výhradně práci ve větvi funkce.

   * Pokud `.lu` zdroj vašeho řešení již existuje ve větvi, protože byl uložen po dokončení práce v jiné větvi dříve v projektu, vytvořte Luis aplikaci pro vývojovou větev pomocí importu `.lu` souboru.

   * Pokud spouštíte práci na novém projektu, zatím nebudete mít `.lu` zdroj pro svou hlavní aplikaci Luis v úložišti. Soubor vytvoříte tak, `.lu` že svou aplikaci pro vývojovou větev vyexportujete z portálu, když jste dokončili práci ve větvi vaší funkce, a odešlete ji jako součást vaší žádosti o přijetí změn.

1. K implementaci požadovaných změn Pracujte na aktivní verzi aplikace pro vývojovou větev. Doporučujeme pracovat pouze v jediné verzi aplikace pro vývojovou větev pro veškerou funkci práce s větví. Pokud vytvoříte více než jednu verzi v aplikaci pro vývojovou větev, buďte opatrní, abyste sledovali, která verze obsahuje změny, které chcete vrátit se změnami při vyvolání žádosti o přijetí změn.

1. Testování aktualizací – podrobnosti o testování aplikace pro vývojovou větev najdete v tématu [testování pro Luis DevOps](luis-concept-devops-testing.md) .

1. Exportujte aktivní verzi aplikace pro vývojovou větev `.lu` ze [seznamu verzí](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions).

1. Zaregistrujte aktualizace a pozvěte druhou kontrolu vašich aktualizací. Pokud používáte GitHub, vyvoláte [žádost o](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)přijetí změn.

1. Pokud jsou změny schváleny, sloučí aktualizace do hlavní větve. V tomto okamžiku vytvoříte novou [verzi](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) *hlavní* aplikace Luis pomocí aktualizovaného `.lu` v hlavní části. Pokyny k nastavení názvu verze najdete v tématu [Správa verzí](#versioning) .

1. Když je větev funkce odstraněna, je vhodné odstranit aplikaci pro vývojovou větev LUIS, kterou jste vytvořili pro funkci pracovní větve.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Vývojáři mohou současně pracovat v několika větvích.

Pokud budete postupovat podle vzoru popsaného výše v tématu [vývojáři můžou pracovat z nezávislých větví](#developers-can-work-from-independent-branches), pak v každé větvi funkce použijete jedinečnou aplikaci Luis. Jeden vývojář může pracovat souběžně s několika větvemi, pokud se přepne do správné aplikace LUIS pro vývojovou větev pro větev, na které aktuálně pracují.

Doporučujeme použít stejný název pro větev funkcí i pro aplikaci LUIS pro vývojovou větev, kterou vytvoříte pro práci ve větvi funkce, aby bylo méně pravděpodobný, že nebudete muset omylně fungovat na nesprávné aplikaci.

Jak je uvedeno výše, doporučujeme, abyste pro zjednodušení pracovali v jedné verzi v každé aplikaci pro vývoj bran. Pokud používáte víc verzí, při přepínání mezi aplikacemi pro vývojovou větev se ujistěte, že jste si aktivovali správnou verzi.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Současně může ve stejné větvi fungovat více vývojářů

Současně můžete podporovat více vývojářů pracujících na stejné větvi funkcí:

- Vývojáři si můžou zaregistrovat stejnou větev funkcí a nabízené změny a žádosti o přijetí změn, které odeslali sami a další vývojáři, zatímco fungují normálně.

- Pokud budete postupovat podle vzoru popsaného výše v tématu [vývojáři můžou pracovat z nezávislých větví](#developers-can-work-from-independent-branches), pak tato větev bude k podpoře vývoje používat jedinečnou aplikaci Luis. Tato aplikace LUIS pro vývojovou větev bude vytvořena prvním členem vývojového týmu, který začíná pracovat ve větvi funkce.

- [Přidejte členy týmu jako přispěvatele](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate) do aplikace dev Branch Luis.

- Když je dokončená větev funkcí, exportujte aktivní verzi aplikace dev Work LUIS jako `.lu` v [seznamu verze](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions), uložte aktualizovaný `.lu` soubor do úložiště a vraťte se změnami a přičtěte změny.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Zahrnutí změn z jedné větve do druhé se přenesením změn nebo sloučením

Někteří jiní vývojáři v týmu, kteří pracují v jiné větvi, mohli ve svém týmu aktualizovat `.lu` zdroj a sloučit je do hlavní větve až po vytvoření vaší větve funkce. Před tím, než budete moci provádět vlastní změny v rámci větve funkcí, můžete chtít své změny začlenit do své pracovní verze. To můžete provést [přenesením změn nebo sloučením do Hlavní](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) větve stejným způsobem jako jakýkoli jiný prostředek kódu. Vzhledem k tomu, že aplikace LUIS ve formátu LUDown je humánní čitelnost, podporuje sloučení pomocí standardních nástrojů pro sloučení.

Pokud vaše aplikace LUIS přenášíte do větve funkcí, postupujte podle těchto tipů:

- Před přenesením nebo sloučením se ujistěte, že vaše místní kopie `.lu` zdrojového kódu vaší aplikace obsahuje všechny nejnovější změny, které jste použili na portálu Luis, a to tak, že aplikaci znovu exportujete z portálu. Tímto způsobem se můžete ujistit, že jakékoli změny, které jste provedli na portálu a ještě nejsou exportovány, nebudou ztraceny.

- Během sloučení můžete vyřešit všechny konflikty sloučení pomocí standardních nástrojů.

- Nezapomeňte po přenesení změn nebo sloučení dokončit, aby se znovu naimportovala aplikace zpátky na portál, takže budete pracovat s aktualizovanou aplikací, protože budete dál používat vlastní změny.

### <a name="merge-prs"></a>Sloučit PR

Po schválení žádosti o přijetí změn můžete sloučit změny do své hlavní větve. Na zdroj LUDown pro aplikaci LUIS se nevztahují žádné zvláštní požadavky: Jedná se o lidské čtení a podporuje slučování pomocí standardních nástrojů pro sloučení. Jakékoli konflikty sloučení lze vyřešit stejným způsobem jako u jiných zdrojových souborů.

Po sloučení žádosti o přijetí změn se doporučuje vyčistit:

- Odstranění větve ve vašem úložišti

- Odstraňte aplikaci LUIS pro vývojovou větev, kterou jste vytvořili pro práci ve větvi funkce.

Stejným způsobem jako u prostředků kódu aplikace byste měli napsat testy jednotek, které doprovázejí aktualizace aplikací LUIS. Měli byste využít pracovní postupy průběžné integrace k testování:

- Aktualizace v žádosti o přijetí změn před sloučením žádosti o přijetí změn
- Hlavní aplikace LUIS po schválení žádosti o přijetí změn a změny byly sloučeny do hlavní větve.

Další informace o testování pro LUIS DevOps najdete v tématu [testování pro DevOps pro Luis](luis-concept-devops-testing.md). Další podrobnosti o implementaci pracovních postupů najdete v tématu [pracovní postupy automatizace pro Luis DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Revize kódu

Aplikace LUIS ve formátu LUDown je humánní čitelnost, která podporuje komunikaci se změnami v žádosti o přijetí změn, která je vhodná ke kontrole. Soubory testování částí jsou také zapsány ve formátu LUDown a lze je snadno zobrazit v žádosti o přijetí změn.

## <a name="versioning"></a>Správa verzí

Aplikace se skládá z několika komponent, které můžou zahrnovat věci, jako je robot běžící v [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QnA maker](https://www.qnamaker.ai/), [Služba Azure Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)a další. Aby bylo možné dosáhnout cíle volně vázaných aplikací, použijte [řízení verze](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control) tak, aby každá komponenta aplikace byla oddělená od verze, a umožní vývojářům detekovat zásadní změny nebo aktualizace pouze tím, že si prohlíží číslo verze. Aplikaci LUIS je snazší používat nezávisle na jiných součástech, pokud ji udržujete ve vlastním úložišti.

Aplikace LUIS pro hlavní větev by měla mít použité schéma správy verzí. Když sloučíte aktualizace do `.lu` aplikace pro Luis do hlavní větve, naimportujete aktualizovaný zdroj do nové verze v aplikaci Luis pro hlavní větev.

Doporučuje se pro hlavní verzi aplikace LUIS použít schéma numerických verzí, například:

`major.minor[.build[.revision]]`

Každá aktualizace číslo verze se zvýší na poslední číslici.

Hlavní a vedlejší verzi lze použít k označení rozsahu změn funkce aplikace LUIS:

* Hlavní verze: významná změna, například podpora pro nový [záměr](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent) nebo [entitu](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)
* Podverze: zpětně kompatibilní menší změny, například po významném novém školení
* Sestavení: žádné změny funkčnosti, pouze jiné sestavení.

Jakmile určíte číslo verze pro nejnovější revizi hlavní aplikace LUIS, je potřeba sestavit a otestovat novou verzi aplikace a publikovat ji na koncový bod, kde se dá použít v různých prostředích buildu, jako je například zabezpečování kvality nebo produkce. V pracovním postupu průběžná integrace (CI) se důrazně doporučuje automatizovat všechny tyto kroky.

Přečtěte si:
- [Pracovní postupy služby Automation](luis-concept-devops-automation.md) pro podrobnosti o implementaci pracovního postupu CI pro otestování a vydání aplikace Luis
- [Release Management](luis-concept-devops-automation.md#release-management) informace o tom, jak nasadit aplikaci Luis.

### <a name="versioning-the-feature-branch-luis-app"></a>Správa verzí aplikace LUIS pro větev funkcí

Když pracujete s aplikací LUIS vývojová větev, kterou jste vytvořili pro podporu práce ve větvi funkce, budete aplikaci exportovat po dokončení práce a v žádosti o přijetí změn zahrnete aktualizovaný `'lu` . Větev v úložišti a aplikace LUIS pro vývojovou větev by se měla po sloučení žádosti o přijetí změn do hlavní větve odstranit. Vzhledem k tomu, že tato aplikace existuje výhradně pro podporu práce ve větvi funkce, neexistuje žádné konkrétní schéma správy verzí, které by bylo potřeba v této aplikaci použít.

Když jsou změny v žádosti o přijetí změn sloučeny do hlavní větve, to znamená, že by se měla použít Správa verzí, aby všechny aktualizace Master byly nezávislé na verzi.

## <a name="next-steps"></a>Další kroky

* Další informace o [testování pro Luis DevOps](luis-concept-devops-testing.md)
* Naučte se [implementovat DevOps pro Luis s GitHubem](luis-how-to-devops-with-github.md) .
