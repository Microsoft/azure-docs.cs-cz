---
title: Začínáme se základními komponentami architektury Azure Communication Services UI
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak začít se základními komponentami uživatelského rozhraní.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488172"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Rychlý Start: Začínáme se základními komponentami uživatelského rozhraní

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Začněte s komunikačními službami Azure pomocí architektury uživatelského rozhraní, abyste mohli rychle integrovat komunikační prostředí do svých aplikací. V tomto rychlém startu se dozvíte, jak integrovat základní součásti uživatelského rozhraní do vaší aplikace pro vytváření komunikačních prostředí. 

Komponenty rozhraní uživatelského rozhraní jsou dodávány ve dvou charakterech: základní a složené.

- **Základní komponenty** reprezentují diskrétní možnosti komunikace; jsou to základní stavební kameny, které se dají použít k vytváření komplexních komunikačních prostředí. 
- **Složené komponenty** jsou uživatelsky funkční prostředí pro běžné komunikační scénáře, které byly sestaveny pomocí **základních komponent** jako stavební bloky a zabalené, aby je bylo možné snadno integrovat do aplikací.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (doporučeno uzlem 12).
- Aktivní prostředek služby Communication Services. [Vytvořte prostředek služby Communications](./../create-communication-resource.md).
- Přístupový token uživatele pro vytvoření instance klienta volání. Naučte se [vytvářet a spravovat tokeny přístupu uživatelů](./../access-tokens.md).

## <a name="setting-up"></a>Nastavení

ROZHRANÍ .NET Framework vyžaduje, aby bylo nastavené prostředí pro reakce. Pak to provedeme. Pokud už aplikaci reagujete, můžete tuto část přeskočit.

### <a name="set-up-react-app"></a>Nastavit reakci aplikace

Pro tento rychlý Start použijeme šablonu pro vytvoření reakce na aplikaci. Další informace najdete v tématu o tom, [jak začít reagovat](https://reactjs.org/docs/create-a-new-react-app.html) .

```console

npx create-react-app my-app

cd my-app

```

Na konci tohoto procesu by měla být ve složce úplná aplikace `my-app` . V tomto rychlém startu budeme měnit soubory ve `src` složce.

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte pro JavaScript službu Azure Communication Services volání klientské knihovny. Přesuňte poskytnuté tarballu (Private Preview) do adresáře moje aplikace.

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

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny uživatelského rozhraní služby Azure Communication Services:

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Poskytovatel| Poskytovatel uživatelského rozhraní Fluent, který umožňuje vývojářům upravovat základní součásti uživatelského rozhraní Fluent|
| CallingProvider| Volání poskytovatele pro vytvoření instance volání. Požadováno pro přidání dalších součástí|
| ChatProvider | Poskytovatel chatu pro vytvoření instance konverzačního vlákna. Požadováno pro přidání dalších součástí|
| MediaGallery | Základní komponenta, která zobrazuje účastníky volání a jejich vzdálené streamy videa |
| MediaControls | Základní komponenta pro řízení volání včetně ztlumení, videa, sdílení obrazovky |
| ChatThread | Základní komponenta, která vykreslí chatovací podproces se zadáním indikátorů, potvrzením čtení atd. |
| SendBox | Základní komponenta, která umožňuje uživateli zadat zprávy, které budou odeslány do připojeného vlákna|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Inicializovat poskytovatele volání a chatu pomocí přihlašovacích údajů služby Azure Communication Services

Přejít do `src` složky uvnitř `my-app` a vyhledat soubor `app.js` . Tady vymažeme následující kód, který inicializuje poskytovatele volání a chatu. Tito poskytovatelé jsou odpovědní za udržování kontextu volání a konverzačních prostředí. Můžete zvolit, která z nich se má použít v závislosti na typu komunikačního prostředí, které sestavujete. V případě potřeby můžete použít obojí současně. Abyste mohli komponenty inicializovat, budete potřebovat přístupový token načtený z komunikačních služeb Azure. Podrobnosti o tom, jak získat přístupové tokeny, najdete v tématu: [vytváření a Správa přístupových tokenů](./../access-tokens.md).

> [!NOTE]
> Komponenty negenerují přístupové tokeny, ID skupin nebo ID vláken. Tyto prvky pocházejí ze služeb, které procházejí pomocí správných kroků ke generování těchto ID a předávají je klientské aplikaci. Další informace najdete v tématu [Architektura klientského serveru](./../../concepts/client-and-server-architecture.md).
> 
> Například: poskytovatel chat očekává, že `userId` přidružený k, který se `token` používá k inicializaci, již byl připojen k `threadId` poskytovanému. Pokud token nebyl připojen k ID vlákna, zprostředkovatel konverzace se nezdaří. Další informace o chatu najdete v tématu: [Začínáme pomocí chatu](./../chat/get-started.md) .

K vylepšení vzhledu a chování aplikace budeme používat motiv Fluent UI:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Po inicializaci vám tento poskytovatel umožní vytvořit vlastní rozložení pomocí základních komponent rozhraní uživatelského rozhraní a jakékoli další logiky rozložení. Poskytovatel se postará o inicializaci všech základních logik a správné propojení různých komponent. Dále budeme používat různé základní komponenty poskytované rozhraním UI Framework k vytváření komunikačních prostředí. Můžete přizpůsobit rozložení těchto komponent a přidat všechny další vlastní součásti, které chcete s nimi vykreslit. 

## <a name="build-ui-framework-calling-component-experiences"></a>Rozhraní Build UI, které volá prostředí komponent

Pro volání budeme používat `MediaGallery` `MediaControls` komponenty a. Další informace o těchto funkcích najdete v tématu [Možnosti rozhraní .NET Framework](./../../concepts/ui-framework/ui-sdk-features.md). Chcete-li spustit, ve `src` složce vytvořte nový soubor s názvem `CallingComponents.js` . Tady inicializujeme komponentu funkcí, která bude obsahovat naše základní komponenty pro následné importování `app.js` . Můžete přidat další rozložení a styl kolem komponent. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

V dolní části tohoto souboru jsme exportovali volání komponent pomocí  `connectFuncsToContext` metody z rozhraní UI a připojili volající součásti uživatelského rozhraní k základnímu stavu pomocí funkce Mapping `MapToCallingSetupProps` . Tato metoda vede k naplnění komponenty, která má své hodnoty props, kterou pak použijeme ke kontrole stavu a připojení k volání. Pomocí `isCallInitialized` Vlastnosti zkontrolujte, zda `CallAgent` je připravena a pak použijte `joinCall` metodu pro připojení k. ROZHRANÍ .NET Framework podporuje funkce vlastního mapování, které se mají použít pro scénáře, ve kterých vývojáři chtějí řídit způsob, jakým se data odešlou do komponent.

## <a name="build-ui-framework-chat-component-experiences"></a>Prostředí pro vytváření komponent chatu v rozhraní Build UI

V případě chatu použijeme `ChatThread` komponenty a `SendBox` . Další informace o těchto součástech naleznete v tématu [Možnosti architektury uživatelského rozhraní](./../../concepts/ui-framework/ui-sdk-features.md). Chcete-li spustit, ve `src` složce vytvořte nový soubor s názvem `ChatComponents.js` . Tady inicializujeme komponentu funkcí, která bude obsahovat naše základní komponenty pro následné importování `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Přidání komponent volání a chatu do hlavní aplikace

Zpět v `app.js` souboru teď přidáme komponenty do `CallingProvider` a `ChatProvider` podobného příkladu.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

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
> [Vyzkoušet složené součásti architektury uživatelského rozhraní](./get-started-with-composites.md)

Další informace naleznete v následujících zdrojích:
- [Přehled rozhraní .NET Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Možnosti architektury uživatelského rozhraní](./../../concepts/ui-framework/ui-sdk-features.md)
