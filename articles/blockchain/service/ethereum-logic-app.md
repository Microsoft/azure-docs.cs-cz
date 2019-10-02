---
title: Jak používat konektor Ethereem blockchain s Azure Logic Apps
description: Jak pomocí konektoru Ethereem blockchain s Azure Logic Apps aktivovat funkce inteligentních kontraktů a reagovat na události inteligentních kontraktů.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720398"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Jak používat konektor Ethereem blockchain s Azure Logic Apps

Pomocí [konektoru Ethereem blockchain](https://docs.microsoft.com/connectors/blockchainethereum/) s [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) provádět akce inteligentních kontraktů a reagovat na události inteligentních kontraktů. Například chcete vytvořit mikroslužbu založenou na REST, která vrací informace z hlavní knihy blockchain. Pomocí aplikace logiky můžete přijmout požadavky HTTP, které dotazují na informace uložené v blockchain knize.

## <a name="prerequisites"></a>Předpoklady

* Dokončete volitelný rychlý úvodní požadavek [: pomocí Visual Studio Code se připojte k síti konsorcia služeb Azure blockchain](connect-vscode.md). Rychlý Start vás provede instalací [vývojové sady Azure blockchain pro ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) a nastavením vašeho vývojového prostředí blockchain.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps vám pomůžou při plánování, automatizaci obchodních procesů a pracovních postupů, pokud potřebujete integrovat systémy a služby. Nejprve vytvoříte logiku, která používá konektor Ethereem blockchain.

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek > integraci > Aplikace logiky**.
1. V části **vytvořit aplikaci logiky**zadejte podrobnosti o tom, kde vytvořit aplikaci logiky. Až budete hotovi, vyberte **vytvořit**.

    Další informace o vytváření Azure Logic Apps naleznete v tématu [Create Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Až Azure nasadí vaši aplikaci, vyberte prostředek aplikace logiky.
1. V Návrháři Logic Apps v části **šablony**vyberte **prázdná aplikace logiky**.

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

Konektor Ethereem blockchain má jednu Trigger a několik akcí. Aktivační událost nebo akce, které použijete, závisí na vašem scénáři.

V případě pracovního postupu vyberte jednu z následujících částí:

* Aktivuje se, když dojde k události v blockchain, a [použijte Trigger události](#use-the-event-trigger).
* Slouží k dotazování nebo nasazení inteligentních kontraktů, [použijte akce](#use-actions).
* [Vygenerujte pracovní postup pomocí sady Developer Kit](#generate-a-workflow), který následuje po běžném scénáři.

## <a name="use-the-event-trigger"></a>Použití triggeru události

Pokud chcete, aby se aplikace logiky spouštěla po výskytu události Smart Contract, použijte triggery Ethereem blockchain události. Například chcete odeslat e-mail při volání funkce inteligentního kontraktu.

1. V návrháři aplikace logiky vyberte konektor Ethereem blockchain.
1. Na kartě **aktivační události** vyberte, **kdy dojde k události inteligentního kontraktu**.
1. Změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure blockchain.
1. Zadejte podrobnosti o inteligentní smlouvě, u které chcete kontrolovat události.

    ![Vlastnosti triggeru události](./media/ethereum-logic-app/event-properties.png)

    | Vlastnost | Popis |
    |----------|-------------|
    | **ABI kontraktu** | Binární rozhraní aplikace (ABI) definuje rozhraní inteligentních kontraktů. Jak [získat kontrakt ABI](#get-contract-abi). |
    | **Adresa inteligentního kontraktu** | Adresa kontraktu je adresa cíle inteligentního kontraktu na Ethereem blockchain. Jak [získat adresu kontraktu](#get-contract-address). |
    | **Název události** | Vyberte událost inteligentního kontraktu, kterou chcete ověřit. Událost aktivuje aplikaci logiky. |
    | **Interval** a **frekvence** | Vyberte, jak často chcete událost kontrolovat. |

1. Vyberte **Save** (Uložit).

K dokončení vaší aplikace logiky můžete přidat nový krok, který provede akci na základě triggeru události Ethereem blockchain. Například odešlete e-mail.

## <a name="use-actions"></a>Akce použití

Akce blockchain Ethereem použijte, pokud chcete, aby aplikace logiky prováděla akci v hlavní knize blockchain. Například chcete vytvořit mikroslužbu založenou na REST, která volá funkci inteligentního kontraktu, když se v aplikaci logiky provede požadavek HTTP.

Akce konektoru vyžadují Trigger. Akci konektoru blockchain Ethereem můžete použít jako další krok po triggeru. Například Trigger požadavku HTTP pro microsservice.

1. V návrháři aplikace logiky vyberte **Nový krok** za aktivační událostí.
1. Vyberte konektor Ethereem blockchain.
1. Na kartě **Akce** vyberte jednu z dostupných akcí.

    ![Vlastnosti akce](./media/ethereum-logic-app/action-properties.png)

1. Změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure blockchain.
1. V závislosti na akci, kterou jste zvolili, zadejte následující podrobnosti o funkci inteligentního kontraktu.

    | Vlastnost | Popis |
    |----------|-------------|
    | **ABI kontraktu** | Binární rozhraní aplikace (ABI) definuje rozhraní inteligentních kontraktů. Jak [získat kontrakt ABI](#get-contract-abi). |
    | **Bajtový kód kontraktu** | Kód zkompilovaného bajtu inteligentního kontraktu. Jak [získat bajtový kód kontraktu](#get-contract-bytecode). |
    | **Adresa inteligentního kontraktu** | Adresa kontraktu je adresa cíle inteligentního kontraktu na Ethereem blockchain. Jak [získat adresu kontraktu](#get-contract-address). |
    | **Název funkce inteligentního kontraktu** | Vyberte název funkce inteligentního kontraktu pro akci. Seznam se vyplní z podrobností v kontraktu ABI. |

    Po výběru názvu funkce inteligentního kontraktu se můžou zobrazit povinná pole pro parametry funkce. Zadejte hodnoty nebo dynamický obsah požadovaný pro váš scénář.

Teď můžete použít aplikaci logiky. Při aktivaci události aplikace logiky se spustí akce Ethereem blockchain. Například Trigger požadavku HTTP spustí akci Ethereem blockchain a provede dotaz na hodnotu stavu inteligentního kontraktu, což vede k odpovědi HTTP, která vrací hodnotu.

## <a name="generate-a-workflow"></a>Generování pracovního postupu

Blockchain Development Kit pro Azure pro Ethereem Visual Studio Code může vygenerovat pracovní postupy aplikace logiky pro běžné scénáře. K dispozici jsou čtyři scénáře:

* Publikování dat na Azure SQL Database
* Publikování událostí do Azure Event Grid nebo Azure Service Bus
* Publikování sestav
* Mikroslužba založená na REST

 Azure blockchain Development Kit používá Truffle ke zjednodušení vývoje blockchain. K vygenerování aplikace logiky založené na inteligentní smlouvě potřebujete řešení Truffle pro inteligentní kontrakt. Budete také potřebovat připojení k vaší síti konsorcia Azure blockchain Service. Další informace najdete v tématu [použití Visual Studio Code pro připojení k rychlému startu sítě služby Azure blockchain Service Consortium](connect-vscode.md).

Následující kroky například generují aplikaci Logic mikroslužeb založenou na REST založené na **HelloBlockchain** Smart Contract pro rychlé zprovoznění:

1. Na bočním panelu Průzkumníka VS Code rozbalte složku **smluv** ve vašem řešení.
1. Klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte **Generovat mikroslužby pro inteligentní kontrakty** .

    ![Generování aplikace logiky](./media/ethereum-logic-app/generate-logic-app.png)

1. V paletě příkazů vyberte **Aplikace logiky**.
1. Zadejte **adresu kontraktu**. Další informace najdete v tématu [Jak získat adresu kontraktu](#get-contract-address).
1. Vyberte předplatné Azure a skupinu prostředků pro aplikaci logiky.

    Konfigurace aplikace logiky a soubory kódu jsou generovány v adresáři **generatedLogicApp** .

1. Zobrazit adresář **generatedLogicApp/HelloBlockchain** Pro každou funkci, událost a vlastnost inteligentního kontraktu je k dispozici soubor JSON aplikace logiky.
1. Otevřete **generatedLogicApp/HelloBlockchain/Service/Property. Soubor RequestMessage. logicapp. JSON** a obsah zkopírujte.

    ![JSON pro vlastnost RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. V aplikaci logiky vyberte **zobrazení kód aplikace logiky**. Nahraďte existující kód JSON vygenerovaným JSON App Apps.

    ![Nahrazení konfigurace aplikace logiky v zobrazení kódu](./media/ethereum-logic-app/code-view.png)

1. Vyberte **Návrhář** a přepněte do zobrazení návrháře.
1. Aplikace logiky obsahuje základní kroky pro scénář. Je ale potřeba aktualizovat podrobnosti o konfiguraci konektoru Ethereem blockchain.
1. Vyberte krok **připojení** a změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure blockchain.

    ![Aplikace logiky mikroslužeb](./media/ethereum-logic-app/microservice-logic-app.png)

1. Teď můžete použít aplikaci logiky. Pokud chcete otestovat mikroslužbu založenou na REST, vydejte požadavek HTTP POST na adresu URL požadavku aplikace logiky. Zkopírujte **adresu URL post protokolu HTTP** z kroku **přijetí požadavku HTTP** .

    ![ADRESA URL PRO POST HTTP](./media/ethereum-logic-app/post-url.png)

1. K vytvoření požadavku HTTP POST použijte oblé. Text zástupného textu **\<HTTP post URL @ no__t-2** nahraďte adresou URL z předchozího kroku.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Příkaz složené vratky vrátí odpověď z aplikace logiky. V tomto případě výstup z funkce **RequestMessage** Smart Contract.

    ![Výstup vlastnosti RequestMessage](./media/ethereum-logic-app/curl.png)

Další informace o používání vývojové sady najdete na stránce [wiki pro ethereem na webu Azure blockchain Development Kit](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) .

## <a name="create-an-api-connection"></a>Vytvoření připojení rozhraní API

Pro konektor blockchain Ethereem se vyžaduje připojení rozhraní API k blockchain. Konektor API můžete použít pro několik aplikací logiky. Některé vlastnosti jsou povinné a jiné závisí na vašem scénáři.

> [!IMPORTANT]
> Pro vytváření transakcí na blockchain se vyžaduje privátní klíč nebo adresa účtu a heslo. Je nutné zadat pouze jednu formu ověřování. Nemusíte zadávat privátní klíč a podrobnosti účtu. Dotazování na smlouvy nevyžaduje transakci. Pokud používáte akce, které dotazují na stav smlouvy, není vyžadován privátní klíč nebo adresa účtu a heslo.

Pokud chcete nastavit připojení ke členu služby Azure blockchain, je možné, že v závislosti na vašem scénáři budete možná potřebovat následující seznam vlastností.

| Vlastnost | Popis |
|----------|-------------|
|**Název připojení** | Název připojení rozhraní API Povinná hodnota. |
|**Koncový bod ethereem RPC** | Adresa HTTP uzlu transakce služby Azure blockchain. Povinná hodnota. Jak [získat koncový bod RPC](#get-rpc-endpoint) |
|**Privátní klíč** | Privátní klíč účtu ethereem Pro transakce se vyžaduje privátní klíč nebo adresa účtu a heslo. Jak [získat privátní klíč](#get-private-key). |
|**Adresa účtu** | Adresa členského účtu služby Azure blockchain Pro transakce se vyžaduje privátní klíč nebo adresa účtu a heslo. Jak [získat adresu účtu](#get-account-address). |
|**Heslo účtu** | Heslo účtu je nastaveno při vytváření člena. Informace o resetování hesla najdete v tématu [ethereem Account](consortium.md#ethereum-account).|

## <a name="get-rpc-endpoint"></a>Získat koncový bod RPC

Adresa koncového bodu RPC služby Azure blockchain se vyžaduje pro připojení k síti blockchain. Adresu koncového bodu můžete získat pomocí Azure blockchain Development Kit pro Ethereem nebo Azure Portal.

**Pomocí vývojové sady:**

1. V části **Služba Azure blockchain** v Visual Studio Code klikněte pravým tlačítkem na konsorcium.
1. Vyberte položku **Kopírovat koncový bod RPC**.

    ![Kopírovat koncový bod RPC](./media/ethereum-logic-app/devkit-rpc.png)

    Koncový bod RPC se zkopíruje do schránky.

**Použití Azure Portal:**

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Přejděte ke členu služby Azure blockchain. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/ethereum-logic-app/transaction-nodes.png)

1. Vyberte **připojovací řetězce > přístupové klíče**.
1. Zkopírujte adresu koncového bodu z **https (přístupová klávesa 1)** nebo přístupová klávesa 2.

    ![Připojovací řetězec](./media/ethereum-logic-app/connection-string.png)

    Koncovým bodem RPC je adresa URL protokolu HTTPS včetně adresy a přístupového klíče k uzlu transakce člena služby Azure blockchain.

## <a name="get-private-key"></a>Získat privátní klíč

Privátní klíč účtu Ethereem se dá použít k ověření při odeslání transakce do blockchain. Veřejný a privátní klíč účtu Ethereem se vygenerují z 12 slovových instrukcí. Azure blockchain Development Kit pro Ethereem generuje instrukci, když se připojíte k členovi konsorcia Azure blockchain Service. Adresu koncového bodu můžete získat pomocí rozšíření vývojové sady.

1. V Visual Studio Code otevřete paletu příkazů (F1).
1. Vyberte **Azure blockchain: načíst privátní klíč**.
1. Vyberte symbol, který jste uložili při připojování ke členu konsorcia.

    ![Výběr klávesové zkratky](./media/ethereum-logic-app/private-key.png)

    Privátní klíč se zkopíruje do schránky.

## <a name="get-account-address"></a>Získat adresu účtu

Členský účet a heslo lze použít k ověření při odeslání transakce do blockchain. Heslo je nastaveno při vytváření člena.

1. V Azure Portal navštivte stránku Přehled služby Azure blockchain.
1. Zkopírujte adresu **členského účtu** .

    ![Kopírovat členský účet](./media/ethereum-logic-app/member-account.png)

Další informace o adrese a hesle účtu najdete v tématu [ethereem Account](consortium.md#ethereum-account).

## <a name="get-contract-abi"></a>Získat kontrakt ABI

Binární rozhraní aplikace (ABI) definuje rozhraní inteligentních kontraktů. Popisuje, jak pracovat s inteligentní smlouvou. Můžete získat kontrakt ABI pomocí vývojové sady Azure blockchain pro Ethereem nebo ze souboru metadat smlouvy kompilátoru Soliding Compiler.

**Pomocí vývojové sady:**

Pokud jste k vytváření inteligentních kontraktů použili vývojovou sadu nebo Truffle, můžete použít rozšíření ke zkopírování kontraktu ABI do schránky.

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte **Kopírovat kontrakt ABI**.

    ![Kopírování kontraktu ABI pomocí DevKit](./media/ethereum-logic-app/abi-devkit.png)

    Kontrakt ABI je zkopírován do schránky.

**Použití souboru metadat kontraktu:**

1. Otevřete soubor s metadaty kontraktu obsaženým ve složce **Build/Contracts** projektu Solid of. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. V souboru JSON vyhledejte část **ABI** .
1. Zkopírujte pole **ABI** JSON.

    ![Část kontraktu ABI v metadatech](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Získání bajtového kódu smlouvy

Bajtový kód kontraktu je kompilovaná chytrá smlouva spuštěná virtuálním počítačem s Ethereem. Můžete získat bajtový kód kontraktu pomocí Azure blockchain Development Kit pro Ethereem nebo z kompilátoru Soliding.

**Pomocí vývojové sady:**

Pokud jste k vytváření inteligentních kontraktů použili vývojovou sadu nebo Truffle, můžete použít rozšíření ke zkopírování bajtového kódu kontraktu do schránky.

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte **Kopírovat bajtový kód kontraktu**.

    ![Kopírování bajtového kódu kontraktu pomocí DevKit](./media/ethereum-logic-app/bytecode-devkit.png)

    Bajtový kód kontraktu je zkopírován do schránky.

**Použití souboru metadat kontraktu:**

1. Otevřete soubor s metadaty kontraktu obsaženým ve složce **Build/Contracts** projektu Solid of. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. V souboru JSON vyhledejte prvek **bytového kódu** .
1. Zkopírujte hodnotu **bajtového kódu** .

    ![Kopírování bytového kódu pomocí metadat](./media/ethereum-logic-app/bytecode-metadata.png)

**Použití kompilátoru hustota:**

K vygenerování bajtového kódu kontraktu použijte příkaz `solc --bin <smart contract>.sol`.

## <a name="get-contract-address"></a>Získat adresu kontraktu

Adresa kontraktu je adresa cíle inteligentního kontraktu na Ethereem blockchain. Tuto adresu použijete k odeslání transakce nebo stavu dotazu pro inteligentní kontrakt. Adresu kontraktu můžete získat z výstupu migrace Truffle nebo ze souboru s metadaty kontraktu.

**Použití Truffle migrace výstupu:**

Truffle zobrazí adresu kontraktu po nasazení čipové smlouvy. Zkopírujte **adresu kontraktu** z výstupu.

![Adresa kontraktu z výstupu Truffle](./media/ethereum-logic-app/contract-address-truffle.png)

**Použití souboru metadat kontraktu:**

1. Otevřete soubor s metadaty kontraktu obsaženým ve složce **Build/Contracts** projektu Solid of. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. V souboru JSON vyhledejte oddíl **sítě** .
1. Privátní sítě jsou identifikovány pomocí celočíselného identifikátoru ID sítě. V části síť Najděte hodnotu adresa.
1. Zkopírujte hodnotu **adresa** .

![Adresa kontraktu z metadat](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Další kroky

Sledujte běžné [scénáře propojení blockchain pomocí Azure Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
