---
title: Začínáme s kombinovanými komponentami sady SDK pro rozhraní .NET Communication Services
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak začít s kombinovanými komponentami rozhraní UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 17a8369463a27acffc8bdc52c48e4ae0624f41cd
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539815"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Rychlý Start: Začínáme s kombinovanými komponentami architektury uživatelského rozhraní

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Začněte s komunikačními službami Azure pomocí architektury uživatelského rozhraní, abyste mohli rychle integrovat komunikační prostředí do svých aplikací. V tomto rychlém startu se dozvíte, jak integrovat do vaší aplikace složené součásti uživatelského rozhraní, abyste mohli vytvářet komunikační prostředí.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (doporučeno uzlem 12).
- Aktivní prostředek služby Communication Services. [Vytvořte prostředek služby Communications](./../create-communication-resource.md).
- Přístupový token uživatele pro vytvoření instance složeného volání. Naučte se [vytvářet a spravovat tokeny přístupu uživatelů](./../access-tokens.md).

## <a name="setting-up"></a>Nastavení

ROZHRANÍ .NET Framework vyžaduje, aby bylo nastavené prostředí pro reakce. Pak to provedeme. Pokud už aplikaci reagujete, můžete tuto část přeskočit.

### <a name="set-up-react-app"></a>Nastavit reakci aplikace

Pro tento rychlý Start budeme používat šablonu vytvořit aplikaci s poreakcim. Další informace najdete v tématu o tom, [jak začít reagovat](https://reactjs.org/docs/create-a-new-react-app.html) .

```console

npx create-react-app my-app

cd my-app

```

Na konci tohoto procesu by měla být ve složce úplná aplikace `my-app` . V tomto rychlém startu budeme měnit soubory ve `src` složce.

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte pro JavaScript službu Azure Communication Services volání klientské knihovny. Přesuňte poskytnuté tarballu (Private Preview) do adresáře moje aplikace.

```console

//Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

### <a name="run-create-react-app"></a>Spustit aplikaci pro vytvoření reakce

Vyzkoušíme instalaci aplikace informující reakce spuštěním:

```console

npm run start 

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny uživatelského rozhraní služby Azure Communication Services:

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | Složená komponenta, která vykresluje možnosti volání skupiny s galerií a ovládacími prvky účastníka. |
| GroupChat | Složená komponenta, která vykresluje prostředí pro konverzaci pomocí konverzačního vlákna a vstupu |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Nainicializovat složené komponenty pro volání skupin a skupinového chatu

Přejít do `src` složky uvnitř `my-app` a vyhledat soubor `app.js` . Tady vymažeme následující kód, který inicializuje naše složené komponenty pro konverzaci a volání skupin. Můžete zvolit, která z nich se má použít v závislosti na typu komunikačního prostředí, které sestavujete. V případě potřeby můžete použít obojí současně. Abyste mohli komponenty inicializovat, budete potřebovat přístupový token načtený z komunikačních služeb Azure. Podrobnosti o tom, jak získat přístupové tokeny, najdete v tématu: [vytváření a Správa tokenů přístupu uživatelů](./../access-tokens.md).

> [!NOTE]
> Komponenty negenerují přístupové tokeny, ID skupin nebo ID vláken. Tyto prvky pocházejí ze služeb, které procházejí pomocí správných kroků ke generování těchto ID a předávají je klientské aplikaci. Další informace najdete v tématu [Architektura klientského serveru](./../../concepts/client-and-server-architecture.md).
> 
> Například: složený ze skupinového chatu očekává, že přidružený k, který se `userId` `token` používá k inicializaci, již byl připojen k `threadId` poskytovanému. Pokud token nebyl připojen k ID podprocesu, zůstane kompozitní konverzace skupiny neúspěšná. Další informace o chatu najdete v tématu: [Začínáme pomocí chatu](./../chat/get-started.md) .


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>Spustit rychlý Start

Chcete-li spustit kód výše, použijte příkaz:

```console

npm run start 

```

K plnému otestování schopností budete potřebovat druhého klienta s funkcí volání a chat pro připojení ke vláknu volání a chatu. Podívejte se na naše [volání Sample Hero Sample](./../../samples/calling-hero-sample.md) a [chat Hero Sample](./../../samples/chat-hero-sample.md) jako potenciální možnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyzkoušet základní komponenty rozhraní UI Framework](./get-started-with-components.md)

Další informace naleznete v následujících zdrojích:
- [Přehled rozhraní .NET Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Možnosti architektury uživatelského rozhraní](./../../concepts/ui-framework/ui-sdk-features.md)
