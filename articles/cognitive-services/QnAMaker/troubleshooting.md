---
title: Řešení potíží – QnA Maker
description: Seznam z nejčastějších dotazů týkajících se QnA Maker služby vám pomůže s tím, že službu povedete rychleji a s lepšími výsledky.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612690"
---
# <a name="troubleshooting-for-qna-maker"></a>Řešení potíží pro QnA Maker

Seznam z nejčastějších dotazů týkajících se QnA Maker služby vám pomůže s tím, že službu povedete rychleji a s lepšími výsledky.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Správa předpovědi

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

<details>
<summary><b>Jak můžu zlepšit výkon propustnosti pro dotaz předpovědi?</b></summary>

**Odpověď**: problémy s výkonem propustnosti znamenají, že potřebujete škálovat jak pro službu App Service, tak pro kognitivní hledání. Zvažte přidání repliky do Kognitivní hledání za účelem zvýšení výkonu.

Přečtěte si další informace o [cenových úrovních](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Jak získat koncový bod služby Qnamakerem</b></summary>

**Odpověď**: koncový bod služby qnamakerem je užitečný pro účely ladění při kontaktování podpory Qnamakerem nebo UserVoice. Koncový bod je adresa URL v tomto formátu: `https://your-resource-name.azurewebsites.net` .

1. Přejít do služby Qnamakerem (skupina prostředků) v [Azure Portal](https://portal.azure.com)

    ![Qnamakerem skupiny prostředků Azure v Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte App Service přidružené k prostředku QnA Maker. Názvy jsou obvykle stejné.

     ![Vyberte Qnamakerem App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Adresa URL koncového bodu je k dispozici v části Přehled.

    ![Koncový bod Qnamakerem](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

<details>
<summary><b>Jak můžu zlepšit výkon propustnosti pro dotaz předpovědi?</b></summary>

**Odpověď**: problémy s výkonem propustnosti znamenají nutnost horizontálního navýšení kapacity kognitivní hledání. Zvažte přidání repliky do Kognitivní hledání za účelem zvýšení výkonu.

Přečtěte si další informace o [cenových úrovních](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>Správa znalostní báze

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

<details>
<summary><b>Omylem jsem odstranili součást mých QnA Maker, co mám dělat?</b></summary>

**Odpověď**: neodstraňujte žádnou ze služeb Azure, které jste vytvořili společně s prostředkem QnA maker, jako je například Search nebo webová aplikace. To je nezbytné, aby QnA Maker fungovalo, pokud ho odstraníte, QnA Maker přestane fungovat správně.

Všechna odstranění jsou trvalá, včetně párů dotazů a odpovědí, souborů, adres URL, vlastních otázek a odpovědí, znalostní báze nebo prostředků Azure. Před odstraněním jakékoli části znalostní báze se ujistěte, že jste vyexportovali svou znalostní bázi ze stránky **Nastavení** .

</details>

<details>
<summary><b>Proč moje adresy URL neextrahují páry otázek a odpovědí?</b></summary>

**Odpověď**: je možné, že QnA Maker nemůže automaticky extrahovat obsah otázek a odpovědí (QnA) z platných adres URL pro nejčastější dotazy. V takových případech můžete vložit obsah QnA do souboru. txt a zjistit, jestli ho může nástroj ingestovat. Alternativně můžete pomocí [portálu QnA maker](https://qnamaker.ai)přidat obsah do znalostní báze.

</details>

<details>
<summary><b>Jak velká znalostní báze můžu vytvořit?</b></summary>

**Odpověď**: velikost znalostní báze závisí na SKU Azure Search, které zvolíte při vytváření služby QnA maker. Další podrobnosti najdete [tady](./concepts/azure-resources.md) .

</details>

<details>
<summary><b>Proč se při pokusu o vytvoření nové znalostní báze nezobrazí žádné informace v rozevíracím seznamu?</b></summary>

**Odpověď**: ještě jste nevytvořili žádné služby QnA maker v Azure. Další informace o tom, jak to provést, najdete [tady](./How-To/set-up-qnamaker-service-azure.md) .

</details>

<details>
<summary><b>Návody sdílet znalostní bázi s ostatními?</b></summary>

**Odpověď**: sdílení funguje na úrovni služby QnA maker, to znamená, že všechny databáze znalostí ve službě budou sdíleny. Přečtěte [si](./index.yml) , jak spolupracovat na znalostní bázi.

</details>

<details>
<summary><b>Můžete znalostní bázi sdílet s přispěvatelem, který není ve stejném tenantovi AAD, abyste mohli upravit znalostní bázi?</b></summary>

**Odpověď**: sdílení je založené na řízení přístupu na základě role Azure (Azure RBAC). Pokud můžete sdílet _libovolný_ prostředek v Azure s jiným uživatelem, můžete také sdílet QnA maker.

</details>

<details>
<summary><b>Pokud máte plán App Service s 5 Qnamakerem znalostní báze. Můžete přiřadit práva ke čtení a zápisu pro 5 různých uživatelů, aby každý z nich měl přístup jenom k 1 Qnamakerem znalostní bázi Knowledge Base?</b></summary>

**Odpověď**: můžete sdílet celou službu qnamakerem, ne jednotlivé znalostní báze.

</details>

<details>
<summary><b>Jak změním výchozí zprávu, když se nenajde žádná dobrá shoda?</b></summary>

**Odpověď**: výchozí zpráva je součástí nastavení ve službě App Service.
- V Azure Portal přejdete do prostředku App Service.

![qnamakerem AppService](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klikněte na možnost **Nastavení** .

![nastavení qnamakerem AppService](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Změna hodnoty nastavení **DefaultAnswer**
- Restartujte službu App Service.

![qnamakerem AppService restartování](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Proč můj odkaz na SharePoint nezíská extrakci?</b></summary>

**Odpověď**: Další informace najdete v tématu [umístění zdrojů dat](./concepts/data-sources-and-content.md#data-source-locations) .

</details>

<details>
<summary><b>Aktualizace, které jsem udělali ve znalostní bázi, se při publikování neprojeví. Proč ne?</b></summary>

**Odpověď**: všechny operace úprav, ať už v tabulce je aktualizace, test nebo nastavení, musí být před publikováním uloženy. Nezapomeňte kliknout na tlačítko **Uložit a naučit** se po každé operaci úprav.

</details>

<details>
<summary><b>Podporuje znalostní báze bohatá data a multimédia?</b></summary>

**Odpověď:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatické extrakce multimédií pro soubory a adresy URL

* Adresy URL – omezená možnost převodu z formátu HTML na Markdownu
* Soubory – nepodporováno

#### <a name="answer-text-in-markdown"></a>Text odpovědi v Markdownu
Jakmile jsou páry QnA ve znalostní bázi, můžete upravit text Markdownu odpovědi tak, aby zahrnoval odkazy na média dostupná z veřejných adres URL.


</details>

<details>
<summary><b>Podporuje QnA Maker jiné než anglické jazyky?</b></summary>

**Odpověď**: Další informace o [podporovaných jazycích](./overview/language-support.md)najdete v tématu.

Pokud máte obsah z více jazyků, nezapomeňte vytvořit samostatnou službu pro každý jazyk.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

<details>
<summary><b>Proč moje adresy URL neextrahují páry otázek a odpovědí?</b></summary>

**Odpověď**: je možné, že QnA Maker nemůže automaticky extrahovat obsah otázek a odpovědí (QnA) z platných adres URL pro nejčastější dotazy. V takových případech můžete vložit obsah QnA do souboru. txt a zjistit, jestli ho může nástroj ingestovat. Alternativně můžete pomocí [portálu QnA maker](https://qnamaker.ai)přidat obsah do znalostní báze.

</details>

<details>
<summary><b>Jak velká znalostní báze můžu vytvořit?</b></summary>

**Odpověď**: velikost znalostní báze závisí na SKU Azure Search, které zvolíte při vytváření služby QnA maker. Další podrobnosti najdete [tady](./concepts/azure-resources.md) .

</details>

<details>
<summary><b>Proč se při pokusu o vytvoření nové znalostní báze nezobrazí žádné informace v rozevíracím seznamu?</b></summary>

**Odpověď**: ještě jste nevytvořili žádné služby QnA maker v Azure. Další informace o tom, jak to provést, najdete [tady](./How-To/set-up-qnamaker-service-azure.md) .

</details>

<details>
<summary><b>Návody sdílet znalostní bázi s ostatními?</b></summary>

**Odpověď**: sdílení funguje na úrovni služby QnA maker, to znamená, že všechny databáze znalostí ve službě budou sdíleny. Přečtěte [si](./index.yml) , jak spolupracovat na znalostní bázi.

</details>

<details>
<summary><b>Můžete pro úpravu znalostní báze sdílet znalostní bázi přispěvatele, který není ve stejném tenantovi Azure Active Directory?</b></summary>

**Odpověď**: sdílení je založené na řízení přístupu na základě role Azure (Azure RBAC). Pokud můžete sdílet _libovolný_ prostředek v Azure s jiným uživatelem, můžete také sdílet QnA maker.

</details>

<details>
<summary><b>Můžete přiřadit práva ke čtení a zápisu pro 5 různých uživatelů, aby každý z nich měl přístup jenom k 1 Qnamakerem znalostní bázi Knowledge Base?</b></summary>

**Odpověď**: můžete sdílet celou službu qnamakerem, ne jednotlivé znalostní báze.

</details>

<details>
<summary><b>Proč můj odkaz na SharePoint nezíská extrakci?</b></summary>

**Odpověď**: Další informace najdete v tématu [umístění zdrojů dat](./concepts/data-sources-and-content.md#data-source-locations) .

</details>

<details>
<summary><b>Aktualizace, které jsem udělali ve znalostní bázi, se při publikování neprojeví. Proč ne?</b></summary>

**Odpověď**: všechny operace úprav, ať už v tabulce je aktualizace, test nebo nastavení, musí být před publikováním uloženy. Nezapomeňte kliknout na tlačítko **Uložit a naučit** se po každé operaci úprav.

</details>

<details>
<summary><b>Podporuje znalostní báze bohatá data a multimédia?</b></summary>

**Odpověď:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatické extrakce multimédií pro soubory a adresy URL

* Adresy URL – omezená možnost převodu z formátu HTML na Markdownu
* Soubory – nepodporováno

#### <a name="answer-text-in-markdown"></a>Text odpovědi v Markdownu
Jakmile jsou páry QnA ve znalostní bázi, můžete upravit text Markdownu odpovědi tak, aby zahrnoval odkazy na média dostupná z veřejných adres URL.


</details>

<details>
<summary><b>Podporuje QnA Maker jiné než anglické jazyky?</b></summary>

**Odpověď**: Další informace o [podporovaných jazycích](./overview/language-support.md)najdete v tématu.

Pokud máte obsah z více jazyků, nezapomeňte vytvořit samostatnou službu pro každý jazyk.

</details>

---

## <a name="manage-service"></a>Správa služby

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

<details>
<summary><b>Kdy bych měl restartovat službu App Service?</b></summary>

**Odpověď**: aktualizujte službu App Service, pokud je ikona upozornění vedle hodnoty verze znalostní báze v tabulce **klíče koncového bodu** na [stránce](https://www.qnamaker.ai/UserSettings) **uživatelská nastavení** .

</details>

<details>
<summary><b>Odstranil (a) jsem stávající vyhledávací službu. Jak mohu tuto situaci opravit?</b></summary>

**Odpověď**: Pokud odstraníte index služby Azure kognitivní hledání, operace je finální a index nelze obnovit.

</details>

<details>
<summary><b>Odstranil (a) jsem svůj `testkb` index ve vyhledávací službě. Jak mohu tuto situaci opravit?</b></summary>

**Odpověď**: stará data nejde obnovit. Vytvořte nový prostředek QnA Maker a znovu vytvořte znalostní bázi.

</details>

<details>
<summary><b>Kdy mám aktualizovat klíče koncového bodu?</b></summary>

**Odpověď**: aktualizujte klíče koncového bodu, pokud máte podezření, že došlo k ohrožení zabezpečení.

</details>

<details>
<summary><b>Můžu použít stejný prostředek Kognitivní hledání služby Azure pro znalostní báze s použitím více jazyků?</b></summary>

**Odpověď**: Chcete-li použít více jazyků a více znalostní báze, musí uživatel vytvořit prostředek QnA maker pro každý jazyk. Tím se vytvoří samostatná služba Azure Search na jednotlivé jazyky. Kombinování různých jazykových znalostí v jedné službě Azure Search má za následek zhoršení důležitosti výsledků.

</details>

<details>
<summary><b>Jak mohu změnit název prostředku Kognitivní hledání služby Azure, který používá QnA Maker?</b></summary>

**Odpověď**: název prostředku Azure kognitivní hledání je název prostředku QnA maker s některými náhodnými písmeny připojenými na konci. Díky tomu je obtížné rozlišovat mezi více prostředky vyhledávání pro QnA Maker. Vytvořte samostatnou vyhledávací službu (pojmenujte ji tak, jak byste chtěli) a připojte ji ke službě QnA. Postup je podobný postupům, které je třeba provést při [upgradu služby Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Když QnA Maker vrátí `Runtime core is not initialized,` , jak ho mám opravit?</b></summary>

**Odpověď**: místo na disku pro službu App Service může být plné. Postup opravy místa na disku:

1. V [Azure Portal](https://portal.azure.com)vyberte službu App service vaší QnA maker a pak tuto službu zastavte.
1. Pořád ve službě App Service vyberte **vývojové nástroje**, pak **Rozšířené nástroje** a pak **Přejít** na. Otevře se nové okno prohlížeče.
1. Vyberte **ladit konzola** a pak **cmd** a otevřete nástroj příkazového řádku.
1. Přejděte do _lokality/wwwroot/data/qnamakerem/_ Directory.
1. Odeberte všechny složky, jejichž název začíná na `rd` .

    **Neodstraňujte** následující:

    * Soubor KbIdToRankerMappings.txt
    * EndpointSettings.jsv souboru
    * EndpointKeys složka

1. Spusťte službu App Service.
1. Přístup k vaší znalostní bázi vám umožní ověřit, jestli je teď funguje.

</details>
<details>
<summary><b>Proč můj Application Insights nefunguje?</b></summary>

**Odpověď**: Projděte si výše uvedené kroky a opravte problém:

1. V části App Service-> nastavení – > konfiguračního oddílu > nastavení aplikace-> Name-se správně nakonfigurují a nastavují na příslušnou kartu Přehled Application Insights ("klíč instrumentace") GUID. 

1. V části App Service-> nastavení – > oddílu "Application Insights" – > Ujistěte se, že je App Insights povolený a připojený ke příslušnému prostředku Application Insights.

</details>

<details>
<summary><b>Můj Application Insights je povolený, ale proč nefunguje správně?</b></summary>

**Odpověď**: postupujte prosím podle níže uvedených kroků: 

1.  Pokud zde existuje nějaká hodnota, zkopírujte hodnotu APPINSIGHTS_INSTRUMENTATIONKEY název do názvu ' UserAppInsightsKey '. 

1.  Pokud klíč "UserAppInsightsKey" v nastavení aplikace neexistuje, přidejte prosím nový klíč s tímto názvem a zkopírujte hodnotu.

1.  Uložte ho a automaticky se restartuje App Service. Tato chyba by měla tento problém vyřešit. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)


<details>
<summary><b>Odstranil (a) jsem stávající vyhledávací službu. Jak mohu tuto situaci opravit?</b></summary>

**Odpověď**: Pokud odstraníte index služby Azure kognitivní hledání, operace je finální a index nelze obnovit.

</details>

<details>
<summary><b>Odstranil (a) jsem svůj `testkb` index ve vyhledávací službě. Jak mohu tuto situaci opravit?</b></summary>

**Odpověď**: stará data nejde obnovit. Vytvořte nový prostředek QnA Maker a znovu vytvořte znalostní bázi.

</details>

<details>
<summary><b>Můžu použít stejný prostředek Kognitivní hledání služby Azure pro znalostní báze s použitím více jazyků?</b></summary>

**Odpověď**: Chcete-li použít více jazyků a více znalostní báze, musí uživatel vytvořit prostředek QnA maker pro každý jazyk. Tím se vytvoří samostatná služba Azure Search na jednotlivé jazyky. Kombinování různých jazykových znalostí v jedné službě Azure Search má za následek zhoršení důležitosti výsledků.

</details>

<details>
<summary><b>Jak mohu změnit název prostředku Kognitivní hledání služby Azure, který používá QnA Maker?</b></summary>

**Odpověď**: název prostředku Azure kognitivní hledání je název prostředku QnA maker s některými náhodnými písmeny připojenými na konci. Díky tomu je obtížné rozlišovat mezi více prostředky vyhledávání pro QnA Maker. Vytvořte samostatnou vyhledávací službu (pojmenujte ji tak, jak byste chtěli) a připojte ji ke službě QnA. Postup je podobný postupům, které je třeba provést při [upgradu služby Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integrace s dalšími službami, včetně roboty

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

<details>
<summary><b>Potřebuji použít rozhraní bot Framework, aby bylo možné použít QnA Maker?</b></summary>

**Odpověď**: Ne, nemusíte používat [rozhraní Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) s QnA maker. QnA Maker se ale nabízí jako jedna z několika šablon v [Azure bot Service](/azure/bot-service/). Robot Service umožňuje rychlý vývoj v rámci inteligentních robotů přes Microsoft bot Framework a běží v prostředí bez serveru.

</details>

<details>
<summary><b>Jak můžu vytvořit nový robot s QnA Maker?</b></summary>

**Odpověď**: podle pokynů v [této](./Quickstarts/create-publish-knowledge-base.md) dokumentaci vytvořte robota s Azure bot Service.

</details>

<details>
<summary><b>Návody použít jinou znalostní bázi se stávající službou Azure bot Service?</b></summary>

**Odpověď**: musíte mít následující informace o znalostní bázi Knowledge Base:

* ID znalostní báze
* Název vlastní subdomény publikovaného koncového bodu znalostní báze, `host` který se po publikování nachází na stránce **Nastavení** .
* Klíč publikovaného koncového bodu znalostní báze – byl po publikování na stránce **Nastavení** nalezen.

Tyto informace najdete v Azure Portal do App Service bot. V části **Nastavení-> konfigurace-> nastavení aplikace** změňte tyto hodnoty.

Klíč koncového bodu znalostní báze je označený `QnAAuthkey` ve službě ABS.

</details>

<details>
<summary><b>Můžou dvě nebo víc klientských aplikací sdílet znalostní bázi?</b></summary>

**Odpověď**: Ano, znalostní bázi se dá dotazovat z libovolného počtu klientů. Pokud se odezva ze znalostní báze jeví jako pomalá nebo časový limit, zvažte možnost upgradovat úroveň služby pro službu App Service přidruženou ke znalostní bázi.

</details>

<details>
<summary><b>Návody službu QnA Maker na svém webu vložit?</b></summary>

**Odpověď**: postupujte podle těchto kroků a vložte QnA maker službu jako ovládací prvek webové konverzace na svém webu:

1. Pomocí pokynů [zde můžete](./Quickstarts/create-publish-knowledge-base.md)vytvořit robotská okna s nejčastějšími dotazy.
2. Povolte webový chat podle kroků uvedených [tady](/azure/bot-service/bot-service-channel-connect-webchat) .

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)


<details>
<summary><b>Potřebuji použít rozhraní bot Framework, aby bylo možné použít QnA Maker?</b></summary>

**Odpověď**: Ne, nemusíte používat [rozhraní Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) s QnA maker. QnA Maker se ale nabízí jako jedna z několika šablon v [Azure bot Service](/azure/bot-service/). Robot Service umožňuje rychlý vývoj v rámci inteligentních robotů přes Microsoft bot Framework a běží v prostředí bez serveru.

</details>

<details>
<summary><b>Jak můžu vytvořit nový robot s QnA Maker?</b></summary>

**Odpověď**: podle pokynů v [této](./Quickstarts/create-publish-knowledge-base.md) dokumentaci vytvořte robota s Azure bot Service.

</details>

<details>
<summary><b>Návody použít jinou znalostní bázi se stávající službou Azure bot Service?</b></summary>

**Odpověď**: musíte mít následující informace o znalostní bázi Knowledge Base:

* ID znalostní báze
* Název vlastní subdomény publikovaného koncového bodu znalostní báze, `host` který se po publikování nachází na stránce **Nastavení** .
* Klíč publikovaného koncového bodu znalostní báze – byl po publikování na stránce **Nastavení** nalezen.

Tyto informace najdete v Azure Portal do App Service bot. V části **Nastavení-> konfigurace-> nastavení aplikace** změňte tyto hodnoty.

Klíč koncového bodu znalostní báze je označený `QnAAuthkey` ve službě ABS.

</details>

<details>
<summary><b>Můžou dvě nebo víc klientských aplikací sdílet znalostní bázi?</b></summary>

**Odpověď**: Ano, znalostní bázi se dá dotazovat z libovolného počtu klientů. Pokud se odezva ze znalostní báze jeví jako pomalá nebo časový limit, zvažte možnost upgradovat úroveň služby pro službu App Service přidruženou ke znalostní bázi.

</details>

<details>
<summary><b>Návody službu QnA Maker na svém webu vložit?</b></summary>

**Odpověď**: postupujte podle těchto kroků a vložte QnA maker službu jako ovládací prvek webové konverzace na svém webu:

1. Pomocí pokynů [zde můžete](./Quickstarts/create-publish-knowledge-base.md)vytvořit robotská okna s nejčastějšími dotazy.
2. Povolte webový chat podle kroků uvedených [tady](/azure/bot-service/bot-service-channel-connect-webchat) .

---

## <a name="data-storage"></a>Úložiště dat

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

<details>
<summary><b>Jaká data jsou uložená a kde jsou uložená?</b></summary>

**Odpověď:**

Při vytváření služby QnA Maker jste vybrali oblast Azure. Vaše znalostní báze a soubory protokolu jsou uloženy v této oblasti.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

<details>
<summary><b>Jaká data jsou uložená a kde jsou uložená?</b></summary>

**Odpověď:**

Při vytváření služby QnA Maker jste vybrali oblast Azure. Vaše znalostní báze a soubory protokolu jsou uloženy v této oblasti.

</details>

---
