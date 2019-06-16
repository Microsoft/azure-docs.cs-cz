---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133233"
---
## <a name="test-your-code"></a>Testování kódu

Testování kódu pomocí některé z těchto prostředích.

### <a name="test-with-nodejs"></a>Testování s využitím Node.js

Pokud nepoužíváte Visual Studio, ujistěte se, že váš webový server je spuštěna.

1. Konfigurace serveru tak, aby naslouchala na portu TCP, který je založen na umístění vašeho *index.html* souboru. Pro Node.js spusťte webový server tak, aby naslouchala na port spuštěním následujících příkazů na příkazovém řádku ve složce aplikace:

    ```bash
    npm install
    node server.js
    ```
1. V prohlížeči zadejte **http://\<span >\</span > localhost:30662** nebo **http://\<span >\</span > localhost: {port}** , kde *port* je port, který váš webový server naslouchá. Zobrazí se obsah vaší *index.html* souboru a **Sign In** tlačítko.

### <a name="test-with-visual-studio"></a>Testování pomocí sady Visual Studio

Pokud používáte Visual Studio, vyberte projekt řešení a potom stisknutím klávesy F5 ke spuštění projektu. V prohlížeči se otevře http://<span></span>localhost: {port} umístění a **Sign In** tlačítko bude viditelné.

## <a name="test-your-application"></a>Testování aplikace

Po prohlížeč načte vaše *index.html* souboru, vyberte **Sign In**. Budete vyzváni k přihlášení pomocí koncového bodu Microsoft identity platform:

![Účet přihlášení okno JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Zadejte svůj souhlas pro přístup k aplikaci

Při prvním přihlášení do vaší aplikace se zobrazí výzva k přihlášení a získání přístupu k vašemu profilu:

![V okně "Požadováno oprávnění"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Zobrazení výsledků aplikace

Po přihlášení, údajům vašeho uživatelského profilu je vrácená v odpovědi rozhraní Microsoft Graph API, který se zobrazí na stránce.

![Výsledky z volání rozhraní Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API *user.read* obory a čtení profilu uživatele. Tento obor se automaticky přidá ve výchozím nastavení v každé aplikaci, která je zaregistrovaná na portálu pro registraci. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back endového serveru může vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje *Calendars.Read* oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte *Calendars.Read* delegovaná oprávnění aplikace informace o registraci. Pak přidejte *Calendars.Read* rozsah `acquireTokenSilent` volání.

>[!NOTE]
>Uživatel může zobrazit výzva pro další souhlasy zvýšit počet oborů.

Pokud back endového rozhraní API nevyžaduje oboru (nedoporučuje se), můžete použít *clientId* jako oboru ve volání k získání tokenů.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
