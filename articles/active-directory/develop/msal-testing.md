---
title: Testování aplikací pro Microsoft Authentication Library (MSAL) | Azure
description: Další informace o testování aplikací pro Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/28/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3495ad5f691ac57b69ab5d3efcd5436cc66e9a6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307525"
---
# <a name="testing-msal-applications"></a>Testování aplikací MSAL
Tento článek popisuje návrhy pro testování aplikací pro Microsoft Authentication Library (MSAL).

## <a name="unit-testing"></a>Testování částí
Rozhraní API MSAL [Tvůrce modelu](https://wikipedia.org/wiki/Builder_pattern) silně. Tvůrci jsou složitý a zdlouhavý napodobení. Namísto toho doporučujeme zabalení veškerou logiku ověřování za rozhraní a model, který ve vaší aplikaci.

## <a name="end-to-end-testing"></a>Testování začátku do konce
Pro účely testování začátku do konce, můžete nastavit testovací účty, testování aplikací nebo i oddělení adresáře. Uživatelské jméno a hesla je možné nasadit prostřednictvím kanálu průběžné integrace (například tajného kódu sestavení proměnné v Azure DevOps). Jiné strategie je mít testovat pověření na [služby Key Vault](/azure/key-vault/) a nakonfigurujte počítač, který spouští testy pro přístup k trezoru klíčů), například tím, že instalace certifikátu. Nebojte se využít pro MSAL [strategie pro přístup ke službě Key Vault](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.LabInfrastructure/KeyVaultSecretsProvider.cs#L112).

Jakmile dojde k získání tokenu, přístupového tokenu a tokenu obnovení jsou uložené v mezipaměti. První je doba platnosti jednu hodinu, ten několika měsíců. Když vyprší platnost přístupového tokenu, MSAL automaticky použít obnovovací token získat nový, bez zásahu uživatele. Můžete se spolehnout na toto chování zřídit testy.

Pokud máte nakonfigurovaný podmíněný přístup, bude obtížné automatizace kolem něj. Ji bude snazší mít manuální krok, která se zabývá podmíněný přístup (například vícefaktorové ověřování), které přidáte do mezipaměti MSAL tokeny a potom využívají tiché token akvizice, to znamená, závisí na dříve přihlášeného uživatele.

## <a name="web-apps"></a>Webové aplikace
Využijte Selenium nebo ekvivalentní technologie k automatizaci webové aplikace. Načtěte ze zabezpečeného umístění v úložišti uživatelských jmen a hesel.

## <a name="daemon-apps"></a>Aplikace démonů
Aplikace démonů použití předem nasazené tajných kódů (hesla nebo certifikáty) mluvit k Azure Active Directory (AAD). Můžete nasadit tajného kódu do testovacího prostředí nebo použít token ukládání do mezipaměti techniku ke zřízení testy. [Udělení přihlašovacích údajů klienta](msal-authentication-flows.md#client-credentials) neobsahuje načíst tokeny obnovení, pouze přístupové tokeny, které vyprší za jednu hodinu.

## <a name="native-client-apps"></a>Nativní klientské aplikace
Pro nativní klienty existuje několik přístupů k testování:

* Použití [poskytnutí uživatelského jména a hesla](msal-authentication-flows.md#usernamepassword) načíst token neinteraktivním způsobem. Tento tok se nedoporučuje v produkčním prostředí, ale je vhodné použít pro testování.

* Použití rozhraní, jako je Appium nebo Xamarin.Test, poskytující rozhraní automatizace pro vaši aplikaci a MSAL vytvořili prohlížeče.

* Knihovna MSAL zpřístupní bod rozšiřitelnosti, které umožňuje vývojářům vkládat své vlastní prostředí v prohlížeči. Knihovna MSAL používá tým to interně pro otestování scénářů, interaktivní ověřování. Podívejte se na [projekt testu této .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.Integration.net45/SeleniumTests/InteractiveFlowTests.cs) se dozvíte, jak vkládat [s využitím Selenium prohlížeče](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/tree/master/tests/Microsoft.Identity.Test.Integration.net45/Infrastructure) ověřování, která dokáže zpracovat.

## <a name="xamarin-apps"></a>Aplikace pro Xamarin
Tým MSAL aktuálně spuštěných testů v aplikaci Xamarin, která používá MSAL.NET; Používáme [App Center](https://appcenter.ms/apps) ke správě zařízení, testovací běhy atd. Je rozhraní pro testování [Xamarin.UITest](/appcenter/test-cloud/uitest/). Omezení, která jsme našli je to, že nelze testovat prohlížeče systému, pouze vložené prohlížeče.

Při vyhodnocování testovací rozhraní, je vhodné také podívat Appium a jiných rozhraní pro testování, jakož i jiných poskytovatelů CI/CD s mobilních místa.

## <a name="testing-the-token-cache"></a>Testování mezipaměť tokenu
Bez ohledu na to, jakou platformu používáte ukládá MSAL tokeny mezipaměť tokenu. Na některých platformách dáte MSAL jak k serializaci tuto mezipaměť. Na mobilních platformách MSAL serializuje mezipaměti za vás. Z pohledu aplikace mezipaměť tokenu zodpovídá za tři věci:

* ukládání tokeny v mezipaměti, po které byly získány (například pomocí `AcquireTokenInteractive` metoda)
* Při volání metody načítání tokeny z mezipaměti `AcquireTokenSilent` – metoda
* Při volání metody načítá metadata účtu z mezipaměti `GetAccount` – metoda

Takže pokud chcete otestovat mezipaměti scénáře, zvažte vytvoření scénář, který by:

* získat jednu nebo více tokenů (například pomocí [poskytnutí uživatelského jména a hesla](msal-authentication-flows.md#usernamepassword) tok, který je nejjednodušší pro účely testování)
* Ověřte, že `GetAccounts` funguje
* Ověřte, že `AcquireTokenSilent` funguje

Můžete také vyzkoušet:

* restartování aplikace se neodebere do mezipaměti
* `AcquireTokenSilent` neaktualizuje obnovovací token (to znamená síť volání do AAD), ale slouží přístupový token, pokud nevypršela jeho platnost. Můžete dosáhnout tím a další scénáře související s HTTP převzetí kontroly nad klienta HTTP.  Příklad rozhraní .NET najdete v tématu [poskytuje vlastní HttpClient](msal-net-provide-httpclient.md)

## <a name="feedback"></a>Váš názor
Je možné [protokolu problémy](developer-support-help-options.md#create-a-github-issue) nebo položit dotazy týkající se testování. Poskytování dobré testovacího prostředí je jedním z cílů týmu.

## <a name="next-steps"></a>Další postup
Další informace o implementaci [protokolování](msal-logging.md) ve vaší aplikaci MSAL.