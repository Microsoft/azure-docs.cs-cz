---
title: 'Rychlý Start: QnA Maker klientskou knihovnu pro Ruby'
description: V tomto rychlém startu se dozvíte, jak začít s knihovnou klienta QnA Maker pro Ruby.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: ef0db373dc6faaa470470b8169fdb6ae61aa8dde
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982763"
---
Použijte QnA Maker klientskou knihovnu pro Ruby do:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Získat klíč koncového bodu předpovědi za běhu
* Počkat na dlouhodobě běžící úlohu
* Stáhnout znalostní báze
* Získání odpovědi
* Odstranit znalostní bázi

[Zdrojový kód knihovny (vytváření obsahu)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated)  |  [Zdrojový kód knihovny (Runtime)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated)  |  [Balíček (vytváření obsahu)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker)  |  [Balíček (Runtime)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime)  |  [Ukázky Ruby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Ruby 2. x](https://www.ruby-lang.org/)
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-libraries"></a>Instalace klientských knihoven

Po instalaci Ruby můžete klientské knihovny nainstalovat pomocí nástroje:

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>Vytvoření nové aplikace v Ruby

Vytvořte nový soubor s názvem `quickstart.rb` a importujte následující knihovny.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.
> K vytvoření vaší znalostní báze budete potřebovat celý klíč. Z koncového bodu potřebujete jenom název prostředku. Formát je `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` .
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](https://docs.microsoft.com/azure/key-vault/key-vault-overview) poskytuje zabezpečené úložiště klíčů.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>Objektové modely

QnA Maker používá dva různé objektové modely:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování a stažení znalostní báze Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** je objekt pro dotazování znalostní báze pomocí rozhraní GenerateAnswer API a posílání nových navrhovaných dotazů pomocí rozhraní API pro vlaky (jako součást [aktivního učení](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Objektový model QnAMakerClient

QnA Maker klient pro vytváření obsahu je objekt [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) , který se ověřuje v Azure pomocí MsRest:: ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte metody vlastnosti [znalostní](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) báze klienta k vytvoření, správě a publikování znalostní báze.

Pro okamžité operace metoda obvykle vrací výsledek, pokud existuje. V případě dlouhotrvajících operací je odezva objektem [operace](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) . Zavolejte [operaci. get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) metodu s `operation.operation_id` hodnotou pro určení [stavu požadavku](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb).

### <a name="qnamakerruntimeclient-object-model"></a>Objektový model QnAMakerRuntimeClient

Běhový QnA Maker klient je objekt [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) .

Po publikování znalostní báze pomocí klienta pro vytváření obsahu použijte k získání odpovědi ze znalostní báze běhový klient [. generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) metodu.

Když zavoláte `generate_answer` , předejte hodnotu hash pro `custom_headers` volitelný parametr. Tato hodnota hash by měla obsahovat klíč `Authorization` a hodnotu `EndpointKey YOUR_ENDPOINT_KEY` . Pro hodnotu YOUR_ENDPOINT_KEY použijte k volání [endpoint_keys. get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32)klienta pro vytváření.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Ověřování klienta pro vytváření znalostní báze

Vytvořte instanci klienta s koncovým bodem pro vytváření obsahu a klíčem předplatného.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb) .
    * Chcete-li použít metadata a výzvy pro následné zpracování, použijte redakční kontext, protože tato data jsou přidána na jednotlivé úrovně páru QnA.
* Pro **soubory**použijte objekt [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) . FileDTO zahrnuje název souboru i veřejnou adresu URL pro přístup k souboru.
* V případě **adres URL**použijte seznam řetězců, které reprezentují veřejně dostupné adresy URL.

Zavolejte metodu [Create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) a potom předejte `operation_id` vlastnost vrácené operace do [operací. get_details](#get-status-of-an-operation) metoda pro dotazování na stav.

Poslední řádek následujícího kódu vrátí ID znalostní báze.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat voláním znalostní báze [. aktualizujte](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) a předáte ID znalostní báze a objekt [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) . Tento objekt zase může obsahovat:
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

Předejte `operation_id` vlastnost vracené operace do [operací Operations. get_details](#get-status-of-an-operation) pro cyklické dotazování na stav.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

K stažení databáze jako seznamu [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb)použijte metodu [znalostní báze. download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) . Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor TSV.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [znalostní báze. Publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>Získat klíč běhu dotazu

Po publikování znalostní báze budete potřebovat klíč koncového bodu modulu runtime pro dotazování znalostní báze. To není stejné jako klíč předplatného, který se používá k vytvoření klienta pro vytváření obsahu.

K získání objektu [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) použijte metodu [endpoint_keys. get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) .

Použijte jednu z klíčových vlastností vrácených v objektu k dotazování znalostní báze.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro vygenerování odpovědi

Vytvořte [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) pro dotazování znalostní báze a vygenerujte odpověď nebo vlak z aktivního učení.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

Použijte QnAMakerRuntimeClient k získání odpovědi ze znalostí nebo k odeslání nových navrhovaných otázek do znalostní báze pro [aktivní učení](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí metody RuntimeClient. Runtime. generateAnswer. Tato metoda přijímá ID znalostní báze a QueryDTO. Získejte přístup k dalším vlastnostem QueryDTO, jako je například začátek a kontext, který se má použít v robotu chatu.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

Toto je jednoduchý příklad dotazování znalostní báze. Pokud chcete pochopit pokročilé scénáře dotazování, Projděte si [Další příklady dotazů](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [znalostní báze. Delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) s parametrem ID znalostní báze.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) . K určení stavu původní metody použijte ID operace z operace k dotazování (s logikou opakování).

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>Spuštění aplikace

Toto je metoda Main pro aplikaci.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

Spusťte aplikaci pomocí příkazu Ruby v souboru pro rychlý Start.

```console
ruby quickstart.rb
```

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb).
