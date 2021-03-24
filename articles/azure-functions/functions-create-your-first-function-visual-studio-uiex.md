---
title: 'Rychlý Start: Vytvoření první funkce v Azure pomocí sady Visual Studio'
description: V tomto rychlém startu se dozvíte, jak pomocí sady Visual Studio vytvořit a publikovat funkci Azure Functions triggeru HTTP.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "102050116"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rychlý Start: Vytvoření první funkce v Azure pomocí sady Visual Studio

V tomto článku vytvoříte pomocí sady Visual Studio funkci založenou na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do <abbr title="Běhové výpočetní prostředí, ve kterém jsou všechny podrobnosti serveru transparentní pro vývojáře aplikací, což zjednodušuje proces nasazení a správy kódu.">Prostředí bez serveru</abbr> prostředí pro <abbr title="Služba Azure, která pro aplikace poskytuje výpočetní prostředí bez serveru s nízkými náklady.">Azure Functions</abbr>.

Po dokončení tohoto rychlého startu dojde k malým nákladům na několik centů nebo méně. <abbr title="Profil, který uchovává fakturační informace pro využití Azure">Účet Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

+ Vytvoření Azure <abbr title="Profil, který uchovává fakturační informace pro využití Azure">account</abbr> s aktivní <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Nainstalujte [Visual Studio 2019](https://azure.microsoft.com/downloads/) a při instalaci vyberte úlohu **vývoj pro Azure** . 

![Instalace sady Visual Studio pomocí úlohy vývoj pro Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>Místo toho použít Azure Functions projekt</strong></summary>
Pokud chcete vytvořit <abbr title="Logický kontejner pro jednu nebo více jednotlivých funkcí, které lze nasadit a spravovat společně.">Azure Functions projekt</abbr> pomocí sady Visual Studio 2017 je třeba nejprve nainstalovat [nejnovější nástroje Azure Functions](functions-develop-vs.md#check-your-tools-version).
</details>

## <a name="2-create-a-function-app-project"></a>2. vytvoření projektu Function App

1. V nabídce sady Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**.

1. V části **vytvořit nový projekt** zadejte do vyhledávacího pole *funkce* , vyberte šablonu **Azure Functions** a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt** zadejte **<abbr title=" název aplikace funkcí musí být platný jako obor názvů jazyka C#, proto nepoužívejte podtržítka, spojovníky nebo jakékoli jiné nealfanumerické znaky. "> </abbr>Název projektu** a pak vyberte **vytvořit**. 

1. Pro **Vytvoření nového Azure Functions nastavení aplikace** zadejte následující informace:

    + Když vyberete **<abbr title=" tuto hodnotu, vytvoří se projekt funkce, který používá modul runtime verze 3. x Azure Functions, který podporuje .NET Core 3. x. Azure Functions 1. x podporuje .NET Framework. "> Azure Functions V3 (.NET Core) </abbr>** z rozevíracího seznamu Functions runtime. (Další informace najdete v tématu [Přehled verzí modulu runtime Azure Functions](functions-versions.md).)
    
    + Výběrem **<abbr title=" této hodnoty se vytvoří funkce aktivovaná požadavkem http. "> Aktivační procedura </abbr> http** jako šablona funkce
    
    + Vyberte **<abbr title=" , protože funkce Azure vyžaduje účet úložiště, při publikování projektu do Azure se přiřadí nebo vytvoří. Aktivační událost protokolu HTTP nepoužívá připojovací řetězec Azure Storage účtu; všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu Azure Storage. "> Emulátor </abbr> úložiště** z rozevíracího seznamu účtu úložiště
        
    + Vyberte možnost **anonymní** z <abbr title="Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí.">Úroveň autorizace</abbr> rozevírací. (Další informace o klíčích a autorizaci najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys) a [vazby HTTP a Webhooku](functions-bindings-http-webhook.md).)

    + Vyberte **Vytvořit**.
        
## <a name="3-rename-the-function"></a>3. Přejmenování funkce

`FunctionName`Atribut Method nastaví název funkce, která je ve výchozím nastavení generována jako `Function1` . Vzhledem k tomu, že nástroje neumožňují při vytváření projektu přepsat výchozí název funkce, vytvoření lepšího názvu pro třídu funkce, soubor a metadata vydejte za minutu.

1. V **Průzkumníku souborů** klikněte pravým tlačítkem na soubor function1. cs a přejmenujte ho na *HttpExample. cs*.

1. V kódu přejmenujte třídu Function1 na ' HttpExample '.

1. V `HttpTrigger` metodě s názvem `Run` , přejmenujte `FunctionName` atribut Method na `HttpExample` .


## <a name="4-run-the-function-locally"></a>4. Spusťte funkci místně.

1. Chcete-li spustit funkci, stiskněte klávesu <kbd>F5</kbd> v aplikaci Visual Studio.

1. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Do této adresy URL přidejte řetězec dotazu **? Name =<YOUR_NAME>** a spusťte požadavek. 

    ![Odezva místního hostitele funkce v prohlížeči](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. Pokud chcete zastavit ladění, stiskněte <kbd>SHIFT</kbd> + <kbd>F5</kbd> v aplikaci Visual Studio.

<br/>
<details>
<summary><strong>Řešení potíží</strong></summary>
 Možná budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP. Při místním spuštění funkce se nikdy neuplatní úrovně autorizace.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. publikujte projekt do Azure.

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V **cíli** vyberte **Azure** .

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Vybrat cíl Azure":::

1. V **konkrétním cíli** vyberte **Azure Function App (Windows)** .

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Vyberte Azure Function App":::

1. V části **instance funkce** vyberte **vytvořit novou funkci Azure Functions...** a pak použijte hodnoty uvedené v následujícím seznamu:

    + Pro **název** zadejte <abbr title="Použijte název, který jedinečně identifikuje vaši novou aplikaci Function App. Přijměte tento název nebo zadejte nový název. Platné znaky jsou: `a-z` , `0-9` , a `-` ..">Globálně jedinečný název</abbr>
    
    + V rozevíracím seznamu **Vyberte** předplatné.
    
    + **Vybrat** existující <abbr title="Logický kontejner pro související prostředky Azure, které můžete spravovat jako jednotku.">skupina prostředků</abbr> v rozevíracím seznamu nebo vyberte možnost **nové** a vytvořte novou skupinu prostředků.
    
    + **Výběr** <abbr title="Když publikujete projekt do aplikace Function App, která běží v plánu spotřeby, platíte jenom za spuštění vaší aplikace Functions. Jiné plány hostování účtují vyšší náklady.">Využití</abbr> v rozevíracím seznamu typ přehrávání. (Další informace najdete v tématu [plán spotřeby](consumption-plan.md).)
    
    + **Vyberte**  <abbr title="Zeměpisná reference na konkrétní datacentrum Azure, ve kterém jsou prostředky přiděleny. Seznam oblastí, které jsou k dispozici, najdete v části [oblasti](https://azure.microsoft.com/regions/) .">location</abbr> z rozevíracího seznamu.
    
    + **Vyberte** <abbr = "modul runtime funkcí vyžaduje účet Azure Storage. Vyberte nový a nakonfigurujte účet úložiště pro obecné účely. Můžete také zvolit existující účet, který splňuje požadavky účtu úložiště. " >Azure Storage</abbr> účet z rozevírací nabídky

    ![Dialogové okno Vytvoření služby App Service](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Vyberte **Vytvořit**. 

1. V **instanci Functions** se ujistěte, že je zaškrtnuté políčko **Spustit ze souboru balíčku** . 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Dokončit vytvoření profilu":::

    <br/>
    <details>
    <summary><strong>Co toto nastavení dělá?</strong></summary>
    Při použití rutiny **Run ze souboru balíčku** je aplikace Function App nasazena pomocí nástroje [zip Deploy](functions-deployment-technologies.md#zip-deploy) s povoleným režimem [spuštění z balíčku](run-functions-from-deployment-package.md) . Toto je doporučená metoda nasazení pro váš projekt Functions, protože má za následek lepší výkon.    
    </details>   

1. Vyberte **Dokončit**.

1. Na stránce Publikovat vyberte **Publikovat**.

1. Na stránce Publikovat zkontrolujte kořenovou adresu URL aplikace Function App.

1. Na kartě publikovat vyberte v **<abbr title=" Průzkumníku cloudu možnost spravovat, abyste mohli zobrazit obsah webu, spustit a zastavit aplikaci Function App a přejít přímo na prostředky aplikace Function App v Azure a v Azure Portal. "> </abbr>Průzkumník cloudu**
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Zpráva o úspěšném publikování":::
    

## <a name="6-test-your-function-in-azure"></a>6. otestujte funkci v Azure

1. V Průzkumníku cloudu by se měla vybrat nová aplikace Function App. Pokud ne, rozbalte své předplatné, rozbalte **App Services** a vyberte svou novou aplikaci Function App.

1. Klikněte pravým tlačítkem na aplikaci Function App a vyberte **otevřít v prohlížeči**. Tím se otevře kořen vaší aplikace Function App ve výchozím webovém prohlížeči a zobrazí se stránka, která indikuje, že je spuštěná vaše aplikace Function App. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Spuštěná aplikace Function App":::

1. Do panelu Adresa v prohlížeči přidejte řetězec **/API/HttpExample? Name =** k základní adrese URL a spusťte požadavek.

    Adresa URL, která volá funkci triggeru HTTP, je v následujícím formátu:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Přejděte na tuto adresu URL a v prohlížeči se zobrazí odpověď na požadavek na vzdálenou kopii vrácenou funkcí, který vypadá podobně jako v následujícím příkladu:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Odezva funkce v prohlížeči":::

## <a name="7-clean-up-resources"></a>7. vyčištění prostředků

Odstraňte aplikaci funkcí a její prostředky, abyste se vyhnuli dalším nákladům.

1. V Průzkumníku cloudu rozbalte své předplatné, rozbalte **App Services**, klikněte pravým tlačítkem myši na svou aplikaci Function App a vyberte **otevřít na portálu**. 

1. Na stránce Function App vyberte kartu **Přehled** a potom vyberte odkaz v části **Skupina prostředků**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Vyberte skupinu prostředků, kterou chcete odstranit ze stránky Function App.":::

1. Na stránce **Skupina prostředků** zkontrolujte seznam zahrnutých prostředků a ověřte, že se jedná o ty, které chcete odstranit.
 
1. Vyberte **Odstranit skupinu prostředků** a postupujte podle pokynů.

    Odstranění může trvat několik minut. Po jeho dokončení se na několik sekund zobrazí oznámení. K zobrazení tohoto oznámení můžete také vybrat ikonu zvonku v horní části stránky.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak přidat <abbr title="Způsob, jak přidružit funkci k frontě úložiště, aby mohla vytvářet zprávy ve frontě. Vazby jsou deklarativní připojení mezi funkcí a dalšími prostředky. Vstupní vazba poskytuje data funkci. výstupní vazba poskytuje data z funkce do jiných prostředků.">Výstupní vazba fronty Azure Storage</abbr> na funkci:

> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage k funkci](functions-add-output-binding-storage-queue-vs.md)
