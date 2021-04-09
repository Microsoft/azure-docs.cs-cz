---
title: Použití konektoru Ethereem blockchain s Azure Logic Apps – Azure blockchain Service
description: Pomocí konektoru Ethereem blockchain s Azure Logic Apps můžete aktivovat funkce inteligentních kontraktů a reagovat na události inteligentních kontraktů.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763787"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Použití konektoru Ethereem blockchain s Azure Logic Apps

Pomocí [konektoru Ethereem blockchain](/connectors/blockchainethereum/) s [Azure Logic Apps](../../logic-apps/index.yml) provádět akce inteligentních kontraktů a reagovat na události inteligentních kontraktů. Tento článek vysvětluje, jak můžete pomocí konektoru Ethereem blockchain odeslat informace blockchain do jiné služby nebo zavolat funkci blockchain. Řekněme například, že chcete vytvořit mikroslužbu založenou na REST, která vrací informace z hlavní knihy blockchain. Pomocí aplikace logiky můžete přijmout požadavky HTTP, které dotazují na informace uložené v blockchain knize.

## <a name="prerequisites"></a>Požadavky

- Dokončete volitelný rychlý úvodní požadavek [: pomocí Visual Studio Code se připojte k síti konsorcia služeb Azure blockchain](connect-vscode.md). Rychlý Start vás provede instalací [sady Azure blockchain Development Kit pro ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) a nastavením vašeho vývojového prostředí blockchain.
- Pokud s Azure Logic Apps začínáte, zvažte, jak si projít Microsoft Learn moduly [Úvod do Azure Logic Apps](/learn/modules/intro-to-logic-apps/) a [zavolejte rozhraní API z pracovního postupu Logic Apps pomocí vlastního konektoru](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps pomáhá při plánování a automatizaci obchodních procesů a pracovních postupů, pokud potřebujete integrovat systémy a služby. Nejprve vytvoříte logiku, která používá konektor Ethereem blockchain.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Integrace** > **Aplikace logiky**.
1. V části **vytvořit aplikaci logiky** zadejte podrobnosti o tom, kde vytvořit aplikaci logiky. Až budete hotovi, vyberte **vytvořit**.

    Další informace o vytváření aplikací logiky najdete v tématu [Vytvoření automatizovaných pracovních postupů pomocí Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Až Azure nasadí vaši aplikaci, vyberte prostředek aplikace logiky.
1. V Návrháři Logic Apps v části **šablony** vyberte **prázdná aplikace logiky**.

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

Konektor Ethereem blockchain má jednu Trigger a několik akcí. Aktivační událost nebo akce, které použijete, závisí na vašem scénáři. Postupujte podle části v tomto článku, který nejlépe odpovídá vašemu scénáři.

Pokud váš pracovní postup:

* Aktivuje se, když dojde k události v blockchain, a [použijte Trigger události](#use-the-event-trigger).
* Slouží k dotazování nebo nasazení inteligentních kontraktů, [použijte akce](#use-actions).
* [Vygenerujte pracovní postup pomocí sady Developer Kit](#generate-a-workflow), která se řídí běžným scénářem.

## <a name="use-the-event-trigger"></a>Použití triggeru události

Pokud chcete, aby se aplikace logiky spouštěla po výskytu události Smart Contract, použijte triggery Ethereem blockchain události. Například chcete odeslat e-mail při volání funkce inteligentního kontraktu.

1. V Návrháři Logic Apps vyberte konektor Ethereem blockchain.
1. Na kartě **triggery** vyberte, **kdy dojde k události inteligentního kontraktu**.
1. Změna nebo [vytvoření připojení rozhraní API](#create-an-api-connection) ke službě Azure blockchain
1. Zadejte podrobnosti o inteligentním kontraktu, u kterých chcete vyhledat události.

    ![Návrhář Logic Apps s vlastnostmi triggeru události](./media/ethereum-logic-app/event-properties.png)

    | Vlastnost | Popis |
    |----------|-------------|
    | **ABI kontraktu** | Binární rozhraní aplikace (ABI) definuje rozhraní inteligentních kontraktů. Další informace najdete v tématu [získání kontraktu ABI](#get-the-contract-abi). |
    | **Adresa inteligentního kontraktu** | Adresa kontraktu je adresa cíle inteligentního kontraktu na Ethereem blockchain. Další informace najdete v tématu [získání adresy kontraktu](#get-the-contract-address). |
    | **Název události** | Vyberte událost inteligentního kontraktu, kterou chcete ověřit. Událost aktivuje aplikaci logiky. |
    | **Interval** a **frekvence** | Vyberte, jak často chcete událost kontrolovat. |

1. Vyberte **Uložit**.

K dokončení vaší aplikace logiky můžete přidat nový krok, který provede akci na základě triggeru události Ethereem blockchain. Například odešlete e-mail.

## <a name="use-actions"></a>Používání akcí

Akce blockchain Ethereem použijte, pokud chcete, aby aplikace logiky prováděla akci v hlavní knize blockchain. Například chcete vytvořit mikroslužbu založenou na REST, která volá funkci inteligentního kontraktu, když se v aplikaci logiky provede požadavek HTTP.

Akce konektoru vyžadují Trigger. Akci konektoru blockchain Ethereem můžete použít jako další krok po triggeru, jako je například Trigger požadavku HTTP pro mikroslužbu.

1. V Návrháři Logic Apps vyberte **Nový krok** po triggeru.
1. Vyberte konektor Ethereem blockchain.
1. Na kartě **Akce** vyberte jednu z dostupných akcí.

    ![Logic Apps návrháře s akcemi vlastností](./media/ethereum-logic-app/action-properties.png)

1. Změna nebo [vytvoření připojení rozhraní API](#create-an-api-connection) ke službě Azure blockchain
1. V závislosti na akci, kterou jste zvolili, zadejte následující podrobnosti o funkci inteligentního kontraktu.

    | Vlastnost | Popis |
    |----------|-------------|
    | **ABI kontraktu** | Smlouva ABI definuje rozhraní inteligentních kontraktů. Další informace najdete v tématu [získání kontraktu ABI](#get-the-contract-abi). |
    | **Bajtový kód kontraktu** | Kód zkompilovaného bajtu inteligentního kontraktu. Další informace najdete v tématu [získání bajtového kódu kontraktu](#get-the-contract-bytecode). |
    | **Adresa inteligentního kontraktu** | Adresa kontraktu je adresa cíle inteligentního kontraktu na Ethereem blockchain. Další informace najdete v tématu [získání adresy kontraktu](#get-the-contract-address). |
    | **Název funkce inteligentního kontraktu** | Vyberte název funkce inteligentního kontraktu pro akci. Seznam se vyplní z podrobností v kontraktu ABI. |

    Po výběru názvu funkce inteligentního kontraktu se můžou zobrazit povinná pole pro parametry funkce. Zadejte hodnoty nebo dynamický obsah požadovaný pro váš scénář.

Teď můžete použít aplikaci logiky. Při aktivaci události aplikace logiky se spustí akce Ethereem blockchain. Například Trigger požadavku HTTP spustí akci Ethereem blockchain pro dotaz na hodnotu stavu inteligentního kontraktu. Výsledkem tohoto dotazu je odpověď HTTP, která vrací hodnotu.

## <a name="generate-a-workflow"></a>Generování pracovního postupu

Blockchain Development Kit pro Azure pro Ethereem Visual Studio Code může vygenerovat pracovní postupy aplikace logiky pro běžné scénáře. K dispozici jsou čtyři scénáře:

* Publikování dat do instance Azure SQL Database
* Publikování událostí do instance Azure Event Grid nebo Azure Service Bus
* Publikování sestav
* Mikroslužba založená na REST

 Azure blockchain Development Kit používá Truffle ke zjednodušení vývoje blockchain. K vygenerování aplikace logiky založené na inteligentní smlouvě potřebujete řešení Truffle pro inteligentní kontrakt. Budete také potřebovat připojení k vaší síti konsorcia Azure blockchain Service. Další informace najdete v tématu [použití Visual Studio Code pro připojení k rychlému startu sítě služby Azure blockchain Service Consortium](connect-vscode.md).

Následující kroky například generují aplikaci Logic mikroslužeb založenou na REST založené na **HelloBlockchain** Smart Contract pro rychlé zprovoznění:

1. Na bočním panelu Průzkumníka Visual Studio Code rozbalte složku **smluv** ve vašem řešení.
1. Klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte **Generovat mikroslužby pro inteligentní kontrakty** .

    ![Podokno Visual Studio Code s vygenerováním mikroslužeb pro výběr inteligentních kontraktů](./media/ethereum-logic-app/generate-logic-app.png)

1. V paletě příkazů vyberte **Aplikace logiky**.
1. Zadejte **adresu kontraktu**. Další informace najdete v tématu [získání adresy kontraktu](#get-the-contract-address).
1. Vyberte předplatné Azure a skupinu prostředků pro aplikaci logiky.

    Konfigurace aplikace logiky a soubory kódu jsou generovány v adresáři **generatedLogicApp** .

1. Zobrazit adresář **generatedLogicApp/HelloBlockchain** Pro každou funkci, událost a vlastnost inteligentního kontraktu je k dispozici soubor JSON aplikace logiky.
1. Otevřete soubor **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.js** a zkopírujte jeho obsah.

    ![Soubor JSON s kódem ke zkopírování](./media/ethereum-logic-app/requestmessage.png)

1. V aplikaci logiky vyberte **zobrazení kód aplikace logiky**. Nahraďte existující kód JSON vygenerovaným JSON App Apps.

    ![Zobrazení kódu aplikace logiky s novým nahrazeným kódem aplikace](./media/ethereum-logic-app/code-view.png)

1. Vyberte **Návrhář** a přepněte do zobrazení návrháře.
1. Aplikace logiky obsahuje základní kroky pro scénář. Je ale potřeba aktualizovat podrobnosti o konfiguraci konektoru Ethereem blockchain.
1. Vyberte krok **připojení** a změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure blockchain.

    ![Zobrazení návrháře s výběrem připojení](./media/ethereum-logic-app/microservice-logic-app.png)

1. Teď můžete použít aplikaci logiky. Pokud chcete otestovat mikroslužbu založenou na REST, vydejte požadavek HTTP POST na adresu URL požadavku aplikace logiky. Zkopírujte obsah **adresy URL http post** z kroku **při přijetí požadavku HTTP** .

    ![Podokno návrháře Logic Apps s adresou URL POST protokolu HTTP](./media/ethereum-logic-app/post-url.png)

1. K vytvoření požadavku HTTP POST použijte oblé. Zástupný text nahraďte *\<HTTP POST URL\>* adresou URL z předchozího kroku.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Příkaz složené vratky vrátí odpověď z aplikace logiky. V tomto případě je odpovědí výstupem z funkce **RequestMessage** Smart Contract.

    ![Výstup kódu z funkce RequestMessage Smart Contract](./media/ethereum-logic-app/curl.png)

Další informace o používání vývojové sady najdete na [stránce wiki pro Azure blockchain Development Kit pro ethereem](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Vytvoření připojení rozhraní API

Pro konektor blockchain Ethereem se vyžaduje připojení rozhraní API k blockchain. Konektor API můžete použít pro několik aplikací logiky. Některé vlastnosti jsou povinné a jiné závisí na vašem scénáři.

> [!IMPORTANT]
> Pro vytváření transakcí na blockchain se vyžaduje privátní klíč nebo adresa účtu a heslo. Je nutné zadat pouze jednu formu ověřování. Nemusíte zadávat privátní klíč a podrobnosti účtu. Dotazování na smlouvy nevyžaduje transakci. Pokud používáte akce, které dotazují na stav smlouvy, není vyžadován privátní klíč nebo adresa účtu a heslo.

Aby bylo možné nastavit připojení ke členu služby Azure blockchain, následující seznam obsahuje vlastnosti, které mohou být potřeba v závislosti na vašem scénáři.

| Vlastnost | Popis |
|----------|-------------|
|**Název připojení** | Název připojení rozhraní API Povinná hodnota. |
|**Koncový bod ethereem RPC** | Adresa HTTP uzlu transakce služby Azure blockchain. Povinná hodnota. Další informace najdete v tématu [získání koncového bodu RPC](#get-the-rpc-endpoint). |
|**Privátní klíč** | Privátní klíč účtu ethereem Pro transakce se vyžaduje privátní klíč nebo adresa účtu a heslo. Další informace najdete v tématu [získání soukromého klíče](#get-the-private-key). |
|**Adresa účtu** | Adresa členského účtu služby Azure blockchain Pro transakce se vyžaduje privátní klíč nebo adresa účtu a heslo. Další informace najdete v tématu [získání adresy účtu](#get-the-account-address). |
|**Heslo účtu** | Heslo účtu je nastaveno při vytváření člena. Informace o resetování hesla najdete v tématu [ethereem Account](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Získání koncového bodu RPC

Adresa koncového bodu RPC služby Azure blockchain se vyžaduje pro připojení k síti blockchain. Adresu koncového bodu můžete získat pomocí Azure blockchain Development Kit pro Ethereem nebo Azure Portal.

**Používání vývojové sady:**

1. V části **Služba Azure blockchain** v Visual Studio Code klikněte pravým tlačítkem na konsorcium.
1. Vyberte **Kopírovat adresu koncového bodu RPC**.

    ![Visual Studio Code podokno znázorňující konsorcium s vybranou adresou pro kopírování koncových bodů RPC](./media/ethereum-logic-app/devkit-rpc.png)

    Koncový bod RPC se zkopíruje do schránky.

**Použití Azure Portal:**

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejít na člena služby Azure blockchain. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Stránka uzly transakce s výběrem (výchozí uzel)](./media/ethereum-logic-app/transaction-nodes.png)

1. Vyberte přístupové klíče **připojovacích řetězců**  >  .
1. Zkopírujte adresu koncového bodu z **https (přístupový klíč 1)** nebo **https (přístupový klíč 2)**.

    ![Azure Portal pomocí přístupových klíčů připojovacího řetězce](./media/ethereum-logic-app/connection-string.png)

    Koncový bod RPC je adresa URL HTTPS, která zahrnuje adresu a přístupový klíč vašeho uzlu transakce člena služby Azure blockchain.

## <a name="get-the-private-key"></a>Získat privátní klíč

Pomocí privátního klíče účtu Ethereem můžete ověřit odeslání transakce do blockchain. Veřejné a privátní klíče vašeho účtu Ethereem se generují z 12 slov. Sada Azure blockchain Development Kit pro Ethereem generuje při připojení k členovi konsorcia služby Azure blockchain symbolická klávesa. Adresu koncového bodu můžete získat pomocí rozšíření vývojové sady.

1. V Visual Studio Code otevřete paletu příkazů (F1).
1. Vyberte **blockchain: načíst privátní klíč**.
1. Vyberte symbol, který jste uložili při připojování k členu konsorcia.

    ![Paleta příkazů s možností výběru klávesové zkratky](./media/ethereum-logic-app/private-key.png)

    Privátní klíč se zkopíruje do schránky.

## <a name="get-the-account-address"></a>Získat adresu účtu

K ověření při odeslání transakce do blockchain můžete použít členský účet a heslo. Heslo je nastaveno při vytváření člena.

1. V Azure Portal navštivte stránku Přehled služby Azure blockchain.
1. Zkopírujte adresu **členského účtu** .

    ![Stránka s přehledem s adresou členského účtu](./media/ethereum-logic-app/member-account.png)

Další informace o adrese a hesle účtu najdete v tématu [ethereem Account](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Získat kontrakt ABI

Smlouva ABI definuje rozhraní inteligentních kontraktů. Popisuje, jak pracovat s inteligentní smlouvou. Můžete získat kontrakt ABI pomocí Azure blockchain Development Kit pro Ethereem. Můžete ho také získat ze souboru metadat kontraktu vytvořeného kompilátorem hustoty.

**Používání vývojové sady:**

Pokud jste k sestavení vaší inteligentní smlouvy použili vývojovou sadu nebo Truffle, můžete použít rozšíření ke zkopírování kontraktu ABI do schránky.

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte **Kopírovat kontrakt ABI**.

    ![Podokno Visual Studio Code s výběrem kopírovat kontrakt ABI](./media/ethereum-logic-app/abi-devkit.png)

    Kontrakt ABI je zkopírován do schránky.

**Použití souboru metadat kontraktu:**

1. Otevřete soubor s metadaty kontraktu obsaženým ve složce **Build/Contracts** projektu Solid of. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. V souboru JSON vyhledejte část **ABI** .
1. Zkopírujte pole **ABI** JSON.

    ![Kód ABI v souboru metadat kontraktu](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Získání bajtového kódu smlouvy

Bajtový kód kontraktu je kompilovaná chytrá smlouva spuštěná virtuálním počítačem s Ethereem. Můžete získat bajtový kód kontraktu pomocí Azure blockchain Development Kit pro Ethereem. Můžete ho také získat z kompilátoru hustoty.

**Používání vývojové sady:**

Pokud jste k sestavení vaší inteligentní smlouvy použili vývojovou sadu nebo Truffle, můžete použít rozšíření ke zkopírování bajtového kódu kontraktu do schránky.

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte **Kopírovat bajtový kód kontraktu**.

    ![Podokno Visual Studio Code s výběrem Kopírovat kód kontraktu](./media/ethereum-logic-app/bytecode-devkit.png)

    Bajtový kód kontraktu je zkopírován do schránky.

**Použití souboru metadat kontraktu:**

1. Otevřete soubor s metadaty kontraktu obsaženým ve složce **Build/Contracts** projektu Solid of. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. V souboru JSON vyhledejte prvek **bytového kódu** .
1. Zkopírujte hodnotu **bajtového kódu** .

    ![Visual Studio Code podokno s podbajtem v metadatech](./media/ethereum-logic-app/bytecode-metadata.png)

**Použití kompilátoru hustota:**

`solc --bin <smart contract>.sol`K vygenerování bajtového kódu kontraktu použijte příkaz.

## <a name="get-the-contract-address"></a>Získat adresu kontraktu

Adresa kontraktu je adresa cíle inteligentního kontraktu na Ethereem blockchain. Tuto adresu použijete k odeslání transakce nebo stavu dotazu pro inteligentní kontrakt. Adresu kontraktu můžete získat z výstupu migrace Truffle nebo ze souboru s metadaty kontraktu.

**Použití výstupu Truffle migrace:**

Truffle zobrazí adresu kontraktu po nasazení čipové smlouvy. Zkopírujte **adresu kontraktu** z výstupu.

![Výstup migrace Truffle s adresou kontraktu v Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Použití souboru metadat kontraktu:**

1. Otevřete soubor s metadaty kontraktu obsaženým ve složce **Build/Contracts** projektu Solid of. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. V souboru JSON vyhledejte oddíl **sítě** .
1. Privátní sítě jsou identifikovány pomocí celočíselného identifikátoru ID sítě. V části síť Najděte hodnotu adresa.
1. Zkopírujte hodnotu **adresa** .

![Metadata s hodnotou adresy v Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Další kroky

Sledujte běžné scénáře ve videu [s Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
