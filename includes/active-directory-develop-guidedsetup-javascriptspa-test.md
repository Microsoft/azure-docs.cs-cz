---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 9817e94ba77c83b8620274ba41f9d862b6a5ce6d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293615"
---
## <a name="test-your-code"></a>Testování kódu

### <a name="test-with-visual-studio"></a>Testování pomocí sady Visual Studio
Pokud používáte Visual Studio, stiskněte **F5** ke spuštění projektu. V prohlížeči se otevře http://<span></span>localhost: {port} umístění a **volat Microsoft Graph API** tlačítko.

<p/><!-- -->

### <a name="test-with-node-or-other-web-server"></a>Test uzlu, nebo jiný webový server
Pokud nepoužíváte Visual Studio, ujistěte se, že váš webový server je spuštěna. Konfigurace serveru tak, aby naslouchala na portu TCP, který je založen na umístění vašeho **index.html** souboru. U uzlu spusťte webový server tak, aby naslouchala na port spuštěním následujících příkazů na příkazovém řádku ve složce aplikace:

```bash
npm install
node server.js
```
Otevřete prohlížeč a zadat http://<span></span>localhost:30662 nebo http://<span></span>localhost: {port} kde **port** je port, který váš webový server naslouchá. Zobrazí se obsah souboru index.html a **volat Microsoft Graph API** tlačítko.

## <a name="test-your-application"></a>Testování aplikace

Po načtení souboru index.html prohlížeči vyberte **volat Microsoft Graph API**. Při prvním spuštění aplikace, prohlížeč vás přesměruje na koncový bod v2.0 Microsoft Azure Active Directory (Azure AD) a zobrazí se výzva k přihlášení:

![Přihlaste se ke svému účtu JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Zadejte svůj souhlas pro přístup k aplikaci

Při prvním přihlášení do aplikace, budete vyzváni k souhlasíte s tím, aby aplikace k profilu a pro přihlášení:

![Zadejte svůj souhlas pro přístup k aplikaci](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Zobrazení výsledků aplikace
Po přihlášení, zobrazí se informace z vašeho profilu uživatele v **odpověď volání rozhraní API Graph** pole.
 
![Očekávané výsledky z volání rozhraní Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Zobrazí se také základní informace o tokenu, který se získal v **přístupový Token** a **deklarací identity tokenu ID** polí.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API **user.read** obory a čtení profilu uživatele. Tento obor se automaticky přidá ve výchozím nastavení v každé aplikaci, která je zaregistrovaná na portálu pro registraci. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back endového serveru může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API **Calendars.Read** oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte **Calendars.Read** delegovaná oprávnění aplikace informace o registraci. Pak přidejte **Calendars.Read** rozsah **acquireTokenSilent** volání. 

>[!NOTE]
>Uživatel může zobrazit výzva pro další souhlasy zvýšit počet oborů.

Pokud back endového rozhraní API nevyžaduje oboru (nedoporučuje se), můžete použít **clientId** jako obor v **acquireTokenSilent** a **acquireTokenRedirect** volání.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
