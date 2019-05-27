---
title: zahrnout soubor
description: zahrnout soubor
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/25/2018
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 894dd5ea7270390780813b647fe7a8b4c0f173bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66139898"
---
1. Klikněte na tlačítko **App Services**, vyberte svůj back-end Mobile Apps, vyberte **Rychlý start** a potom vyberte svou klientskou platformu (iOS, Android, Xamarin, Cordova).

    ![Azure Portal se zvýrazněnou možností Rychlý start Mobile Apps][quickstart]

1. Pokud není nakonfigurované připojení k databázi, vytvořte ho následujícím způsobem:

    ![Azure Portal s připojením k databázi pro Mobile Apps][connect]

    a. Vytvořte novou databázi SQL a server. V poli s názvem připojovacího řetězce možná budete muset ponechat výchozí hodnotu MS_TableConnectionString, abyste mohli dokončit níže uvedený krok 3.

    ![Azure Portal s vytvořením nové databáze a serveru pro Mobile Apps][server]

    b. Počkejte na úspěšné vytvoření datového připojení.

    ![Oznámení na webu Azure Portal o úspěšném vytvoření datového připojení][notification]

    c. Vytvoření datového připojení musí proběhnout úspěšně.

    ![Oznámení na webu Azure Portal s textem „Už máte datové připojení“][already-connection]

1. V části **2. Vytvoření rozhraní API tabulky** vyberte jako **Jazyk back-endu** možnost Node.js.

1. Přijměte potvrzení a potom vyberte **Create TodoItem table** (Vytvořit tabulku TodoItem).
    Tato akce ve vaší databázi vytvoří novou tabulku úkolů.

    >[!IMPORTANT]
    > Přepnutím stávajícího back-endu na Node.js se přepíše veškerý obsah. Pokud místo toho chcete vytvořit back-end v .NET, přečtěte si téma [Práce se sadou SDK serveru back-end v .NET pro Mobile Apps][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
