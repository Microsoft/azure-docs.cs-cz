---
title: Vytvoření vlastní komponenty architektury uživatelského rozhraní
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak vytvořit vlastní komponentu kompatibilní s architekturou uživatelského rozhraní.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 798b898f6e2367e3096bcf985715fd48de3d394f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930213"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Rychlý Start: Vytvoření vlastní komponenty architektury uživatelského rozhraní

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Začněte s komunikačními službami Azure pomocí architektury uživatelského rozhraní, abyste mohli rychle integrovat komunikační prostředí do svých aplikací.

V tomto rychlém startu se dozvíte, jak vytvořit vlastní komponenty pomocí předdefinovaného rozhraní stavu nabízeného architekturou uživatelského rozhraní. Tento přístup je ideální pro vývojáře, kteří potřebují další přizpůsobení a chtějí používat vlastní prostředky návrhu pro prostředí. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (doporučeno uzlem 12).
- Aktivní prostředek služby Communication Services. [Vytvořte prostředek služby Communications](./../create-communication-resource.md).
- Přístupový token uživatele pro vytvoření instance klienta volání. Naučte se [vytvářet a spravovat tokeny přístupu uživatelů](./../access-tokens.md).

Architektura uživatelského rozhraní vyžaduje, aby bylo nastavené prostředí pro reakce. Pak to provedeme. Pokud už aplikaci reagujete, můžete tuto část přeskočit.

### <a name="set-up-react-app"></a>Nastavit reakci aplikace

Pro tento rychlý Start použijeme šablonu pro vytvoření reakce na aplikaci. Další informace najdete v tématu o tom, [jak začít reagovat](https://reactjs.org/docs/create-a-new-react-app.html) .

```console

npx create-react-app my-app

cd my-app

```

Na konci tohoto procesu byste měli mít úplnou aplikaci v rámci této složky `my-app` . V tomto rychlém startu budeme měnit soubory ve `src` složce.

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujete službu Azure Communication Services, která volá sadu SDK pro JavaScript. Přesuňte poskytnuté tarballu (Private Preview) do adresáře moje aplikace.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

### <a name="run-create-react-app"></a>Spustit aplikaci pro vytvoření reakce

Vyzkoušíme instalaci aplikace informující reakce spuštěním:

```console

npm run start   

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady SDK uživatelského rozhraní služby Azure Communication Services:

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Poskytovatel| Poskytovatel uživatelského rozhraní Fluent, který umožňuje vývojářům upravovat základní součásti uživatelského rozhraní Fluent|
| CallingProvider| Volání poskytovatele pro vytvoření instance volání. Požadováno pro přidání základních součástí|
| ChatProvider | Poskytovatel chatu pro vytvoření instance konverzačního vlákna. Požadováno pro přidání základních součástí|
| connectFuncsToContext | Metoda připojení komponent architektury uživatelského rozhraní k základním poskytovatelům pomocí mapovačů |
| MapToChatMessageProps | Mapovač vrstev dat zprávy chatu, který poskytuje komponenty se zprávami chatu |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Inicializovat poskytovatele chatu pomocí přihlašovacích údajů služby Azure Communication Services

V tomto rychlém startu budeme používat chat jako příklad. Další informace o volání najdete v tématu rychlý Start [základních komponent](./get-started-with-components.md) a [rychlý Start složených komponent](./get-started-with-composites.md).

Přejít do `src` složky uvnitř `my-app` a vyhledat soubor `app.js` . Tady vymažeme následující kód pro inicializaci našeho poskytovatele chatu. Tento poskytovatel se účtuje za udržování kontextu volání a konverzačních prostředí. Abyste mohli komponenty inicializovat, budete potřebovat přístupový token načtený z komunikačních služeb Azure. Podrobnosti o tom, jak získat přístupový token, najdete v tématu: [vytváření a Správa přístupových tokenů](./../access-tokens.md).

Komponenty architektury uživatelského rozhraní se řídí stejnou obecnou architekturou pro zbytek služby. Komponenty negenerují přístupové tokeny, ID skupin nebo ID vláken. Tyto prvky pocházejí ze služeb, které procházejí pomocí správných kroků ke generování těchto ID a předávají je klientské aplikaci. Další informace najdete v tématu [Architektura klientského serveru](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Po inicializaci vám tento poskytovatel umožní vytvořit vlastní rozložení pomocí komponenty rozhraní uživatelského rozhraní a jakékoli další logiky rozložení. Poskytovatel se postará o inicializaci všech základních logik a správné propojení různých komponent. V dalším kroku vytvoříme vlastní komponentu pomocí mapovačů rozhraní UI pro připojení k našemu poskytovateli chatu.


## <a name="create-a-custom-component-using-mappers"></a>Vytvoření vlastní komponenty pomocí mapovačů

Začneme vytvořením nového souboru s názvem `SimpleChatThread.js` , kde vytvoříme komponentu. Začneme importem komponent rozhraní .NET Framework, které budeme potřebovat. Tady použijeme z pole HTML a reagujeme na vytvoření plně vlastní komponenty pro jednoduché chatovací vlákno. Pomocí metody použijeme `connectFuncsToContext` `MapToChatMessageProps` mapovače k mapování props na  `SimpleChatThread` vlastní komponenty. Tyto props nám poskytnou přístup k odesílaným a přijímaným zprávám chatu, aby je naplnili do našeho jednoduchého vlákna.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Přidání vlastní komponenty do aplikace

Teď, když máme naši vlastní komponentu připravenou, naimportujeme ji a přidáme ji do rozložení.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Spustit rychlý Start

Chcete-li spustit kód výše, použijte příkaz:

```console

npm run start   

```

K plnému otestování schopností budete potřebovat druhého klienta s funkcí chatu pro posílání zpráv, které se budou přijímat pomocí našeho jednoduchého konverzačního vlákna. Podívejte se na naše [volání Sample Hero Sample](./../../samples/calling-hero-sample.md) a [chat Hero Sample](./../../samples/chat-hero-sample.md) jako potenciální možnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyzkoušet složené součásti architektury uživatelského rozhraní](./get-started-with-composites.md)

Další informace naleznete v následujících zdrojích:
- [Přehled rozhraní .NET Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Možnosti architektury uživatelského rozhraní](./../../concepts/ui-framework/ui-sdk-features.md)
- [Základní komponenty rozhraní UI Framework – rychlý Start](./get-started-with-components.md)
