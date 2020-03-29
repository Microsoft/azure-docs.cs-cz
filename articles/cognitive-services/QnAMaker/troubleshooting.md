---
title: Řešení potíží – QnA Maker
description: Sestavený seznam nejčastěji kladených otázek týkajících se služby QnA Maker vám pomůže přijmout službu rychleji a s lepšími výsledky.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: e002efe74bf7bcd3d944b01b0a25a731a2db3f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284258"
---
# <a name="troubleshooting-for-qna-maker"></a>Řešení potíží pro QnA Maker

Sestavený seznam nejčastěji kladených otázek týkajících se služby QnA Maker vám pomůže přijmout službu rychleji a s lepšími výsledky.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Správa předpovědí

<details>
<summary><b>Jak lze zlepšit výkon propustnost pro předpovědi dotazů?</b></summary>

**Odpověď**: Problémy s výkonem propustností naznačují, že je třeba vertikálně navýšit kapacitu pro vaši službu App service i kognitivní vyhledávání. Chcete-li zlepšit výkon, zvažte přidání repliky do kognitivního vyhledávání.

Přečtěte si další informace o [cenových úrovních](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Jak získat koncový bod služby QnAMaker</b></summary>

**Odpověď**: Koncový bod služby QnAMaker je užitečný pro účely ladění, když kontaktujete podporu QnAMaker nebo UserVoice. Koncový bod je adresa URL https://your-resource-name.azurewebsites.netv tomto formuláři: .

1. Přejděte na službu QnAMaker (skupinu prostředků) na [webu Azure Portal.](https://portal.azure.com)

    ![Skupina prostředků Azure QnAMaker na webu Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte službu App Service přidruženou k prostředku QnA Maker. Obvykle jsou názvy stejné.

     ![Vybrat službu aplikace QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Adresa URL koncového bodu je k dispozici v části Přehled

    ![Koncový bod QnAMakeru](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Správa znalostní báze

<details>
<summary><b>Omylem jsem smazal část svého QnA Makeru, co mám dělat?</b></summary>

**Odpověď**: Neodstraňujte žádnou ze služeb Azure vytvořených spolu s prostředkem QnA Maker, jako je vyhledávání nebo webová aplikace. Ty jsou nezbytné pro QnA Maker pracovat, pokud odstraníte jeden, QnA Maker přestane pracovat správně.

Všechny odstranění jsou trvalé, včetně párů otázek a odpovědí, souborů, adres URL, vlastních otázek a odpovědí, znalostních bází nebo prostředků Azure. Před odstraněním jakékoli části znalostní báze nezapomeňte exportovat znalostní bázi ze stránky **Nastavení.**

</details>

<details>
<summary><b>Proč moje adresy URL/soubory neextrahují dvojice otázek a odpovědí?</b></summary>

**Odpověď**: Je možné, že QnA Maker nemůže automaticky extrahovat nějaký obsah otázek a odpovědí (QnA) z platných adres URL s nejčastějšími dotazy. V takových případech můžete vložit obsah QnA do souboru TXT a zjistit, zda jej nástroj může ingestovat. Případně můžete redakčním nastavením přidávat obsah do znalostní báze prostřednictvím [portálu QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Jak velkou znalostní bázi mohu vytvořit?</b></summary>

**Odpověď**: Velikost znalostní báze závisí na skladové jednotce azure vyhledávání, které zvolíte při vytváření služby QnA Maker. Přečtěte si [zde](./Tutorials/choosing-capacity-qnamaker-deployment.md) pro více informací.

</details>

<details>
<summary><b>Proč nevidím nic v rozevíracím panelu při pokusu o vytvoření nové znalostní báze?</b></summary>

**Odpověď**: V Azure jste ještě nevytvořili žádné služby QnA Maker. Přečtěte si [zde](./How-To/set-up-qnamaker-service-azure.md) se dozvíte, jak to udělat.

</details>

<details>
<summary><b>Jak mohu sdílet znalostní bázi s ostatními?</b></summary>

**Odpověď**: Sdílení funguje na úrovni služby QnA Maker, to znamená, že budou sdíleny všechny znalostní báze ve službě. Přečtěte [si,](./How-To/collaborate-knowledge-base.md) jak spolupracovat na znalostní bázi.

</details>

<details>
<summary><b>Můžete sdílet znalostní bázi s přispěvatelem, který není ve stejném tenantovi AAD, a upravit znalostní bázi?</b></summary>

**Odpověď**: Sdílení je založeno na řízení přístupu na základě rolí Azure (RBAC). Pokud můžete sdílet _libovolný_ prostředek v Azure s jiným uživatelem, můžete také sdílet QnA Maker.

</details>

<details>
<summary><b>Pokud máte plán služby App Service s 5 znalostními bázemi QnAMaker. Můžete přiřadit práva pro čtení a zápis 5 různým uživatelům, aby každý z nich měl přístup pouze k 1 znalostní bázi QnAMaker?</b></summary>

**Odpověď**: Můžete sdílet celou službu QnAMaker, nikoli jednotlivé znalostní báze.

</details>

<details>
<summary><b>Jak mohu změnit výchozí zprávu, když není nalezena žádná dobrá shoda?</b></summary>

**Odpověď**: Výchozí zpráva je součástí nastavení ve službě App Service.
- Přejděte na prostředek služby App service na webu Azure Portal.

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klikněte na možnost **Nastavení.**

![qnamaker appservice nastavení](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Změna hodnoty nastavení **DefaultAnswer**
- Restartování služby App Service

![restartování služby aplikace qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Proč se můj odkaz na SharePoint neextrahuje?</b></summary>

**Odpověď**: Další informace naleznete v [tématu Umístění zdrojů dat.](./Concepts/knowledge-base.md#data-source-locations)

</details>

<details>
<summary><b>Aktualizace, které jsem provedl(a) ve své znalostní bázi, se neprojeví v publikování. Proč ne?</b></summary>

**Odpověď**: Před publikováním je třeba uložit každou operaci úprav, ať už v aktualizaci tabulky, test nebo nastavení. Nezapomeňte kliknout na tlačítko **Uložit a trénovat** po každé operaci úprav.

</details>

<details>
<summary><b>Podporuje znalostní báze multimédia?</b></summary>

**Odpověď**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatické odsávání multimédií pro soubory a adresy URL

* Adresy URL – omezená možnost převodu HTML na Markdown.
* Soubory - není podporováno

#### <a name="answer-text-in-markdown"></a>Odpovědět na text v markdownu
Jakmile jsou sady QnA ve znalostní bázi, můžete upravit text markdownu odpovědi tak, aby obsahoval odkazy na média dostupná z veřejných adres URL.


</details>

<details>
<summary><b>Podporuje QnA Maker neanglické jazyky?</b></summary>

**Odpověď**: Další podrobnosti o [podporovaných jazycích](./Overview/languages-supported.md)naleznete v

Pokud máte obsah z více jazyků, nezapomeňte vytvořit samostatnou službu pro každý jazyk.

</details>

## <a name="manage-service"></a>Správa služby

<details>
<summary><b>Kdy mám službu app service restartovat?</b></summary>

**Odpověď**: Aktualizujte službu aplikace, když je ikona upozornění vedle hodnoty verze znalostní báze v tabulce **Klíče koncového bodu** na [stránce](https://www.qnamaker.ai/UserSettings) **Nastavení uživatele** .

</details>

<details>
<summary><b>Odstranil jsem stávající vyhledávací službu. Jak to mohu opravit?</b></summary>

**Odpověď**: Pokud odstraníte index Azure Cognitive Search, operace je konečná a index nelze obnovit.

</details>

<details>
<summary><b>Ve vyhledávací `testkb` službě jsem index smazal. Jak to mohu opravit?</b></summary>

**Odpověď:** Stará data nelze obnovit. Vytvořte nový prostředek QnA Maker u znovu a vytvořte znalostní bázi.

</details>

<details>
<summary><b>Kdy mám aktualizovat klíče koncového bodu?</b></summary>

**Odpověď**: Aktualizujte klíče koncového bodu, pokud máte podezření, že byly ohroženy.

</details>

<details>
<summary><b>Můžu použít stejný prostředek Azure Cognitive Search pro znalostní báze používající více jazyků?</b></summary>

**Odpověď**: Chcete-li používat více jazyků a více znalostních bází, musí uživatel pro každý jazyk vytvořit prostředek qnA makeru. Tím se vytvoří samostatná vyhledávací služba Azure pro každý jazyk. Smíchání různých jazykových znalostních bází v jedné vyhledávací službě Azure bude mít za následek sníženou relevanci výsledků.

</details>

<details>
<summary><b>Jak můžu změnit název prostředku Azure Cognitive Search používaného qnA makerem?</b></summary>

**Odpověď**: Název prostředku Azure Cognitive Search je název prostředku QnA Maker s některými náhodnými písmeny připojenými na konci. To ztěžuje rozlišení mezi více vyhledávacích prostředků pro QnA Maker. Vytvořte samostatnou vyhledávací službu (pojmenujte ji tak, jak byste chtěli) a připojte ji ke službě QnA. Kroky jsou podobné krokům, které je třeba provést při [upgradu vyhledávání Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Když QnA `Runtime core is not initialized,` Maker vrátí, jak to mohu opravit?</b></summary>

**Odpověď**: Místo na disku pro službu aplikace může být plné. Postup opravy místa na disku:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte službu aplikace QnA Maker a službu zastavte.
1. Pokud jste ještě ve službě App Service, vyberte **vývojové nástroje**, pak **pokročilé nástroje**a pak **přejít**. Otevře se nové okno prohlížeče.
1. Vyberte **ladicí konzolu**a pak **cmd** otevřete nástroj příkazového řádku.
1. Přejděte do adresáře _site/wwwroot/Data/QnAMaker/._
1. Odeberte všechny složky, `rd`jejichž název začíná písmenem .

    **Neodstraňujte** následující:

    * KbIdToRankerMappings.txt soubor
    * Soubor EndpointSettings.json
    * Složka EndpointKeys

1. Spusťte službu App Service.
1. Získejte přístup ke své znalostní bázi a ověřte, zda nyní funguje.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrace s dalšími službami, včetně botů

<details>
<summary><b>Musím používat Bot Framework, abych mohl používat QnA Maker?</b></summary>

**Odpověď**: Ne, nemusíte používat [Rozhraní bot ů](https://github.com/Microsoft/botbuilder-dotnet) s QnA Makerem. QnA Maker je však nabízen jako jedna z několika šablon ve [službě Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot Service umožňuje rychlý inteligentní vývoj botů prostřednictvím microsoft bot frameworku a běží v prostředí bez serveru.

</details>

<details>
<summary><b>Jak mohu vytvořit nového robota s QnA Makerem?</b></summary>

**Odpověď**: Podle pokynů v [této](./Quickstarts/create-publish-knowledge-base.md) dokumentaci vytvořte bota pomocí služby Azure Bot Service.

</details>

<details>
<summary><b>Jak se používá jiná znalostní báze s existující službou Azure bot?</b></summary>

**Odpověď**: Musíte mít následující informace o vaší znalostní bázi:

* ID znalostní báze.
* Publikovaný název vlastní subdomény znalostního bodu `host`znalostní báze Knowledge Base, známý jako , nalezený na stránce **Nastavení** po publikování.
* Publikovaný koncový bod znalostní báze Knowledge Base – po publikování najdete na stránce **Nastavení.**

S těmito informacemi přejděte na webu Azure Portal na app službu vašeho robota. V **části Nastavení -> Konfigurace -> Nastavení aplikace**tyto hodnoty změňte.

Koncový bod znalostní báze `QnAAuthkey` znalostní báze je označen ve službě ABS.

</details>

<details>
<summary><b>Mohou dvě nebo více klientských aplikací sdílet znalostní bázi?</b></summary>

**Odpověď**: Ano, znalostní bázi lze dotazovat z libovolného počtu klientů. Pokud odpověď ze znalostní báze se zdá být pomalé nebo časový limit, zvažte upgrade úrovně služby pro aplikační služby přidružené ke znalostní bázi.

</details>

<details>
<summary><b>Jak vložím službu QnA Maker na svůj web?</b></summary>

**Odpověď**: Chcete-li službu QnA Maker vložit jako ovládací prvek webového chatu na web, postupujte takto:

1. Vytvořte si FAQ bot podle pokynů [zde](./Quickstarts/create-publish-knowledge-base.md).
2. Povolení webového chatu podle následujících [kroků](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

</details>

## <a name="data-storage"></a>Úložiště dat

<details>
<summary><b>Jaká data jsou uložena a kde jsou uložena?</b></summary>

**Odpověď**:

Při vytváření služby QnA Maker jste vybrali oblast Azure. Znalostní báze a soubory protokolu jsou uloženy v této oblasti.

</details>