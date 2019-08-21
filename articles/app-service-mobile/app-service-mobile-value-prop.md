---
title: Informace o funkci Mobile Apps v Azure App Service
description: Zjistěte, jaké výhody App Service přináší pro vaše firemní mobilní aplikace.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a3817b3e9f62a863f3fea9305ef169db2eb84491
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637945"
---
# <a name="getting-started"></a>Informace o funkci Mobile Apps v Azure App Service

> [!NOTE]
> Visual Studio App Center je investována v nových a integrovaných službách z centrálního vývoje mobilních aplikací. Vývojáři mohou pomocísestavování, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a diagnostických služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu. Podívejte se [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-value-prop) ještě dnes.
>

Azure App Service nabízí plně spravovanou [platformu jako službu](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) pro profesionální vývojáře. Tato služba přináší bohatou sadu možností pro webové, mobilní a integrační scénáře. 

Funkce Mobile Apps služby Azure App Service nabízí podnikovým vývojářům a systémovým integrátorům vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací.

![Vizuální přehled možností funkce Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Proč Mobile Apps?
S funkcí Mobile Apps můžete:

* **Sestavování nativních aplikací a aplikací pro různé platformy**: Bez ohledu na to, jestli vytváříte nativní aplikace pro iOS, Android a Windows nebo aplikace Xamarin nebo Cordova pro různé platformy (PhoneGap), můžete využít výhod App Service pomocí nativních sad SDK.
* **Připojte se k podnikovým systémům**: Pomocí funkce Mobile Apps můžete přidat podnikové přihlášení v řádu minut a připojit se k místním nebo cloudovým prostředkům vaší organizace.
* **Vytvářejte aplikace připravené pro offline práci pomocí synchronizace dat**: Vytvářejte aplikace, které pracují v režimu offline, a využijte Mobile Apps k synchronizaci dat na pozadí, když se k dispozici v jakémkoli podnikovém zdroji dat nebo v rozhraních API SaaS (software jako služba).
* **Nabízená oznámení na miliony v řádu sekund**: Zapojte své zákazníky pomocí okamžitých nabízených oznámení na jakékoli zařízení, přizpůsobené podle svých potřeb a odešlete, když je čas správný.

## <a name="mobile-apps-features"></a>Funkce Mobile Apps
Pro vývoj mobilních řešení využívajících cloud jsou důležité následující funkce:

* **Ověřování a autorizace**: Podpora pro zprostředkovatele identity, včetně Azure Active Directory pro podnikové ověřování a poskytovatelů sociálních sítí, jako je Facebook, Google, Twitter a účty Microsoft. Funkce Mobile Apps nabízí službu OAuth 2.0 pro každého zprostředkovatele. Můžete také integrovat sadu SDK pro daného zprostředkovatele, aby se zajistila specifická funkcionalita.

    Projděte si další informace o [funkcích ověřování].

* **Přístup k datům**: Mobile Apps poskytuje zdroj dat OData V3 s mobilním nasazením, který je propojený s Azure SQL Database nebo místním SQL serverem. Protože tato služba může být založena na rozhraní Entity Framework, můžete ji snadno integrovat s jinými poskytovateli dat NoSQL i SQL, včetně [Azure Table Storage], MongoDB, [Azure Cosmos DB] a poskytovateli rozhraní API pro SaaS, jako je Office 365 a Salesforce.com.

* **Offline synchronizace**: Klientské sady SDK usnadňují vytváření robustních a reagujících mobilních aplikací, které pracují s offline datovou sadou. Tuto datovou sadu můžete automaticky synchronizovat s daty back-end, včetně podpory řešení konfliktů.

  Projděte si další informace o [datových funkcích].

* **Nabízená oznámení**: Klientské sady SDK se hladce integrují s registračními funkcemi Azure Notification Hubs, takže můžete posílat nabízená oznámení milionům uživatelů současně.

  Projděte si další informace o [funkcích pro nabízená oznámení].

* **Klientské sady SDK**: K dispozici je kompletní sada klientských sad SDK, které zahrnují nativní vývoj ([iOS], [Android]a [Windows]), vývoj pro různé platformy ([Xamarin.iOS a Xamarin.Android], [Xamarin.Forms]) a hybridní aplikace. vývoj ([Apache Cordova]). Všechny klientské sady SDK jsou k dispozici s licencí MIT a jako open-source.

## <a name="azure-app-service-features"></a>Funkce Azure App Service
U systémů pro mobilní zařízení v provozním prostředí jsou užitečné následující funkce platformy:

* Automatické **škálování**: Pomocí App Service můžete rychle škálovat nebo škálovat na zpracování libovolného příchozího zatížení zákazníků. Počet a velikost virtuálních počítačů můžete vybrat ručně nebo je možné u back-endu mobilní aplikace nastavit automatické škálování podle plánu nebo momentální zátěže.

  Projděte si další informace o [automatickém škálování].

* Přípravná **prostředí**: App Service může spustit více verzí webu, takže můžete provádět testování a/B, testovat v produkčním prostředí v rámci většího plánu DevOps a provádět místní přípravu nového back-endu.

  Projděte si další informace o [přípravná prostředí].

* **Průběžné nasazování**: App Service lze integrovat s běžnými systémy _správy zdrojového kódu_ (SCM), což vám umožní snadno nasadit novou verzi back-endu.

  Projděte si další informace o [možnostech nasazení](../app-service/deploy-local-git.md).

* **Virtuální síť**: App Service se můžou připojit k místním prostředkům pomocí služby Virtual Network, Azure ExpressRoute nebo hybridních připojení.

  Projděte si další informace pro [hybridní připojení], [virtuální sítě] a [ExpressRoute].

* **Izolovaná a vyhrazená prostředí**: Pro bezpečné spouštění aplikací Azure App Service můžete App Service spustit v plně izolovaném a vyhrazeném prostředí. Toto prostředí je ideální pro úlohy aplikací vyžadující velké škálování, izolaci nebo zabezpečený přístup k síti.

  Projděte si další informace o [Prostředí App Service].

## <a name="next-steps"></a>Další postup

Pokud chcete začít s funkcí Mobile Apps ve službě Azure App Service, dokončete [úvodní] kurz. Tento kurz popisuje základy vytváření libovolného mobilního back-endu a klienta. Zabývá se také integrací ověřování, offline synchronizací a nabízenými oznámeními. Kurz můžete absolvovat vícekrát, pokaždé pro jinou klientskou aplikaci.

Pokud chcete další informace o funkci Mobile Apps, prohlédněte si naši [mapu kurzů].
Další informace o platformě Azure App Service najdete v článku [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[úvodní]: app-service-mobile-ios-get-started.md
[Azure Table storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[funkcích ověřování]: ./app-service-mobile-auth.md
[datových funkcích]: ./app-service-mobile-offline-data-sync.md
[funkcích pro nabízená oznámení]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS a Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatickém škálování]: ../app-service/manage-scale-up.md
[přípravná prostředí]: ../app-service/deploy-staging-slots.md
[hybridní připojení]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuální sítě]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[Prostředí App Service]: ../app-service/environment/intro.md
[mapu kurzů]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure App Service]: ../app-service/overview.md
