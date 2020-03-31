---
title: Použití konektoru Ethereum Blockchain s Azure Logic Apps – Azure Blockchain Service
description: Pomocí konektoru Ethereum Blockchain s Azure Logic Apps můžete aktivovat inteligentní smluvní funkce a reagovat na události inteligentních smluv.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: chrisseg
ms.openlocfilehash: 4a9acfd6098ed45fd92c7e3047b5d1446eeddbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325226"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Použití konektoru Ethereum Blockchain s aplikacemi Azure Logic Apps

Pomocí [konektoru Ethereum Blockchain](https://docs.microsoft.com/connectors/blockchainethereum/) s [azure logic apps](https://docs.microsoft.com/azure/logic-apps/) můžete provádět akce inteligentních smluv a reagovat na události inteligentních smluv. Řekněme například, že chcete vytvořit mikroslužbu založenou na REST, která vrací informace z hlavní knihy blockchainu. Pomocí aplikace logiky můžete přijímat požadavky HTTP, které dotazují informace uložené v hlavní knize blockchain.

## <a name="prerequisites"></a>Požadavky

Vyplňte volitelný předpoklad [Rychlý start: Pomocí kódu Visual Studio se připojte k síti konsorcia služby Azure Blockchain Service](connect-vscode.md). Rychlý start vás provede instalací [Azure Blockchain Development Kit pro ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) a nastavením vývojového prostředí blockchainu.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps vám pomůže naplánovat a automatizovat obchodní procesy a pracovní postupy, když potřebujete integrovat systémy a služby. Nejprve vytvoříte logiku, která používá konektor Ethereum Blockchain.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Integrace** > **Aplikace logiky**.
1. V části **Vytvořit aplikaci logiky**zadejte podrobnosti o tom, kde vytvořit aplikaci logiky. Až budete hotovi, vyberte **Vytvořit**.

    Další informace o vytváření aplikací logiky najdete v [tématu Vytváření automatizovaných pracovních postupů pomocí Aplikací logiky Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po nasazení aplikace Azure vyberte prostředek aplikace logiky.
1. V Návrháři aplikací **logiky**vyberte v části Šablony **možnost Blank Logic App**.

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

Konektor Ethereum Blockchain má jednu spoušť a několik akcí. Která aktivační událost nebo akce, kterou používáte, závisí na vašem scénáři.

Pokud váš pracovní postup:

* Aktivuje se, když dojde k události na [blockchainu, použijte aktivační událost](#use-the-event-trigger).
* Dotazy nebo nasazuje inteligentní [smlouvy, použít akce](#use-actions).
* Následuje běžný [scénář, Generovat pracovní postup pomocí sady pro vývojáře](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Použití aktivační události

Aktivační události Ethereum Blockchain použijte, když chcete, aby se aplikace logiky spustila po výskytu události inteligentní smlouvy. Například chcete odeslat e-mail, když je volána inteligentní funkce smlouvy.

1. V Návrháři aplikací logiky vyberte konektor Ethereum Blockchain.
1. Na kartě **Aktivační události** vyberte Když dojde k události **inteligentní smlouvy**.
1. Změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure Blockchain Service.
1. Zadejte podrobnosti o inteligentní smlouvě, kterou chcete zkontrolovat pro události.

    ![Návrhář aplikací logiky s vlastnostmi aktivační události](./media/ethereum-logic-app/event-properties.png)

    | Vlastnost | Popis |
    |----------|-------------|
    | **Smlouva ABI** | Binární rozhraní aplikace smlouvy (ABI) definuje rozhraní inteligentní smlouvy. Další informace naleznete [v tématu Získání smlouvy ABI](#get-the-contract-abi). |
    | **Inteligentní adresa smlouvy** | Adresa smlouvy je inteligentní cílová adresa smlouvy na blockchainu Ethereum. Další informace naleznete [v tématu Získání adresy smlouvy](#get-the-contract-address). |
    | **Název události** | Vyberte inteligentní událost smlouvy ke kontrole. Událost aktivuje aplikaci logiky. |
    | **Interval** a **frekvence** | Vyberte, jak často chcete událost kontrolovat. |

1. Vyberte **Uložit**.

Chcete-li dokončit aplikaci logiky, můžete přidat nový krok, který provede akci založenou na aktivační události Ethereum Blockchain. Například pošlete e-mail.

## <a name="use-actions"></a>Používání akcí

Akce Ethereum Blockchain použijte, když chcete, aby aplikace logiky provedla akci v hlavní knize blockchainu. Například chcete vytvořit mikroslužbu založené na REST, která volá inteligentní funkce smlouvy při požadavku HTTP do aplikace logiky.

Akce konektoru vyžadují aktivační událost. Můžete použít ethereum Blockchain konektor akce jako další krok po aktivační události, jako je například aktivační událost požadavku HTTP pro mikroslužbu.

1. V Návrháři aplikací logiky vyberte **Nový krok** za aktivační událostí.
1. Vyberte konektor Ethereum Blockchain.
1. Na kartě **Akce** vyberte jednu z dostupných akcí.

    ![Návrhář aplikací logiky s vlastnostmi akcí](./media/ethereum-logic-app/action-properties.png)

1. Změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure Blockchain Service.
1. V závislosti na vybrané akci zadejte následující podrobnosti o funkci inteligentní smlouvy.

    | Vlastnost | Popis |
    |----------|-------------|
    | **Smlouva ABI** | Smlouva ABI definuje inteligentní smluvní rozhraní. Další informace naleznete [v tématu Získání smlouvy ABI](#get-the-contract-abi). |
    | **Kód bytecode smlouvy** | Kompilovaný inteligentní kontrakt bytecode. Další informace naleznete [v tématu Získání kódu bytecode smlouvy](#get-the-contract-bytecode). |
    | **Inteligentní adresa smlouvy** | Adresa smlouvy je inteligentní cílová adresa smlouvy na blockchainu Ethereum. Další informace naleznete [v tématu Získání adresy smlouvy](#get-the-contract-address). |
    | **Název funkce inteligentní smlouvy** | Vyberte název funkce inteligentní smlouvy pro akci. Seznam je naplněn z podrobností ve smlouvě ABI. |

    Po výběru inteligentní název funkce smlouvy se mohou zobrazit požadovaná pole pro parametry funkce. Zadejte hodnoty nebo dynamický obsah potřebný pro váš scénář.

Teď můžete použít aplikaci logiky. Když se aktivuje událost aplikace logiky, spustí se akce Ethereum Blockchain. Aktivační událost požadavku HTTP například spustí akci blockchain ethereum, která zadá dotaz na hodnotu stavu inteligentní smlouvy. Výsledkem tohoto dotazu je odpověď HTTP, která vrací hodnotu.

## <a name="generate-a-workflow"></a>Generovat pracovní postup

Azure Blockchain Development Kit pro ethereum Visual Studio Code rozšíření můžete generovat pracovní postupy aplikace logiky pro běžné scénáře. K dispozici jsou čtyři scénáře:

* Publikování dat do instance Azure SQL Database
* Publikování událostí do instance Azure Event Grid nebo Azure Service Bus
* Publikování sestavy
* Mikroslužba založená na REST

 Azure Blockchain Development Kit používá Lanýže ke zjednodušení vývoje blockchainu. Chcete-li generovat aplikaci logiky založenou na inteligentní smlouvy, potřebujete řešení Lanýž pro inteligentní smlouvy. Potřebujete také připojení k síti konsorcia Azure Blockchain Service. Další informace najdete [v tématu Použití kódu Visual Studio pro připojení k síti konsorcia Azure Blockchain Service .](connect-vscode.md)

Například následující kroky generují aplikaci logiky mikroslužeb založenou na rest založené na inteligentním kontraktu quickstart **HelloBlockchain:**

1. Na postranním panelu průzkumníka kódu visual studia rozbalte složku **Smluv** ve vašem řešení.
1. Klikněte pravým tlačítkem myši na **HelloBlockchain.sol** a v nabídce vyberte **Generovat mikroslužby pro inteligentní kontrakty.**

    ![Podokno Kódu sady Visual Studio s výběrem Generovat mikroslužby pro inteligentní smlouvy](./media/ethereum-logic-app/generate-logic-app.png)

1. V paletě příkazů vyberte **Aplikaci logiky**.
1. Zadejte **adresu smlouvy**. Další informace naleznete [v tématu Získání adresy smlouvy](#get-the-contract-address).
1. Vyberte předplatné Azure a skupinu prostředků pro aplikaci logiky.

    Konfigurace aplikace logiky a soubory kódu jsou generovány v **adresáři generatedLogicApp.**

1. Zobrazení **generovaného adresáře LogicApp/HelloBlockchain.** Existuje soubor JSON aplikace logiky pro každou funkci inteligentní smlouvy, událost a vlastnost.
1. Otevřete **generatedLogicApp/HelloBlockchain/Service/property. RequestMessage.logicapp.json** a zkopírujte obsah.

    ![Soubor JSON s kódem ke kopírování](./media/ethereum-logic-app/requestmessage.png)

1. V aplikaci logiky vyberte **zobrazení kódu aplikace Logika**. Nahraďte existující JSON generovanou aplikací logiky JSON.

    ![Zobrazení kódu aplikace logiky s nově nahrazeným kódem aplikace](./media/ethereum-logic-app/code-view.png)

1. Vyberte **Návrhář,** chcete-li přepnout do zobrazení návrháře.
1. Aplikace logiky obsahuje základní kroky pro scénář. Je však nutné aktualizovat podrobnosti konfigurace konektoru Ethereum Blockchain.
1. Vyberte krok **Připojení** a změňte nebo [vytvořte připojení rozhraní API](#create-an-api-connection) ke službě Azure Blockchain.

    ![Zobrazení návrháře s výběrem Připojení](./media/ethereum-logic-app/microservice-logic-app.png)

1. Teď můžete použít aplikaci logiky. Chcete-li otestovat mikroslužbu založenou na rest, vydejte požadavek HTTP POST na adresu URL požadavku aplikace logiky. Zkopírujte obsah **adresy URL HTTP POST** z kroku Při přijetí požadavku **HTTP.**

    ![Podokno Návrhář eseje s adresou URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. K vytvoření požadavku HTTP POST použijte cURL. Nahraďte zástupný text * \<HTTP POST URL\> * adresou URL z předchozího kroku.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Příkaz cURL vrátí odpověď z aplikace logiky. V tomto případě je odpovědí výstup z funkce smart contract **RequestMessage.**

    ![Výstup kódu z funkce smart contract RequestMessage](./media/ethereum-logic-app/curl.png)

Další informace o používání vývojové sady najdete na [stránce Azure Blockchain Development Kit for Ethereum wiki .](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)

## <a name="create-an-api-connection"></a>Vytvoření připojení rozhraní API

Pro konektor Ethereum Blockchain je vyžadováno připojení ROZHRANÍ API k blockchainu. Konektor rozhraní API můžete použít pro více aplikací logiky. Některé vlastnosti jsou povinné a jiné závisí na scénáři.

> [!IMPORTANT]
> Pro vytváření transakcí na blockchainu je vyžadován soukromý klíč nebo adresa účtu a heslo. Je potřeba pouze jedna forma ověřování. Nemusíte zaznat podrobnosti o soukromém klíči i účtu. Dotazování smluv nevyžaduje transakci. Pokud používáte akce, které dotaz stavu smlouvy, soukromý klíč nebo adresu účtu a heslo nejsou vyžadovány.

Následující seznam má možné vlastnosti, které můžete potřebovat v závislosti na vašem scénáři, abyste mohli nastavit připojení k členovi služby Azure Blockchain Service.

| Vlastnost | Popis |
|----------|-------------|
|**Název připojení** | Název připojení rozhraní API. Povinná hodnota. |
|**Koncový bod RPC etherea** | HTTP adresa transakčního uzlu služby Azure Blockchain Service. Povinná hodnota. Další informace naleznete [v tématu Získání koncového bodu vzdáleného volání procedur](#get-the-rpc-endpoint). |
|**Privátní klíč** | Ethereum účet soukromý klíč. Pro transakce je vyžadovánsoukromý klíč nebo adresa účtu a heslo. Další informace naleznete [v tématu Získání soukromého klíče](#get-the-private-key). |
|**Adresa účtu** | Adresa členského účtu služby Azure Blockchain Service. Pro transakce je vyžadovánsoukromý klíč nebo adresa účtu a heslo. Další informace naleznete [v tématu Získání adresy účtu](#get-the-account-address). |
|**Heslo účtu** | Heslo účtu je nastaveno při vytváření člena. Informace o resetování hesla naleznete v [tématu ethereum account](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Získání koncového bodu vzdáleného volání procedur

Pro připojení k blockchainové síti je potřeba adresa koncového bodu Služby Azure Blockchain Service RPC. Adresu koncového bodu můžete získat pomocí Azure Blockchain Development Kit pro ethereum nebo portál Azure.

**Použití vývojové sady:**

1. V části **Azure Blockchain Service** v kódu Visual Studia klikněte pravým tlačítkem myši na konsorcium.
1. Vyberte **možnost Kopírovat adresu koncového bodu vzdáleného volání procedur**.

    ![Podokno Kódu sady Visual Studio zobrazující konsorcium s výběrem adresy koncového bodu Vzdáleného volání procedur copy](./media/ethereum-logic-app/devkit-rpc.png)

    Koncový bod vzdáleného volání procedur se zkopíruje do schránky.

**Použití portálu Azure:**

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na člena služby Azure Blockchain Service. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Stránka uzly transakcí s výběrem (výchozí uzel)](./media/ethereum-logic-app/transaction-nodes.png)

1. Vyberte **připojovací řetězce** > **Přístupové klíče**.
1. Zkopírujte adresu koncového bodu z **protokolu HTTPS (přístupový klíč 1)** nebo **HTTPS (přístupový klíč 2).**

    ![Portál Azure s přístupovými klíči pro připojovací řetězec](./media/ethereum-logic-app/connection-string.png)

    Koncový bod vzdáleného volání procedur je adresa URL HTTPS, která obsahuje adresu a přístupový klíč vašeho uzlu transakce člena služby Azure Blockchain Service.

## <a name="get-the-private-key"></a>Získání soukromého klíče

Soukromý klíč účtu Ethereum můžete použít k ověření při odesílání transakce do blockchainu. Veřejné a soukromé klíče vašeho účtu Ethereum jsou generovány z 12slovné mnemotechnické pomůcky. Azure Blockchain Development Kit pro Ethereum generuje mnemotechnické pomůcky, když se připojíte k členu konsorcia Služby Azure Blockchain. Adresu koncového bodu můžete získat pomocí rozšíření vývojové sady.

1. V kódu sady Visual Studio otevřete paletu příkazů (F1).
1. Vyberte **Azure Blockchain: Načíst soukromý klíč**.
1. Vyberte mnemotechnické pomůcky, které jste uložili při připojování ke členovi konsorcia.

    ![Paleta příkazů s možností výběru mnemotechnické pomůkyně](./media/ethereum-logic-app/private-key.png)

    Soukromý klíč se zkopíruje do schránky.

## <a name="get-the-account-address"></a>Získání adresy účtu

Můžete použít členský účet a heslo k ověření při odeslání transakce do blockchainu. Heslo je nastaveno při vytváření člena.

1. Na webu Azure Portal přejděte na stránku přehledu služby Azure Blockchain.
1. Zkopírujte adresu **členského účtu.**

    ![Stránka s přehledem s adresou členského účtu](./media/ethereum-logic-app/member-account.png)

Další informace o adrese a hesle účtu účtu Ethereum naleznete v [tématu Ethereum account](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Získejte smlouvu ABI

Smlouva ABI definuje inteligentní smluvní rozhraní. Popisuje, jak pracovat s inteligentní smlouvy. Smlouvu ABI můžete získat pomocí Azure Blockchain Development Kit pro ethereum. Můžete jej také získat ze souboru metadat smlouvy vytvořeného kompilátorem Solidity.

**Použití vývojové sady:**

Pokud jste k vytvoření inteligentního kontraktu použili vývojovou soupravu nebo lanýž, můžete rozšíření použít ke zkopírování smlouvy ABI do schránky.

1. V podokně průzkumníkkódu sady Visual Studio rozbalte složku **sestavení/smluv** projektu Solidity.
1. Klikněte pravým tlačítkem myši na soubor JSON metadat smlouvy. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Vyberte **možnost Kopírovat smlouvu ABI**.

    ![Podokno Kódu sady Visual Studio s výběrem ABI smlouvy copy](./media/ethereum-logic-app/abi-devkit.png)

    Smlouva ABI je zkopírována do schránky.

**Použití souboru metadat smlouvy:**

1. Otevřete soubor metadat smlouvy obsažený ve složce **sestavení/smluv** projektu Solidity. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Najděte sekci **abi** v souboru JSON.
1. Zkopírujte pole **abi** JSON.

    ![Kód ABI v souboru metadat smlouvy](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Získat kód bytecode smlouvy

Příkaz bytecode smlouvy je kompilovaný inteligentní kontrakt prováděný virtuálním počítačem Ethereum. Můžete získat smlouvy bytecode pomocí Azure Blockchain Development Kit pro ethereum. Můžete jej také získat z kompilátoru Solidity.

**Použití vývojové sady:**

Pokud jste k vytvoření inteligentnísmlouvy použili vývojovou sadu nebo lanýž, můžete pomocí rozšíření zkopírovat kód bytecode smlouvy do schránky.

1. V podokně průzkumníkkódu sady Visual Studio rozbalte složku **sestavení/smluv** projektu Solidity.
1. Klikněte pravým tlačítkem myši na soubor JSON metadat smlouvy. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Vyberte **možnost Kopírovat bajtový kód smlouvy**.

    ![Podokno Kódu sady Visual Studio s výběrem bajtového kódu smlouvy Copy Contract](./media/ethereum-logic-app/bytecode-devkit.png)

    Bajtový kód smlouvy je zkopírován do schránky.

**Použití souboru metadat smlouvy:**

1. Otevřete soubor metadat smlouvy obsažený ve složce **sestavení/smluv** projektu Solidity. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Najděte prvek **bytecode** v souboru JSON.
1. Zkopírujte hodnotu **bajtového kódu.**

    ![Podokno kódu sady Visual Studio s bajtovým kódem v metadatech](./media/ethereum-logic-app/bytecode-metadata.png)

**Použití kompilátoru Solidity:**

Příkaz slouží `solc --bin <smart contract>.sol` ke generování bajtového kódu smlouvy.

## <a name="get-the-contract-address"></a>Získat adresu smlouvy

Adresa smlouvy je inteligentní cílová adresa smlouvy na blockchainu Ethereum. Tato adresa slouží k odeslání transakce nebo stavu dotazu inteligentní smlouvy. Adresu smlouvy můžete získat z výstupu migrace lanýžů nebo ze souboru metadat smlouvy.

**Použití výstupu migrace lanýžů:**

Lanýž zobrazí adresu smlouvy po nasazení inteligentní smlouvy. Zkopírujte **adresu smlouvy** z výstupu.

![Výstup migrace lanýžů s adresou smlouvy v kódu Visual Studia](./media/ethereum-logic-app/contract-address-truffle.png)

**Použití souboru metadat smlouvy:**

1. Otevřete soubor metadat smlouvy obsažený ve složce **sestavení/smluv** projektu Solidity. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Část **sítě** naleznete v souboru JSON.
1. Privátní sítě jsou identifikovány id celé sítě. Najděte hodnotu adresy v části síť.
1. Zkopírujte hodnotu **adresy.**

![Metadata s hodnotou adresy v kódu sady Visual Studio](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na běžné scénáře ve videu [Další práce s Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
